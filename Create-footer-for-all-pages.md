# Create footer for all pages
The next step is to create a footer for all pages of your portfolio site. You can use this footer to display your social media links. This will ensure consistency throughout the site.

## Add a base application
<!-- 
Ask Thibaud:
The reason for creating the base app;
Apart from readability, is there any special reason why this is in parentheses?
-->
Since the items you want to add to your footer are related, site-wide, and require a similar configuration, putting them in a separate app is a great idea. You can call this separate app `base`. To generate the `base` app, run the command, `python manage.py startapp base`.

After generating the `base` app, you must add it to your site. In your `<mysite>/settings/base.py` file, add _"base"_ to the `INSTALLED_APPS` list.

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
class NavigationSettings(ClusterableModel, BaseGenericSetting):
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

<!-- Ask Thibaud for resources explaining `from modelcluster.models import ClusterableModel` 

Ask Thibaud if there is any special reason for using parentheses to import modules apart from readability.
-->

The `BaseGenericSetting` base model class allows you to define a settings model that applies to all sites rather than just one site.

Now migrate your database by running the commands, `python manage.py makemigrations` and `python manage.py migrate`. After migrating your database, run your server and go to your admin interface. You will get the error `'wagtailsettings' is not a registered namespace`. THis is because you have yet to install the `wagtail.contrib.settings` module.

The `wagtail.contrib.settings` module allows you to define models that hold settings that are common across all site records or specific to each site. So, to successfully import the `BaseGenericSetting` and `register_setting`, you must install the `wagtail.contrib.settings` module to your site. To install it, go to your `<mysite>/settings/base.py` file and add `wagtail.contrib.settings` to the `INSTALLED_APPS` list.

Reload your admin interface and click **Settings** from your [Sidebar](https://guide.wagtail.org/en-latest/how-to-guides/find-your-way-around/#the-sidebar). You can see your **NavigationSettings**. Clicking the **NavigationSettings** gives you a form to add your social media account links.

To display your footer with the social media links, you must provide a template. In `<mysite>/templates/includes/footer.html`, add the following:
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
You have to create the `includes` folder in your `<mysite>/templates`.
```

Now go to your `<mysite>/templates/base.html` file and modify it:

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

<!-- 
Ask Thibaud for the differences between "all site records" as used in `wagtail.contrib.settings` and "all sites" as used in `BaseGenericSetting`.
https://docs.wagtail.org/en/stable/reference/contrib/settings.html#settings
https://docs.wagtail.org/en/stable/releases/4.0.html#global-settings-models
 -->

 <!-- 
 After completing the steps in https://github.com/thibaudcolas/your-wagtail-portfolio/commit/b8f417e, the footer text and footer links are not displayed. Ask Thibaud the reason for this. Note that it is vital for our users to see the result of a significant feature that was implemented in the tutorial. For instance, the Create footer for all pages tutorial should end with the users having a footer on their homepage and the footer links showing. 
 -->