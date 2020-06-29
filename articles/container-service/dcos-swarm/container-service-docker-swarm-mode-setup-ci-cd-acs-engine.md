---
title: (PRECADO) CI/CD com motor de serviço de contentores Azure e modo de enxame
description: Utilize o motor de serviço de contentores Azure com o modo De enxame de Docker, um registo de contentores Azure e Azure DevOps para entregar continuamente uma aplicação multi-contentores .NET Core
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 81be5c4db21e3a2201b8802a2e796f45494fd0dc
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445462"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>(PRECADO) Pipeline CI/CD completo para implantar uma aplicação multi-contentor no Serviço de Contentores Azure com motor ACS e modo de enxame de Docker usando Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Este artigo [baseia-se no gasoduto Full CI/CD para implantar uma aplicação multi-contentor no Serviço de Contentores Azure com Docker Swarm usando documentação do Azure DevOps](container-service-docker-swarm-setup-ci-cd.md)*

Hoje em dia, um dos maiores desafios no desenvolvimento de aplicações modernas para a nuvem é conseguir entregar estas aplicações de forma contínua. Neste artigo, aprende-se a implementar um gasoduto de integração e implantação contínua (CI/CD) com: 
* Motor de serviço de contentores Azure com modo de enxame de Docker
* Registo de Contentores do Azure
* Azure DevOps


![Aplicação da amostra MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

O objetivo é entregar esta aplicação continuamente num cluster Docker Swarm Mode, utilizando Azure DevOps. O seguinte valor detalha este gasoduto de entrega contínua:

![Aplicação da amostra MyShop](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Aqui está uma breve explicação dos passos:

1. As alterações de código são comprometidas com o repositório de código fonte (aqui, GitHub) 
2. GitHub despoleta uma construção em Azure DevOps 
3. Azure DevOps obtém a versão mais recente das fontes e constrói todas as imagens que compõem a aplicação 
4. Azure DevOps empurra cada imagem para um registo Docker criado usando o serviço de registo de contentores Azure 
5. Azure DevOps despoleta novo lançamento 
6. O lançamento executa alguns comandos usando SSH no nó mestre de cluster de serviço de contentores Azure 
7. Docker Swarm Mode no cluster puxa a versão mais recente das imagens 
8. A nova versão da aplicação é implementada usando Docker Stack 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, tem de completar as seguintes tarefas:

- [Criar um cluster de modo swarm no serviço de contentores Azure com motor ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Ligar com o cluster Swarm no Serviço de Contentor do Azure](../container-service-connect.md)
- [Criar um registo de contentor do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Tenha uma organização e projeto Azure DevOps criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Fork o repositório GitHub para a sua conta GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> O orchestrator do Docker Swarm no Azure Container Service utiliza o legado autónomo do Swarm. Atualmente, o [modo Swarm](https://docs.docker.com/engine/swarm/) integrado (no Docker 1.12 e superior) não é um orchestrator suportado no Azure Container Service. Por esta razão, estamos a usar [o ACS Engine](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), um [modelo de arranque rápido](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)contribuído pela comunidade, ou uma solução Docker no [Azure Marketplace.](https://azuremarketplace.microsoft.com)
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Passo 1: Configurar a sua organização Azure DevOps 

Nesta secção, configurar a sua organização Azure DevOps. Para configurar os pontos finais dos serviços Azure DevOps, no seu projeto Azure DevOps, clique no ícone Definições na barra de **ferramentas** e selecione **Serviços**.

![Ponto final de serviços abertos](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Ligue a conta Azure DevOps e Azure

Estabeleça uma ligação entre o seu projeto Azure DevOps e a sua conta Azure.

1. À esquerda, clique em **New Service Endpoint**  >  **Azure Resource Manager**.
2. Para autorizar a Azure DevOps a trabalhar com a sua conta Azure, selecione a sua **Subscrição** e clique **em OK**.

    ![Azure DevOps - Authorize Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Ligue Azure DevOps e GitHub

Estabeleça uma ligação entre o seu projeto Azure DevOps e a sua conta GitHub.

1. À esquerda, clique em **New Service Endpoint**  >  **GitHub**.
2. Para autorizar a Azure DevOps a trabalhar com a sua conta GitHub, clique em **"Autorizar"** e siga o procedimento na janela que se abre.

    ![Azure DevOps - Authorize GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Ligue os DevOps do Azure ao seu cluster de serviço de contentores Azure

Os últimos passos antes de entrar no gasoduto CI/CD são para configurar ligações externas ao seu cluster Docker Swarm em Azure. 

1. Para o aglomerado De Docker Swarm, adicione um ponto final do tipo **SSH**. Em seguida, introduza a informação de ligação SSH do seu cluster Swarm (nó mestre).

    ![Azure DevOps - SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Toda a configuração está feita agora. Nos próximos passos, cria-se o oleoduto CI/CD que constrói e implanta a aplicação no cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passo 2: Criar o gasoduto de construção

Neste passo, você configura um pipeline de construção para o seu projeto Azure DevOps e definir o fluxo de trabalho de construção para as suas imagens de contentores

### <a name="initial-pipeline-setup"></a>Configuração inicial do gasoduto

1. Para criar um oleoduto de construção, ligue-se ao seu projeto Azure DevOps e clique em **Build & Release**. Na secção **Definições Build,** clique **+ Novo**. 

    ![Azure DevOps - Novo Pipeline de Construção](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Selecione o **processo Empty**.

    ![Azure DevOps - Novo Pipeline de Construção Vazia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Em seguida, clique no separador **Variáveis** e crie duas novas variáveis: **RegistryURL** e **AgentURL**. Cole os valores do seu DNS de Registo e Cluster.

    ![Azure DevOps - Configuração de variáveis de construção](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na página **Definições de Construção,** abra o **separador Gatilhos** e configuure a construção para utilizar a integração contínua com o garfo do projeto MyShop que criou nos pré-requisitos. Em seguida, selecione **alterações de lote**. Certifique-se de que seleciona *docker-linux* como **especificação do Ramo**.

    ![Azure DevOps - Construa configuração de repositório](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Por fim, clique no separador **Opções** e configuure a fila do agente predefinido para **visualização do Linux hospedado**.

    ![Azure DevOps - Configuração do Agente Anfitrião](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Defina o fluxo de trabalho de construção
Os próximos passos definem o fluxo de trabalho de construção. Primeiro, tens de configurar a origem do código. Para o fazer, selecione **GitHub** e o seu **repositório** e **ramo** (docker-linux).

![Azure DevOps - Fonte de código de configuração](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Há cinco imagens de contentores para construir para a aplicação *MyShop.* Cada imagem é construída utilizando o Dockerfile localizado nas pastas do projeto:

* ProdutosApi
* Proxy
* RatingsApi
* RecomendaçõesApi
* ShopFront

Precisa de dois passos docker para cada imagem, um para construir a imagem, e um para empurrar a imagem no registo do contentor Azure. 

1. Para adicionar um passo no fluxo de trabalho de construção, clique **em + Adicione o passo de construção** e selecione **Docker**.

    ![Azure DevOps - Adicionar passos de construção](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Para cada imagem, configuure um passo que use o `docker build` comando.

    ![Azure DevOps - Construção de Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Para a operação de construção, selecione o registo do seu contentor Azure, o **Build a ação de imagem** e o Dockerfile que define cada imagem. Defina o **Diretório de Trabalho** como diretório de raiz do Dockerfile, defina o Nome de **Imagem**e selecione Incluir a Etiqueta **Mais Recente**.
    
    O Nome da Imagem tem de estar neste formato: ```$(RegistryURL)/[NAME]:$(Build.BuildId)``` . Substitua **[NOME]** pelo nome de imagem:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Para cada imagem, configuure um segundo passo que use o `docker push` comando.

    ![Azure DevOps - Docker Push](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    Para a operação push, selecione o seu registo de contentores Azure, o **Push a** action, insira o Nome **de Imagem** que foi construído no passo anterior e selecione Incluir a Etiqueta **Mais Recente**.

4. Depois de configurar a construção e empurrar os passos para cada uma das cinco imagens, adicione mais três passos no fluxo de trabalho de construção.

   ![Azure DevOps - Adicionar Tarefa de Linha de Comando](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Uma tarefa de linha de comando que usa um script de bash para substituir a ocorrência *de RegistryURL* no ficheiro docker-compose.yml pela variável RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps - Atualizar Ficheiro composição com URL de registo](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Uma tarefa de linha de comando que usa um script de bash para substituir a ocorrência *de AgentURL* no ficheiro docker-compose.yml pela variável AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Uma tarefa que deixa cair o ficheiro Compose atualizado como um artefacto de construção para que possa ser usado no lançamento. Consulte o seguinte ecrã para mais detalhes.

      ![Azure DevOps - Publicar Artefacto](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps - Publicar ficheiro Compose](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Clique **em Guardar & fila** para testar o seu pipeline de construção.

   ![Azure DevOps - Salvar e fazer fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps - Nova Fila](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Se o **Build** estiver correto, tem de ver este ecrã:

   ![Azure DevOps - Build conseguiu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Passo 3: Criar o gasoduto de libertação

O Azure DevOps [permite-lhe gerir lançamentos em todos os ambientes.](https://azure.microsoft.com/services/devops/pipelines/) Pode ativar a implementação contínua para se certificar de que a sua aplicação é implantada nos seus diferentes ambientes (tais como dev, teste, pré-produção e produção) de forma suave. Pode criar um ambiente que represente o seu cluster de modo de enxame de serviço de contentores Azure.

![Azure DevOps - Lançamento para ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração inicial do lançamento

1. Para criar um pipeline de libertação, clique em **Versões**  >  **+ Versão**

2. Para configurar a fonte do artefacto, clique em **Artefactos**  >  **Ligue uma fonte de artefacto**. Aqui, ligue este novo oleoduto de lançamento à construção que definiu no passo anterior. Depois disso, o ficheiro docker-compose.yml está disponível no processo de libertação.

    ![Azure DevOps - Release Artifacts](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Para configurar o gatilho de desbloqueio, clique em **Gatilhos** e selecione **Implementação Contínua**. Coloque o gatilho na mesma fonte de artefacto. Esta definição garante que uma nova versão começa quando a construção estiver concluída com sucesso.

    ![Azure DevOps - Release Triggers](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Para configurar as variáveis de libertação, clique em **Variáveis** e selecione **+Variable** para criar três novas variáveis com a informação do registo: **docker.username,** **docker.password,** e **docker.registry**. Cole os valores do seu DNS de Registo e Cluster.

    ![Azure DevOps - Construa configuração de repositório](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Como mostrado no ecrã anterior, clique na caixa de verificação **Lock** em docker.password. Esta definição é importante para restringir a palavra-passe.
    >

### <a name="define-the-release-workflow"></a>Defina o fluxo de trabalho de libertação

O fluxo de trabalho de libertação é composto por duas tarefas que adiciona.

1. Configure uma tarefa para copiar de forma segura o ficheiro de composição para uma pasta *de implantação* no nó master do Docker Swarm, utilizando a ligação SSH que configuraste anteriormente. Consulte o seguinte ecrã para mais detalhes.
    
    Pasta de origem:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps - Lançamento SCP](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Configurar uma segunda tarefa para executar um comando de bash para executar `docker` e `docker stack deploy` comanda no nó mestre. Consulte o seguinte ecrã para mais detalhes.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps - Release Bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    O comando executado no mestre utiliza o CLI do Docker e o CLI Docker-Compose para fazer as seguintes tarefas:

   - Faça login no registo do contentor Azure (utiliza três variáveis de construção que são definidas no separador **Variáveis)**
   - Defina a variável **DOCKER_HOST** para trabalhar com o ponto final do Enxame (:2375)
   - Navegue para a pasta *de implantação* que foi criada pela tarefa de cópia segura anterior e que contém o ficheiro docker-compose.yml 
   - Execute `docker stack deploy` comandos que puxem as novas imagens e criem os recipientes.

     >[!IMPORTANT]
     > Como mostrado no ecrã anterior, deixe a Falha na caixa de verificação **STDERR** sem ser verificada. Esta definição permite-nos completar o processo de libertação devido às `docker-compose` impressões de várias mensagens de diagnóstico, tais como contentores que estão a parar ou a ser eliminados, na saída padrão de erro. Se verificar a caixa de verificação, a Azure DevOps informa que ocorreram erros durante o lançamento, mesmo que tudo corra bem.
     >
3. Guarde este novo oleoduto de lançamento.

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4: Testar o gasoduto CI/CD

Agora que terminou a configuração, está na hora de testar este novo pipeline CI/CD. A forma mais fácil de testá-lo é atualizar o código fonte e comprometer as alterações no seu repositório GitHub. Alguns segundos depois de premir o código, verá uma nova construção a decorrer em Azure DevOps. Uma vez concluído com sucesso, um novo lançamento é desencadeado e implementado a nova versão da aplicação no cluster de Serviço de Contentores Azure.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre CI/CD com Azure DevOps, consulte o artigo [de Documentação de Gasodutos Azure.](/azure/devops/pipelines/?view=azure-devops)
* Para obter mais informações sobre o motor ACS, consulte o [repo DO Motor ACS GitHub](https://github.com/Azure/acs-engine).
* Para obter mais informações sobre o modo Docker Swarm, consulte a [visão geral](https://docs.docker.com/engine/swarm/)do modo Docker Swarm .
