# Set up your Wagtail project

Before setting up Wagtail, make sure you have Python installed on your local machine. If you don't have Python installed, follow the instructions in the [Install dependencies](https://docs.wagtail.org/en/stable/getting_started/tutorial.html#install-dependencies) section of the Your first Wagtail site tutorial.

## Create and activate your virtual environment

This tutorial uses [venv](https://docs.python.org/3/tutorial/venv.html)for managing virtual environments. Choose the appropriate set of commands for your operating system:

On **Windows (cmd.exe)**:

```doscon
py -m venv portfoliosite\env

# then

portfoliosite\env\Scripts\activate.bat

# if mysite\env\Scripts\activate.bat doesn't work, run:

portfoliosite\env\Scripts\activate
```

On **On GNU/Linux or MacOS (bash)**:

```sh
python -m venv portfoliosite/env
# then:
source portfoliosite/env/bin/activate
```

For other shell environments, refer to the [venv documentation](https://docs.python.org/3/library/venv.html).

## Install Wagtail

Install Wagtail and its dependencies by running the following command:

```sh
pip install wagtail
```

## Generate your portfolio site

Generate your portfolio site by running the command:

```sh
wagtail start portfoliosite portfoliosite
```

## Install your project dependencies
To install your project dependencies, navigate to your `portfoliosite` folder in your terminal:

```sh
cd portfoliosite
```

Then, install your project dependencies by running the command:

```sh
pip install -r requirements.txt
```

## Create your database

In this tutorial, you will use the default Wagtail database, SQLite. Now migrate your database by running the command:

```sh
python manage.py migrate
```

## Create an admin user

The admin user will have the default login access to your admin interface. To create an admin user, run the following command:

```sh
python manage.py createsuperuser
```

After running the command, follow the prompts in your terminal to enter your email address, username, and password.

Now you have your portfolio project up and running smoothly. 