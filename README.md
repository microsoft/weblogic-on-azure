# Weblogic on Azure
This repository shows how to deploy a Java EE application to Azure using WebLogic and Linux virtual machines. The repository hosts the demos for this [talk](abstract.md) or materials for [this](lab-abstract.md) lab. The prerequistes for the lab are [here](prerequisites.md).

The basic Java EE application used throughout is in the [javaee](/javaee) folder. You should set up and explore this application on your local machine.

The scenarios demonstrated include:

* Deploying a Java EE application on Azure using a simple instance of WebLogic using a marketplace solution. The [simple](/simple) folder shows how this is done.
* Deploying a Java EE application on Azure into a WebLogic cluster using a marketplace solution. The [cluster](/cluster) folder shows how this is done.

We recommend doing the samples in the following order, but the text is
written with sufficient redundancy so that you can do them in any
order. Just be advised that you may have to skip over sections that
you already completed if you jump around in this way.

**preview** This workshop requires interacting with the Azure web portal.  While using the Azure web portal, if you see an icon in the top of the page that looks like a bug ![Azure portal dogfood image](images/portal-dogfood.jpg "Azure portal dogfood image"), you are using the so-called "Dogfood portal".  This may have bugs.  For increased reliability while executing the steps in this workshop, exit the dogfood portal by following these steps.

1. **preview** Click on the ![Azure portal dogfood image](images/portal-dogfood.jpg "Azure portal dogfood image")
2. **preview** In the modal dialog that appears, click on the link with text "click here".
3. **preview** Dismiss any email window that may have been popped up.
4. **preview** You will now be in the non-dogfood portal.

**preview** The preview links are in the **Oracle Enterprise Java** Azure subscription.  In the portal, modify the "Directory + Subscription" control to ensure this subsription and only this subscription is selected, as shown here.

![Directory + Subscription image](images/portal-subscription-filter.jpg "Directory + Subscription image")

* [javaee](/javaee)
* [simple](/simple)
* [cluster](/cluster)

The demos use Java EE 7, Maven, Eclipse and PostgreSQL.


# Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.
