# Overview
Mulesoft (and maybe other Web API development ecosystems) application development requires meta-data to teach the implementation what its input and output data should look like for each endpoint in the web API. RAML projects allow API designers to create similar meta-data files in the form of RAML NAMEDEXAMPLE fragment files.  Historically, developers have had to create samples for both the API definition and the application project.   This is tedious redundant work.

This Mule Application (packaged as a deployable jar file), **build-samples-daemon** creates JSON and XML sample files from NAMEDEXAMPLE RAML files in a RAML project. It is meant to be run in a development environment, i.e. a stand-alone runtime on a development system. It is NOT meant to be run in organization's a test or production environments.


# How it works
The app is a _daemon_, running continuously. It monitors the _/tmp/apis_to_be_processed_ folder for files that end in _.zip_, which would presumably RAML projects (typically downloaded from **Mulesoft Anypoint Design Center**, but perhaps created with other tools). Upon finding such a file, it:
1. Creates a folder in _/tmp/apis_being_processed_ by the name of the zip file, without the .zip extension.
2. For each RAML fragments of type NAMEDEXAMPLE in the ZIP, it creates two "sample" files using the "value" item in the fragment.
3. Moves the resulting folder of sample files to _/tmp/apis_processed_.
4. Moves the zip file from _/tmp/apis_to_process_ to _/tmp/apis_processed_.

## Changing the root folder
There is a global emement that sets the root folder of the to_be_processed / processing / processed folders to _/tmp_.  IF you want to use a different folder, change the global element (there is only one).

## Tags in the XML examples
When creating the XML sample files, the app uses the name of the fragment as a basis for the tag(s) in said sample, as follows:
- **NAMEDEXAMPLE LISTS / repeating series**: If the fragment contains a list of values, then the application uses a pluralized version of the fragment filename (with "Example" or "Examples") as the root tag, and a singularized version of the fragment filename for the repeating-series tag.
- **Flat Objects**: If the fragment does not contain a list, the app creates a single root object whose root tag is a singularized version of the fragment filename.

# Deployment
To deploy the app on your development system:
1. If you haven't already, download a version of the Mule Standalone Runtime (see https://www.mulesoft.com/lp/dl/mule-esb-enterprise) and then deploy it according to the directions there.
2. Build the Mule Application JAR file. To do build it, you can either:
 - Import the Deployable JAR file associated with this project (_build-samples-daemon.jar_) and then build it from the command line.
 - Download the project source into **Studio** and build it.
3. Copy the resulting Mule Application JAR file to the MULE_HOME folder (set as an evnironment variable in Step 1).

# Usage
Once you have deployed the app you can start using it. To do so:
1. Download a _.zip_ file of your RAML API project, or create said _.zip_ from a RAML projec in your local filesystem (e.g. if you are using ATOM's RAML Workbench plugin).
2. Move the _.zip_ file in Step 1 to /tmp/apis_to_process. (NOTE: if you modified the global element from /tmp, use the folder in that element).
3. Monitor the _/tmp/apis_processed_ folder for a sub-folder with the same name as the _zip_ file, but without the _.zip_ extension.  This sub-folder will contain the two sample files (XML and JSON) for each NAMEDEXAMPLE fragment file. NOTE that the sample files' pathnames mirror their pathname in the API project.
3) Copy the sample files from these folders into your Studio project folder's /src/test/examples folder.
4) Use Studio's Meta-data Editor to teach the project about each sample (or those you need).

# Feedback
Please provide feedback in the github project.
