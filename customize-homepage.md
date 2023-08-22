Customize the home page
  a. Add a field for a headshot or graphic
  b. Add a field for text that can include a short bio or enticing “hire me” text.
  c. Add a documents field for a downloadable copy of a resume/CV to demonstrate how document fields work 
  d. Add template for displaying these items.


When building your portfolio website, the first step is to set up and personalize your homepage. The homepage is your chance to make an excellent first impression and convey the core message of your portfolio. So your homepage should include the following features:

1. Introduction: A concise introduction captures visitors' attention.
2. Biography: Include a brief biography that introduces yourself. This section should mention your name, role, expertise, and unique qualities.
3. Hero Image: This may be a professional headshot or graphical representation that showcases your work and adds visual appeal.
4. Call to Action (CTA): Incorporate a CTA that guides visitors to take a specific action, such as "View Portfolio," "Hire Me," or "Learn More."
5. Downloadable Resume

In this section, you'll learn how to add features 1 to 4 to your homepage. We'll cover adding your resume or CV later in the tutorial.

Now, modify your `home/models` to include the following:

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

You might already be familiar with the different parts of your Homepage model. The `image` field is a `ForeignKey` referencing Wagtail's built-in Image model for storing images. Similarly, `hero_cta_link` is a `ForeignKey` to `wagtailcore.Page`, representing a page instance. Using `on_delete=models.SET_NULL` ensures that if you remove an image or hero link from your admin interface, the `image` and `hero_cta_link` fields on your Homepage will be set to null, preserving the entries. The `related_name` attribute defines the reverse relationship from the related model, providing a readable attribute name when accessing associated objects. When you use `related_name="+"`, then you don't want to create a reverse relationship for your `ForeignKey` fields. In other words, you're instructing Django not to create a way to access `image` from `wagtailimages.Image` or `hero_cta_link` from 
`wagtailcore.Page`. 

While `body` is a `RichTextField`, `hero_text` and `hero_cta` are `CharField`, a typical Django string field for storing short texts.



