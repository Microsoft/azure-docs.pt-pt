---
title: (DEPRECIADO) CI/CD com motor de serviço de contentores azure e modo enxame
description: Utilize o motor de serviço de contentores Azure com o Modo Swarm Docker, um registo de contentores Azure e A Azure DevOps para entregar continuamente uma aplicação multi-contentor .NET Core
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277919"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(DEPRECIADO) Gasoduto CI/CD completo para implantar uma aplicação multi-contentor no Serviço de Contentores Azure com motor ACS e modo swarm Docker utilizando O Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Este artigo [baseia-se no gasoduto CI/CD completo para implantar uma aplicação multi-contentor no Serviço de Contentores Azure com docker Swarm usando documentação Azure DevOps](container-service-docker-swarm-setup-ci-cd.md)*

Hoje em dia, um dos maiores desafios no desenvolvimento de aplicações modernas para a nuvem é conseguir entregar estas aplicações continuamente. Neste artigo, aprende-se a implementar um gasoduto de integração e implantação contínua completo (CI/CD) utilizando: 
* Motor de serviço de contentores azure com modo swarm Docker
* Registo de Contentores do Azure
* DevOps do Azure


![Aplicação de amostra myShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

O objetivo é entregar esta aplicação continuamente num cluster docker Swarm Mode, utilizando O Azure DevOps. A figura seguinte detalha este gasoduto de entrega contínua:

![Aplicação de amostra myShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Aqui está uma breve explicação dos passos:

1. As alterações de código são comprometidas com o repositório de código fonte (aqui, GitHub) 
2. GitHub desencadeia uma construção em Azure DevOps 
3. Azure DevOps obtém a versão mais recente das fontes e constrói todas as imagens que compõem a aplicação 
4. Azure DevOps empurra cada imagem para um registo docker criado usando o serviço de registo de contentores Azure 
5. Azure DevOps despoleta novo lançamento 
6. A libertação executa alguns comandos usando SSH no nó principal do cluster de serviço de contentores Azure 
7. Docker Swarm Mode no cluster puxa a versão mais recente das imagens 
8. A nova versão da aplicação é implementada usando Docker Stack 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, precisa de completar as seguintes tarefas:

- [Crie um cluster de modo enxame no serviço de contentores Azure com motor ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Ligar com o cluster Swarm no Serviço de Contentor do Azure](../container-service-connect.md)
- [Criar um registo de contentor do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização e projeto Azure DevOps criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Fork the GitHub repositório para a sua conta GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> O orchestrator do Docker Swarm no Azure Container Service utiliza o legado autónomo do Swarm. Atualmente, o [modo Swarm](https://docs.docker.com/engine/swarm/) integrado (no Docker 1.12 e superior) não é um orchestrator suportado no Azure Container Service. Por esta razão, estamos a usar o [Motor ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), um modelo de [arranque rápido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)com contribuição para a comunidade, ou uma solução Docker no [Azure Marketplace.](https://azuremarketplace.microsoft.com)
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Passo 1: Configure a sua organização Azure DevOps 

Nesta secção, configura a sua organização Azure DevOps. Para configurar os pontos finais dos Serviços Azure DevOps, no seu projeto Azure DevOps, clique no ícone **Definições** na barra de ferramentas e selecione **Serviços**.

![Ponto final de serviços abertos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Ligue a conta Azure DevOps e Azure

Estabeleça uma ligação entre o seu projeto Azure DevOps e a sua conta Azure.

1. À esquerda, clique em **New Service Endpoint** > **Azure Resource Manager**.
2. Para autorizar o Azure DevOps a trabalhar com a sua conta Azure, selecione a sua **Subscrição** e clique em **OK**.

    ![Azure DevOps - Autorizar o Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Connect Azure DevOps e GitHub

Estabeleça uma ligação entre o seu projeto Azure DevOps e a sua conta GitHub.

1. À esquerda, clique no **Novo Ponto Final de** > Serviço**GitHub**.
2. Para autorizar o Azure DevOps a trabalhar com a sua conta GitHub, clique **em Autorizar** e siga o procedimento na janela que se abre.

    ![Azure DevOps - Autorizar o GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Ligue os DevOps Azure ao seu cluster de serviço de contentores Azure

Os últimos passos antes de entrar no oleoduto CI/CD são configurar ligações externas ao seu cluster Docker Swarm em Azure. 

1. Para o cluster Docker Swarm, adicione um ponto final de tipo **SSH**. Em seguida, introduza as informações de ligação SSH do seu cluster Swarm (nó mestre).

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Toda a configuração está feita agora. Nos próximos passos, cria-se o oleoduto CI/CD que constrói e implanta a aplicação para o cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passo 2: Criar o gasoduto de construção

Neste passo, você configura um pipeline de construção para o seu projeto Azure DevOps e define o fluxo de trabalho de construção para as suas imagens de contentores

### <a name="initial-pipeline-setup"></a>Configuração inicial do gasoduto

1. Para criar um pipeline de construção, ligue-se ao seu projeto Azure DevOps e clique em **Construir & Lançamento**. Na secção **Definições de Construção,** clique **+ Novo**. 

    ![Azure DevOps - Novo Pipeline de Construção](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecione o **processo Empty**.

    ![Azure DevOps - Novo Oleoduto de Construção Vazia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Em seguida, clique no separador **Variáveis** e crie duas novas variáveis: **RegistryURL** e **AgentURL**. Colhe os valores do seu Registo e Dos Agentes de Cluster DNS.

    ![Azure DevOps - Configuração de Variáveis de Construção](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na página **Build Definições,** abra o separador **Triggers** e configure a construção para utilizar a integração contínua com a forquilha do projeto MyShop que criou nos pré-requisitos. Em seguida, selecione **alterações de lote**. Certifique-se de que seleciona *o docker-linux* como especificação do **ramo**.

    ![Azure DevOps - Construa configuração repositória](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Por fim, clique no separador **Opções** e configure a fila do agente Predefinido para **a pré-visualização do Linux .**

    ![Azure DevOps - Configuração do Agente Anfitrião](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Defina o fluxo de trabalho de construção
Os próximos passos definem o fluxo de trabalho de construção. Primeiro, tens de configurar a fonte do código. Para isso, selecione **GitHub** e o seu **repositório** e **ramo** (docker-linux).

![Azure DevOps - Configure fonte de código](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Existem cinco imagens de contentores para construir para a aplicação *MyShop.* Cada imagem é construída utilizando o Dockerfile localizado nas pastas do projeto:

* ProdutosApi
* Proxy
* RatingsApi
* RecomendaçõesApi
* Frente à Loja

Precisa de dois passos Docker para cada imagem, um para construir a imagem, e um para empurrar a imagem no registo de contentores Azure. 

1. Para adicionar um passo no fluxo de trabalho de construção, clique + Adicione o passo de **construção** e selecione **Docker**.

    ![Azure DevOps - Adicionar passos de construção](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Para cada imagem, configure um `docker build` passo que utilize o comando.

    ![Azure DevOps - Docker Build](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Para a operação de construção, selecione o registo de contentores Azure, a **construção de uma** ação de imagem e o Dockerfile que define cada imagem. Defina o **Diretório de Trabalho** como o diretório raiz do Dockerfile, defina o Nome de **Imagem,** e selecione Incluir a **Etiqueta Mais Recente**.
    
    O Nome da Imagem tem ```$(RegistryURL)/[NAME]:$(Build.BuildId)```de estar neste formato: . Substitua **[NOME]** com o nome de imagem:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Para cada imagem, configure um `docker push` segundo passo que utilize o comando.

    ![Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Para a operação push, selecione o registo do seu recipiente Azure, o Push uma ação de **imagem,** introduza o Nome de **Imagem** que foi construído no passo anterior e selecione Incluir a **Última Etiqueta**.

4. Depois de configurar os passos de construção e impulso para cada uma das cinco imagens, adicione mais três passos no fluxo de trabalho de construção.

   ![Azure DevOps - Adicionar tarefa de linha de comando](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Uma tarefa de linha de comando que utiliza um script de bash para substituir a ocorrência *de RegistryURL* no ficheiro docker-compose.yml com a variável RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps - Update Compose file with Registry URL](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Uma tarefa de linha de comando que utiliza um script de bash para substituir a ocorrência *do AgentURL* no ficheiro docker-compose.yml com a variável AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Uma tarefa que deixa cair o ficheiro compor atualizado como um artefacto de construção para que possa ser usado na libertação. Consulte o seguinte ecrã para obter mais detalhes.

      ![Azure DevOps - Publicar Artefacto](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps - Publicar ficheiro Compor](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Clique em **Guardar & fila** para testar o seu oleoduto de construção.

   ![Azure DevOps - Poupe e faça fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps - Nova Fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Se a **Build** estiver correta, tem de ver este ecrã:

   ![Azure DevOps - Construção conseguida](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Passo 3: Criar o gasoduto de libertação

O Azure DevOps permite-lhe [gerir lançamentos em ambientes](https://www.visualstudio.com/team-services/release-management/). Pode ativar a implantação contínua para se certificar de que a sua aplicação é implantada nos seus diferentes ambientes (como dev, teste, pré-produção e produção) de forma suave. Pode criar um ambiente que represente o seu cluster modo Swarm De Swarm de Serviço de Contentores Azure.

![Azure DevOps - Lançamento para ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração inicial de lançamento

1. Para criar um pipeline de libertação, clique em **Lançamentos** > **+ Lançamento**

2. Para configurar a fonte do artefacto, clique em **Artefactos** > **Link uma fonte**de artefacto . Aqui, ligue este novo oleoduto de lançamento à construção que definiu no passo anterior. Depois disso, o ficheiro docker-compose.yml está disponível no processo de libertação.

    ![Azure DevOps - Artefactos de Libertação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Para configurar o gatilho de desbloqueio, clique em **Gatilhos** e selecione **Implantação Contínua**. Acione o gatilho na mesma fonte de artefacto. Esta definição garante que uma nova versão começa quando a construção se completa com sucesso.

    ![Azure DevOps - Gatilhos de libertação](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Para configurar as variáveis de lançamento, clique em **Variáveis** e **selecione +Variável** para criar três novas variáveis com a informação do registo: **docker.username,** **docker.password**, e **docker.registry**. Colhe os valores do seu Registo e Dos Agentes de Cluster DNS.

    ![Azure DevOps - Construa configuração repositória](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Como mostrado no ecrã anterior, clique na caixa de verificação **Lock** em docker.password. Esta definição é importante para restringir a palavra-passe.
    >

### <a name="define-the-release-workflow"></a>Defina o fluxo de trabalho de libertação

O fluxo de trabalho de libertação é composto por duas tarefas que se adicionam.

1. Configure uma tarefa para copiar de forma segura o ficheiro de composição para uma pasta de *implantação* no nó principal do Docker Swarm, utilizando a ligação SSH configurada anteriormente. Consulte o seguinte ecrã para obter mais detalhes.
    
    Pasta de origem:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps - Lançamento SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configure uma segunda tarefa para executar `docker` `docker stack deploy` um comando de batida para executar e comande no nó principal. Consulte o seguinte ecrã para obter mais detalhes.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps - Release Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    O comando executado no mestre usa o Docker CLI e o Docker-Compose CLI para fazer as seguintes tarefas:

   - Inicie sessão no registo de contentores Azure (utiliza três variáveis de construção que são definidas no separador **Variáveis)**
   - Defina a variável **DOCKER_HOST** para trabalhar com o ponto final do Enxame (:2375)
   - Navegue para a pasta de *implantação* que foi criada pela tarefa de cópia segura anterior e que contém o ficheiro docker-compose.yml 
   - Execute `docker stack deploy` comandos que puxem as novas imagens e criem os recipientes.

     >[!IMPORTANT]
     > Como mostrado no ecrã anterior, deixe a caixa de verificação Fail na Caixa de Verificação **STDERR** sem ser controlada. Esta definição permite-nos completar `docker-compose` o processo de libertação devido à impressão de várias mensagens de diagnóstico, como os contentores estão a parar ou a ser eliminados, na saída padrão de erros. Se verificar a caixa de verificação, a Azure DevOps informa que ocorreram erros durante a libertação, mesmo que tudo corra bem.
     >
3. Guarde este novo oleoduto de lançamento.

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4: Testar o gasoduto CI/CD

Agora que terminou a configuração, é hora de testar este novo oleoduto CI/CD. A forma mais fácil de o testar é atualizar o código fonte e comprometer as alterações no seu repositório GitHub. Alguns segundos depois de empurrar o código, verá uma nova construção em funcionamento em Azure DevOps. Uma vez concluída com sucesso, um novo lançamento é desencadeado e implementado a nova versão da aplicação no cluster do Serviço de Contentores Azure.

## <a name="next-steps"></a>Passos seguintes

* Para mais informações sobre CI/CD com Azure DevOps, consulte o artigo de Documentação de [Pipelines Azure.](/azure/devops/pipelines/?view=azure-devops)
* Para mais informações sobre o Motor ACS, consulte o [repo gitHub do motor ACS](https://github.com/Azure/acs-engine).
* Para obter mais informações sobre o modo Docker Swarm, consulte a visão geral do [modo Docker Swarm](https://docs.docker.com/engine/swarm/).
