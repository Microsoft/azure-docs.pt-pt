---
title: Integração contínua com os Pipelines do Azure
description: Aprenda a construir, testar e implementar continuamente modelos do Gestor de Recursos Azure.
author: mumian
ms.date: 10/29/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e7d6b23aa3f35c99cf03f855152b2b231a60a965
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75921628"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Integração contínua de modelos de Gestor de Recursos Azure com Pipelines Azure

Aprenda a usar os Pipelines Azure para construir e implementar continuamente projetos de modelo seleto de recursos Azure.

A Azure DevOps presta serviços de desenvolvimento para apoiar equipas para planear o trabalho, colaborar no desenvolvimento de códigos e construir e implementar aplicações. Os desenvolvedores podem trabalhar na nuvem usando os Serviços Azure DevOps. A Azure DevOps fornece um conjunto integrado de funcionalidades a que pode aceder através do seu navegador web ou cliente IDE. O Pipeline Azure é uma destas características. O Azure Pipelines é um serviço de integração contínua (CI) e entrega contínua (CD). Trabalha com o seu fornecedor git preferido e pode ser implantado na maioria dos principais serviços de nuvem. Depois, pode automatizar a construção, teste e implementação do seu código para o Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

Este tutorial foi concebido para desenvolvedores de modelos de gestor de recursos Azure que são novos Serviços Azure DevOps e Pipelines Azure. Se já está familiarizado com gitHub e DevOps, pode saltar para [Criar um pipeline](#create-a-pipeline).

> [!NOTE]
> Escolha um nome de projeto. Quando passar pelo tutorial, substitua qualquer um do **AzureRmPipeline** pelo seu nome de projeto.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório do GitHub
> * Criar um projeto de DevOps do Azure
> * Criar um oleoduto Azure
> * Verifique a implantação do gasoduto
> * Atualizar o modelo e recolocar
> * Limpar recursos

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* **Uma conta GitHub,** onde a usa para criar um repositório para os seus modelos. Se não tiver um, pode [criar um de graça.](https://github.com) Para obter mais informações sobre a utilização de repositórios GitHub, consulte [os repositórios Build GitHub](/azure/devops/pipelines/repos/github).
* **Instale Git**. Esta instrução tutorial utiliza *Git Bash* ou *Git Shell*. Para obter instruções, consulte [Instalar Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Uma organização Azure DevOps.** Se não tiver um, pode criar um de graça. Ver Criar uma organização ou coleção de [projetos.]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* Visual Studio Code com extensão Ferramentas do Resource Manager. Consulte [o Código do Estúdio Visual para criar modelos](use-vs-code-to-create-template.md)de Gestor de Recursos Azure .

## <a name="prepare-a-github-repository"></a>Preparar um repositório do GitHub

O GitHub é usado para armazenar o seu código fonte de projeto, incluindo modelos de Gestor de Recursos. Para outros repositórios apoiados, consulte [repositórios apoiados por Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se não tiver uma conta GitHub, consulte [os pré-requisitos.](#prerequisites)

1. Inscreva-se no [GitHub.](https://github.com)
2. Selecione a imagem da sua conta no canto superior direito e, em seguida, selecione **os seus repositórios**.

    ![Azure DevOps Azure Pipelines do Azure Pipelines do Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecione **Novo,** um botão verde.
1. Em **nome de Repositório,** insira um nome de repositório.  Por exemplo, **AzureRmPipeline-repo**. Lembre-se de substituir qualquer um dos **AzureRmPipeline** pelo seu nome de projeto. Pode selecionar **público** ou **privado** para passar por este tutorial. E, em seguida, **selecione Criar repositório**.
1. Escreva a URL. O URL repositório é o seguinte formato:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Este repositório é referido como um *repositório remoto.* Cada um dos desenvolvedores do mesmo projeto pode clonar o seu próprio *repositório local,* e fundir as alterações ao repositório remoto.

### <a name="clone-the-remote-repository"></a>Clone o repositório remoto

1. Open Git Shell ou Git Bash.  Veja [Pré-requisitos](#prerequisites).
1. Verifique se a sua pasta atual é **github**.
1. Execute o seguinte comando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Substitua **[YourAccountName]** pelo nome da conta GitHub e substitua **[YourGitHubRepositório]** pelo seu nome de repositório que criou no procedimento anterior.

    A imagem que se segue mostra um exemplo.

    ![Azure DevOps Azure Pipelines do Azure Pipelines do Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

A pasta **CreateAzureStorage** é a pasta onde o modelo é armazenado. O comando **pwd** mostra o caminho da pasta. O caminho é onde você guarda o modelo no seguinte procedimento.

### <a name="download-a-quickstart-template"></a>Descarregue um modelo Quickstart

Em vez de criar um modelo, pode descarregar um [modelo Quickstart]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). Este modelo cria uma conta de Armazenamento Azure.

1. Código open visual studio. Veja [Pré-requisitos](#prerequisites).
2. Abra o modelo com o seguinte URL:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Guarde o ficheiro como **azuredeploy.json** para a pasta **CreateAzureStorage.** Tanto o nome da pasta como o nome do ficheiro são utilizados tal como estão no gasoduto.  Se alterar estes nomes, tem de atualizar os nomes utilizados no oleoduto.

### <a name="push-the-template-to-the-remote-repository"></a>Empurre o modelo para o repositório remoto

O azuredeploy.json foi adicionado ao repositório local. Em seguida, você envia o modelo para o repositório remoto.

1. Abra *Git Shell* ou *Git Bash,* se não estiver aberto.
1. Mude o diretório para a pasta CreateAzureStorage no seu repositório local.
1. Verifique se o ficheiro **azuredeploy.json** está na pasta.
1. Execute o seguinte comando:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Pode receber um aviso sobre lf. Pode ignorar o aviso. **mestre** é o ramo principal.  Normalmente cria-se um ramo para cada atualização. Para simplificar o tutorial, usa o ramo principal diretamente.
1. Navegue no seu repositório GitHub a partir de um browser.  O URL é ** https://github.com/[YourAccountName]/[YourGitHubRepository]**. Verá a pasta **CreateAzureStorage** e **o Azuredeploy.json** dentro da pasta.

Até agora, criou um repositório GitHub e carregou um modelo para o repositório.

## <a name="create-a-devops-project"></a>Criar um projeto DevOps

Uma organização DevOps é necessária antes de poder passar ao próximo procedimento.  Se não tem um, consulte [os pré-requisitos.](#prerequisites)

1. Inscreva-se no [Azure DevOps.](https://dev.azure.com)
1. Selecione uma organização DevOps da esquerda.

    ![Azure DevOps Azure Pipelines do Azure Pipelines azure da Azure cria projeto Azure DevOps](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Create project** (Criar projeto). Se não tiver quaisquer projetos, a página de criação do projeto é aberta automaticamente.
1. Introduza os seguintes valores:

    * **Nome**do projeto : insira um nome de projeto. Pode usar o nome do projeto que escolheu no início do tutorial.
    * **Controlo da versão**: Selecione **Git**. Pode ser necessário expandir o **Advanced** para ver **o controlo da versão.**

    Utilize o valor predefinido para as outras propriedades.
1. Selecione **Create project** (Criar projeto).

Crie uma ligação de serviço que seja usada para implementar projetos para o Azure.

1. Selecione **as definições** do Projeto a partir da parte inferior do menu esquerdo.
1. Selecione **ligações de serviço** em **Pipelines**.
1. Selecione **a nova ligação de serviço**e, em seguida, selecione **AzureResourceManager**.
1. Introduza os seguintes valores:

    * Nome da **ligação:** introduza um nome de ligação. Por exemplo, **AzureRmPipeline-conn**. Escreva este nome, precisa do nome quando criar o seu oleoduto.
    * **Nível de âmbito**: selecione **Subscrição**.
    * **Subscrição**: selecione a sua subscrição.
    * **Grupo de Recursos**: Deixe-o em branco.
    * **Permita que todos os gasodutos utilizem esta ligação**. (selecionado)
1. Selecione **OK**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Até agora, completou as seguintes tarefas.  Se saltar as secções anteriores porque está familiarizado com gitHub e DevOps, deve completar as tarefas antes de continuar.

- Crie um repositório GitHub e guarde [este modelo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) para a pasta **CreateAzureStorage** no repositório.
- Crie um projeto DevOps e crie uma ligação de serviço do Gestor de Recursos Azure.

Para criar um oleoduto com um passo para implementar um modelo:

1. Selecione **Pipelines** a partir do menu esquerdo.
1. Selecione **Novo oleoduto**.
1. No separador **Connect** (Ligar), selecione **GitHub**. Se for solicitado, insira as suas credenciais GitHub e siga as instruções. Se vir o ecrã seguinte, selecione **Apenas selecione repositórios**, e verifique se o seu repositório está na lista antes de selecionar **& Instalar**.

    ![Azure DevOps Azure Pipelines, gestor de recursos azure, apenas seleciona repositórios](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. A partir do separador **Select,** selecione o seu repositório.  O nome predefinido é **[YourAccountName]/[YourGitHubRepositoryName]**.
1. A partir do separador **Configurar, selecione** **o gasoduto Starter**. Mostra o ficheiro do gasoduto **azure-yml** com dois passos de script.
1. Substitua a secção **de passos** pelo seguinte YAML:

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

    Vai parecer:

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Efetue as seguintes alterações:

    * **implementaçãoScope**: Selecione o `Management Group`âmbito `Subscription` `Resource Group`de implantação a partir das opções: , e . Use **o Grupo de Recursos** neste tutorial. Para saber mais sobre os âmbitos, consulte [os âmbitos de implantação.](deploy-rest.md#deployment-scope)
    * **Nome de serviço ligado**: Especifique o nome de ligação de serviço que criou anteriormente.
    * **Nome de subscrição**: Especifique o ID de subscrição do alvo.
    * **ação**: a ação **create or update Resource Group** faz 2 ações - 1. criar um grupo de recursos se for fornecida uma nova nome de grupo de recursos; 2. implementar o modelo especificado.
    * **recursoGroupName**: especificar um novo nome de grupo de recursos. Por exemplo, **AzureRmPipeline-rg**.
    * **localização**: especificar a localização do grupo de recursos.
    * **modeloLocalização**: quando o **artefacto Linked** é especificado, a tarefa procura o ficheiro do modelo diretamente do repositório ligado.
    * **csmFile** é o caminho para o ficheiro do modelo. Não é necessário especificar um ficheiro de parâmetros de modelo porque todos os parâmetros definidos no modelo têm valores predefinidos.

    Para mais informações sobre a tarefa, consulte a tarefa de implantação do Grupo de [Recursos Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)e a tarefa de implantação do modelo do Gestor de [Recursos Azure](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)
1. Selecione **Guardar e executar**.
1. Selecione **Guardar e correr** novamente. Uma cópia do ficheiro YAML é guardada no repositório ligado. Pode ver o ficheiro YAML navegando no seu repositório.
1. Verifique se o gasoduto é executado com sucesso.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. O nome é o que especificou no ficheiro YAML do oleoduto.  Verá uma conta de armazenamento criada.  O nome da conta de armazenamento começa com **a loja**.
1. Selecione o nome da conta de armazenamento para abri-lo.
1. Selecione **Propriedades**. Note que a **Replicação** é **armazenamento localmente redundante (LRS)**.

    ![Verificação do portal Azure DevOps Azure Pipelines Do Gestor de Recursos Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Atualizar e redistribuir

Quando atualiza o modelo e empurra as alterações para o repositório remoto, o gasoduto atualiza automaticamente os recursos, a conta de armazenamento neste caso.

1. Abra **azuredeploy.json** do seu repositório local em Visual Studio Code.
1. Atualize o **valor padrãoValue** do **storageAccountType** para **Standard_GRS**. Veja a captura de ecrã abaixo:

    ![Azure DevOps Azure Pipelines atualização de Pipelines Azure](./media/template-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Guarde as alterações.
1. Empurre as alterações para o repositório remoto executando os seguintes comandos de Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    O primeiro comando sincroniza o repositório local com o repositório remoto. Lembre-se que o ficheiro YAML do gasoduto foi adicionado ao repositório remoto.

    Com o ramo principal do repositório remoto atualizado, o oleoduto é disparado novamente.

Para verificar as alterações, pode verificar a propriedade de Replicação da conta de armazenamento.  Ver [Verificar a implantação](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.

Também pode querer eliminar o repositório GitHub e o projeto Azure DevOps.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você cria um pipeline Azure DevOps para implementar um modelo de Gestor de Recursos Azure. Para saber como implementar recursos do Azure em várias regiões e como utilizar práticas de implementação seguras, veja

> [!div class="nextstepaction"]
> [Utilize práticas de implementação seguras](./deployment-manager-tutorial.md)
