---
title: Desenvolva aplicativos Java com Visual Studio Code
description: Este artigo mostra como criar, implantar e depurar aplicativos Java Service Fabric usando Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610051"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Desenvolva aplicativos Java Service Fabric com Visual Studio Code

A [extensão de Reliable Services Service Fabric para vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a criação de aplicativos de Service Fabric Java em sistemas operacionais Windows, Linux e MacOS.

Este artigo mostra como criar, implantar e depurar um aplicativo Java Service Fabric usando Visual Studio Code.

> [!IMPORTANT]
> Service Fabric aplicativos Java podem ser desenvolvidos em computadores Windows, mas podem ser implantados somente em clusters Linux do Azure. Não há suporte para a depuração de aplicativos Java no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha instalado VS Code, a extensão Service Fabric Reliable Services para VS Code e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Para saber mais, consulte [introdução](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo usa o aplicativo de votação no [repositório do GitHub de exemplo Service Fabric início rápido de aplicativo Java](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Para clonar o repositório em seu computador de desenvolvimento, execute o seguinte comando em uma janela de terminal (janela de comando no Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Abra o aplicativo no VS Code

Abra VS Code.  Clique no ícone do Explorer na **barra de atividades** e clique em **abrir pasta**ou clique em **arquivo-> abrir pasta**. Navegue até o diretório *./Service-Fabric-Java-QuickStart/Voting* na pasta em que você clonou o repositório e clique em **OK**. O espaço de trabalho deve conter os mesmos arquivos mostrados na captura de tela abaixo.

![Aplicativo de votação Java no espaço de trabalho](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Criar a aplicação

1. Pressione (Ctrl + Shift + p) para abrir a **paleta de comandos** no vs Code.
2. Pesquise e selecione o comando **Service Fabric: Compilar aplicativo** . A saída da compilação é enviada para o terminal integrado.

   ![Comando criar aplicativo no VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implantar o aplicativo no cluster local
Depois de criar o aplicativo, você pode implantá-lo no cluster local. 

> [!IMPORTANT]
> Não há suporte para a implantação de aplicativos Java no cluster local em computadores Windows.

1. Na **paleta de comandos**, selecione o **comando Service Fabric: implantar aplicativo (localhost)** . A saída do processo de instalação é enviada para o terminal integrado.

   ![Comando implantar aplicativo no VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implantação for concluída, inicie um navegador e abra Service Fabric Explorer: `http://localhost:19080/Explorer`. Você deve ver que o aplicativo está em execução. Isso pode levar algum tempo, portanto, seja paciente. 

   ![Aplicativo de votação no Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Depois de verificar se o aplicativo está em execução, inicie um navegador e abra esta página: `http://localhost:8080`. Este é o front-end da Web do aplicativo. Você pode adicionar itens e clicar neles para votar.

   ![Aplicativo de votação no navegador](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Para remover o aplicativo do cluster, selecione o comando **Service Fabric: remover aplicativo** da paleta de **comandos**. A saída do processo de desinstalação é enviada para o terminal integrado. Você pode usar Service Fabric Explorer para verificar se o aplicativo foi removido do cluster local.

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar aplicativos no VS Code, o aplicativo deve estar em execução em um cluster local. Os pontos de interrupção podem ser adicionados ao código.

> [!IMPORTANT]
> Não há suporte para depuração de aplicativos Java em computadores Windows.

Para preparar o arquivo votingdataservice e o aplicativo de votação para depuração, conclua as seguintes etapas:

1. Atualize o arquivo de *votação/VotingApplication/VotingDataServicePkg/código/EntryPoint. sh* .
Comente o comando na linha 6 (use ' # ') e adicione o seguinte comando à parte inferior do arquivo:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Atualize o arquivo de *votação/VotingApplication/ApplicationManifest. xml* . Defina os atributos **MinReplicaSetSize** e **TargetReplicaSetSize** como "1" no elemento **StatefulService** :
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Clique no ícone de depuração na **barra de atividade** para abrir a exibição do depurador no vs Code. Clique no ícone de engrenagem na parte superior da exibição do depurador e selecione **Java** no menu do ambiente suspenso. O arquivo launch. JSON é aberto. 

   ![Ícone de depuração no espaço de trabalho VS Code](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. No arquivo launch. JSON, defina o valor da porta na configuração chamada **debug (Attach)** como **8001**. Guarde o ficheiro.

   ![Configuração de depuração para o Launch. JSON](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implante o aplicativo no cluster local usando o comando **Service Fabric: implantar aplicativo (localhost)** . Verifique se o aplicativo está em execução no Service Fabric Explorer. Seu aplicativo agora está pronto para ser depurado.

Para definir um ponto de interrupção, conclua as seguintes etapas:

1. No Explorer, abra o arquivo */Voting/VotingDataService/src/statefulservice/VotingDataService.java* . Defina um ponto de interrupção na primeira linha de código no bloco de `try` no método `addItem` (linha 80).
   
   ![Definir ponto de interrupção no serviço de dados de votação](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Certifique-se de definir pontos de interrupção em linhas de código executáveis. Por exemplo, os pontos de interrupção definidos em declarações de método, instruções de `try` ou instruções de `catch` serão perdidas pelo depurador.
2. Para iniciar a depuração, clique no ícone de depuração na **barra de atividade**, selecione a configuração **depurar (anexar)** no menu Depurar e clique no botão Executar (seta verde).

   ![Depurar (anexar) configuração](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Em um navegador da Web, vá para `http://localhost:8080`. Digite um novo item na caixa de texto e clique em **+ Adicionar**. Seu ponto de interrupção deve ser atingido. Você pode usar a barra de ferramentas de depuração na parte superior de VS Code para continuar a execução, passar sobre linhas, entrar em métodos ou sair do método atual. 
   
   ![Ponto de interrupção de clique](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Para encerrar a sessão de depuração, clique no ícone de plugue na barra de ferramentas de depuração na parte superior da VS Code.
   
   ![Desconectar do depurador](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Quando terminar a depuração, você poderá usar o comando **Service Fabric: remover aplicativo** para remover o aplicativo de votação do cluster local. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar C# Service Fabric aplicativos com vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
