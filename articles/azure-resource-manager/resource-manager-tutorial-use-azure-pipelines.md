---
title: Integração contínua com Pipelines do Azure | Documentos da Microsoft
description: Saiba como criar, testar e implementar modelos Azure Resource Manager de forma contínua.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 85dc0476da12bea64610b6910b0682fef00f4b5a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057793"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Integração contínua de modelos do Azure Resource Manager com Pipelines do Azure

Saiba como utilizar Pipelines do Azure para criar e implantar projetos de modelo do Azure Resource Manager continuamente.

DevOps do Azure fornece serviços de programação para suportar as equipes de plano de trabalho, colaborar em desenvolvimento de código e criar e implementar aplicações. Os desenvolvedores podem trabalhar na cloud com os serviços de DevOps do Azure. DevOps do Azure fornece um conjunto integrado de recursos que pode aceder através de seu navegador da web ou o cliente do IDE. Pipeline do Azure é uma destas funcionalidades. Pipelines do Azure é uma com todas as funcionalidades de integração contínua (CI) e o serviço de entrega contínua (CD). Ele funciona com o seu fornecedor preferencial do Git e pode implementar para a maioria dos principais serviços cloud. Em seguida, pode automatizar a compilação, teste e implantação do seu código para o Microsoft Azure, o Google Cloud Platform ou o Amazon Web Services.

Este tutorial foi concebido para os desenvolvedores do modelo do Azure Resource Manager que são novos Pipelines do Azure e serviços de DevOps do Azure. Se já estiver familiarizado com o GitHub e DevOps, pode avançar para [criar um pipeline](#create-a-pipeline).

> [!NOTE]
> Escolha um nome de projeto. Quando seguir o tutorial, substitua qualquer um da **AzureRmPipeline** com o nome do projeto.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório do GitHub
> * Criar um projeto de DevOps do Azure
> * Criar um pipeline do Azure
> * Verificar a implementação de pipeline
> * Atualizar o modelo e Reimplementar
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* **Uma conta do GitHub**, em que o utilize para criar um repositório para seus modelos. Se não tiver uma, pode [criar uma gratuitamente](https://github.com). Para obter mais informações sobre a utilização de repositórios do GitHub, veja [repositórios do GitHub criar](/azure/devops/pipelines/repos/github).
* **Instalar o Git**. Esta instrução tutorial utiliza *Git Bash* ou *Git Shell*. Para obter instruções, consulte [instalar o Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Uma organização do Azure DevOps**. Se não tiver uma, pode criar uma gratuitamente. Ver [crie uma coleção de projeto ou organização]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) com a extensão do Resource Manager Tools**. Veja [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Preparar um repositório do GitHub

GitHub é utilizado para armazenar seu código de origem do projeto incluindo modelos do Resource Manager. Para outros repositórios suportados, consulte [repositórios suportados pelo Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Criar um repositório do GitHub

Se não tiver uma conta do GitHub, veja [pré-requisitos](#prerequisites).

1. Inicie sessão no [GitHub](https://github.com).
2. Selecione a sua imagem de conta no canto superior direito e, em seguida, selecione **seus repositórios**.

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines criar repositório do GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecione **New**, um botão a verde.
1. Na **nome do repositório**, introduza um nome do repositório.  Por exemplo, **AzureRmPipeline repositório**. Não se esqueça de substituir alguma parte **AzureRmPipeline** com o nome do projeto. Pode selecionar **pública** ou **privada** para percorrer neste tutorial. E, em seguida, selecione **criar repositório**.
1. Anote o URL. O URL do repositório é o seguinte formato:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Este repositório é referido como um *repositório remoto*. Cada um dos desenvolvedores do mesmo projeto pode clonar seu próprio *repositório local*e intercalar as alterações para o repositório remoto.

### <a name="clone-the-remote-repository"></a>Clonar o repositório remoto

1. Abra a Shell de Git ou o Git Bash.  Veja [Pré-requisitos](#prerequisites).
1. Certifique-se de que a pasta atual é **github**.
1. Execute o seguinte comando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Substitua **[YourAccountName]** nome da conta com o seu GitHub e substitua **[YourGitHubRepositoryName]** com o nome do repositório que criou no procedimento anterior.

    As capturas de ecrã seguinte mostra um exemplo.

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines criar bash do GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

O **CreateAzureStorage** pasta é a pasta onde está armazenado o modelo. O **pwd** comando mostra o caminho da pasta. O caminho é onde guardar o modelo no procedimento seguinte.

### <a name="download-a-quickstart-template"></a>Transferir um modelo de início rápido

Em vez de criar um modelo, pode transferir uma [modelo de início rápido]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Este modelo cria uma conta de armazenamento do Azure.

1. Abra o Visual Studio code. Veja [Pré-requisitos](#prerequisites).
2. Abra o modelo com o seguinte URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Guarde o ficheiro como **azuredeploy. JSON** para o **CreateAzureStorage** pasta. O nome da pasta e o nome de ficheiro são utilizados como estão no pipeline.  Se alterar esses nomes, tem de atualizar os nomes utilizados no pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Emitir o modelo para o repositório remoto

Foi adicionado o azuredeploy. JSON no repositório local. Em seguida, vai carregar o modelo para o repositório remoto.

1. Open *Git Shell* ou *Git Bash*, se não estiver aberto.
1. Altere o diretório para a pasta de CreateAzureStorage no seu repositório local.
1. Verifique se o **azuredeploy. JSON** arquivo está na pasta.
1. Execute o seguinte comando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Poderá receber um aviso sobre LF. Pode ignorar o aviso. **mestre** é o ramo principal.  Normalmente, vai criar um ramo para cada atualização. Para simplificar o tutorial, utilize o ramo principal diretamente.
1. Navegue para o seu repositório do GitHub a partir de um browser.  O URL é  **https://github.com/ [YourAccountName] / [YourGitHubRepository]** . Deverá ver o **CreateAzureStorage** pasta e **azuredeploy. JSON** dentro da pasta.

Até agora, criou um repositório do GitHub e carregar um modelo para o repositório.

## <a name="create-a-devops-project"></a>Criar um projeto de DevOps

Uma organização de DevOps é necessária antes de poder prosseguir para o procedimento seguinte.  Se não tiver uma, veja [pré-requisitos](#prerequisites).

1. Inicie sessão no [do Azure DevOps](https://dev.azure.com).
1. Selecione uma organização de DevOps da esquerda.

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines criar projeto de DevOps do Azure](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Create project** (Criar projeto). Se não tiver todos os projetos, a página de criação de projeto é aberta automaticamente.
1. Introduza os seguintes valores:

    * **Nome do projeto**: introduza um nome de projeto. Pode utilizar o nome de projeto que selecionou logo no início do tutorial.
    * **Controle de versão**: Selecione **Git**. Poderá ter de expandir **avançadas** para ver **controle de versão**.

    Utilize o valor predefinido para as outras propriedades.
1. Selecione **Create project** (Criar projeto).

Crie uma ligação de serviço que é utilizada para implementar projetos no Azure.

1. Selecione **definições do projeto** na parte inferior do menu à esquerda.
1. Selecione **ligações de serviço** sob **Pipelines**.
1. Selecione **ligação de serviço novo**e, em seguida, selecione **AzureResourceManager**.
1. Introduza os seguintes valores:

    * **Nome da ligação**: introduza um nome de ligação. Por exemplo, **AzureRmPipeline conn**. Anote este nome, é necessário o nome quando criar o seu pipeline.
    * **Definir o nível de âmbito**: selecione **subscrição**.
    * **Subscrição**: selecione a sua subscrição.
    * **Grupo de recursos**: Deixe em branco.
    * **Permitir que todos os pipelines utilizar esta ligação**. (selecionado)
1. Selecione **OK**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Até agora, tem de concluir as seguintes tarefas.  Se ignorar as secções anteriores porque está familiarizado com o GitHub e DevOps, tem de concluir as tarefas antes de continuar.

- Criar um repositório do GitHub e salvar [este modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) para o **CreateAzureStorage** pasta no repositório.
- Criar um projeto de DevOps e criar uma ligação de serviço do Azure Resource Manager.

Para criar um pipeline com um passo para implementar um modelo:

1. Selecione **Pipelines** no menu à esquerda.
1. Selecione **novo pipeline**.
1. Partir do **Connect** separador, selecione **GitHub**. Se lhe for pedido, introduza as suas credenciais do GitHub e, em seguida, siga as instruções. Se vir o ecrã seguinte, selecione **apenas selecionar repositórios**e certifique-se de que o repositório está na lista antes de selecionar **aprovar e instalar o**.

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines apenas selecionar repositórios](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Partir do **selecione** separador, selecione o seu repositório.  O nome predefinido é **[YourAccountName] / [YourGitHubRepositoryName]** .
1. Partir do **configurar** separador, selecione **Starter pipeline**. Mostra os **azure-pipelines.yml** ficheiro de pipeline com duas etapas de script.
1. Substitua a **passos** secção com o YAML seguinte:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Ele deve ter o seguinte aspeto:

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Efetue as seguintes alterações:

    * **azureSubscription**: Atualize o valor com a ligação de serviço que criou no procedimento anterior.
    * **ação**: a **criar ou atualizar grupo de recursos** ação faz 2 ações - 1. criar um grupo de recursos se não for fornecido um nome de grupo de recursos novo; 2. Implemente o modelo especificado.
    * **resourceGroupName**: Especifique um nome de grupo de recursos novo. Por exemplo, **AzureRmPipeline-rg**.
    * **localização**: Especifique a localização para o grupo de recursos.
    * **templateLocation**: quando **ligado artefacto** for especificado, a tarefa de procura o ficheiro de modelo diretamente a partir do repositório ligado.
    * **csmFile** é o caminho para o ficheiro de modelo. Não precisa de especificar um ficheiro de parâmetros de modelo, porque todos os parâmetros definidos no modelo têm valores predefinidos.

    Para obter mais informações sobre a tarefa, consulte [tarefas de implementação de grupo de recursos do Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Selecione **Guardar e executar**.
1. Selecione **salve e execute** novamente. É guardada uma cópia do ficheiro YAML para o repositório ligado. Pode ver o ficheiro YAML, navegue para o seu repositório.
1. Certifique-se de que o pipeline é executado com êxito.

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. O nome é especificado no ficheiro YAML pipeline.  Deverá ver uma conta de armazenamento que criou.  O nome de conta de armazenamento começa com **armazenar**.
1. Selecione o nome de conta de armazenamento para abri-lo.
1. Selecione **propriedades**. Observe que o **SKU** é **Standard_LRS**.

    ![Verificação de portal do Azure Resource Manager do Azure DevOps do Azure Pipelines](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Atualização e volte a implementar

Quando atualizar o modelo e envie as alterações para o repositório remoto, o pipeline atualiza automaticamente os recursos, a conta de armazenamento neste caso.

1. Open **azuredeploy. JSON** do seu repositório local no Visual Studio Code.
1. Atualização do **defaultValue** dos **storageAccountType** para **Standard_GRS**. Veja a captura de ecrã abaixo:

    ![O Azure Resource Manager do Azure DevOps do Azure Pipelines atualizar yaml](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Guarde as alterações.
1. Envie as alterações para o repositório remoto, executando os seguintes comandos do Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    O primeiro comando sincroniza o repositório local com o repositório remoto. Lembre-se de que o ficheiro YAML de pipeline foi adicionado para o repositório remoto.

    Com o ramo principal do repositório remoto atualizado, o pipeline é acionado novamente.

Para verificar as alterações, pode verificar o SKU da conta de armazenamento.  Ver [verificar a implementação](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

Também poderá eliminar o repositório do GitHub e o projeto de DevOps do Azure.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, vai criar um pipeline de DevOps do Azure para implementar um modelo Azure Resource Manager. Para saber como implementar recursos do Azure em várias regiões e como utilizar práticas de implementação seguras, veja

> [!div class="nextstepaction"]
> [Utilizar o Azure Deployment Manager](./resource-manager-tutorial-deploy-vm-extensions.md)
