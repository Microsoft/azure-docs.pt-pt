---
title: Integração contínua com os Pipelines do Azure
description: Aprenda a construir, testar e implementar continuamente modelos de Gestor de Recursos Azure.
ms.date: 08/24/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 433811cb632aae0d7370fc8e401c01fe36621a5b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333242"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Tutorial: Integração contínua de modelos do Gestor de Recursos Azure com Pipelines Azure

No [tutorial anterior,](./deployment-tutorial-linked-template.md)implementa-se um modelo ligado.  Neste tutorial, você aprende a usar pipelines Azure para construir e implementar continuamente projetos de modelo de Gestor de Recursos Azure.

A Azure DevOps fornece serviços de desenvolvimento para apoiar equipas para planear o trabalho, colaborar no desenvolvimento de códigos e construir e implementar aplicações. Os desenvolvedores podem trabalhar na nuvem usando os Serviços Azure DevOps. O Azure DevOps fornece um conjunto integrado de funcionalidades que pode aceder através do seu navegador web ou cliente IDE. Azure Pipeline é uma destas funcionalidades. Azure Pipelines é um serviço de integração contínua (CI) e entrega contínua (CD) totalmente apresentado. Funciona com o seu fornecedor de Git preferido e pode implementar para a maioria dos principais serviços em nuvem. Em seguida, pode automatizar a construção, teste e implementação do seu código para o Microsoft Azure, Google Cloud Platform ou Amazon Web Services.

> [!NOTE]
> Escolha um nome de projeto. Quando passar pelo tutorial, substitua qualquer um dos **AzureRmPipeline** pelo nome do seu projeto.
> Este nome do projeto é usado para gerar nomes de recursos.  Um dos recursos é uma conta de armazenamento. Os nomes das contas de armazenamento devem ter entre 3 e 24 caracteres de comprimento e utilizar apenas números e letras minúsculas. O nome tem de ser exclusivo. No modelo, o nome da conta de armazenamento é o nome do projeto com "store" anexado, e o nome do projeto deve estar entre 3 e 11 caracteres. Assim, o nome do projeto deve satisfazer os requisitos de nome da conta de armazenamento e tem menos de 11 caracteres.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Preparar um repositório do GitHub
> * Criar um projeto do Azure DevOps
> * Criar um gasoduto Azure
> * Verifique a implantação do gasoduto
> * Atualizar o modelo e reimplantar
> * Limpar os recursos

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* **Uma conta GitHub,** onde a utiliza para criar um repositório para os seus modelos. Caso não tenha uma, pode [criá-la gratuitamente](https://github.com). Para obter mais informações sobre a utilização de repositórios GitHub, consulte [os repositórios Build GitHub](/azure/devops/pipelines/repos/github).
* **Instale git**. Esta instrução tutorial usa *Git Bash* ou *Git Shell.* Para obter instruções, consulte [instalar o Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Uma organização da Azure DevOps.** Caso não tenha uma, pode criá-la gratuitamente. Ver [Criar uma organização ou coleção de projetos.](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* (opcional) **Código de estúdio visual com extensão de Ferramentas do Gestor de Recursos**. Consulte [Quickstart: Crie modelos de Gestor de Recursos Azure com Código de Estúdio Visual](quickstart-create-templates-use-visual-studio-code.md).

## <a name="prepare-a-github-repository"></a>Preparar um repositório do GitHub

O GitHub é utilizado para armazenar o seu código fonte de projeto, incluindo modelos de Gestor de Recursos. Para outros repositórios apoiados, consulte [repositórios apoiados pela Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops).

### <a name="create-a-github-repository"></a>Criar um repositório GitHub

Se não tiver uma conta GitHub, consulte [Pré-requisitos.](#prerequisites)

1. Inscreva-se no [GitHub.](https://github.com)
1. Selecione a imagem da sua conta no canto superior direito e, em seguida, selecione **os seus repositórios**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines criam repositório GitHub](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-github-repository.png)

1. Selecione **Novo,** um botão verde.
1. Em **nome do Repositório,** insira um nome de repositório.  Por exemplo, **AzureRmPipeline-repo**. Lembre-se de substituir qualquer um de **AzureRmPipeline** pelo nome do seu projeto. Pode selecionar **público** ou **privado** para passar por este tutorial. E, em seguida, **selecione Criar repositório**.
1. Escreva a URL. O URL do repositório é o seguinte formato - **`https://github.com/[YourAccountName]/[YourRepositoryName]`** .

Este repositório é referido como um *repositório remoto.* Cada um dos desenvolvedores do mesmo projeto pode clonar o seu próprio *repositório local,* e fundir as alterações ao repositório remoto.

### <a name="clone-the-remote-repository"></a>Clone o repositório remoto

1. Abra Git Shell ou Git Bash.  Veja [Pré-requisitos](#prerequisites).
1. Verifique se a sua pasta atual é **GitHub**.
1. Execute o seguinte comando:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateWebApp
    cd CreateWebApp
    pwd
    ```

    Substitua **[YourAccountName]** pelo seu nome de conta GitHub e substitua **[YourGitHubRepositoryName]** pelo seu nome de repositório que criou no procedimento anterior.

A pasta **CreateWebApp** é a pasta onde o modelo é armazenado. O comando **da pwd** mostra o caminho da pasta. O caminho é onde se guarda o modelo para o seguinte procedimento.

### <a name="download-a-quickstart-template"></a>Descarregue um modelo Quickstart

Em vez de criar os modelos, pode descarregar os modelos e guardá-los para a pasta **CreateWebApp.**

* O modelo principal: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
* O modelo ligado: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Tanto o nome da pasta como os nomes dos ficheiros são utilizados como estão na calha.  Se alterar estes nomes, tem de atualizar os nomes utilizados no oleoduto.

### <a name="push-the-template-to-the-remote-repository"></a>Empurre o modelo para o repositório remoto

O azuredeploy.jsfoi adicionado ao repositório local. Em seguida, faça o upload do modelo para o repositório remoto.

1. Abra *Git Shell* ou Git *Bash,* se não for aberto.
1. Altere o diretório para a pasta CreateWebApp no seu repositório local.
1. Verifique se a **azuredeploy.jsno** ficheiro está na pasta.
1. Execute o seguinte comando:

    ```bash
    git add .
    git commit -m "Add web app templates."
    git push origin master
    ```

    Pode receber um aviso sobre a LF. Pode ignorar o aviso. **mestre** é o ramo mestre.  Normalmente cria-se um ramo para cada atualização. Para simplificar o tutorial, usa-se o ramo principal diretamente.
1. Navegue pelo seu repositório GitHub a partir de um browser.  A URL **`https://github.com/[YourAccountName]/[YourGitHubRepository]`** é. Verá a pasta **CreateWebApp** e os três ficheiros dentro da pasta.
1. Selecione **linkedStorageAccount.js** para abrir o modelo.
1. Selecione o botão **Raw.** A URL começa com **raw.githubusercontent.com.**
1. Faça uma cópia do URL.  Você precisa fornecer este valor quando configurar o oleoduto mais tarde no tutorial.

Até agora, criou um repositório GitHub e carregou os modelos para o repositório.

## <a name="create-a-devops-project"></a>Criar um projeto DevOps

Uma organização de DevOps é necessária antes de poder avançar para o procedimento seguinte.  Se não tiver, consulte [pré-requisitos.](#prerequisites)

1. Inscreva-se na [Azure DevOps](https://dev.azure.com).
1. Selecione uma organização DevOps a partir da esquerda.

    ![Azure Resource Manager Azure DevOps Azure Pipelines cria projeto Azure DevOps](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Selecione **Novo projeto**. Se não tiver projetos, a página do projeto create é aberta automaticamente.
1. Introduza os seguintes valores:

    * **Nome do**projeto : insira um nome de projeto. Pode usar o nome do projeto que escolheu no início do tutorial.
    * **Controlo da versão**: Selecione **Git**. Poderá ser necessário expandir o **Advanced** para ver o **controlo da versão.**

    Utilize o valor predefinido para as outras propriedades.
1. Selecione **Criar**.

Crie uma ligação de serviço que seja usada para implantar projetos para a Azure.

1. Selecione **as definições** do Projeto a partir da parte inferior do menu esquerdo.
1. Selecione **ligações de serviço** em **Pipelines**.
1. Selecione **nova ligação de serviço**, selecione **Azure Resource Manager**e, em seguida, selecione **Seguinte**.
1. Selecione **o principal do serviço**e, em seguida, selecione **Seguinte**.
1. Introduza os seguintes valores:

    * **Nível de âmbito**: selecione **Subscrição**.
    * **Subscrição**: selecione a sua subscrição.
    * **Grupo de Recursos**: Deixe-o em branco.
    * **Nome de ligação**: introduza um nome de ligação. Por exemplo, **AzureRmPipeline-conn**. Escreva este nome, precisa do nome quando criar o seu oleoduto.
    * **Conceder permissão de acesso a todos os oleodutos.** (selecionado)
1. Selecione **Guardar**.

## <a name="create-a-pipeline"></a>Criar um pipeline

Até agora, completou as seguintes tarefas.  Se saltar as secções anteriores porque está familiarizado com GitHub e DevOps, tem de completar as tarefas antes de continuar.

* Crie um repositório GitHub e guarde os modelos para a pasta **CreateWebApp** no repositório.
* Crie um projeto DevOps e crie uma ligação de serviço Azure Resource Manager.

Para criar um oleoduto com um passo para implementar um modelo:

1. Selecione **Pipelines** do menu esquerdo.
1. Selecione **Novo oleoduto**.
1. No separador **Connect** (Ligar), selecione **GitHub**. Se solicitado, insira as suas credenciais GitHub e siga as instruções. Se vir o ecrã a seguir, selecione **Apenas selecione repositórios**e verifique se o seu repositório está na lista antes de selecionar **Aprovar & instalar**.

    ![Azure Resource Manager Azure DevOps Azure Pipelines apenas seleciona repositórios](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. No **separador Select,** selecione o seu repositório.  O nome padrão é **[YourAccountName]/[YourGitHubRepositoryName]**.
1. A partir do **separador Configurar,** selecione **Starter pipeline**. Mostra o arquivo **de gasodutos azure.yml** com dois passos de script.
1. Apague os dois passos do script do ficheiro yml.
1. Mova o cursor para a linha após **os passos:**.
1. Selecione o assistente de **exibição** à direita do ecrã para abrir o painel **de tarefas.**
1. Selecione **a implementação do modelo ARM**.
1. Introduza os seguintes valores:

    * **deploymentScope**: Select **Resource Group**.. Para saber mais sobre os âmbitos, consulte os [âmbitos de implementação](deploy-rest.md#deployment-scope).
    * **Ligação Azure Resource Manager**: Selecione o nome de ligação de serviço que criou anteriormente.
    * **Assinatura**: Especifique o ID de subscrição-alvo.
    * **Ação**: Selecione a ação **do Grupo de Recursos de Criar ou Atualizar** faz 2 ações - 1. criar um grupo de recursos se for fornecido um novo nome de grupo de recursos; 2. implementar o modelo especificado.
    * **Grupo de recursos**: Introduza um novo nome de grupo de recursos. Por exemplo, **AzureRmPipeline-rg**.
    * **Localização**: Selecione uma localização para o grupo de recursos, por exemplo, **Central US**.
    * **Localização do modelo**: Selecione **artefacto ligado,** o que significa que a tarefa procura o ficheiro do modelo diretamente do repositório ligado.
    * **Modelo**: **Introduza CreateWebApp/azuredeploy.jsem**. Se alterar o nome da pasta e o nome do ficheiro, tem de alterar este valor.
    * **Parâmetros do modelo**: Deixe este campo em branco. Especificará os valores dos parâmetros nos parâmetros do **modelo de substituição**.
    * **Parâmetros do modelo de substituição**: **Enter-projectName [EnterAProjectName] -linkedTemplateUri [EnterTheLinkedTemplateURL]**. Substitua o nome do projeto e o url do modelo ligado. O URL de modelo ligado é o que você escreveu no final do [repositório Criar um GitHub](#create-a-github-repository). Começa **https://raw.githubusercontent.com** com.
    * **Modo de implantação**: Selecione **Incremental**.
    * **Nome de implementação**: Enter **DeployPipelineTemplate**. Selecione **Avançado** antes de ver **o nome de Implementação**.

    ![A screenshot mostra a página de implementação do modelo ARM com os valores necessários introduzidos.](./media/deployment-tutorial-pipeline/resource-manager-template-pipeline-configure.png)

1. Selecione **Adicionar**.

    Para obter mais informações sobre a tarefa, consulte [a tarefa de implementação do Grupo de Recursos Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)e [a tarefa de implementação do modelo do Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

    O ficheiro yml deve ser semelhante:

    ![O Screenshot mostra a página 'Rever' com o novo pipline intitulado Rever o seu pipeline YAML.](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-yml.png)

1. Selecione **Guardar e executar**.
1. A partir do **painel Save and run,** selecione **Save and run** again. Uma cópia do ficheiro YAML é guardada no repositório ligado. Pode ver o ficheiro YAML navegando no seu repositório.
1. Verifique se o gasoduto é executado com sucesso.

    ![Azure Resource Manager Azure DevOps Azure Pipelines yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos. O nome é o que especificou no ficheiro YAML do pipeline.  Verá uma conta de armazenamento criada.  O nome da conta de armazenamento começa com **a loja.**
1. Selecione o nome da conta de armazenamento para abri-la.
1. Selecione **Propriedades**. Note que a **replicação** é **armazenamento localmente redundante (LRS)**.

## <a name="update-and-redeploy"></a>Atualizar e reafectar

Quando atualiza o modelo e empurra as alterações para o repositório remoto, o pipeline atualiza automaticamente os recursos, a conta de armazenamento neste caso.

1. Abra **linkedStorageAccount.jsdo** seu repositório local no Código do Estúdio Visual ou em qualquer editor de texto.
1. Atualizar o **padrãoValue** do **armazenamentoAccountType** para **Standard_GRS**. Veja a captura de ecrã abaixo:

    ![Azure Resource Manager Azure DevOps Azure Pipelines update yaml](./media/deployment-tutorial-pipeline/azure-resource-manager-devops-pipelines-update-yml.png)

1. Guarde as alterações.
1. Empurre as alterações para o repositório remoto executando os seguintes comandos a partir de Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m "Update the storage account type."
    git push origin master
    ```

    O primeiro comando (puxar) sincroniza o repositório local com o repositório remoto. O ficheiro YAML do gasoduto só foi adicionado ao repositório remoto. Executar o comando de puxar descarrega uma cópia do ficheiro YAML para a filial local.

    O quarto comando (push) envia o linkedStorageAccount.jsrevisto no ficheiro para o repositório remoto. Com o ramo principal do repositório remoto atualizado, o gasoduto é disparado novamente.

Para verificar as alterações, pode verificar a propriedade replicação da conta de armazenamento.  Ver [Verificar a implantação](#verify-the-deployment).

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Introduza o nome do grupo de recursos no campo **Filtrar por nome**.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.

Também pode querer eliminar o repositório GitHub e o projeto Azure DevOps.

## <a name="next-steps"></a>Passos seguintes

Parabéns, terminou este tutorial de implementação de modelos do Gestor de Recursos. Informe-nos se tiver algum comentário e sugestões na secção de comentários. Obrigado!
Está pronto para saltar para conceitos mais avançados sobre modelos. O próximo tutorial vai para mais detalhes sobre o uso de documentação de referência do modelo para ajudar a definir recursos para implementar.

> [!div class="nextstepaction"]
> [Utilizar referência de modelo](./template-tutorial-use-template-reference.md)
