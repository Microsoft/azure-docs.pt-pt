---
title: Tutorial - CI/CD para VMs Azure usando Gasodutos Azure
description: Neste tutorial, aprende-se a configurar a integração contínua (CI) e a implementação contínua (CD) de uma aplicação Node.js para VMs Azure utilizando o gasoduto Azure baseado em YAML.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552495"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Tutorial: Implemente a sua aplicação para máquinas virtuais Linux em Azure utilizando serviços Azure DevOps e Pipelines Azure

A integração contínua (CI) e a implantação contínua (CD) formam um oleoduto através do qual pode construir, soltar e implementar o seu código após cada código se comprometer. Este documento contém os passos associados à criação de um gasoduto CI/CD para a realização de implementações multi-máquinas utilizando gasodutos Azure.

A Azure Pipelines fornece um conjunto completo e totalmente apresentado de ferramentas de automação CI/CD para implementações em máquinas Virtuais, tanto no pré-m como em qualquer nuvem.

Neste tutorial, irá configurar um pipeline CI/CD baseado em YAML para implementar a sua app num [Azure](/azure/devops/pipelines/process/environments) Pipelines Environment com máquinas Virtual Linux como recursos, cada um dos quais serve como servidores web para executar a aplicação.

Saiba como:

> [!div class="checklist"]
> * Arranja um aplicativo de amostra.
> * Crie um pipeline CI Azure Pipelines baseado em YAML para a construção da aplicação de amostra.
> * Criar um ambiente de gasodutos Azure para as máquinas virtuais Azure
> * Crie um pipeline de CD Azure Pipelines.
> * Executar implementações manuais e acionadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Inscreva-se na sua organização Azure DevOps Services **https://dev.azure.com/** (). 
  Pode obter uma [organização de Serviços de DevOps do Azure gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obter mais informações, veja [Ligar aos Serviços de DevOps do Azure](/azure/devops/organizations/projects/connect-to-projects).

*  Precisa de uma máquina virtual do Linux para um destino de implementação.  Para obter mais informações, veja [Criar e gerir VMs do Linux com a CLI do Azure](./tutorial-manage-vm.md).

*  Abra a porta de entrada 80 para a máquina virtual. Para obter mais informações, veja [Criar grupos de segurança de rede com o portal do Azure](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Obtenha o seu código de aplicação de amostra

Se já tem uma aplicação no GitHub que pretende implementar, pode tentar criar um pipeline para esse código.

No entanto, se for um novo utilizador, poderá começar melhor usando o nosso código de amostra. Nesse caso, forque este repo no GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic é uma aplicação [Java Spring Boot](https://spring.io/guides/gs/spring-boot) construída com recurso a [Maven.](https://spring.io/guides/gs/maven/)

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Esta Node.js aplicação foi construída através do [Yeoman.](https://yeoman.io/learning/index.html) utiliza o Express, o bower e o grunt. Possui também alguns pacotes npm como dependências.
> O exemplo contém também um script que configura o Nginx e implementa a aplicação, que é executada nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, o Nginx e o PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia a aplicação Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Pré-requisitos para o Linux VM

As aplicações de amostra acima mencionadas foram testadas em Ubuntu 16.04, e recomendamos que utilize a mesma versão do Linux VM para este arranque rápido.
Siga os passos adicionais descritos abaixo com base na pilha de tempo de execução utilizada para a aplicação.

#### <a name="java"></a>[Java](#tab/java)

- Para implementar aplicações baseadas em Java Spring Boot e Spring Cloud, crie um Linux VM em Azure utilizando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) modelo, que fornece um tempo de execução totalmente suportado com base em OpenJDK.
- Para implementar servlets Java no servidor Tomcat, crie um VM Linux com Java 8 utilizando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) modelo Azure e [configuure Tomcat 9.x como um serviço](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Para implementar uma aplicação baseada em Java EE, utilize um modelo Azure para criar um [Linux VM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) ou um [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) ou um [Linux VM +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Para instalar uma aplicação JavaScript ou uma aplicação Node.js, precisará de um VM Linux com servidor web Nginx para implementar a aplicação.
Se ainda não tem um Linux VM com Nginx, crie um agora em Azure usando os passos [neste exemplo.](./quick-create-cli.md)

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Criar um ambiente Azure Pipelines com máquinas virtuais Azure

As máquinas virtuais podem ser adicionadas como recursos dentro de ambientes e podem ser [direcionadas](/azure/devops/pipelines/process/environments) para implementações multi-máquinas. As vistas de histórico de implantação dentro do ambiente proporcionam rastreabilidade de VM para o oleoduto e, em seguida, para o compromisso.

Pode criar um ambiente no centro "**Ambientes**" dentro da secção "**Pipelines**".
1.  Inscreva-se na sua organização Azure DevOps e navegue para o seu projeto.
2.  No seu projeto, navegue na página **Pipelines.** Em seguida, escolha **Ambientes** e clique em **Criar Ambiente.** Especifique um **nome** (obrigatório) para o ambiente e uma **descrição**.
3.  Escolha **máquinas virtuais** como um **recurso** a adicionar ao ambiente e clique em **Seguinte**.
4.  Escolha o Sistema Operativo (Windows/Linux) e **copie o script de registo PS**. 
5.  Agora execute o script copiado a partir de um aviso de comando PowerShell de administrador em cada um dos VMs alvo a ser registado com este Ambiente.
    > [!NOTE]
    > - O Token de Acesso Pessoal do utilizador registado é pré-inserido no script que expira no mesmo dia, tornando o script copiado inutilizável.
    > - Se o seu VM já tiver algum agente a funcionar nele, forneça um nome único para "agente" registar-se com ambiente.
6.  Uma vez registado o VM, começará a aparecer como um recurso ambiental sob o separador "recursos" do ambiente.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Para adicionar mais VMs, pode visualizar e copiar novamente o script clicando em "Adicionar recurso" e escolhendo "Máquinas Virtuais" como recurso. Este script permaneceria o mesmo para que todos os VMs fossem adicionados a este ambiente. 
8.  Cada máquina interage com a Azure Pipelines para coordenar a implementação da sua aplicação.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Pode adicionar tags ao VM como parte do script interativo de registo PowerShell (ou) também pode adicionar/remover o mesmo da vista de recursos clicando nos pontos triplos no final de cada recurso VM na vista de recursos.

   As tags que atribui permitem limitar a implantação a máquinas virtuais específicas quando o ambiente é utilizado numa função de Implantação. As etiquetas são limitadas a 256 caracteres, mas não há limite para o número de tags que pode usar.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Defina o seu oleoduto de construção de CI

Você precisará de um pipeline de integração contínua (CI) que publique a sua aplicação web, bem como um script de implementação que pode ser executado localmente no servidor Ubuntu. Configurar um pipeline de construção de CI com base no tempo de funcionação que pretende utilizar. 

1. Inscreva-se na sua organização Azure DevOps e navegue para o seu projeto.

1. No seu projeto, navegue na página **Pipelines.** Em seguida, escolha a ação para criar um novo oleoduto.

1. Caminhe pelos degraus do assistente selecionando primeiro o **GitHub** como a localização do seu código fonte.

1. Pode ser redirecionado para o GitHub para se inscrever. Em caso afirmativo, insira as suas credenciais GitHub.

1. Quando aparecer a lista de repositórios, selecione o repositório de aplicações de amostra pretendido.

1. Os Gasodutos Azure analisarão o seu repositório e recomendarão um modelo de gasoduto adequado.

#### <a name="java"></a>[Java](#tab/java)

Selecione o modelo **de arranque** e copie o snippet abaixo do YAML que constrói o seu projeto Java e executa testes com Apache Maven:

```YAML
jobs:
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

Para obter mais orientação, siga os passos mencionados na [Build your Java app with Maven.](/azure/devops/pipelines/ecosystems/java)

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Selecione o modelo **de arranque** e copie o snippet yaML abaixo que constrói um projeto de Node.js geral com npm.

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

Para obter mais orientação, siga os passos na [Build your Node.js app com golep](/azure/devops/pipelines/ecosystems/javascript).

- Dê uma olhada no oleoduto para ver o que faz. Certifique-se de que todas as entradas predefinidos são apropriadas para o seu código.

- **Selecione Guardar e executar,** em seguida, selecione **Comprometa-se diretamente no ramo principal** e, em seguida, escolha Guardar e **executar** novamente.

- Uma nova corrida começou. Aguarde a conclusão da execução.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definir passos de CD para implementar no Linux VM

1. Altere o ficheiro YAML para o pipeline acima indicado para incluir um [trabalho de implantação,](/azure/devops/pipelines/process/deployment-jobs) referindo-se ao ambiente e aos recursos VM que tem anteriormente utilizando a sintaxe YAML abaixo:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Pode selecionar conjuntos específicos de máquinas virtuais do ambiente para receber a implementação especificando as **etiquetas** que definiu para cada máquina virtual no ambiente.
[Aqui](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) está o esquema YAML completo para o trabalho de implantação.

3. Pode especificar uma `runOnce` ou como estratégia de `rolling` implantação. 

   `runOnce` é a estratégia de implantação mais simples em que todos os ganchos do ciclo de vida, nomeadamente `preDeploy` `deploy` , e , são `routeTraffic` `postRouteTraffic` executados uma vez. Então, `on:` `success` ou é `on:` `failure` executado.

   Abaixo está o exemplo YAML snippet `runOnce` para:
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

4. Abaixo está um exemplo do snippet YAML que pode usar para definir uma estratégia de rolamento para máquinas virtuais atualiza até 5 alvos em cada iteração. `maxParallel` determinará o número de alvos que podem ser implantados, paralelamente. A seleção representa um número absoluto ou uma percentagem de metas que devem permanecer disponíveis a qualquer momento, excluindo os objetivos a que estão a ser implementados. Também é usado para determinar as condições de sucesso e insucesso durante a implantação.

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

   A cada execução deste trabalho, o histórico de implantação é registado contra o `<environment name>` ambiente que criou e registou os VMs.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Faça o seu oleoduto e obtenha vistas de rastreabilidade em ambiente
A visão de implantação do ambiente proporciona uma rastreabilidade completa de compromissos e artigos de trabalho, e um histórico de implantação de gasodutos cruzados por ambiente/recurso.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Passos seguintes
- Pode proceder à [personalização do oleoduto](/azure/devops/pipelines/customize-pipeline) que acabou de criar.
- Para saber o que mais pode fazer nos oleodutos YAML, consulte [a referência de esquema YAML](/azure/devops/pipelines/yaml-schema).
- Para saber mais sobre como implementar uma pilha LAMP (Linux, Apache, MySQL e PHP), avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Implementar pilha LAMP](tutorial-lamp-stack.md)
