---
title: Depurar uma aplicação Java num cluster do Service Fabric local | Microsoft Docs
description: Neste tutorial, saiba como depurar e obter registos de uma aplicação Java do Service Fabric em execução num cluster local.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: abcc317233d7304365f8687de3c9bec2d09f7b33
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57247414"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Tutorial: Depurar uma aplicação Java implementada num cluster do Service Fabric local

Este tutorial é a segunda parte de uma série. Saiba como anexar um depurador remoto com o Eclipse para a aplicação Service Fabric. Além disso, saiba como redirecionar registos das aplicações em execução para uma localização conveniente para o programador.

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação Java Service Fabric Reliable Services](service-fabric-tutorial-create-java-app.md)
> * Implementar e depurar a aplicação num cluster local
> * [Implementar a aplicação num cluster do Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Configurar a monitorização e os diagnósticos da aplicação](service-fabric-tutorial-java-elk.md)
> * [Configurar CI/CD](service-fabric-tutorial-java-jenkins.md)


Na segunda parte da série, saiba como:
> [!div class="checklist"]
> * Implementar a aplicação Java com o Eclipse
> * Redirecionar os registos para uma localização configurável


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Configure o ambiente de desenvolvimento para [Mac](service-fabric-get-started-mac.md) ou [Linux](service-fabric-get-started-linux.md). Siga as instruções para instalar o plug-in do Eclipse, Gradle, o SDK do Service Fabric e a CLI do Service Fabric (sfctl).

## <a name="download-the-voting-sample-application"></a>Transferir a aplicação de votação de exemplo

Se não conseguiu criar a aplicação de votação de exemplo na [primeira parte desta série de tutoriais](service-fabric-tutorial-create-java-app.md), pode transferi-la. Numa janela do comando, execute o seguinte comando para clonar o repositório da aplicação de exemplo para o seu computador local.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Crie e implemente](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) a aplicação no cluster de desenvolvimento local.

## <a name="debug-java-application-using-eclipse"></a>Implementar a aplicação Java com o Eclipse

1. Abra o Eclipse IDE no computador e clique em **Ficheiro -> Importar...**

2. Na janela de pop-up, selecione a opção **Geral -> Projetos Existentes na Área de Trabalho** e prima Seguinte.

3. Na janela Importar Projetos, selecione a opção **Selecionar diretório de raiz** e selecione o diretório **Voting**. Se seguiu o tutorial da primeira série, o diretório **Voting** está no diretório **Eclipse-workspace**.

4. Atualize entryPoint.sh do serviço que quer depurar para iniciar o processo de Java com parâmetros de depuração remota. Para este tutorial, é utilizado o front-end sem monitoração de estado: *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh*. A porta 8001 está definida para depuração neste exemplo.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Atualize o Manifesto da Aplicação ao definir o número de instâncias ou de réplicas para o serviço que está a ser depurado como um. Esta definição evita conflitos na porta utilizada para depuração. Por exemplo, para serviços sem estado, defina ``InstanceCount="1"`` e para serviços com estado, defina os tamanhos de destino e de réplica mínimos como 1 da seguinte forma: ``TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. No Eclipse IDE, selecione **Executar -> Configurações de Depuração -> Aplicação Java Remota**, prima o botão **Novo**, defina as propriedades da seguinte forma e clique em **Aplicar**.

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Coloque um ponto de interrupção na linha 109 do ficheiro *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*.

8. No Explorador de Pacotes, clique com o botão direito do rato no projeto **Voto** e clique em **Service Fabric -> Publicar a Aplicação...**

9. Na janela **Publicar a Aplicação**, selecione **Local.json** na lista pendente e clique em **Publicar**.

10. No Eclipse IDE, selecione **Executar -> Configurações de Depuração -> Aplicação Java Remota**, clique na configuração **Voto** que criou e clique em **Depurar**.

11. Aceda ao seu navegador da web e acesso **localhost:8080**. Isso será automaticamente ponto de interrupção e entrará no Eclipse a **perspetiva de depuração**.

Agora pode aplicar essas mesmas etapas para depurar qualquer aplicação do Service Fabric no Eclipse.

## <a name="redirect-application-logs-to-custom-location"></a>Redirecionar os registos de aplicações para uma localização personalizada

Os passos seguintes permitem saber como redirecionar os registos de aplicações da localização */var/log/syslog* predefinida para uma localização personalizada.

1. Atualmente, as aplicações em execução em clusters do Service Fabric do Linux apenas suportam pegou um único ficheiro de registo. Para configurar uma aplicação para que os registos vão sempre */tmp/mysfapp0.0.log*, crie um ficheiro denominado Logging na seguinte localização *Voting/VotingApplication/VotingWebPkg/Code/logging.properties*  e adicione o seguinte conteúdo.

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Adicione o seguinte parâmetro em *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* para o comando de execução Java:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    O exemplo seguinte mostra a execução de exemplo com o depurador anexado, semelhante a execução na secção anterior.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

Nesta fase, aprendeu a depurar e aceder aos seus registos de aplicações ao programar as suas aplicações Java do Service Fabric.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:

> [!div class="checklist"]
> * Implementar a aplicação Java com o Eclipse
> * Redirecionar os registos para uma localização configurável

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Implementar a aplicação no Azure](service-fabric-tutorial-java-deploy-azure.md)