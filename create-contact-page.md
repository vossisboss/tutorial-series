<!-- 
Ask Thibaud if the addition of panels and other imported libraries have to follow a specific order.
-->

The contact page of any website serves as a means for visitors to contact the website owner. Having one on your portfolio site will help you connect with potential clients, employers, or other professionals interested in your skills.

In this tutorial, you'll add a contact page to your portfolio site using the Wagtail forms. 

Start by modifying your `base/models.py` file:

```python
from django.db import models

# import parentalKey:
from modelcluster.fields import ParentalKey

# import FieldRowPanel and FieldRowPanel:
from wagtail.admin.panels import (
    FieldPanel,
    FieldRowPanel,
    InlinePanel,
    MultiFieldPanel,
    PublishingPanel,
)

from wagtail.fields import RichTextField
from wagtail.models import (
    DraftStateMixin,
    PreviewableMixin,
    RevisionMixin,
    TranslatableMixin,
)

# import AbstractEmailForm and AbstractFormField:
from wagtail.contrib.forms.models import AbstractEmailForm, AbstractFormField

# import FormSubmissionsPanel:
from wagtail.contrib.forms.panels import FormSubmissionsPanel
from wagtail.contrib.settings.models import (
    BaseGenericSetting,
    register_setting,
)
from wagtail.snippets.models import register_snippet


# ... keep the definition of NavigationSettings and FooterText. Add FormField and FormPage:
class FormField(AbstractFormField):
    page = ParentalKey('FormPage', on_delete=models.CASCADE, related_name='form_fields')


class FormPage(AbstractEmailForm):
    intro = RichTextField(blank=True)
    thank_you_text = RichTextField(blank=True)

    content_panels = AbstractEmailForm.content_panels + [
        FormSubmissionsPanel(),
        FieldPanel('intro'),
        InlinePanel('form_fields', label="Form fields"),
        FieldPanel('thank_you_text'),
        MultiFieldPanel([
            FieldRowPanel([
                FieldPanel('from_address'),
                FieldPanel('to_address'),
            ]),
            FieldPanel('subject'),
        ], "Email"),
    ]
```

In the preceding code, your `FormField` model inherits from `AbstractFormField`. `AbstractFormField` allows you to define any form field type of your choice in the admin interface. `page = ParentalKey('FormPage', on_delete=models.CASCADE, related_name='form_fields')` defines a parent-child relationship between the `FormField` and `FormPage` models.

On the other hand, your `FormPage` model inherits from `AbstractEmailForm`. Unlike `AbstractFormField`, `AbstractEmailForm` offers a form-to-email capability. Also, it defines the `to_address`, `from_address`, and `subject` fields. It expects a `form_fields` to be defined. 

After defining your `FormField` and `FormPage` models, you have to create `form_page` and `form_page_landing` templates. The `form_page` template differs from a standard Wagtail template because it's passed a variable named `form` containing a Django `Form` object in addition to the usual `Page` variable. The `form_page_landing.html`, on the other hand is a standard Wagtail template displayed after the user makes a successful form submission.

<!-- Ask Thibaud for a web page explaining the Django `Form` object -->

Now, create a `base/templates/base/form_page.html` file and add the following to it:

```html+django
{% extends "base.html" %}
{% load wagtailcore_tags %}

{% block body_class %}template-formpage{% endblock %}

{% block content %}
    <h1>{{ page.title }}</h1>
    <div>{{ page.intro|richtext }}</div>

    <form class="page-form" action="{% pageurl page %}" method="POST">
        {% csrf_token %}
        {{ form.as_div }}
        <button type="Submit">Submit</button>
    </form>
{% endblock content %}
```

Also, create a `base/templates/base/form_page_landing.html` file and add the following to it:

```html+django
{% extends "base.html" %}
{% load wagtailcore_tags %}

{% block body_class %}template-formpage{% endblock %}

{% block content %}
    <h1>{{ page.title }}</h1>
    <div>{{ page.thank_you_text|richtext }}</div>
{% endblock content %}
```

Well done! You've added all the necessary lines of code and templates that will allow you to add a contact page to your portfolio website. 

However, there are five things left to do: 
1. Migrate your database by running `python manage.py makemigrations` and then `python manage.py migrate`.
2. Create a **Form page** as a child page of **Home** by following these steps:

a. Go to your admin interface
b. Click `Pages` in your [Sidebar](https://guide.wagtail.org/en-latest/how-to-guides/find-your-way-around/#the-sidebar)
c. click `Home`
d. click the `...` icon at the top of the resulting page
e. click `Home`
f. click `add child page`
g. click `Form page`

3. Add the necessary data. To do this, follow these steps:
4. Publish your `Form Page`
5. Style your contact page by adding the following CSS to your `mysite/static/css/mysite.css` file:

```css
.page-form label {
  display: block;
  margin-top: 10px;
  margin-bottom: 5px;
}

.page-form :is(textarea, input, select) {
  width: 100%;
  max-width: 500px;
  min-height: 40px;
  margin-top: 5px;
  margin-bottom: 10px;
}

.page-form .helptext {
  font-style: italic;
}
```

<!-- Inform Thibaud that there is no code to add the Contact page to the site menu 
Add instrductions for users to tick the Show in menu checkbox to show contact page in site menu
-->