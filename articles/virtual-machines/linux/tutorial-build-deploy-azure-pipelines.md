---
title: Tutorial – CI/CD para VMs do Azure usando Azure Pipelines
description: Neste tutorial, você aprende a configurar a integração contínua (CI) e a implantação contínua (CD) de um aplicativo node. js para VMs do Azure usando o pipeline do Azure baseado em YAML.
author: ushan
manager: jpconnock
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: c9d8ec2ce78746352b1fc5d2f337ad8686213839
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75662368"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Tutorial: implantar seu aplicativo em máquinas virtuais do Linux no Azure usando Azure DevOps Services e Azure Pipelines

A CI (integração contínua) e o CD (implantação contínua) formam um pipeline pelo qual você pode compilar, liberar e implantar seu código depois de cada confirmação de código. Este documento contém as etapas associadas à configuração de um pipeline de CI/CD para fazer implantações de vários computadores usando o Azure Pipelines.

O Azure Pipelines fornece um conjunto completo de ferramentas de automação de CI/CD para implantações em máquinas virtuais, tanto local quanto em qualquer nuvem.

Neste tutorial, você configurará um pipeline de CI/CD baseado em YAML para implantar seu aplicativo em um [ambiente](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) de Azure pipelines com máquinas virtuais do Linux como recursos, cada um deles servirá como servidores Web para executar o aplicativo.

Saiba como:

> [!div class="checklist"]
> * Obtenha um aplicativo de exemplo.
> * Crie um pipeline de CI Azure Pipelines baseado em YAML para criar o aplicativo de exemplo.
> * Criar um ambiente de Azure Pipelines para as máquinas virtuais do Azure
> * Crie um pipeline de CD Azure Pipelines.
> * Executar implementações manuais e acionadas por CI.

## <a name="before-you-begin"></a>Antes de começar

* Entre em sua organização do Azure DevOps Services ( **https://dev.azure.com/** ). 
  Pode obter uma [organização de Serviços de DevOps do Azure gratuita](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Para obter mais informações, veja [Ligar aos Serviços de DevOps do Azure](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Precisa de uma máquina virtual do Linux para um destino de implementação.  Para obter mais informações, veja [Criar e gerir VMs do Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Abra a porta de entrada 80 para a máquina virtual. Para obter mais informações, veja [Criar grupos de segurança de rede com o portal do Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>Obtenha o código do aplicativo de exemplo

Se você já tiver um aplicativo no GitHub que deseja implantar, você pode tentar criar um pipeline para esse código.

No entanto, se você for um novo usuário, poderá obter um início melhor usando nosso código de exemplo. Nesse caso, bifurcar este repositório no GitHub:

#### <a name="javatabjava"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic é um aplicativo [Java Spring boot](https://spring.io/guides/gs/spring-boot) criado usando o [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Este aplicativo node. js foi criado por meio de [Yeoman](https://yeoman.io/learning/index.html). utiliza o Express, o bower e o grunt. Possui também alguns pacotes npm como dependências.
> O exemplo contém também um script que configura o Nginx e implementa a aplicação, que é executada nas máquinas virtuais. Especificamente, o script:
> 1. Instala o Node, o Nginx e o PM2.
> 2. Configura o Nginx e o PM2.
> 3. Inicia a aplicação Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Pré-requisitos para a VM do Linux

Os aplicativos de exemplo mencionados acima foram testados no Ubuntu 16, 4 e recomendamos que você use a mesma versão da VM do Linux para este guia de início rápido.
Siga as etapas adicionais descritas abaixo com base na pilha de tempo de execução usada para o aplicativo.

#### <a name="javatabjava"></a>[Java](#tab/java)

- Para implantar aplicativos baseados em Java Spring boot e Spring Cloud, crie uma VM do Linux no Azure usando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) modelo, que fornece um tempo de execução baseado em OpenJDK totalmente compatível.
- Para implantar Servlets Java no servidor Tomcat, crie uma VM do Linux com o Java 8 usando [este](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) modelo do Azure e [Configure o Tomcat 9. x como um serviço](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Para implantar o aplicativo baseado em Java EE, use um modelo do Azure para criar uma [VM do Linux + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) ou uma [VM do Linux + Java + WebLogic 12. x](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleWebLogicServer12cEnterprise) ou uma [VM do Linux + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

Para instalar um aplicativo JavaScript ou um aplicativo node. js, você precisará de uma VM do Linux com o servidor Web Nginx para implantar o aplicativo.
Se você ainda não tiver uma VM do Linux com Nginx, crie uma agora no Azure usando as etapas neste [exemplo](/azure/virtual-machines/linux/quick-create-cli).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Criar um ambiente de Azure Pipelines com máquinas virtuais do Azure

As máquinas virtuais podem ser adicionadas como recursos em [ambientes](https://docs.microsoft.com/azure/devops/pipelines/process/environments) e podem ser destinadas a implantações de vários computadores. As exibições do histórico de implantação no ambiente fornecem rastreamento da VM para o pipeline e, em seguida, para a confirmação.

Você pode criar um ambiente no Hub "**ambientes**" dentro da seção "**pipelines**".
1.  Entre na sua organização do Azure DevOps e navegue até seu projeto.
2.  Em seu projeto, navegue até a página **pipelines** . Em seguida, escolha **ambientes** e clique em **criar ambiente**. Especifique um **nome** (obrigatório) para o ambiente e uma **Descrição**.
3.  Escolha **máquinas virtuais** como um **recurso** a ser adicionado ao ambiente e clique em **Avançar**.
4.  Escolha sistema operacional (Windows/Linux) e **Copie o script de registro do PS**. 
5.  Agora, execute o script copiado de um prompt de comando do PowerShell de administrador em cada uma das VMs de destino a serem registradas com esse ambiente.
    > [!NOTE]
    > - O token de acesso pessoal do usuário conectado é previamente inserido no script que expira no mesmo dia, fazendo com que o script copiado fique inutilizável.
    > - Se sua VM já tiver algum agente em execução, forneça um nome exclusivo para "Agent" para se registrar no ambiente.
6.  Depois que a VM for registrada, ela começará a aparecer como um recurso de ambiente na guia "recursos" do ambiente.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Para adicionar mais VMs, você pode exibir e copiar o script novamente clicando em "Adicionar recurso" e escolhendo "máquinas virtuais" como recurso. Esse script permaneceria o mesmo para que todas as VMs fossem adicionadas a esse ambiente. 
8.  Cada computador interage com Azure Pipelines para coordenar a implantação do seu aplicativo.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Você pode adicionar marcas à VM como parte do script de registro do PS interativo (ou) também pode adicionar/remover o mesmo do modo de exibição de recursos clicando nos três pontos no final de cada recurso da VM na exibição de recursos.

   As marcas atribuídas permitem que você limite a implantação a máquinas virtuais específicas quando o ambiente é usado em um trabalho de implantação. As marcas são limitadas a 256 caracteres, mas não há nenhum limite para o número de marcas que você pode usar.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definir o pipeline de Build do CI

Você precisará de um pipeline de compilação de CI (integração contínua) que publica seu aplicativo Web, bem como um script de implantação que pode ser executado localmente no servidor Ubuntu. Configure um pipeline de Build de CI com base no tempo de execução que você deseja usar. 

1. Entre na sua organização do Azure DevOps e navegue até seu projeto.

1. Em seu projeto, navegue até a página **pipelines** . Em seguida, escolha a ação para criar um novo pipeline.

1. Percorra as etapas do assistente selecionando primeiro o **GitHub** como o local do código-fonte.

1. Você pode ser redirecionado para o GitHub para entrar. Nesse caso, insira suas credenciais do GitHub.

1. Quando a lista de repositórios for exibida, selecione o repositório de aplicativo de exemplo desejado.

1. Azure Pipelines analisará seu repositório e recomendará um modelo de pipeline adequado.

#### <a name="javatabjava"></a>[Java](#tab/java)

Selecione o modelo **inicial** e copie o trecho YAML abaixo que cria seu projeto Java e executa testes com o Apache Maven:

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

Para obter mais diretrizes, siga as etapas mencionadas em [compilar seu aplicativo Java com o Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascripttabjava-script"></a>[JavaScript](#tab/java-script)

Selecione o modelo **inicial** e copie o trecho de código YAML abaixo que cria um projeto do node. js geral com NPM.

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

Para obter mais diretrizes, siga as etapas em [criar seu aplicativo node. js com Gulp](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript).

- Dê uma olhada no pipeline para ver o que ele faz. Verifique se todas as entradas padrão são apropriadas para seu código.

- Selecione **salvar e executar**e, em seguida, selecione **confirmar diretamente no Branch mestre**e, em seguida, escolha **salvar e executar** novamente.

- Uma nova execução é iniciada. Aguarde a conclusão da execução.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definir as etapas do CD para implantar na VM do Linux

1. Edite o pipeline acima e inclua um [trabalho de implantação](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) referenciando o ambiente e os recursos da VM que você usou anteriormente usando a sintaxe YAML abaixo:

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
2. Você pode selecionar conjuntos específicos de máquinas virtuais do ambiente para receber a implantação, especificando as **marcas** que você definiu para cada máquina virtual no ambiente.
[Aqui](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) está o esquema completo do YAML para o trabalho de implantação.

3. Você pode especificar eithor `runOnce` ou `rolling` como estratégia de implantação. 

   `runOnce` é a estratégia de implantação mais simples em que todos os ganchos do ciclo de vida, ou seja, `preDeploy` `deploy`, `routeTraffic`e `postRouteTraffic`, são executados uma vez. Em seguida, `on:` `success` ou `on:` `failure` é executado.

   Veja abaixo o trecho de código YAML de exemplo para `runOnce`:
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

4. Abaixo está um exemplo do trecho de código YAML que você pode usar para definir uma estratégia sem interrupção para atualizações de máquinas virtuais de até 5 destinos em cada iteração. `maxParallel` determinará o número de destinos que podem ser implantados em paralelo. As contas de seleção para número absoluto ou percentual de destinos que devem permanecer disponíveis a qualquer momento, excluindo os destinos que estão sendo implantados. Ele também é usado para determinar as condições de êxito e falha durante a implantação.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 2  #for percentages, mention as x%
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

   Com cada execução desse trabalho, o histórico de implantação é registrado no ambiente de `<environment name>` que você criou e registrou as VMs.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Executar seu pipeline e obter exibições de rastreamento no ambiente
A exibição de implantações do ambiente fornece rastreamento completo de confirmações e itens de trabalho e um histórico de implantação entre pipelines por ambiente/recurso.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Passos seguintes
- Você pode continuar a [Personalizar o pipeline](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) que acabou de criar.
- Para saber o que mais você pode fazer em pipelines do YAML, consulte [referência de esquema do YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema).
