# Create footer for all pages
The next step is to create a footer for all pages of your portfolio site. You can use this footer to display your social media links to ensure consistency throughout the site.

## Add a base application
<!-- Ask Thibaud for the reason for creating the base app -->
Generate a base app for your portfolio site by running `python manage.py startapp base`.

After creating the base app, you need to add it to your site. In your `<mysite>/settings/base.py` file, add _"base"_ to the `INSTALLED_APPS` list.

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

In your `NavigationSettings` model, there are two imports that you may be unfamiliar with:
1. `from modelcluster.models import ClusterableModel`
2. `from wagtail.contrib.settings.models import ( BaseGenericSetting, register_setting)`

<!-- Ask Thibaud for resources explaining  `from modelcluster.models import ClusterableModel` 

Ask Thibaud if there is any special reason for using parentheses to import modeules apart from readability
-->

The `wagtail.contrib.settings` module allows you to define models that hold settings that are either common across all site records. To use the, module you need to install it. Go to your <mysite>/settings/base.py and add `wagtail.contrib.settings` to the `INSTALLED_APPS` list.