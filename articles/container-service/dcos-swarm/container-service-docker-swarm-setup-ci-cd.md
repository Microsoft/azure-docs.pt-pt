---
title: (PRECADO) CI/CD com Serviço de Contentores Azure e Enxame
description: Utilize o Serviço de Contentores Azure com o Docker Swarm, um registo de contentores Azure e Azure DevOps para entregar continuamente uma aplicação multi-contentores .NET Core
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: ded7d1b6a5f353c009b5c56bcaabe96baf424c51
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445411"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(PRECADO) Pipeline CI/CD completo para implantar uma aplicação multi-contentor no Serviço de Contentores Azure com Docker Swarm usando serviços Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Um dos maiores desafios no desenvolvimento de aplicações modernas para a nuvem é conseguir entregar estas aplicações de forma contínua. Neste artigo, aprende-se a implementar um oleoduto de integração e implantação contínua (CI/CD) utilizando o Serviço de Contentores Azure com a gestão do Docker Swarm, do Registo de Contentores Azure e da gestão dos Gasodutos Azure.


![Aplicação da amostra MyShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo é entregar esta aplicação continuamente num cluster Docker Swarm, utilizando os Serviços Azure DevOps. O seguinte valor detalha este gasoduto de entrega contínua:

![Aplicação da amostra MyShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Aqui está uma breve explicação dos passos:

1. As alterações de código são comprometidas com o repositório de código fonte (aqui, GitHub) 
1. GitHub despoleta construção nos Serviços Azure DevOps 
1. A Azure DevOps Services obtém a versão mais recente das fontes e constrói todas as imagens que compõem a aplicação 
1. A Azure DevOps Services empurra cada imagem para um registo Docker criado usando o serviço de registo de contentores Azure 
1. Azure DevOps Services despoleta novo lançamento 
1. O lançamento executa alguns comandos usando SSH no nó mestre de cluster de serviço de contentores Azure 
1. Docker Swarm no cluster puxa a versão mais recente das imagens 
1. A nova versão da aplicação é implementada usando Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, tem de completar as seguintes tarefas:

- [Criar um cluster Swarm no Serviço de Contentor do Azure](container-service-deployment.md)
- [Ligar com o cluster Swarm no Serviço de Contentor do Azure](../container-service-connect.md)
- [Criar um registo de contentor do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Tenha uma organização e projeto Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Fork o repositório GitHub para a sua conta GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Também precisa de uma máquina Ubuntu (14.04 ou 16.04) com Docker instalada. Esta máquina é utilizada pelos Serviços Azure DevOps durante os processos Azure Pipelines. Uma forma de criar esta máquina é usar a imagem disponível no Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Passo 1: Configurar a sua organização de Serviços Azure DevOps 

Nesta secção, configura a sua organização Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configure um agente de construção Azure DevOps Linux

Para criar imagens docker e empurrar estas imagens para um registo de contentores Azure a partir de uma construção de Serviços Azure DevOps, você precisa registar um agente Linux. Tem estas opções de instalação:

* [Colocar um agente no Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Use Docker para dirigir o agente dos Serviços Azure DevOps](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instale a extensão dos Serviços de DevOps de Integração do Docker

A Microsoft fornece uma extensão de Serviços Azure DevOps para trabalhar com o Docker nos processos Azure Pipelines. Esta extensão está disponível no [Azure DevOps Services Marketplace.](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) Clique **em Instalar** para adicionar esta extensão à sua organização de Serviços Azure DevOps:

![Instalar a Integração Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

É-lhe pedido que se conecte à sua organização Azure DevOps Services usando as suas credenciais. 

### <a name="connect-azure-devops-services-and-github"></a>Ligue os serviços de devops da Azure e o GitHub

Estabeleça uma ligação entre o seu projeto Azure DevOps Services e a sua conta GitHub.

1. No seu projeto Azure DevOps Services, clique no ícone Definições na barra de **ferramentas** e selecione **Serviços**.

    ![Serviços Azure DevOps - Conexão Externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. À esquerda, clique em **New Service Endpoint**  >  **GitHub**.

    ![Serviços Azure DevOps - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Para autorizar a Azure DevOps Services a trabalhar com a sua conta GitHub, clique em **Autorizar** e siga o procedimento na janela que se abre.

    ![Serviços Azure DevOps - Autorizar GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Ligue os serviços Azure DevOps ao seu registo de contentores Azure e ao cluster de serviços de contentores Azure

Os últimos passos antes de entrar no gasoduto CI/CD são para configurar ligações externas ao seu registo de contentores e ao seu cluster Docker Swarm em Azure. 

1. Nas definições de **Serviços** do seu projeto Azure DevOps Services, adicione um ponto final de serviço do tipo **Registo Docker**. 

1. No popup que abre, insira o URL e as credenciais do seu registo de contentores Azure.

    ![Serviços Azure DevOps - Registo de Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Para o aglomerado De Docker Swarm, adicione um ponto final do tipo **SSH**. Em seguida, insira as informações de ligação SSH do seu cluster Swarm.

    ![Serviços Azure DevOps - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração está feita agora. Nos próximos passos, cria-se o oleoduto CI/CD que constrói e implanta a aplicação no cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passo 2: Criar o gasoduto de construção

Neste passo, você configura um pipeline de construção para o seu projeto Azure DevOps Services e definir o fluxo de trabalho de construção para as suas imagens de contentores

### <a name="initial-pipeline-setup"></a>Configuração inicial do gasoduto

1. Para criar um pipeline de construção, ligue-se ao seu projeto Azure DevOps Services e clique em **Build & Release**. 

1. Na secção **Definições Build,** clique **+ Novo**. Selecione o modelo **Vazio.**

    ![Azure DevOps - Novo Pipeline de Construção](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configure a nova construção com uma fonte de repositório GitHub, verifique **a integração contínua**e selecione a fila do agente onde registou o seu agente Linux. Clique em **Criar** para criar o pipeline de construção.

    ![Serviços Azure DevOps - Criar Pipeline de Construção](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na página **Definições de Construção,** primeiro abra o **separador Repositório** e configuure a construção para utilizar o garfo do projeto MyShop que criou nos pré-requisitos. Certifique-se de que seleciona *acs-docs* como o **ramo Predefinido**.

    ![Serviços Azure DevOps - Construa configuração de repositório](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. No **separador Gatilhos,** configuure a construção a ser ativada após cada compromisso. Selecione **integração contínua** e **alterações no Lote**.

    ![Serviços Azure DevOps - Configuração de gatilho de construção](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Defina o fluxo de trabalho de construção
Os próximos passos definem o fluxo de trabalho de construção. Há cinco imagens de contentores para construir para a aplicação *MyShop.* Cada imagem é construída utilizando o Dockerfile localizado nas pastas do projeto:

* ProdutosApi
* Proxy
* RatingsApi
* RecomendaçõesApi
* ShopFront

É preciso adicionar dois passos docker para cada imagem, um para construir a imagem, e outro para empurrar a imagem no registo do contentor Azure. 

1. Para adicionar um passo no fluxo de trabalho de construção, clique **em + Adicione o passo de construção** e selecione **Docker**.

    ![Serviços Azure DevOps - Adicionar passos de construção](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Para cada imagem, configuure um passo que use o `docker build` comando.

    ![Serviços Azure DevOps - Docker Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de construção, selecione o seu registo de contentores Azure, o **Build a action** de imagem e o Dockerfile que define cada imagem. Defina o **contexto Build** como o diretório de raiz do Dockerfile e defina o Nome **de Imagem**. 
    
    Como mostrado no ecrã anterior, inicie o nome de imagem com o URI do seu registo de contentores Azure. (Também pode utilizar uma variável de construção para parametrizar a etiqueta da imagem, tal como o identificador de construção neste exemplo.)

1. Para cada imagem, configuure um segundo passo que use o `docker push` comando.

    ![Serviços Azure DevOps - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação push, selecione o seu registo do contentor Azure, o **Push uma** ação de imagem e introduza o Nome de **Imagem** que é construído no passo anterior.

1. Depois de configurar a construção e empurrar os passos para cada uma das cinco imagens, adicione mais dois passos no fluxo de trabalho de construção.

    a. Uma tarefa de linha de comando que usa um script de bash para substituir a ocorrência *buildNumber* no ficheiro docker-compose.yml com o Id de construção atual. Consulte o seguinte ecrã para mais detalhes.

    ![Serviços Azure DevOps - Atualizar ficheiro Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que deixa cair o ficheiro Compose atualizado como um artefacto de construção para que possa ser usado no lançamento. Consulte o seguinte ecrã para mais detalhes.

    ![Serviços Azure DevOps - Publicar ficheiro Compose](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Clique **em Guardar** e nomeie o seu pipeline de construção.

## <a name="step-3-create-the-release-pipeline"></a>Passo 3: Criar o gasoduto de libertação

A Azure DevOps Services permite-lhe [gerir lançamentos em todos os ambientes.](https://azure.microsoft.com/services/devops/pipelines/) Pode ativar a implementação contínua para se certificar de que a sua aplicação é implantada nos seus diferentes ambientes (tais como dev, teste, pré-produção e produção) de forma suave. Pode criar um novo ambiente que represente o seu cluster de enxame de serviço de contentores Azure.

![Serviços Azure DevOps - Lançamento para ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração inicial do lançamento

1. Para criar um pipeline de libertação, clique em **Versões**  >  **+ Versão**

1. Para configurar a fonte do artefacto, clique em **Artefactos**  >  **Ligue uma fonte de artefacto**. Aqui, ligue este novo oleoduto de lançamento à construção que definiu no passo anterior. Ao fazê-lo, o ficheiro docker-compose.yml está disponível no processo de libertação.

    ![Serviços Azure DevOps - Release Artifacts](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Para configurar o gatilho de desbloqueio, clique em **Gatilhos** e selecione **Implementação Contínua**. Coloque o gatilho na mesma fonte de artefacto. Esta definição garante que um novo desbloqueio começa assim que a construção estiver concluída com sucesso.

    ![Serviços Azure DevOps - Lançamento de Gatilhos](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Defina o fluxo de trabalho de libertação

O fluxo de trabalho de libertação é composto por duas tarefas que adiciona.

1. Configure uma tarefa para copiar de forma segura o ficheiro de composição para uma pasta *de implantação* no nó master do Docker Swarm, utilizando a ligação SSH que configuraste anteriormente. Consulte o seguinte ecrã para mais detalhes.

    ![Serviços Azure DevOps - Lançamento SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configurar uma segunda tarefa para executar um comando de bash para executar `docker` e `docker-compose` comanda no nó mestre. Consulte o seguinte ecrã para mais detalhes.

    ![Serviços Azure DevOps - Release Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando executado no mestre usa o Docker CLI e o CLI Docker-Compose para fazer as seguintes tarefas:

   - Faça login no registo do contentor Azure (utiliza três variáveis de construção que são definidas no separador **Variáveis)**
   - Defina a variável **DOCKER_HOST** para trabalhar com o ponto final do Enxame (:2375)
   - Navegue para a pasta *de implantação* que foi criada pela tarefa de cópia segura anterior e que contém o ficheiro docker-compose.yml 
   - Execute `docker-compose` comandos que puxem as novas imagens, parem os serviços, removam os serviços e criem os contentores.

     >[!IMPORTANT]
     > Como mostrado no ecrã anterior, deixe a Falha na caixa de verificação **STDERR** sem ser verificada. Esta é uma configuração importante, porque `docker-compose` imprime várias mensagens de diagnóstico, como os contentores, que estão a parar ou a ser eliminadas, na saída padrão de erro. Se verificar a caixa de verificação, a Azure DevOps Services informa que ocorreram erros durante o lançamento, mesmo que tudo corra bem.
     >
1. Guarde este novo oleoduto de lançamento.


>[!NOTE]
>Esta implantação inclui algum tempo de inatividade porque estamos a parar os serviços antigos e a gerir o novo. É possível evitar isto fazendo uma implantação azul-esverdeado.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4. Testar o gasoduto CI/CD

Agora que terminou a configuração, está na hora de testar este novo pipeline CI/CD. A forma mais fácil de testá-lo é atualizar o código fonte e comprometer as alterações no seu repositório GitHub. Alguns segundos depois de premir o código, verá uma nova construção a decorrer nos Serviços Azure DevOps. Uma vez concluída com sucesso, será desencadeada uma nova versão da aplicação no cluster do Serviço de Contentores Azure.

## <a name="next-steps"></a>Passos Seguintes

* Para obter mais informações sobre o CI/CD com os Serviços Azure DevOps, consulte o artigo [de Documentação de Gasodutos Azure.](/azure/devops/pipelines/?view=azure-devops)
