---
title: (DEPRECIADO) CI/CD com Serviço de Contentores Azure e Enxame
description: Utilize o Serviço de Contentores Azure com docker Swarm, um registo de contentores Azure, e Azure DevOps para entregar continuamente uma aplicação multi-contentor .NET Core
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76549058"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(DEPRECIADO) Gasoduto CI/CD completo para implantar uma aplicação multi-contentor no Serviço de Contentores Azure com Docker Swarm utilizando os Serviços Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Um dos maiores desafios no desenvolvimento de aplicações modernas para a nuvem é conseguir entregar estas aplicações continuamente. Neste artigo, aprende-se a implementar um gasoduto de integração e implantação contínua (CI/CD) utilizando o Serviço de Contentores Azure com Docker Swarm, Registo de Contentores Azure e gestão de Pipelines Azure.


![Aplicação de amostra myShop](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

O objetivo é entregar esta aplicação continuamente num cluster Docker Swarm, utilizando os Serviços Azure DevOps. A figura seguinte detalha este gasoduto de entrega contínua:

![Aplicação de amostra myShop](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Aqui está uma breve explicação dos passos:

1. As alterações de código são comprometidas com o repositório de código fonte (aqui, GitHub) 
1. GitHub desencadeia uma construção nos Serviços Azure DevOps 
1. A Azure DevOps Services obtém a versão mais recente das fontes e constrói todas as imagens que compõem a aplicação 
1. A Azure DevOps Services empurra cada imagem para um registo do Docker criado através do serviço de registo de contentores Azure 
1. Serviços Azure DevOps despoleta novo lançamento 
1. A libertação executa alguns comandos usando SSH no nó principal do cluster de serviço de contentores Azure 
1. Docker Swarm no cluster puxa a versão mais recente das imagens 
1. A nova versão da aplicação é implementada usando Docker Compose 

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, precisa de completar as seguintes tarefas:

- [Criar um cluster Swarm no Serviço de Contentor do Azure](container-service-deployment.md)
- [Ligar com o cluster Swarm no Serviço de Contentor do Azure](../container-service-connect.md)
- [Criar um registo de contentor do Azure](../../container-registry/container-registry-get-started-portal.md)
- [Ter uma organização e projeto Azure DevOps Services criado](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Fork the GitHub repositório para a sua conta GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Você também precisa de uma máquina Ubuntu (14.04 ou 16.04) com Docker instalado. Esta máquina é utilizada pela Azure DevOps Services durante os processos dos Oleodutos Azure. Uma forma de criar esta máquina é utilizar a imagem disponível no Mercado Azure. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Passo 1: Configure a sua organização de Serviços Azure DevOps 

Nesta secção, configura a sua organização Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Configure um agente de construção azure DevOps Serviços Linux

Para criar imagens do Docker e empurrar estas imagens para um registo de contentores Azure a partir de uma construção de Serviços Azure DevOps, você precisa registar um agente Linux. Tem estas opções de instalação:

* [Desloque um agente em Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Use docker para dirigir o agente de serviços Azure DevOps](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instale a extensão dos Serviços de Integração DevOps da Docker Integration Azure

A Microsoft fornece uma extensão de Serviços Azure DevOps para trabalhar com o Docker nos processos de Pipelines Azure. Esta extensão está disponível no [Azure DevOps Services Marketplace.](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker) Clique **em Instalar** para adicionar esta extensão à sua organização de Serviços Azure DevOps:

![Instalar a Integração do Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

É-lhe pedido que se ligue à sua organização de Serviços Azure DevOps usando as suas credenciais. 

### <a name="connect-azure-devops-services-and-github"></a>Connect Azure DevOps Services e GitHub

Estabeleça uma ligação entre o seu projeto Azure DevOps Services e a sua conta GitHub.

1. No seu projeto DevOps Services Azure, clique no ícone **Definições** na barra de ferramentas e selecione **Serviços**.

    ![Serviços Azure DevOps - Ligação Externa](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. À esquerda, clique no **Novo Ponto Final de** > Serviço**GitHub**.

    ![Serviços Azure DevOps - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Para autorizar a Azure DevOps Services a trabalhar com a sua conta GitHub, clique **em Autorizar** e siga o procedimento na janela que abre.

    ![Serviços Azure DevOps - Autorizar o GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Ligue os Serviços Azure DevOps ao seu registo de contentores Azure e ao cluster do Serviço de Contentores Azure

Os últimos passos antes de entrar no oleoduto CI/CD são configurar ligações externas ao seu registo de contentores e ao seu cluster Docker Swarm em Azure. 

1. Nas definições de **Serviços** do seu projeto Azure DevOps Services, adicione um ponto final de serviço do tipo **Docker Registry**. 

1. No popup que abre, introduza o URL e as credenciais do seu registo de contentores Azure.

    ![Serviços Azure DevOps - Registo de Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Para o cluster Docker Swarm, adicione um ponto final de tipo **SSH**. Em seguida, introduza as informações de ligação SSH do seu cluster Swarm.

    ![Serviços Azure DevOps - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Toda a configuração está feita agora. Nos próximos passos, cria-se o oleoduto CI/CD que constrói e implanta a aplicação para o cluster Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Passo 2: Criar o gasoduto de construção

Neste passo, você configura um pipeline de construção para o seu projeto Azure DevOps Services e define o fluxo de trabalho de construção para as suas imagens de contentores

### <a name="initial-pipeline-setup"></a>Configuração inicial do gasoduto

1. Para criar um pipeline de construção, ligue-se ao seu projeto DevOps Services e clique em **Construir & Lançamento**. 

1. Na secção **Definições de Construção,** clique **+ Novo**. Selecione o modelo **Vazio.**

    ![Azure DevOps - Novo Pipeline de Construção](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Configure a nova construção com uma fonte de repositório GitHub, verifique a **integração contínua**e selecione a fila do agente onde registou o seu agente Linux. Clique em **Criar** para criar o pipeline de construção.

    ![Serviços Azure DevOps - Criar pipeline de construção](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na página **Build Definições,** abra primeiro o **separador Repositório** e configure a construção para utilizar a bifurcação do projeto MyShop que criou nos pré-requisitos. Certifique-se de que seleciona *acs-docs* como o **ramo Padrão**.

    ![Serviços Azure DevOps - Construção de Configuração repositória](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. No separador **Triggers,** configure a construção a acionar após cada compromisso. Selecione **a integração contínua** e **as alterações do Lote.**

    ![Serviços Azure DevOps - Configuração do gatilho de construção](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Defina o fluxo de trabalho de construção
Os próximos passos definem o fluxo de trabalho de construção. Existem cinco imagens de contentores para construir para a aplicação *MyShop.* Cada imagem é construída utilizando o Dockerfile localizado nas pastas do projeto:

* ProdutosApi
* Proxy
* RatingsApi
* RecomendaçõesApi
* Frente à Loja

Você precisa adicionar dois passos Docker para cada imagem, um para construir a imagem, e um para empurrar a imagem no registo de contentores Azure. 

1. Para adicionar um passo no fluxo de trabalho de construção, clique + Adicione o passo de **construção** e selecione **Docker**.

    ![Serviços Azure DevOps - Adicionar passos de construção](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Para cada imagem, configure um `docker build` passo que utilize o comando.

    ![Serviços Azure DevOps - Docker Build](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Para a operação de construção, selecione o registo de contentores Azure, a **Construção de uma** ação de imagem e o Dockerfile que define cada imagem. Defina o **contexto Build** como o diretório raiz de Dockerfile, e defina o Nome de **Imagem**. 
    
    Como mostrado no ecrã anterior, inicie o nome da imagem com o URI do seu registo de contentores Azure. (Também pode utilizar uma variável de construção para parametrizar a etiqueta da imagem, como o identificador de construção neste exemplo.)

1. Para cada imagem, configure um `docker push` segundo passo que utilize o comando.

    ![Serviços Azure DevOps - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    Para a operação push, selecione o registo do seu recipiente Azure, o **Push uma** ação de imagem e introduza o Nome de **Imagem** que é construído no passo anterior.

1. Depois de configurar os passos de construção e impulso para cada uma das cinco imagens, adicione mais dois passos no fluxo de trabalho de construção.

    a. Uma tarefa de linha de comando que usa um script de bash para substituir a ocorrência *BuildNumber* no ficheiro docker-compose.yml com o id de construção atual. Consulte o seguinte ecrã para obter mais detalhes.

    ![Serviços Azure DevOps - Ficheiro Compor Update](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Uma tarefa que deixa cair o ficheiro compor atualizado como um artefacto de construção para que possa ser usado na libertação. Consulte o seguinte ecrã para obter mais detalhes.

    ![Serviços Azure DevOps - Publicar ficheiro Compor](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Clique em **Guardar** e nomear o seu oleoduto de construção.

## <a name="step-3-create-the-release-pipeline"></a>Passo 3: Criar o gasoduto de libertação

A Azure DevOps Services permite-lhe [gerir lançamentos em ambientes](https://www.visualstudio.com/team-services/release-management/). Pode ativar a implantação contínua para se certificar de que a sua aplicação é implantada nos seus diferentes ambientes (como dev, teste, pré-produção e produção) de forma suave. Você pode criar um novo ambiente que represente o seu cluster De Serviço de Contentores Azure Docker Swarm.

![Serviços Azure DevOps - Lançamento para ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Configuração inicial de lançamento

1. Para criar um pipeline de libertação, clique em **Lançamentos** > **+ Lançamento**

1. Para configurar a fonte do artefacto, clique em **Artefactos** > **Link uma fonte**de artefacto . Aqui, ligue este novo oleoduto de lançamento à construção que definiu no passo anterior. Ao fazê-lo, o ficheiro docker-compose.yml está disponível no processo de libertação.

    ![Serviços Azure DevOps - Artefactos de Libertação](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Para configurar o gatilho de desbloqueio, clique em **Gatilhos** e selecione **Implantação Contínua**. Acione o gatilho na mesma fonte de artefacto. Esta definição garante que um novo lançamento comece assim que a construção se concretize com sucesso.

    ![Serviços Azure DevOps - Triggers de lançamento](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Defina o fluxo de trabalho de libertação

O fluxo de trabalho de libertação é composto por duas tarefas que se adicionam.

1. Configure uma tarefa para copiar de forma segura o ficheiro de composição para uma pasta de *implantação* no nó principal do Docker Swarm, utilizando a ligação SSH configurada anteriormente. Consulte o seguinte ecrã para obter mais detalhes.

    ![Serviços Azure DevOps - Lançamento SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Configure uma segunda tarefa para executar `docker` `docker-compose` um comando de batida para executar e comande no nó principal. Consulte o seguinte ecrã para obter mais detalhes.

    ![Serviços Azure DevOps - Release Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    O comando executado no mestre usa o Docker CLI e o Docker-Compose CLI para fazer as seguintes tarefas:

   - Login no registo de contentores Azure (utiliza três variações de construção que são definidas no separador **Variáveis)**
   - Defina a variável **DOCKER_HOST** para trabalhar com o ponto final do Enxame (:2375)
   - Navegue para a pasta de *implantação* que foi criada pela tarefa de cópia segura anterior e que contém o ficheiro docker-compose.yml 
   - Executar `docker-compose` comandos que puxem as novas imagens, parar os serviços, remover os serviços e criar os recipientes.

     >[!IMPORTANT]
     > Como mostrado no ecrã anterior, deixe a caixa de verificação Fail na Caixa de Verificação **STDERR** sem ser controlada. Esta é uma definição importante, porque `docker-compose` imprime várias mensagens de diagnóstico, como os contentores estão a parar ou a ser eliminadas, na saída padrão de erros. Se verificar a caixa de verificação, a Azure DevOps Services informa que ocorreram erros durante o lançamento, mesmo que tudo corra bem.
     >
1. Guarde este novo oleoduto de lançamento.


>[!NOTE]
>Esta implantação inclui algum tempo de inatividade porque estamos a parar os antigos serviços e a gerir o novo. É possível evitá-lo fazendo uma implantação azul-verde.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Passo 4. Testar o gasoduto CI/CD

Agora que terminou a configuração, é hora de testar este novo oleoduto CI/CD. A forma mais fácil de o testar é atualizar o código fonte e comprometer as alterações no seu repositório GitHub. Alguns segundos depois de premir o código, verá uma nova construção a funcionar nos Serviços Azure DevOps. Uma vez concluída com sucesso, será desencadeada uma nova versão e implementará a nova versão da aplicação no cluster do Serviço de Contentores Azure.

## <a name="next-steps"></a>Passos Seguintes

* Para mais informações sobre ci/CD com os Serviços Azure DevOps, consulte o artigo de Documentação de [Pipelines Azure.](/azure/devops/pipelines/?view=azure-devops)
