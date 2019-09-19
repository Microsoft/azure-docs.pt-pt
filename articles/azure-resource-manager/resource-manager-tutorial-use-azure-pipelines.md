---
title: Integração contínua com o Azure Pipelines | Microsoft Docs
description: Saiba como criar, testar e implantar continuamente modelos de Azure Resource Manager.
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
ms.openlocfilehash: 462d9cd6d2a911e660221621ebde5829e928cf00
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122229"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Integração contínua de modelos de Azure Resource Manager com Azure Pipelines

Saiba como usar Azure Pipelines para criar e implantar continuamente projetos de modelo de Azure Resource Manager.

O Azure DevOps fornece serviços de desenvolvedor para dar suporte às equipes para planejar o trabalho, colaborar no desenvolvimento de código e criar e implantar aplicativos. Os desenvolvedores podem trabalhar na nuvem usando Azure DevOps Services. O Azure DevOps fornece um conjunto integrado de recursos que você pode acessar por meio de seu navegador da Web ou cliente IDE. O pipeline do Azure é um desses recursos. Azure Pipelines é um serviço de CI (integração contínua) e distribuição contínua (CD) completo. Ele funciona com seu provedor de git preferido e pode ser implantado na maioria dos principais serviços de nuvem. Em seguida, você pode automatizar a compilação, os testes e a implantação de seu código para Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

Este tutorial foi projetado para desenvolvedores de modelos Azure Resource Manager que são novos Azure DevOps Services e Azure Pipelines. Se você já estiver familiarizado com o GitHub e o DevOps, poderá pular para [criar um pipeline](#create-a-pipeline).

> [!NOTE]
> Escolha um nome de projeto. Quando você passar pelo tutorial, substitua qualquer um dos **AzureRmPipeline** com o nome do projeto.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório do GitHub
> * Criar um projeto de DevOps do Azure
> * Criar um pipeline do Azure
> * Verificar a implantação do pipeline
> * Atualizar o modelo e reimplantar
> * Limpar recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* **Uma conta do GitHub**, onde você a usa para criar um repositório para seus modelos. Se você não tiver uma, poderá [criar uma gratuitamente](https://github.com). Para obter mais informações sobre como usar repositórios do GitHub, consulte [criar repositórios do GitHub](/azure/devops/pipelines/repos/github).
* **Instalar o Git**. Esta instrução de tutorial usa *git bash* ou *git Shell*. Para obter instruções, consulte [instalar o Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Uma organização DevOps do Azure**. Se você não tiver uma, poderá criar uma gratuitamente. Consulte [criar uma organização ou coleção de projetos]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) com a extensão de ferramentas do Resource Manager**. Veja [Instalar a extensão](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Preparar um repositório do GitHub

O GitHub é usado para armazenar o código-fonte do projeto, incluindo modelos do Resource Manager. Para outros repositórios com suporte, consulte [repositórios com suporte do Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se você não tiver uma conta do GitHub, consulte [pré-requisitos](#prerequisites).

1. Entre no [GitHub](https://github.com).
2. Selecione a imagem da conta no canto superior direito e, em seguida, selecione **seus repositórios**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar repositório GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecione **novo**, um botão verde.
1. Em **nome do repositório**, insira um nome de repositório.  Por exemplo, **AzureRmPipeline-repositório**. Lembre-se de substituir qualquer **AzureRmPipeline** pelo nome do projeto. Você pode selecionar **público** ou **privado** para passar por este tutorial. E, em seguida, selecione **criar repositório**.
1. Anote a URL. A URL do repositório é o seguinte formato:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Esse repositório é conhecido como *repositório remoto*. Cada um dos desenvolvedores do mesmo projeto pode clonar seu próprio *repositório local*e mesclar as alterações para o repositório remoto.

### <a name="clone-the-remote-repository"></a>Clonar o repositório remoto

1. Abra git Shell ou git bash.  Veja [Pré-requisitos](#prerequisites).
1. Verifique se sua pasta atual é **GitHub**.
1. Execute o seguinte comando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Substitua **[YourAccountName]** pelo nome da sua conta do GitHub e substitua **[YourGitHubRepositoryName]** pelo nome do repositório que você criou no procedimento anterior.

    As capturas de tela a seguir mostram um exemplo.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar bash do GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

A pasta **CreateAzureStorage** é a pasta em que o modelo está armazenado. O comando **pwd** mostra o caminho da pasta. O caminho é onde você salva o modelo no procedimento a seguir.

### <a name="download-a-quickstart-template"></a>Baixar um modelo de início rápido

Em vez de criar um modelo, você pode baixar um [modelo de início rápido]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Este modelo cria uma conta de armazenamento do Azure.

1. Abra o Visual Studio Code. Veja [Pré-requisitos](#prerequisites).
2. Abra o modelo com a seguinte URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Salve o arquivo como **azuredeploy. JSON** na pasta **CreateAzureStorage** . O nome da pasta e o nome do arquivo são usados como estão no pipeline.  Se você alterar esses nomes, deverá atualizar os nomes usados no pipeline.

### <a name="push-the-template-to-the-remote-repository"></a>Enviar por push o modelo para o repositório remoto

O azuredeploy. JSON foi adicionado ao repositório local. Em seguida, carregue o modelo no repositório remoto.

1. Abra *git Shell* ou *git bash*, se não estiver aberto.
1. Altere o diretório para a pasta CreateAzureStorage em seu repositório local.
1. Verifique se o arquivo **azuredeploy. JSON** está na pasta.
1. Execute o seguinte comando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Você pode receber um aviso sobre LF. Você pode ignorar o aviso. **mestre** é a ramificação mestre.  Normalmente, você cria uma ramificação para cada atualização. Para simplificar o tutorial, você usa a ramificação mestre diretamente.
1. Navegue até o repositório GitHub em um navegador.  A URL é  **https://github.com/ [YourAccountName]/[YourGitHubRepository]** . Você deverá ver a pasta **CreateAzureStorage** e **Azuredeploy. JSON** dentro da pasta.

Até agora, você criou um repositório GitHub e carregou um modelo para o repositório.

## <a name="create-a-devops-project"></a>Criar um projeto DevOps

Uma organização DevOps é necessária para que você possa prosseguir para o próximo procedimento.  Se você não tiver um, consulte [pré-requisitos](#prerequisites).

1. Entre no [Azure DevOps](https://dev.azure.com).
1. Selecione uma organização DevOps à esquerda.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criar projeto DevOps do Azure](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Create project** (Criar projeto). Se você não tiver nenhum projeto, a página Criar projeto será aberta automaticamente.
1. Introduza os seguintes valores:

    * **Nome do projeto**: Insira um nome de projeto. Você pode usar o nome do projeto que você escolheu no início do tutorial.
    * **Controle de versão**: Selecione **git**. Talvez seja necessário expandir **avançado** para ver o **controle de versão**.

    Use o valor padrão para as outras propriedades.
1. Selecione **Create project** (Criar projeto).

Crie uma conexão de serviço que é usada para implantar projetos no Azure.

1. Selecione **configurações do projeto** na parte inferior do menu à esquerda.
1. Selecione **conexões de serviço** em **pipelines**.
1. Selecione **nova conexão de serviço**e, em seguida, selecione **AzureResourceManager**.
1. Introduza os seguintes valores:

    * **Nome da conexão**: Insira um nome de conexão. Por exemplo, **AzureRmPipeline-Conn**. Anote esse nome, você precisará do nome ao criar seu pipeline.
    * **Nível de escopo**: selecione **assinatura**.
    * **Assinatura**: selecione sua assinatura.
    * **Grupo de recursos**: Deixe em branco.
    * **Permitir que todos os pipelines usem esta conexão**. Selecione
1. Selecione **OK**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Até agora, você concluiu as tarefas a seguir.  Se você ignorar as seções anteriores porque está familiarizado com o GitHub e o DevOps, você deve concluir as tarefas antes de continuar.

- Crie um repositório GitHub e salve [esse modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) na pasta **CreateAzureStorage** no repositório.
- Crie um projeto DevOps e crie uma conexão de serviço Azure Resource Manager.

Para criar um pipeline com uma etapa para implantar um modelo:

1. Selecione **pipelines** no menu à esquerda.
1. Selecione **novo pipeline**.
1. Na guia **conectar** , selecione **GitHub**. Se solicitado, insira suas credenciais do GitHub e siga as instruções. Se você vir a tela a seguir, selecione **somente selecionar repositórios**e verifique se o repositório está na lista antes de selecionar **aprovar & instalar**.

    ![Azure Resource Manager DevOps do Azure Azure Pipelines selecionar somente repositórios](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. Na guia **selecionar** , selecione seu repositório.  O nome padrão é **[YourAccountName]/[YourGitHubRepositoryName]** .
1. Na guia **Configurar** , selecione **pipeline de início**. Ele mostra o arquivo de pipeline **Azure-pipelines. yml** com duas etapas de script.
1. Substitua a seção **etapas** pelo seguinte YAML:

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

    Deve ser semelhante a:

    ![Azure Resource Manager DevOps do Azure Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Efetue as seguintes alterações:

    * **azureSubscription**: Atualize o valor com a conexão de serviço criada no procedimento anterior.
    * **ação**: a ação **criar ou atualizar grupo de recursos** executa duas ações-1. criar um grupo de recursos se um novo nome de grupo de recursos for fornecido; 2. implante o modelo especificado.
    * **resourceGroupName**: especifique um novo nome de grupo de recursos. Por exemplo, **AzureRmPipeline-RG**.
    * **local**: especifique o local para o grupo de recursos.
    * **templateLocation**: quando o **artefato vinculado** é especificado, a tarefa procura o arquivo de modelo diretamente do repositório conectado.
    * **csmFile** é o caminho para o arquivo de modelo. Você não precisa especificar um arquivo de parâmetros de modelo porque todos os parâmetros definidos no modelo têm valores padrão.

    Para obter mais informações sobre a tarefa, consulte [tarefa de implantação do grupo de recursos do Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Selecione **Guardar e executar**.
1. Selecione **salvar e executar** novamente. Uma cópia do arquivo YAML é salva no repositório conectado. Você pode ver o arquivo YAML navegando até o repositório.
1. Verifique se o pipeline foi executado com êxito.

    ![Azure Resource Manager DevOps do Azure Azure Pipelines YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. O nome é o que você especificou no arquivo YAML do pipeline.  Você deverá ver uma conta de armazenamento criada.  O nome da conta de armazenamento começa com **Store**.
1. Selecione o nome da conta de armazenamento para abri-lo.
1. Selecione **propriedades**. Observe que o **SKU** é **Standard_LRS**.

    ![Azure Resource Manager a verificação do portal de Azure Pipelines do Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Atualizar e reimplantar

Quando você atualiza o modelo e envia por push as alterações para o repositório remoto, o pipeline atualiza automaticamente os recursos, a conta de armazenamento nesse caso.

1. Abra o **azuredeploy. JSON** em seu repositório local em Visual Studio Code.
1. Atualize o **DefaultValue** de **storageAccountType** para **Standard_GRS**. Veja a captura de ecrã abaixo:

    ![Azure Resource Manager Azure DevOps Azure Pipelines atualizar YAML](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Guarde as alterações.
1. Envie por push as alterações para o repositório remoto executando os seguintes comandos de git bash/shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    O primeiro comando sincroniza o repositório local com o repositório remoto. Lembre-se de que o arquivo YAML do pipeline foi adicionado ao repositório remoto.

    Com a ramificação mestre do repositório remoto atualizada, o pipeline é acionado novamente.

Para verificar as alterações, você pode verificar a SKU da conta de armazenamento.  Consulte [verificar a implantação](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.

Você também pode querer excluir o repositório GitHub e o projeto DevOps do Azure.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você cria um pipeline de DevOps do Azure para implantar um modelo de Azure Resource Manager. Para saber como implementar recursos do Azure em várias regiões e como utilizar práticas de implementação seguras, veja

> [!div class="nextstepaction"]
> [Utilize as práticas recomendadas de implementação segura](./deployment-manager-tutorial.md)
