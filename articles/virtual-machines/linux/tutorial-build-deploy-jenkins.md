---
title: Tutorial – CI/CD de Jenkins para VMs do Azure com os Serviços de DevOps do Azure | Microsoft Docs
description: Neste tutorial, vai aprender a configurar a integração contínua (CI) e a implementação contínua (CD) de uma aplicação Node.js com o Jenkins para VMs do Azure da Gestão de Versões no Visual Studio Team Services ou no Microsoft Team Foundation Server
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: 7cd7b8f7b49915db9fcf17602429e47c1b9da95d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901428"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Tutorial: Implementar a sua aplicação para máquinas virtuais do Linux no Azure com o com o Jenkins e serviços do Azure DevOps

A integração contínua (CI) e a implementação contínua (CD) formam um pipeline através do qual pode criar, lançar e implementar o seu código. Os Serviços de DevOps do Azure fornecem um conjunto completo com todas a funcionalidades das ferramentas de automatização de CI/CD para implementação no Azure. Jenkins é uma ferramenta popular de terceiros baseada no servidor de CI/CD que também fornece a automatização de CI/CD. Pode utilizar os Serviços de DevOps do Azure e o Jenkins em conjunto para personalizar a forma como fornece o serviço ou a aplicação na cloud.

Neste tutorial, vai utilizar o Jenkins para criar uma aplicação Web Node.js. Em seguida, utilize o Azure DevOps para implementá-la

num [grupo de implementação](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) que contém máquinas virtuais do Linux (VMs). Saiba como:

> [!div class="checklist"]
> * Obter a aplicação de exemplo.
> * Configurar os plug-ins Jenkins.
> * Configurar um projeto Jenkins Freestyle para Node.js.
> * Configurar o Jenkins para integração nos Serviços de DevOps do Azure.
> * Criar um ponto final de serviço Jenkins.
> * Criar um grupo de implementação para as máquinas virtuais do Azure.
> * Crie um pipeline de lançamento de Pipelines do Azure.
> * Executar implementações manuais e acionadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Precisa de acesso a um servidor Jenkins. Se ainda não criou um servidor Jenkins, veja [Criar um Jenkins mestre numa máquina virtual do Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Inicie sessão na sua organização de Serviços de DevOps do Azure (**https://{yourorganization}.visualstudio.com**). 
  Pode obter uma [organização de Serviços de DevOps do Azure gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obter mais informações, veja [Ligar aos Serviços de DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Precisa de uma máquina virtual do Linux para um destino de implementação.  Para obter mais informações, veja [Criar e gerir VMs do Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para a máquina virtual. Para obter mais informações, veja [Criar grupos de segurança de rede com o portal do Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Obter a aplicação de exemplo

Precisa de uma aplicação para a implementação, armazenada num repositório de Git.
Para este tutorial, recomendamos que utilize [esta aplicação de exemplo disponível no GitHub](https://github.com/azooinmyluggage/fabrikam-node). Este tutorial contém um script de exemplo que serve para instalar o Node.js e uma aplicação. Se pretender trabalhar com o seu próprio repositório, deve configurar um exemplo semelhante.

Crie uma bifurcação desta aplicação e tome nota da localização (URL) para utilizar em passos posteriores deste tutorial. Para obter mais informações, veja [Fork a repo](https://help.github.com/articles/fork-a-repo/) (Bifurcar um repositório).    

> [!NOTE]
> A aplicação foi criada através do [Yeoman](https://yeoman.io/learning/index.html) e utiliza o Express, o bower e o grunt. Possui também alguns pacotes npm como dependências.
> O exemplo contém também um script que configura o Nginx e implementa a aplicação, que é executada nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, o Nginx e o PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia a aplicação Node.

## <a name="configure-jenkins-plug-ins"></a>Configurar os plug-ins Jenkins

Em primeiro lugar, tem de configurar dois plug-ins Jenkins: **NodeJS** e **VS Team dos serviços de implementação contínua**.

1. Abra a sua conta do Jenkins e selecione **Gerir Jenkins**.
2. Na página **Gerir Jenkins**, selecione **Gerir Plug-ins**.
3. Filtre a lista para localizar o plug-in **NodeJS** e selecione a opção **Instalar sem reiniciar**.
    ![Adicionar o plug-in NodeJS ao Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtre a lista para localizar o plug-in **Implementação Contínua do VS Team Services** e selecione a opção **Instalar sem reiniciar**.
5. Aceda ao dashboard do Jenkins e selecione **Gerir Jenkins**.
6. Selecione **Configuração da Ferramenta Global**. Localize o **NodeJS** e selecione **Instalações do NodeJS**.
7. Selecione a opção **Instalar automaticamente** e, em seguida, introduza um valor de **Nome**.
8. Selecione **Guardar**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Configurar um projeto Jenkins Freestyle para Node.js

1. Selecione **Novo Item**. Introduza um nome de item.
2. Selecione **Projeto Freestyle**. Selecione **OK**.
3. No separador **Gestão do Código Fonte**, selecione **Git** e introduza os detalhes do repositório e o ramo que contém o código da aplicação.    
    ![Adicionar um repositório à compilação](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. No separador **Acionadores da Compilação**, selecione **Consultar SCM** e introduza a agenda `H/03 * * * *` para consultar o repositório de Git a cada três minutos para ver se sofreu alterações. 
5. No separador **Ambiente da Compilação**, selecione **Fornecer CAMINHO da posição armázem/pasta de Node &amp; npm** e selecione o valor **Instalação do NodeJS**. Defina o **ficheiro npmrc** para **utilizar a predefinição do sistema**.
6. No separador **Compilação**, selecione **Executar shell** e introduza o comando `npm install` para confirmar que todas as dependências são atualizadas.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Configurar o Jenkins para integração nos Serviços de DevOps do Azure

> [!NOTE]
> Verifique se o token de acesso pessoal (PAT) que utiliza para os seguintes passos contém a permissão *Versão* (ler, escrever, executar e gerir) nos Serviços de DevOps do Azure.
 
1.  Crie um PAT na sua organização de Serviços de DevOps do Azure, se ainda não tiver um. O Jenkins precisa destas informações para aceder à sua organização de Serviços de DevOps do Azure. Confirme que armazena as informações do token para os próximos passos nesta secção.
  
    Para saber como gerar um token, leia [How do I create a personal access token for Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts) (Como criar um token de acesso pessoal para os Azure DevOps Services?).
2. No separador **Ações Pós-compilação**, selecione **Adicionar ação pós-compilação**. Selecione **Arquivar os artefactos**.
3. Em **Ficheiros a arquivar**, introduza `**/*` para incluir todos os ficheiros.
4. Para criar outra ação, selecione **Adicionar ação pós-compilação**.
5. Selecione **Acionar versão no TFS/Team Services**. Introduza o URI para a sua organização de Serviços de DevOps do Azure, tal como **https://{your-organization-name}.visualstudio.com**.
6. Introduza o nome do **Projeto**.
7. Escolha um nome para o pipeline de versão. (Vai criar este pipeline de versão mais tarde nos Serviços de DevOps do Azure.)
8. Escolha as credenciais para ligar ao seu ambiente dos Serviços de DevOps do Azure ou do Team Foundation Server:
   - Deixe o **Nome de utilizador** em branco se estiver a utilizar os Serviços de DevOps do Azure. 
   - Introduza um nome de utilizador e uma palavra-passe, se estiver com uma versão no local do Team Foundation Server.    
   ![Configurar ações pós-compilação do Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Guarde o projeto Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Criar um ponto final de serviço Jenkins

Um ponto final de serviço permite aos Serviços de DevOps do Azure ligar ao Jenkins.

1. Abra a página **Serviços** nos Serviços de DevOps do Azure, abra a lista **Novo Ponto Final de Serviço** e selecione **Jenkins**.
   ![Adicionar um ponto final Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Introduza um nome para a ligação.
3. Introduza o URL do servidor Jenkins e selecione a opção **Aceitar certificados SSL não fidedignos**. Um exemplo de URL é **http://{OseuURLJenkins}.westcentralus.cloudapp.azure.com**.
4. Introduza o nome de utilizador e a palavra-passe da sua conta Jenkins.
5. Selecione **Verificar ligação** para verificar se as informações estão corretas.
6. Selecione **OK** para criar o ponto final de serviço.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Criar um grupo de implementação para as máquinas virtuais do Azure

Precisa de um [grupo de implementação](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) para registar o agente dos Serviços de DevOps do Azure para que o pipeline de versão possa ser implementado na máquina virtual. Os grupos de implementação facilitam a definição de grupos de computadores de destino lógicos para a implementação e para instalar o agente necessário em cada computador.

   > [!NOTE]
   > No procedimento seguinte, instale os pré-requisitos e *não execute o script com privilégios sudo*.

1. Abra o separador **Versões** do hub **Compilação &amp; Versão**, abra os **Grupos de implementação** e selecione **+ Novo**.
2. Introduza um nome para o grupo de implementação e uma descrição opcional. Em seguida, selecione **Criar**.
3. Escolha o sistema operativo da máquina virtual de destino da implementação. Por exemplo, selecione **Ubuntu 16.04 +**.
4. Selecione **Utilizar um token de acesso pessoal no script para autenticação**.
5. Selecione a ligação **Pré-requisitos do sistema**. Instale os pré-requisitos do sistema operativo.
6. Selecione **Copiar script para área de transferência** para copiar o script.
7. Inicie sessão na máquina virtual de destino da implementação e execute o script. Não execute o script com privilégios sudo.
8. Após a instalação, são-lhe solicitadas etiquetas do grupo de implementação. Aceite as predefinições.
9. Nos Serviços de DevOps do Azure, procure a máquina virtual recém-registada em **Destinos**, em **Grupos de Implementação**.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Criar um pipeline de lançamento de Pipelines do Azure

Um pipeline de versão especifica o processo que os Azure Pipelines utilizam para implementar a aplicação. Neste exemplo, executa um script de shell.

Para criar o pipeline de versão no Azure Pipelines:

1. Abra o separador **Versões** do hub **Compilação &amp; Versão** e selecione **Criar pipeline de versão**. 
2. Selecione o modelo **Vazio** ao optar por começar com um **Processo vazio**.
3. Na secção **Artefactos**, selecione **+ Adicionar Artefactos** e escolha **Jenkins** como **Tipo de origem**. Selecione a ligação de ponto final de serviço Jenkins. Em seguida, selecione a tarefa de origem Jenkins e selecione **Adicionar**.
4. Selecione as reticências junto a **Ambiente 1**. Selecione **Adicionar fase do grupo de implementação**.
5. Escolha o grupo de implementação.
5. Selecione **+** para adicionar uma tarefa à **Fase do grupo de implementação**.
6. Selecione a tarefa **Script de Shell** e, em seguida, **Adicionar**. A tarefa **Script de Shell** fornece a configuração para que um script seja executado em cada servidor para instalar o Node.js e iniciar a aplicação.
8. Em **Caminho do Script**, introduza **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Selecione **Avançado** e, em seguida, ative **Especificar Diretório de Trabalho**.
10. Em **Diretório de Trabalho**, introduza **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Edite o nome do pipeline de versão para o nome que especificou no separador **Ações Pós-compilação** da compilação no Jenkins. O Jenkins precisa que este nome tenha a capacidade de acionar uma nova versão quando os artefactos de origem são adaptados.
12. Selecione **Guardar** e **OK** para guardar o pipeline de versão.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Executar implementações manuais e acionadas por CI

1. Selecione **+ Versão** e **Criar Versão**.
2. Selecione a compilação que concluiu na lista pendente realçada e selecione **Fila**.
3. Escolha a ligação da versão na mensagem pop-up. Por exemplo: "Lançamento **Release 1** foi criada."
4. Abra o separador **Registos** para ver o resultado da consola de versão.
5. No browser, abra o URL de um dos servidores que adicionou ao grupo de implementação. Por exemplo, introduza **http://{o-seu-endereço-ip-do-servidor}**.
6. Aceda ao repositório de origem do Git e modifique os conteúdos do cabeçalho **h1** no ficheiro app/views/index.jade com algum texto alterado.
7. Consolide as alterações.
8. Após alguns minutos, verá uma nova versão criada na página **Versões** do Azure DevOps. Abra a versão para ver a implementação a decorrer. Parabéns!

## <a name="troubleshooting-the-jenkins-plugin"></a>Resolver problemas nos plug-ins do Jenkins

Se se deparar com erros nos plug-ins do Jenkins, comunique os problemas com os componentes específicos no [Jenkins JIRA](https://issues.jenkins-ci.org/).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, automatizou a implementação de uma aplicação no Azure com o Jenkins para a compilação e com os Serviços de DevOps do Azure para a versão. Aprendeu a:

> [!div class="checklist"]
> * Criar a aplicação no Jenkins.
> * Configurar o Jenkins para integração nos Serviços de DevOps do Azure.
> * Criar um grupo de implementação para as máquinas virtuais do Azure.
> * Criar um pipeline de versão para configurar as VMs e implementar a aplicação.

Para saber mais sobre como implementar uma pilha LAMP (Linux, Apache, MySQL e PHP), avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Implementar pilha LAMP](tutorial-lamp-stack.md)
