# Getting Started with Python on Bluemix

![Bluemix Deployments](https://deployment-tracker.mybluemix.net/stats/4f7252bee5e6aa6f1611b130ee63dd98/badge.svg)

To get started, we'll take you through a sample Python Flask app, help you set up a development environment and deploy to {{site.data.keyword.Bluemix}}.

## Prerequisites
{: #prereqs}

You'll need the following:
* [{{site.data.keyword.Bluemix_notm}} account](https://console.ng.bluemix.net/registration/)
* [Cloud Foundry CLI ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://github.com/cloudfoundry/cli#downloads){: new_window}
* [Git ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://git-scm.com/downloads){: new_window}
* [Python ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://www.python.org/downloads/){: new_window}

## 1. Clone the sample app
{: #clone}

Now you're ready to start working with the app. Clone the repo and change to the directory where the sample app is located.
  ```
git clone https://github.com/IBM-Bluemix/get-started-python
  ```
  {: pre}
  ```
cd get-started-python
  ```
  {: pre}
  
  Peruse the files in the *get-started-python* directory to familiarize yourself with the contents.

## 2. Run the app locally
{: #run_locally}

Install the dependencies listed in the [requirements.txt ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://pip.readthedocs.io/en/stable/user_guide/#requirements-files) file to be able to run the app locally.

You can optionally use a [virtual environment ![External link icon](../../icons/launch-glyph.svg "External link icon")](https://packaging.python.org/installing/#creating-and-using-virtual-environments) to avoid having these dependencies clash with those of other Python projects or your operating system.
{: tip}
  ```
pip install -r requirements.txt
  ```
  {: pre}

Run the app.
  ```
python hello.py
  ```
  {: pre}

 View your app at: http://localhost:8000

## 3. Prepare the app for deployment
{: #prepare}

To deploy to {{site.data.keyword.Bluemix_notm}}, it can be helpful to set up a manifest.yml file. One is provided for you with the sample. Take a moment to look at it.

The manifest.yml includes basic information about your app, such as the name, how much memory to allocate for each instance and the route. In this manifest.yml **random-route: true** generates a random route for your app to prevent your route from colliding with others.  You can replace **random-route: true** with **host: myChosenHostName**, supplying a host name of your choice. [Learn more...](/docs/manageapps/depapps.html#appmanifest)
 ```
 applications:
 - name: GetStartedPython
   random-route: true
   memory: 128M
 ```
 {: codeblock}

## 4. Deploy the app
{: #deploy}

You can use the Cloud Foundry CLI to deploy apps.

Choose your API endpoint
   ```
cf api <API-endpoint>
   ```
   {: pre}

Replace the *API-endpoint* in the command with an API endpoint from the following list.

|URL                             |Region          |
|:-------------------------------|:---------------|
| https://api.ng.bluemix.net     | US South       |
| https://api.eu-gb.bluemix.net  | United Kingdom |
| https://api.au-syd.bluemix.net | Sydney         |

Login to your {{site.data.keyword.Bluemix_notm}} account

  ```
cf login
  ```
  {: pre}

From within the *get-started-python* directory push your app to {{site.data.keyword.Bluemix_notm}}
  ```
cf push
  ```
  {: pre}

This can take a minute. If there is an error in the deployment process you can use the command `cf logs <Your-App-Name> --recent` to troubleshoot.

When deployment completes you should see a message indicating that your app is running.  View your app at the URL listed in the output of the push command.  You can also issue the
  ```
cf apps
  ```
  {: pre}
  command to view your apps status and see the URL.

## 5. Add a database
{: #add_database}

Next, we'll add a NoSQL database to this application and set up the application so that it can run locally and on {{site.data.keyword.Bluemix_notm}}.

1. Log in to {{site.data.keyword.Bluemix_notm}} in your Browser. Browse to the `Dashboard`. Select your application by clicking on its name in the `Name` column.
2. Click on `Connections` then `Connect new`.
2. In the `Data & Analytics` section, select `Cloudant NoSQL DB` and `Create` the service.
3. Select `Restage` when prompted. {{site.data.keyword.Bluemix_notm}} will restart your application and provide the database credentials to your application using the `VCAP_SERVICES` environment variable. This environment variable is only available to the application when it is running on {{site.data.keyword.Bluemix_notm}}.

Environment variables enable you to separate deployment settings from your source code. For example, instead of hardcoding a database password, you can store this in an environment variable which you reference in your source code. [Learn more...](/docs/manageapps/depapps.html#app_env)
{: tip}

## 6. Use the database
{: #use_database}
We're now going to update your local code to point to this database. We'll create a json file that will store the credentials for the services the application will use. This file will get used ONLY when the application is running locally. When running in {{site.data.keyword.Bluemix_notm}}, the credentials will be read from the VCAP_SERVICES environment variable.

1. Create a file called `vcap-local.json` in the `get-started-python` directory with the following content:
  ```
  {
    "services": {
      "cloudantNoSQLDB": [
        {
          "credentials": {
            "username":"CLOUDANT_DATABASE_USERNAME",
            "password":"CLOUDANT_DATABASE_PASSWORD",
            "host":"CLOUDANT_DATABASE_HOST"
          },
          "label": "cloudantNoSQLDB"
        }
      ]
    }
  }
  ```
  {: pre}

2. Back in the {{site.data.keyword.Bluemix_notm}} UI, select your App -> Connections -> Cloudant -> View Credentials

3. Copy and paste the `username`, `password`, and `host` from the credentials to the same fields of the `vcap-local.json` file replacing **CLOUDANT_DATABASE_USERNAME**, **CLOUDANT_DATABASE_PASSWORD**, and **CLOUDANT_DATABASE_URL**.

4. Run your application locally.
  ```
python hello.py
  ```
  {: pre}

  View your app at: http://localhost:8000. Any names you enter into the app will now get added to the database.

5. Make any changes you want and re-deploy to {{site.data.keyword.Bluemix_notm}}!
  ```
cf push
  ```
  {: pre}

  View your app at the URL listed in the output of the push command, for example, *myUrl.mybluemix.net*.
  
Remember if you don't need your app live, stop it so you don't incur any unexpected charges.
{: tip}

## Privacy Notice

The get-started-python sample web application includes code to track deployments to Bluemix and other Cloud Foundry platforms. The following information is sent to a [Deployment Tracker](https://github.com/IBM-Bluemix/cf-deployment-tracker-service) service on each deployment:

* Python package version
* Python repository URL
* Application Name (`application_name`)
* Space ID (`space_id`)
* Application Version (`application_version`)
* Application URIs (`application_uris`)
* Labels of bound services
* Number of instances for each bound service and associated plan information

This data is collected from the `setup.py` file in the sample application and the `VCAP_APPLICATION` and `VCAP_SERVICES` environment variables in IBM Bluemix and other Cloud Foundry platforms. This data is used by IBM to track metrics around deployments of sample applications to IBM Bluemix to measure the usefulness of our examples, so that we can continuously improve the content we offer to you. Only deployments of sample applications that include code to ping the Deployment Tracker service will be tracked.

### Disabling Deployment Tracking

Deployment tracking can be disabled by removing `cf_deployment_tracker.track()` from the `hello.py` file.

