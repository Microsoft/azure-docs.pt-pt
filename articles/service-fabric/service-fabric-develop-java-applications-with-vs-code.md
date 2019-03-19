---
title: Desenvolver aplicações Java do Azure Service Fabric com o Visual Studio Code | Documentos da Microsoft
description: Este artigo mostra como criar, implementar e depurar aplicações de Java do Service Fabric com o Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 9e5a2138de7e2c0e892bd0efeb0db1e2aee87422
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014687"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicações Java do Service Fabric com o Visual Studio Code

O [extensão de serviços fiáveis do Service Fabric para o VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) torna mais fácil criar aplicações de Java do Service Fabric em sistemas operativos Windows, Linux e macOS.

Este artigo mostra-lhe como criar, implementar e depurar uma aplicação Java do Service Fabric com o Visual Studio Code.

> [!IMPORTANT]
> Aplicações de Java do Service Fabric podem ser desenvolvidas em máquinas do Windows, mas poderá ser implementadas em clusters do Linux do Azure apenas. Depuração de aplicações Java não é suportada no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tem instalado o VS Code, a extensão de serviços fiáveis do Service Fabric para o VS Code e todas as dependências necessárias para o seu ambiente de desenvolvimento. Para obter mais informações, consulte [introdução ao](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo utiliza a aplicação de voto na [repositório de GitHub de exemplo de início rápido de aplicativo de Java do Service Fabric](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Para clonar o repositório para o seu computador de desenvolvimento, execute o seguinte comando a partir de uma janela de terminal (janela de comando no Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Abra a aplicação no VS Code

Código de VS aberto.  Clique no ícone do Explorador no **barra de atividade** e clique em **Abrir pasta**, ou clique em **ficheiro -> Abrir pasta**. Navegue para o *./service-fabric-java-quickstart/Voting* diretório na pasta onde clonou o repositório, em seguida, clique em **OK**. A área de trabalho deve conter os mesmos ficheiros mostrados na captura de ecrã abaixo.

![Aplicação de voto do Java na área de trabalho](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Criar a aplicação

1. Prima (Ctrl + Shift + p) para abrir o **paleta de comandos** no VS Code.
2. Procure e selecione o **Service Fabric: Criar aplicação** comando. O resultado de compilação é enviado para o terminal integrado.

   ![Criar comando de aplicação no VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implementar a aplicação no cluster local
Depois de criar a aplicação, pode implementá-la no cluster local. 

> [!IMPORTANT]
> Implementação de aplicações Java no cluster local não é suportada em máquinas do Windows.

1. Partir do **paleta de comandos**, selecione o **Service Fabric: Implementar o comando de aplicação (Localhost)**. O resultado do processo de instalação é enviado para o terminal integrado.

   ![Implementar a aplicação de comando no VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implementação estiver concluída, inicie um browser e abra o Service Fabric Explorer: `http://localhost:19080/Explorer`. Deverá ver que a aplicação está em execução. Esta operação pode demorar algum tempo, por isso, seja paciente. 

   ![Aplicação de voto no Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Depois de verificar que a aplicação está em execução, inicie um browser e abra a página: `http://localhost:8080`. Esta é a web front-end da aplicação. Pode adicionar itens e clicar para votar.

   ![Aplicação de voto no Browser](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Para remover a aplicação do cluster, selecione o **Service Fabric: Remover aplicativo** comando a partir de **paleta de comandos**. O resultado do processo de desinstalação é enviado para o terminal integrado. Pode utilizar o Service Fabric Explorer para verificar que a aplicação foi removida do local cluster.

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar aplicativos no VS Code, a aplicação tem de estar em execução num local cluster. Pontos de interrupção, em seguida, podem ser adicionados ao código.

> [!IMPORTANT]
> Depuração de aplicações Java não é suportada em máquinas do Windows.

Para preparar o VotingDataService e a aplicação de voto para depuração, conclua os seguintes passos:

1. Atualização do *Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* ficheiro.
Comente o comando na linha 6 (utilize '#') e adicione o seguinte comando na parte inferior do ficheiro:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Atualização do *Voting/VotingApplication/ApplicationManifest.xml* ficheiro. Definir o **MinReplicaSetSize** e o **TargetReplicaSetSize** atributos como "1" no **StatefulService** elemento:
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Clique no ícone de depuração no **barra de atividade** para abrir o modo de exibição do depurador no VS Code. Clique no ícone de engrenagem na parte superior da exibição do depurador e selecione **Java** no menu de ambiente de lista pendente. Abre o ficheiro Launch JSON. 

   ![Depurar o ícone na área de trabalho de código de VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. No ficheiro Launch JSON, defina o valor da porta na configuração com o nome **depurar (anexar)** ao **8001**. Guarde o ficheiro.

   ![Configuração de depuração para o Launch](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implementar a aplicação no cluster local com o **Service Fabric: Implementar a aplicação (Localhost)** comando. Certifique-se de que a aplicação está em execução no Service Fabric Explorer. A aplicação está agora pronta para ser depurado.

Para definir um ponto de interrupção, conclua os seguintes passos:

1. No Explorer, abra a */Voting/VotingDataService/src/statefulservice/VotingDataService.java* ficheiro. Definir um ponto de interrupção na primeira linha do código no `try` bloqueie no `addItem` método (linha 80).
   
   ![Definir o ponto de interrupção no serviço de dados de votação](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Certifique-se de que defina pontos de interrupção no executáveis linhas de código. Por exemplo pontos de interrupção definido em declarações de método, `try` instruções, ou `catch` instruções não serão detetadas pelo depurador.
2. Para iniciar a depuração, clique no ícone de depuração no **barra de atividade**, selecione a **depurar (anexar)** configuração a partir do menu debug e clique no botão de execução (seta verde).

   ![Depurar (anexar) configuração](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Num browser, aceda a `http://localhost:8080`. Escreva um novo item na caixa de texto e clique em **+ adicionar**. O ponto de interrupção deve ser atingido. Pode utilizar a barra de ferramentas de depuração na parte superior do VS Code para continuar a execução, passo em linhas, passo em métodos, ou passo fora o método atual. 
   
   ![Atingir o ponto de interrupção](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Para terminar a sessão de depuração, clique no ícone de plug na barra de ferramentas de depuração na parte superior do VS Code.
   
   ![Desligar do depurador](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Quando tiver concluído a depuração, pode usar o **Service Fabric: Remover aplicação** comando para remover a aplicação de votação do seu cluster local. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [desenvolver e depurar C# aplicações do Service Fabric com o VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
