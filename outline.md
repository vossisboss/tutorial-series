## Introduction

* Did you enjoy building the blog in our Getting Started tutorial? Now we’ll show you how to expand it into a deployable portfolio site in this [TBD] - part tutorial series
* Prerequisites and requirements


## Steps

1. ✅ Customize the home page
    a. Add a field for a headshot or graphic
    b. Add a field for text that can include a short bio or enticing “hire me” text.
    c. Add a documents field for a downloadable copy of a resume/CV to demonstrate how document fields work 
    d. Add template for displaying these items.
2. Create footer for all pages containing social media icons to reinforce Snippets lesson from first tutorial
    a. Fields for social media snippets should include:
        i. Name
        ii. Icon
        iii. Link
    b. Create template for the site footer
    c. Update base.html to include template
3. ✅ Set up a menu for linking to the homepage and other pages as you add them
    a. Implementation: [https://github.com/thibaudcolas/your-wagtail-portfolio#set-up-a-menu-for-linking-to-the-homepage-and-other-pages-as-you-add-them](https://github.com/thibaudcolas/your-wagtail-portfolio#set-up-a-menu-for-linking-to-the-homepage-and-other-pages-as-you-add-them) 
    b. Create menu template
    c. Add menu to base.html
4. ✅ Styling
5. ✅ Create a contact page
    a. Demonstrate how to create a Wagtail form page
    b. Fields needed for form:
        i. Email address
        ii. Large text field for the message
6. Add a Projects/Skills page
    a. This page could be a good opportunity to show people how to group fields together in single blocks depending on how Storm wants to structure the code so there is a single block for each project/skill
    b. Fields for each skill/project
        i. Name (text, required)
        ii. Description (text, required)
        iii. Link to project (optional)
        iv. Image (optional since not everyone has visuals for their projects)
    c. Add template needed for displaying the page
    d. Add page to main menu
7. Add search functionality to the website to demonstrate Wagtail search basics
    a. Make blog and projects/skills content searchable
    b. Add pagination to the search
8. Website deployment
    a. Requirement: a domain name (for HTTPS, won’t work with ip addresses)
    b. Configuring a VM on Digital Ocean or some other platform
    c. Configuring a Postgres database
    d. Setting up HTTPS using Lets Encrypt
    e. Deploying code
    f. Configure static and media files