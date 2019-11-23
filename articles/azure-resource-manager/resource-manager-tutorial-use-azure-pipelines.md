---
title: Integração contínua com os Pipelines do Azure
description: Learn how to continuously build, test, and deploy Azure Resource Manager templates.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 457d2a8868283eed2e211ff26ce77156eee68e01
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326416"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Continuous integration of Azure Resource Manager templates with Azure Pipelines

Learn how to use Azure Pipelines to continuously build and deploy Azure Resource Manager template projects.

Azure DevOps provides developer services to support teams to plan work, collaborate on code development, and build and deploy applications. Developers can work in the cloud using Azure DevOps Services. Azure DevOps provides an integrated set of features that you can access through your web browser or IDE client. Azure Pipeline is one of these features. Azure Pipelines is a fully featured continuous integration (CI) and continuous delivery (CD) service. It works with your preferred Git provider and can deploy to most major cloud services. Then you can automate the build, testing, and deployment of your code to Microsoft Azure, Google Cloud Platform, or Amazon Web Services.

This tutorial is designed for Azure Resource Manager template developers who are new Azure DevOps Services and Azure Pipelines. If you are already familiar with GitHub and DevOps, you can skip to [Create a pipeline](#create-a-pipeline).

> [!NOTE]
> Pick a project name. When you go through the tutorial, replace any of the **AzureRmPipeline** with your project name.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório do GitHub
> * Criar um projeto de DevOps do Azure
> * Create an Azure pipeline
> * Verify the pipeline deployment
> * Update the template and redeploy
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* **A GitHub account**, where you use it to create a repository for your templates. If you don’t have one, you can [create one for free](https://github.com). For more information about using GitHub repositories, see [Build GitHub repositories](/azure/devops/pipelines/repos/github).
* **Instalar o Git**. This tutorial instruction uses *Git Bash* or *Git Shell*. For instructions, see [Install Git]( https://www.atlassian.com/git/tutorials/install-git).
* **An Azure DevOps organization**. If you don't have one, you can create one for free. See [Create an organization or project collection]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).

## <a name="prepare-a-github-repository"></a>Preparar um repositório do GitHub

GitHub is used to store your project source code including Resource Manager templates. For other supported repositories, see [repositories supported by Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Create a GitHub repository

If you don’t have a GitHub account, see [Prerequisites](#prerequisites).

1. Sign in to [GitHub](https://github.com).
2. Select your account image on the upper right corner, and then select **Your repositories**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines create GitHub repository](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Select **New**, a green button.
1. In **Repository name**, enter a repository name.  For example, **AzureRmPipeline-repo**. Remember to replace any of **AzureRmPipeline** with your project name. You can select either **Public** or **private** for going through this tutorial. And then select **Create repository**.
1. Write down the URL. The repository URL is the following format:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

This repository is referred to as a *remote repository*. Each of the developers of the same project can clone his/her own *local repository*, and merge the changes to the remote repository.

### <a name="clone-the-remote-repository"></a>Clone the remote repository

1. Open Git Shell or Git Bash.  Veja [Pré-requisitos](#prerequisites).
1. Verify your current folder is **github**.
1. Execute o seguinte comando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Replace **[YourAccountName]** with your GitHub account name, and replace **[YourGitHubRepositoryName]** with your repository name you created in the previous procedure.

    The following screenshot shows an example.

    ![Azure Resource Manager Azure DevOps Azure Pipelines create GitHub bash](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

The **CreateAzureStorage** folder is the folder where the template is stored. The **pwd** command shows the folder path. The path is where you save the template to in the following procedure.

### <a name="download-a-quickstart-template"></a>Download a Quickstart template

Instead of creating a template, you can download a [Quickstart template]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). This template creates an Azure Storage account.

1. Open Visual Studio code. Veja [Pré-requisitos](#prerequisites).
2. Open the template with the following URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Save the file as **azuredeploy.json** to the **CreateAzureStorage** folder. Both the folder name and the file name are used as they are in the pipeline.  If you change these names, you must update the names used in the pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Push the template to the remote repository

The azuredeploy.json has been added to the local repository. Next, you upload the template to the remote repository.

1. Open *Git Shell* or *Git Bash*, if it is not opened.
1. Change directory to the CreateAzureStorage folder in your local repository.
1. Verify the **azuredeploy.json** file is in the folder.
1. Execute o seguinte comando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    You might get a warning about LF. You can ignore the warning. **master** is the master branch.  You typically create a branch for each update. To simplify the tutorial, you use the master branch directly.
1. Browse to your GitHub repository from a browser.  The URL is **https://github.com/ [YourAccountName]/[YourGitHubRepository]** . You shall see the **CreateAzureStorage** folder and **Azuredeploy.json** inside the folder.

So far, you have created a GitHub repository, and uploaded a template to the repository.

## <a name="create-a-devops-project"></a>Create a DevOps project

A DevOps organization is needed before you can proceed to the next procedure.  If you don’t have one, see [Prerequisites](#prerequisites).

1. Sign in to [Azure DevOps](https://dev.azure.com).
1. Select a DevOps organization from the left.

    ![Azure Resource Manager Azure DevOps Azure Pipelines create Azure DevOps project](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Create project** (Criar projeto). If you don't have any projects, the create project page is opened automatically.
1. Introduza os seguintes valores:

    * **Project name**: enter a project name. You can use the project name you picked at the very beginning of the tutorial.
    * **Version control**: Select **Git**. You might need to expand **Advanced** to see **Version control**.

    Use the default value for the other properties.
1. Selecione **Create project** (Criar projeto).

Create a service connection that is used to deploy projects to Azure.

1. Select **Project settings** from the bottom of the left menu.
1. Select **Service connections** under **Pipelines**.
1. Select **New Service connection**, and then select **AzureResourceManager**.
1. Introduza os seguintes valores:

    * **Connection name**: enter a connection name. For example, **AzureRmPipeline-conn**. Write down this name, you need the name when you create your pipeline.
    * **Scope level**: select **Subscription**.
    * **Subscription**: select your subscription.
    * **Resource Group**: Leave it blank.
    * **Allow all pipelines to use this connection**. (selected)
1. Selecione **OK**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Until now, you have completed the following tasks.  If you skip the previous sections because you are familiar with GitHub and DevOps, you must complete the tasks before you continue.

- Create a GitHub repository, and save [this template](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) to the **CreateAzureStorage** folder in the repository.
- Create a DevOps project, and create an Azure Resource Manager service connection.

To create a pipeline with a step to deploy a template:

1. Select **Pipelines** from the left menu.
1. Select **New pipeline**.
1. From the **Connect** tab, select **GitHub**. If asked, enter your GitHub credentials, and then follow the instructions. If you see the following screen, select **Only select repositories**, and verify your repository is in the list before you select **Approve & Install**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines only select repositories](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. From the **Select** tab, select your repository.  The default name is **[YourAccountName]/[YourGitHubRepositoryName]** .
1. From the **Configure** tab, select **Starter pipeline**. It shows the **azure-pipelines.yml** pipeline file with two script steps.
1. Replace the **steps** section with the following YAML:

    ```yaml
    steps:
    - task: AzureResourceManagerTemplateDeployment@3
      inputs:
        deploymentScope: 'Resource Group'
        ConnectedServiceName: '[EnterYourServiceConnectionName]'
        subscriptionName: '[EnterTheTargetSubscriptionID]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    It shall look like:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Efetue as seguintes alterações:

    * **deloymentScope**: Select the scope of deployment from the options: `Management Group`, `Subscription` and `Resource Group`. Use **Resource Group** in this tutorial. To learn more about the scopes, see [Deployment scopes](./resource-group-template-deploy-rest.md#deployment-scope).
    * **ConnectedServiceName**: Specify the service connection name that you created earlier.
    * **SubscriptionName**:  Specify the target subscription ID.
    * **action**: the **Create Or Update Resource Group** action does 2 actions - 1. create a resource group if a new resource group name is provided; 2. deploy the template specified.
    * **resourceGroupName**: specify a new resource group name. For example, **AzureRmPipeline-rg**.
    * **location**: specify the location for the resource group.
    * **templateLocation**: when **Linked artifact** is specified, the task looks for the template file directly from the connected repository.
    * **csmFile** is the path to the template file. You don't need to specify a template parameters file because all of the parameters defined in the template have default values.

    For more information about the task, see [Azure Resource Group Deployment task](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment), and [Azure Resource Manager template deployment task](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Selecione **Guardar e executar**.
1. Select **Save and run** again. A copy of the YAML file is saved into the connected repository. You can see the YAML file by browse to your repository.
1. Verify that the pipeline is executed successfully.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Open the resource group. The name is what you specified in the pipeline YAML file.  You shall see one storage account created.  The storage account name starts with **store**.
1. Select the storage account name to open it.
1. Selecione **Propriedades**. Notice the **Replication** is **Locally-redundant storage (LRS)** .

    ![Azure Resource Manager Azure DevOps Azure Pipelines portal verification](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Update and redeploy

When you update the template and push the changes to the remote repository, the pipeline automatically updates the resources, the storage account in this case.

1. Open **azuredeploy.json** from your local repository in Visual Studio Code.
1. Update the **defaultValue** of **storageAccountType** to **Standard_GRS**. Veja a captura de ecrã abaixo:

    ![Azure Resource Manager Azure DevOps Azure Pipelines update yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Guarde as alterações.
1. Push the changes to the remote repository by running the following commands from Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    The first command syncs the local repository with the remote repository. Remember the pipeline YAML file was added to the remote repository.

    With the master branch of the remote repository updated, the pipeline is fired again.

To verify the changes, you can check the Replication property of the storage account.  See [Verify the deployment](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

You might also want to delete the GitHub repository and the Azure DevOps project.

## <a name="next-steps"></a>Passos seguintes

In this tutorial, you create an Azure DevOps pipeline to deploy an Azure Resource Manager template. Para saber como implementar recursos do Azure em várias regiões e como utilizar práticas de implementação seguras, veja

> [!div class="nextstepaction"]
> [Utilize as práticas recomendadas de implementação segura](./deployment-manager-tutorial.md)
