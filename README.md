# Weblogic on Azure
This repository shows how to deploy a Java application to Azure using WebLogic and virtual machines. The repository hosts the demos for this [talk](abstract.md) or materials for [this](lab-abstract.md) lab. The prerequistes for the lab are [here](prerequisites.md).

For greater details on the solutions to run WebLogic on Azure virtual machines, please look [here](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/oracle/oracle-weblogic).

The basic Java EE application used throughout is in the [javaee](/javaee) folder. You should set up and explore this application on your local machine.

The scenarios demonstrated include:

* Deploying a Java application on Azure using a simple instance of WebLogic. The [simple](/simple) folder shows how this is done.
* Deploying a Java application on Azure into a load-balanced WebLogic cluster. The [cluster](/cluster) folder shows how this is done.

We recommend exploring the scenarios in the following order, but the text is written with sufficient redundancy so that you can do them in any order.

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
