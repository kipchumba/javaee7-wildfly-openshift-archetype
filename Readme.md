javaee7-wildfly-openshift-archetype
====================

Introduction
---------------------

This projects provides a Maven archetype for JavaEE7. This archetype allows to quickly bootstrap a Maven project to develop a JavaEE7 applications on Wildfly Application Server. The archetype provides several profiles to easily deploy and test your application on [Openshift PaaS](https://www.openshift.com/)

These artifacts are build with [Maven 3](http://maven.apache.org/docs/3.0.5/release-notes.html). For archetype packaging the [maven-archetype-plugin](http://maven.apache.org/archetype/maven-archetype-plugin/) was used.

Be sure to read this entire document before you attempt to work with the archetype. It contains the following information:


* [Overview](#overview): A list of all the features you get out of the box when using this archetype

* [System Requirements](#system): List of software required to run the quickstarts.

* [Build & Install](#build): How to build and install the Archetype into your local Maven repository.

* [Run](#run): How to create a Maven Project with the new Archetype

* [Deploy on Openshift](#deploy): How to deploy your Application to Openshift PaaS

* [Technical Notes](#notes): Technical background, used plugins, etc.



<a id="overview"></a>

Overview
---------------------

This archetype provides a deployable Maven 3 project to help you getting started developing with Java EE 7 on Wildfly Application Server. This project is setup to allow you to create a compliant Java EE 7 application using JSF 2.2, CDI 1.1, EJB 3.2, JPA 2.1, JMS 2.0 and Bean Validation 1.1. It includes certain profiles to build, test and deploy your application on an local or cloud hosted Wildfly Application Server. The goal was to create a minimalistic Maven project without any artifacts that bloat up the complexity of the szenario.

<a id="system"></a>

System Requirements
---------------------

The application this project produce is designed to be run on Wildfly Application Server 8 or later. All you need to build this project is Java 7 (Java JDK 1.7) or newer, Maven 3.0 or newer. If you have not yet installed Maven, see the [Maven Getting Started Guide](http://maven.apache.org/guides/getting-started/index.html) for details. 

Later on for deployment you need an Wildfly 8 or newer Installation on your System a Openshift Account and a Wildfly Cardridge on Opneshift Plattform. If you have not yet installed Wildfly, see the [Wildfly Getting Started Guilde](https://docs.jboss.org/author/display/WFLY8/Getting+Started+Guide) for details. The installation & configuration of Wildfly in the Openshift PaaS will be described in the chapter [Deploy on Openshift](#deploy).

With the prerequisites out of the way, you're ready to build and deploy.


<a id="build"></a>

Build & Install
---------------------
- Clone the javaee7-wildfly-openshift-archetype project into a local folder of your filesystem.
- Open a command line and navigate to the root directory of the archetype project
- Use this command to build the archetype:

        mvn clean install

- Check if the archetype is availible in your local maven repository with following command:
        mvn archetype:generate -DarchetypeCatalog=local
  Maven will print a list of all installed archetypes in the local Maven repository. After a successful build you find a  *local -> de.mszturc:javaee7-wildfly-openshift-archetype* entry in this list.

The archetype is now installed and can be used to create a minimalistic Java EE 7 Maven project.

<a id="run"></a>

Run
---------------------

The simplest way to create a Maven project from the installed archetype ist by command line. Many IDE's provide you a Wizard for this Step but we will keep it basic, and ide independent by opening a command line and navigating to the root directory where we want to start our new project. We use following command to create the project:

        mvn archetype:generate \
          -DarchetypeArtifactId=javaee7-wildfly-openshift-archetype \
          -DarchetypeGroupId=de.mszturc \
          -DarchetypeVersion=1.0 \
          -DgroupId=your.company \
          -DartifactId=your-application-name

Where *your.company* and *your-application-name* should be changed by the actual values you want to use in your application.

Maven will create and configure a minimalistic Java EE 7 Project containing the following project structure:

        root
         ├───pom.xml
         └───src
              └───main
                   ├───java
                   ├───resources
                   └───webapp
                          └───index.html

The Pom contains 2 Profiles to deploy the Application on a local and an Openshift hosted Wildfly Server. The local Wildfly server is used as a development system. Therefore the Profile uses the conventional parameters for this system. This means that the Wildfly AS is hosted on localhost, hearing on the default 9990 port and the credential service is deactivated ( by default ).

The conventinal paramerers can be found on the [Cargo Container: Wildfly 8x](http://cargo.codehaus.org/WildFly+8.x) Documentation

Building & deploying the application to you local development system can be done by following command:

        mvn -P deploy-wildfly-8.x(localhost) clean install

After maven is done you can reach the deployed application by the following url:

        http://localhost:8080/<yout-application-name>/


<a id="deploy"></a>

Deploy on Openshift
---------------------

The Pom contains a preconfigured profile for deployments on the Openshift PaaS. Therefore you have to install a Wildfly AS8 on a Openshift DIY Cardridge. The preparation of the cardridge is descriped in this Article : [Wildfly on Openshift](https://www.openshift.com/blogs/wildfly-on-openshift-interview-with-jboss-developer-stian-thorgersen). In the future i will provide an already prepared DIY Cardridge with preinstalled Wildfly AS.

After the Wildfly Server is setup properly you have to create a [SSH Pord Forward Binding](https://www.openshift.com/blogs/getting-started-with-port-forwarding-on-openshift) to access the Wildfly Administration Console and the deployment API on your local machine. The Openshift PaaS is very restricted here. They provide only on open Port ( 80 ) that is mapped by 8080.

The last step before deploying your application to the Openshift PaaS is the configuration of the project itself. In the pom.xml you find the following block:

        <!-- openshift -->
        <openshift.hostname>localhost</openshift.hostname>
        <openshift.port>9990</openshift.port>
        <openshift.username>user</openshift.username>
        <openshift.password>password</openshift.password>
        

These properties contain all the information that Maven needs to deploy your application to the Openshift PaaS. The properties *openshift.username* and *openshift.password* should be empty if the credential service on the Openshift Wildfly Server is disabled, but this is unusual. I would recommend to activate this in a production environment. RedHat provide an Utility for this out of the box. A guide for this util can be found [here](https://docs.jboss.org/author/display/WFLY8/add-user+utility)

To deploy your application to the Openshift PaaS use the following command line command:

        mvn -P deploy-wildfly-8.x(openshift) clean install
        

After maven is done you can reach the deployed application by the following url:

        http://<cardrige-name>-<openshift-name>.rhcloud.com/<application-name>

A demo of a Maven project generated by this archetype can be found [here](http://wildfly-mszturc.rhcloud.com/basicJEE7)

<a id="notes"></a>

Technical Notes
---------------------

For the creation of the archetype project I used the following tools & plugins:

- Java JDK 1.7.0u25 x64
- Maven 3.0.5
- maven-archetype-plugin 2.2

The by the archetype created project uses the following tools & plugins:

- Java JDK 1.7.0 or newer
- Maven 3.0.0 or newer
- cargo-maven2-plugin 1.4.5
- jboss-javaee-7.0 1.0.0.Beta2

The created project was test in the following environment:

- Windows 7 x64
- Java JDK 1.7.0u25 x64
- Maven 3.0.5
- Wildfly AS 8.0.0.Beta1 ( local & openshift )

