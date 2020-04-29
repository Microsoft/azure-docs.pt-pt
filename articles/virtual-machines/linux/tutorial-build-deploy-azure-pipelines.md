---
title: Tutorial - CI/CD para VMs Azure usando pipelines Azure
description: Neste tutorial, aprende-se a configurar a integração contínua (CI) e a implantação contínua (CD) de uma aplicação Node.js para VMs Azure utilizando o pipeline Azure baseado em YAML.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: bb7c773d02c5da5c115af79cd9e90c78e71eb6bf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76988333"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Tutorial: Implemente a sua aplicação para máquinas virtuais Linux em Azure utilizando os Serviços Azure DevOps e pipelines Azure

A integração contínua (CI) e a implantação contínua (CD) formam um pipeline pelo qual pode construir, libertar e implementar o seu código após cada compromisso de código. Este documento contém os passos associados à criação de um gasoduto CI/CD para a realização de implantações multi-máquinas utilizando gasodutos Azure.

A Azure Pipelines fornece um conjunto completo e completo de ferramentas de automatização CI/CD para implementações em máquinas virtuais, tanto em prem como em qualquer nuvem.

Neste tutorial, irá configurar um pipeline CI/CD baseado em YAML para implantar a sua aplicação num Azure Pipelines [Environment](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) com máquinas Virtuais Linux como recursos, cada um dos quais serve de servidorweb para executar a aplicação.

Saiba como:

> [!div class="checklist"]
> * Arranja uma aplicação de amostras.
> * Crie um pipeline Ci azure baseado em YAML para a construção da aplicação de amostras.
> * Criar um Ambiente de Oleodutos Azure para as máquinas virtuais Azure
> * Crie um pipeline CD azure Pipelines.
> * Executar implementações manuais e acionadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Inscreva-se na sua organização Azure**https://dev.azure.com/** DevOps Services ( ). 
  Pode obter uma [organização de Serviços de DevOps do Azure gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obter mais informações, veja [Ligar aos Serviços de DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Precisa de uma máquina virtual do Linux para um destino de implementação.  Para obter mais informações, veja [Criar e gerir VMs do Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para a máquina virtual. Para obter mais informações, veja [Criar grupos de segurança de rede com o portal do Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>Obtenha o seu código de aplicação de amostra

Se já tem uma aplicação no GitHub que pretende implementar, pode tentar criar um pipeline para esse código.

No entanto, se for um novo utilizador, poderá começar melhor usando o nosso código de amostra. Nesse caso, bifurque este repo no GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic é uma aplicação [Java Spring Boot](https://spring.io/guides/gs/spring-boot) construída com [maven.](https://spring.io/guides/gs/maven/)

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Esta aplicação Node.js foi construída através de [Yeoman.](https://yeoman.io/learning/index.html) utiliza o Express, o bower e o grunt. Possui também alguns pacotes npm como dependências.
> O exemplo contém também um script que configura o Nginx e implementa a aplicação, que é executada nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, o Nginx e o PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia a aplicação Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Pré-requisitos para o Linux VM

As aplicações de amostra sustificadas acima foram testadas em Ubuntu 16.04, e recomendamos que use a mesma versão do Linux VM para este arranque rápido.
Siga os passos adicionais descritos abaixo com base na pilha de tempo de execução utilizada para a aplicação.

#### <a name="java"></a>[Java](#tab/java)

- Para implementar aplicações baseadas em Java Spring Boot e Spring Cloud, crie um VM Linux em Azure usando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) modelo, que fornece um tempo de execução totalmente suportado pelo OpenJDK.
- Para implementar servletes Java no servidor Tomcat, crie um VM Linux com Java 8 utilizando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) modelo Azure e [configure o Tomcat 9.x como um serviço](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Para implementar aplicação baseada em Java EE, utilize um modelo Azure para criar um [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) ou um [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) ou um [Linux VM +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Para instalar uma aplicação javascript ou uma aplicação Node.js, você precisará de um Linux VM com servidor web Nginx para implementar a aplicação.
Se ainda não tem um VM Linux com Nginx, crie um agora em Azure usando os passos [neste exemplo](/azure/virtual-machines/linux/quick-create-cli).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Criar um ambiente de Pipelines Azure com máquinas virtuais Azure

As máquinas virtuais podem ser adicionadas como recursos dentro de [ambientes](https://docs.microsoft.com/azure/devops/pipelines/process/environments) e podem ser direcionadas para implementações multi-máquinas. As vistas de história de implantação dentro do ambiente fornecem rastreabilidade da VM para o oleoduto e, em seguida, para o compromisso.

Você pode criar um ambiente no centro "**Ambientes**" dentro da secção "**Pipelines**".
1.  Inscreva-se na sua organização Azure DevOps e navegue para o seu projeto.
2.  No seu projeto, navegue para a página **Pipelines.** Em seguida, escolha **Ambientes** e clique em **Criar Ambiente**. Especifique um **nome** (obrigatório) para o ambiente e uma **descrição**.
3.  Escolha **máquinas virtuais** como **recurso** a adicionar ao ambiente e clique **em Seguinte**.
4.  Escolha o Sistema Operativo (Windows/Linux) e **copie**o script de registo PS . 
5.  Agora execute o script copiado de um pedido de comando do administrador PowerShell em cada um dos VMs-alvo a registar com este Ambiente.
    > [!NOTE]
    > - Acesso pessoal Token do utilizador registado é pré-inserido no script que expira no mesmo dia tornando o script copiado inutilizável nele.
    > - Se o seu VM já tiver algum agente a funcionar nele, forneça um nome único para "agente" registar-se com o ambiente.
6.  Uma vez registado o VM, começará a aparecer como um recurso ambiental sob separador de "recursos" do ambiente.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Para adicionar mais VMs, pode ver e copiar novamente o script clicando em "Adicionar recurso" e escolhendo "Máquinas Virtuais" como recurso. Este guião permaneceria o mesmo para que todos os VMs fossem adicionados a este ambiente. 
8.  Cada máquina interage com os Pipelines Azure para coordenar a implementação da sua aplicação.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Pode adicionar etiquetas ao VM como parte do script interativo de registo PS (ou) pode também adicionar/remover o mesmo da vista de recursos clicando nos pontos triplos no final de cada recurso VM na vista de recursos.

   As etiquetas que atribui permitem limitar a implantação a máquinas virtuais específicas quando o ambiente é utilizado num trabalho de Implantação. As etiquetas são limitadas a 256 caracteres, mas não há limite para o número de etiquetas que pode utilizar.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Defina o seu pipeline de construção CI

Você precisará de um pipeline de construção de integração contínua (CI) que publique a sua aplicação web, bem como um script de implementação que pode ser executado localmente no servidor Ubuntu. Instale um pipeline de construção ci com base no tempo de execução que pretende utilizar. 

1. Inscreva-se na sua organização Azure DevOps e navegue para o seu projeto.

1. No seu projeto, navegue para a página **Pipelines.** Em seguida, escolha a ação para criar um novo oleoduto.

1. Caminhe pelos degraus do assistente selecionando primeiro o **GitHub** como a localização do seu código fonte.

1. Pode ser redirecionado para o GitHub para assinar. Em caso afirmativo, introduza as suas credenciais GitHub.

1. Quando aparecer a lista de repositórios, selecione o repositório de aplicações de amostra pretendido.

1. Os Gasodutos Azure analisarão o seu repositório e recomendarão um modelo de gasoduto adequado.

#### <a name="java"></a>[Java](#tab/java)

Selecione o modelo de **arranque** e copie o corte yAML abaixo que constrói o seu projeto Java e executa testes com Apache Maven:

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Para obter mais orientação, siga os passos mencionados na [Build your Java app com a Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Selecione o modelo de **arranque** e copie o corte yAML abaixo que constrói um projeto geral node.js com npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Para obter mais orientação, siga os passos em Construir a [sua app Node.js com gole](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript).

- Dê uma olhada no oleoduto para ver o que faz. Certifique-se de que todas as inputs predefinidas são adequadas para o seu código.

- Selecione **Guardar e executar,** em seguida, selecione **Comprometa diretamente ao ramo principal**e, em seguida, escolha Guardar e **executar** novamente.

- Uma nova corrida está iniciada. Espere a corrida para terminar.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Defina os passos de CD para implantar no Linux VM

1. Editar o oleoduto acima e incluir um trabalho de [implantação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) fazendo referência ao ambiente e aos recursos VM que já usou a sintaxe YAML abaixo:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. Pode selecionar conjuntos específicos de máquinas virtuais do ambiente para receber a implementação especificando as **etiquetas** que definiu para cada máquina virtual no ambiente.
[Aqui](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) está o esquema completo da YAML para o trabalho de implantação.

3. Pode especificar `runOnce` eithor `rolling` ou como estratégia de implantação. 

   `runOnce`é a estratégia de implantação mais simples em `preDeploy` `deploy`que `routeTraffic`todos `postRouteTraffic`os ganchos de ciclo de vida, nomeadamente, e , são executados uma vez. Então, `on:` `success` `on:` `failure` ou é executado.

   Abaixo está o exemplo de corte `runOnce` YAML para:
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Abaixo está um exemplo do corte YAML que você pode usar para definir uma estratégia de rolling para atualizações de máquinas virtuais até 5 alvos em cada iteração. `maxParallel`determinará o número de alvos que podem ser implantados para, paralelamente. A seleção é responsável pelo número absoluto ou percentagem de metas que devem permanecer disponíveis a qualquer momento, excluindo os objetivos que estão a ser aplicados. Também é usado para determinar as condições de sucesso e falha durante a implantação.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   A cada execução deste trabalho, o `<environment name>` histórico de implantação é registado contra o ambiente que criou e registou os VMs.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Corra o seu oleoduto e obtenha vistas de rastreabilidade no ambiente
A visão de implantação do ambiente proporciona uma rastreabilidade completa de compromissos e itens de trabalho, e um histórico de implantação transversal por ambiente/recurso.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Passos seguintes
- Pode continuar a [personalizar o oleoduto](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) que acabou de criar.
- Para saber o que mais pode fazer nos oleodutos YAML, consulte a referência do [esquema YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema).
- Para saber mais sobre como implementar uma pilha LAMP (Linux, Apache, MySQL e PHP), avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Implementar pilha LAMP](tutorial-lamp-stack.md)
