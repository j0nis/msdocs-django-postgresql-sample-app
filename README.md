# Deploy a Python (Django) web app with PostgreSQL in Azure

This is a Python web app using the Django framework and the Azure Database for PostgreSQL relational database service. The Django app is hosted in a fully managed Azure App Service. This app is designed to be be run locally and then deployed to Azure. For more information on how to use this web app, see the tutorial [*Deploy a Python (Django or Flask) web app with PostgreSQL in Azure*](https://docs.microsoft.com/en-us/azure/app-service/tutorial-python-postgresql-app).

If you need an Azure account, you can [create on for free](https://azure.microsoft.com/free/).

A Flask sample application is also available for the article at https://github.com/Azure-Samples/msdocs-flask-postgresql-sample-app.

## Requirements

The [requirements.txt](./requirements.txt) has the following packages:

| Package | Description |
| ------- | ----------- |
| [Django](https://pypi.org/project/Django/) | Web application framework. |
| [pyscopg2-binary](https://pypi.org/project/psycopg-binary/) | PostgreSQL database adapter for Python. |
| [python-dotenv](https://pypi.org/project/python-dotenv/) | Read key-value pairs from .env file and set them as environment variables. In this sample app, those variables describe how to connect to the database locally. <br><br> This package is used in the [manage.py](./manage.py) file to load environment variables. |
| [whitenoise](https://pypi.org/project/whitenoise/) | Static file serving for WSGI applications, used in the deployed app. <br><br> This package is used in the [azureproject/production.py](./azureproject/production.py) file, which configures production settings. |

## Using this project with the Azure Developer CLI (azd)

This project is designed to work well with the [Azure Developer CLI](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/overview?WT.mc_id=python-79650-pamelafox),
which makes it easier to develop apps locally, deploy them to Azure, and monitor them.

### Local development

This project has devcontainer support, so you can open it in Github Codespaces or local VS Code with the Dev Containers extension. If you're unable to open the devcontainer,
then it's best to first [create a Python virtual environment](https://docs.python.org/3/tutorial/venv.html#creating-virtual-environments) and activate that.

Install the requirements:

```shell
pip install -r requirements.txt
```

Create an `.env` file using `.env.sample` as a guide. Set the value of `DBNAME` to the name of an existing database in your local PostgreSQL instance. Set the values of `DBHOST`, `DBUSER`, and `DBPASS` as appropriate for your local PostgreSQL instance.

Run the migrations: (or use VS Code "Run" button and select "Migrate")

```shell
python manage.py migrate
```

Run the local server: (or use VS Code "Run" button and select "Run server")

```shell
python manage.py runserver
```

### Deployment

This repository is set up for deployment on Azure App Service (w/PostGreSQL flexible server) using the configuration files in the `infra` folder.

1. Sign up for a [free Azure account](https://azure.microsoft.com/free/?WT.mc_id=python-79461-pamelafox)
2. Install the [Azure Dev CLI](https://learn.microsoft.com/azure/developer/azure-developer-cli/install-azd?WT.mc_id=python-79461-pamelafox). (If you opened this repository in a devcontainer, that part will be done for you.)
3. Provision and deploy all the resources:

```shell
azd up
```

It will prompt you to login and to provide a name (like "django-app") and location (like "centralus"). Then it will provision the resources in your account (if they don't yet exist) and deploy the latest code.

4. To be able to access `/admin`, you'll need a Django superuser. Navigate to the Azure Portal for the App Service, select SSH, and run this command:

```shell
python manage.py createsuperuser
```

5. When you've made any changes to the app code, you can just run:

```shell
azd deploy
```

### CI/CD pipeline

This project includes a Github workflow for deploying the resources to Azure
on every push. That workflow requires several Azure-related authentication secrets to be stored as Github action secrets. To set that up, run:

```shell
azd pipeline config
```

### Monitoring

The deployed resources include a Log Analytics workspace with an Application Insights dashboard to measure metrics like server response time.

To open that dashboard, just run:

```shell
azd monitor --overview
```

## Getting help

If you're working with this project and running into issues, please post in [Discussions](/discussions). 