# Create a footer for all pages
The next step is to create a footer for all pages of your portfolio site. You can display social media links and other data in this footer, such as a copyright statement. 

## Add a base app

Now, you’re going to create a general-purpose app that you’ll call the `base` app. To generate the `base` app, run the command, `python manage.py startapp base`.

After generating the `base` app, you must install it on your site. In your `mysite/settings/base.py` file, add _"base"_ to the `INSTALLED_APPS` list.

## Add social media links

Now, go to your `base/models.py` and add the following lines of code:

```python
from django.db import models
from modelcluster.models import ClusterableModel
from wagtail.admin.panels import (
    FieldPanel,
    MultiFieldPanel,
)
from wagtail.contrib.settings.models import (
    BaseGenericSetting,
    register_setting,
)

@register_setting
class NavigationSettings(BaseGenericSetting):
    twitter_url = models.URLField(verbose_name="Twitter URL", blank=True)
    github_url = models.URLField(verbose_name="GitHub URL", blank=True)
    mastodon_url = models.URLField(verbose_name="Mastodon URL", blank=True)

    panels = [
        MultiFieldPanel(
            [
                FieldPanel("twitter_url"),
                FieldPanel("github_url"),
                FieldPanel("mastodon_url"),
            ],
            "Social settings",
        )
    ]
```

In the preceding code, the `register_setting` decorator` registers your `NavigationSettings` models. You used the `BaseGenericSetting` base model class to define a settings model that applies to all web pages rather than just one page.

Now migrate your database by running the commands, `python manage.py makemigrations` and `python manage.py migrate`. After migrating your database, restart your server and then reload your [admin interface](https://guide.wagtail.org/en-latest/concepts/wagtail-interfaces/#admin-interface). You will get the error _'wagtailsettings' is not a registered namespace_. This is because you haven't install the `wagtail.contrib.settings` module.

The `wagtail.contrib.settings` module allows you to define models that hold settings that are common across all your web pages. So, to successfully import the `BaseGenericSetting` and `register_setting`, you must install the `wagtail.contrib.settings` module on your site. To install it, go to your `mysite/settings/base.py` file and add _"wagtail.contrib.settings"_ to the `INSTALLED_APPS` list.

Reload your admin interface and click **Settings** from your [Sidebar](https://guide.wagtail.org/en-latest/how-to-guides/find-your-way-around/#the-sidebar). You can see your **Navigation Settings**. Clicking the **Navigation Settings** gives you a form to add your social media account links.

## Display social media links

You must provide a template to display the social media links in your footer. In your `mysite/templates/includes/footer.html` file, add the following:
```html+django
<footer>
    <p>Built with Wagtail</p>

    {% with twitter_url=settings.base.NavigationSettings.twitter_url github_url=settings.base.NavigationSettings.github_url mastodon_url=settings.base.NavigationSettings.mastodon_url %}
        {% if twitter_url or github_url or mastodon_url %}
            <p>
                Follow me on:
                {% if github_url %}
                    <a href="{{ github_url }}">GitHub</a>
                {% endif %}
                {% if twitter_url %}
                    <a href="{{ twitter_url }}">Twitter</a>
                {% endif %}
                {% if mastodon_url %}
                    <a href="{{ mastodon_url }}">Mastodon</a>
                {% endif %}
            </p>
        {% endif %}
    {% endwith %}
</footer>
```

```Note
You must create an `includes` folder in your `mysite/templates` folder.
```

Now go to your `mysite/templates/base.html` file and modify it:

```
<body class="{% block body_class %}{% endblock %}">
    {% wagtailuserbar %}

    {% block content %}{% endblock %}

    <!-- Add this to the file: -->
    {% include "includes/footer.html" %}

    {# Global javascript #}
    <script type="text/javascript" src="{% static 'js/portfolio.js' %}"></script>

    {% block extra_js %}
    {# Override this in templates to add extra javascript #}
    {% endblock %}
</body>
```

Also, you have to register the _settings_ context processor. To register the _settings_ context processor, modify your `mysite/settings/base.py` file:

```python
TEMPLATES = [
    {
        "BACKEND": "django.template.backends.django.DjangoTemplates",
        "DIRS": [
            os.path.join(PROJECT_DIR, "templates"),
        ],
        "APP_DIRS": True,
        "OPTIONS": {
            "context_processors": [
                "django.template.context_processors.debug",
                "django.template.context_processors.request",
                "django.contrib.auth.context_processors.auth",
                "django.contrib.messages.context_processors.messages",

                # Add this to register the _settings_ context processor:
                "wagtail.contrib.settings.context_processors.settings",
            ],
        },
    },
]
```

Now, restart your server and reload your homepage. You should see your social media links at the bottom of your homepage.

# Add footer text

Having only your social media links in your portfolio footer isn't ideal. You can add other items, like site credits and copyright notices, to your footer. One way to do this is to use the Wagtail snippet feature to create an editable footer text in your admin interface and display it in your portfolio footer.

To add a footer text  snippet to your admin interface, modify your `base/model.py` file:

```python
from django.db import models
from wagtail.admin.panels import (
    FieldPanel,
    MultiFieldPanel,

    # import PublishingPanel:
    PublishingPanel,
)

# import RichTextField:
from wagtail.fields import RichTextField

# import DraftStateMixin, PreviewableMixin, RevisionMixin, TranslatableMixin:
from wagtail.models import (
    DraftStateMixin,
    PreviewableMixin,
    RevisionMixin,
    TranslatableMixin,
)

from wagtail.contrib.settings.models import (
    BaseGenericSetting,
    register_setting,
)

# import register_snippet:
from wagtail.snippets.models import register_snippet

# ...keep the definition of the NavigationSettings model and add the FooterText model:
@register_snippet
class FooterText(
    DraftStateMixin,
    RevisionMixin,
    PreviewableMixin,
    TranslatableMixin,
    models.Model,
):

    body = RichTextField()

    panels = [
        FieldPanel("body"),
        PublishingPanel(),
    ]

    def __str__(self):
        return "Footer text"

    def get_preview_template(self, request, mode_name):
        return "base.html"

    def get_preview_context(self, request, mode_name):
        return {"footer_text": self.body}

    class Meta(TranslatableMixin.Meta):
        verbose_name_plural = "Footer Text"
```

In the preceding code, the `FooterText` class inherits from several `Mixins`, the `DraftStateMixin`, `RevisionMixin`, `PreviewableMixin`, and `TranslatableMixin`.

Since your `FooterText` model is a Wagtail snippet, you must manually add `Mixins` to your model. This is because snippets aren't Wagtail `Pages` in their own right. Wagtail `Pages` don't require `Mixins` because they already have them. 

`DraftStateMixin` is an abstract model that you can add to any non-page Django model to allow drafts or unpublished changes. The `DraftStateMixin` requires `RevisionMixin`.

`RevisionMixin` is an abstract model that you can add to any non-page Django model to allow saving revisions of its instances. Every time you edit a page, Wagtail creates a new `Revision` and saves it in your database. You can use `Revision` to find the history of all the changes that you make. `Revision` also provides a place to keep new changes before they go live.

`PreviewableMixin` is a `Mixin` class that you can add to any non-page Django model to preview any changes made.

`TranslatableMixin` is an abstract model you can add to any non-page Django model to make it translatable.

Also, with Wagtail, you can set publishing schedules for changes you made to a Snippet. With the `PulishingPanel()` method in your `FooterText`, you can schedule `revisions`.

The `__str__` method defines a human-readable string representation of an instance of the `FooterText` class. It returns the string "Footer text".

The `get_preview_template` method determines the template for rendering the preview. It returns the template name "base.html".

The `get_preview_context` method defines the context data that you can use to render the preview template. It returns a key "footer_text" with the content of the body field as its value.

The `Meta` class holds metadata about the model. It inherits from the `TranslatableMixin.Meta` class and sets the `verbose_name_plural` attribute to "Footer Text".

Now migrate your database by running `python manage.py makemigrations` and `python manage.py migrate`.

After migrating your database, reload your [admin interface](). You can now find **Snippets** in your [Sidebar](https://guide.wagtail.org/en-latest/how-to-guides/find-your-way-around/#the-sidebar). Click **Snippets** and add your footer text.

## Display your footer text

You want to make sure you display only published footer text. One way to do this is by creating a custom template tag using `inclusion_tag`. Inclusion tags allow you to include and render reusable templates with dynamic context data.

In your `base` folder, create a `templatetags` folder. Within your new `templatetags` folder, create the following files:
- `__init__.py` file
- `navigation_tags.py`

In your `base/templatetags/navigation_tags.py` file, add the following lines of code:

```python
from django import template

from base.models import FooterText

register = template.Library()


@register.inclusion_tag("base/includes/footer_text.html", takes_context=True)
def get_footer_text(context):
    footer_text = context.get("footer_text", "")

    if not footer_text:
        instance = FooterText.objects.filter(live=True).first()
        footer_text = instance.body if instance else ""

    return {
        "footer_text": footer_text,
    }
```

In the preceding code, you imported the `template` module, which provides the capability for creating and rendering template tags and filters. Also, you imported the `FooterText` model from your `base/models.py` file.

`register = template.Library()` creates an instance of the Library class from the template module. You can use this instance to register custom template tags and filters.

`@register.inclusion_tag("base/includes/footer_text.html", takes_context=True)` is a decorator that registers an inclusion tag named `get_footer_text`. `"base/includes/footer_text.html"` is the template path that you'll use to render the inclusion tag. `takes_context=True ` indicates that the context of your `footer_text.html` template will be passed as an argument to your inclusion tag function.

The `get_footer_text` inclusion tag function takes a single argument named `context. `context` represents the template context where you will use the tag.

`footer_text = context.get("footer_text", "")` tries to retrieve a value from the context using the key `footer_text`. The `footer_text` variable stores any retrieved value. If there is no `footer_text` value within the context, then the variable stores an empty string `""`.

The `if` statement in the `get_footer_text` inclusion tag function checks whether the `footer_text` exists within the context. If it doesn't, the `if` statement proceeds to retrieve the first published instance of the `FooterText` from the database. If a published instance is found, the statement extracts the `body` content from it. However, if there's no published instance available, it defaults to an empty string.

<!-- Ask thibaud if to use "published" here or "live" -->

Finally, the function returns a dictionary containing the `"footer_text"` key with the value of the retrieved `footer_text` content.
You will use this dictionary as context data when rendering your `footer_text` template.

To use the returned dictionary in your `footer_text` template, add the following to your `base/templates/base/includes/footer_text.html` file:

```html+django
{% load wagtailcore_tags %}

<div>
    {{ footer_text|richtext }}
</div>
```

```Note
You have have to create the `templates/base/includes` folder in your base folder
```

Now add your `footer_text` template to your footer by modifying your `mysite/templates/includes/footer.html` file:

```html+django
<!-- Load navigation_tags at the top of the file: -->
{% load navigation_tags %}

<footer>
    <p>Built with Wagtail</p>

    {% with twitter_url=settings.base.NavigationSettings.twitter_url github_url=settings.base.NavigationSettings.github_url mastodon_url=settings.base.NavigationSettings.mastodon_url %}
        {% if twitter_url or github_url or mastodon_url %}
            <p>
                Follow me on:
                {% if github_url %}
                    <a href="{{ github_url }}">GitHub</a>
                {% endif %}
                {% if twitter_url %}
                    <a href="{{ twitter_url }}">Twitter</a>
                {% endif %}
                {% if mastodon_url %}
                    <a href="{{ mastodon_url }}">Mastodon</a>
                {% endif %}
            </p>
        {% endif %}
    {% endwith %}

    <!-- Add footer_text: -->
    {% get_footer_text %}
</footer>
```

Well done👏! You now have a footer across all pages of your portfolio site. In the next part of this tutorial, you will learn how to set up a menu for linking to the homepage and other pages as you add them.