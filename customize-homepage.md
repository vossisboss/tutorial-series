# Customize your home page

When building your portfolio website, the first step is to set up and personalize your homepage. The homepage is your chance to make an excellent first impression and convey the core message of your portfolio. So your homepage should include the following features:

1. Introduction: A concise introduction captures visitors' attention.
2. Biography: Include a brief biography that introduces yourself. This section should mention your name, role, expertise, and unique qualities.
3. Hero Image: This may be a professional headshot or graphical representation that showcases your work and adds visual appeal.
4. Call to Action (CTA): Incorporate a CTA that guides visitors to take a specific action, such as "View Portfolio," "Hire Me," or "Learn More."
5. Downloadable Resume

In this section, you'll learn how to add features 1 to 4 to your homepage. You will add your resume or CV later in the tutorial.

Now, modify your `home/models` file to include the following:

```python
from django.db import models

from wagtail.models import Page
from wagtail.fields import RichTextField
from wagtail.admin.panels import FieldPanel
from wagtail.admin.panels import FieldPanel, MultiFieldPanel


class HomePage(Page):
    # Hero section of HomePage
    image = models.ForeignKey(
        "wagtailimages.Image",
        null=True,
        blank=True,
        on_delete=models.SET_NULL,
        related_name="+",
        help_text="Homepage image",
    )
    hero_text = models.CharField(
        blank=True,
        max_length=255, help_text="Write an introduction for the site"
    )
    hero_cta = models.CharField(
        blank=True,
        verbose_name="Hero CTA",
        max_length=255,
        help_text="Text to display on Call to Action",
    )
    hero_cta_link = models.ForeignKey(
        "wagtailcore.Page",
        null=True,
        blank=True,
        on_delete=models.SET_NULL,
        related_name="+",
        verbose_name="Hero CTA link",
        help_text="Choose a page to link to for the Call to Action",
    )

    # Body section of the HomePage
    body = RichTextField(blank=True)

    content_panels = Page.content_panels + [
        MultiFieldPanel(
            [
                FieldPanel("image"),
                FieldPanel("hero_text"),
                FieldPanel("hero_cta"),
                FieldPanel("hero_cta_link"),
            ],
            heading="Hero section",
        ),
        FieldPanel('body'),
    ]
```

You might already be familiar with the different parts of your HomePage model. The `image` field is a `ForeignKey` referencing Wagtail's built-in Image model for storing images. Similarly, `hero_cta_link` is a `ForeignKey` to `wagtailcore.Page`. The `wagtailcore.Page` is the base class for all other page types in Wagtail. This means all Wagtail pages inherit from `wagtailcore.Page`. For instance, your `class HomePage(page)` inherits from `wagtailcore.Page`.

Using `on_delete=models.SET_NULL` ensures that if you remove an image or hero link from your admin interface, the `image` and `hero_cta_link` fields on your Homepage will be set to null, preserving their entries. Read the [Django documentation](https://docs.djangoproject.com/en/4.2/ref/models/fields/#django.db.models.ForeignKey.on_delete) for more values for the `on-delete` attribute. 

The `related_name` attribute defines the reverse relationship from the related model, providing a readable attribute name when accessing associated objects. For instance, if you want to access the HomePage's `image` from `wagtailimages.Image`, you can use the value that you give to the `related_name` attribute in your QuerySet. When you use `related_name="+"`, then you don't want to create a reverse relationship for your `ForeignKey` fields. In other words, you're instructing Django not to create a way to access the HomePage's `image` from `wagtailimages.Image` and `hero_cta_link` from `wagtailcore.Page`. 

While `body` is a `RichTextField`, `hero_text` and `hero_cta` are `CharField`, a typical Django string field for storing short texts.

The [Your First Wagtail Tutorial]() already explained `content_panels`. [FieldPanel](https://docs.wagtail.org/en/stable/reference/pages/panels.html#fieldpanel) and [MultiPanel](https://docs.wagtail.org/en/stable/reference/pages/panels.html#multifieldpanel) are types of Wagtail built-in [Panels](https://docs.wagtail.org/en/stable/reference/pages/panels.html#panel-types). They're both subclasses of the base Panel class and accept all of Wagtail's `Panel` parameters in addition to their own. While the `FieldPanel` provides a widget for basic Django model fields, `MultiFieldPanel` helps you decide the structure of the editing form. For example, you can group related fields.

Now that you understand the different parts of your HomePage model, migrate your database by running:

```sh
python manage.py makemigrations
python manage.py migrate
```

After migrating your database, start your server by running:

```sh
python manage.py runserver
```

Now go to your Wagtail admin interface and follow these steps to add data to your Home page:
1. Log in with your admin user details.
2. Click pages.
3. Click the **edit** icon beside **Home**.
4. Choose an image, choose a page, and add the appropriate data to the input fields.

```Note
You can only choose the Home page to link to your Call to Action because you only have the Home page in your site. You will choose a more suitable page later in the tutorial.
```
5. Publish your Home page.

You have all the necessary data for your Home page now. You can visit your Home page in your browser by going to `http://127.0.0.1:8000`. You can't see your data, right? You probably know why you can't see the data you added to your Home page from the Wagtail admin interface. You have to modify your Homepage template to display the data. 

Delete the content of your `home/templates/home/home_page.html` file and add the following to it:

```html+django
{% extends "base.html" %}
{% load wagtailcore_tags wagtailimages_tags %}

{% block body_class %}template-homepage{% endblock %}

{% block content %}
    <div>
        <h1>{{ page.title }}</h1>
        {% image page.image fill-480x320 %}
        <p>{{ page.hero_text }}</p>
        {% if page.hero_cta_link %}
            <a href="{% pageurl page.hero_cta_link %}">
                {% firstof page.hero_cta page.hero_cta_link.title %}
            </a>
        {% endif %}
    </div>

  {{ page.body|richtext }}
{% endblock content %}
```

In your Homepage template, notice the use of `firstof`. The `firstof` template tag displays the first non-empty value from a list of variables or literals. It's helpful when you have a series of fallback options, and you want to display the first one that holds value. So, in your template, the `firstof` template tag displays `page.hero_cta` if it holds a value. If `page.hero_cta` doesn't hold a value, then it displays `page.hero_cta_link.title`.

Congratulations! You have completed the first stage of your Portfolio website 🎉🎉🎉.

<!-- Note to Damilola: Provide an explanation for StreamField and distinguish it from RichtextField; Ask for a draft explanation of wagtailcore.Page from Thibaud. Explain "firstof" in the Homepage template. You can reference the "firstof" in django documentation-->
