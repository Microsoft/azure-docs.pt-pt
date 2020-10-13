---
title: Desenvolver aplicações Java com Código de Estúdio Visual
description: Este artigo mostra como construir, implementar e depurar aplicações java Service Fabric usando Código de Estúdio Visual.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327366"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicações de tecido de serviço java com código de estúdio visual

A [extensão de Serviços Fiáveis de Serviços de Tecido de Serviço para O Código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a construção de aplicações java Service Fabric em sistemas operativos Windows, Linux e macOS.

Este artigo mostra-lhe como construir, implementar e depurar uma aplicação java Service Fabric usando o Código de Estúdio Visual.

> [!IMPORTANT]
> As aplicações de Service Fabric Java podem ser desenvolvidas em máquinas Windows, mas podem ser implementadas apenas em clusters Azure Linux. As aplicações de depuragem java não são suportadas no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já instalou o Código VS, a extensão de Serviços Fiáveis de Tecido de Serviço para O Código VS e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Para saber mais, consulte ["Começar".](./service-fabric-get-started-vs-code.md#prerequisites)

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo utiliza a aplicação de voto na [aplicação Service Fabric Java quickstart sample GitHub repository](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Para clonar o repositório para a sua máquina de desenvolvimento, executar o seguinte comando a partir de uma janela de terminal (janela de comando no Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Abra a aplicação em Código VS

Abra o VS Code.  Clique no ícone Explorer na **Barra de Atividade** e clique em Abrir **Pasta,** ou clique em  **Ficheiros -> Pasta Aberta**. Navegue para o *diretório ./service-fabric-java-quickstart/Vote* na pasta onde clonou o repositório e clique em **OK**. O espaço de trabalho deve conter os mesmos ficheiros apresentados na imagem abaixo.

![Aplicação de voto de Java no espaço de trabalho](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Criar a aplicação

1. Prima (Ctrl + Shift + p) para abrir a **Paleta de Comando** no Código VS.
2. Procure e selecione o **Tecido de Serviço: Constroem** o comando de aplicação. A produção de construção é enviada para o terminal integrado.

   ![Construir o Comando de Aplicação em Código VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implementar a aplicação para o cluster local
Depois de ter construído a aplicação, pode implantá-la para o cluster local. 

> [!IMPORTANT]
> A implementação de aplicações Java no cluster local não é suportada em máquinas Windows.

1. A partir da **Paleta de Comando,** selecione o **tecido de serviço: Implementar o comando (Localhost).** A saída do processo de instalação é enviada para o terminal integrado.

   ![Implementar o Comando de Aplicação em Código VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implementação estiver concluída, lance um browser e abra o Service Fabric Explorer: `http://localhost:19080/Explorer` . Devias ver se a candidatura está a decorrer. Isto pode levar algum tempo, por isso, seja paciente. 

   ![Aplicação de voto no Explorador de Tecido de Serviço](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Depois de verificar que a aplicação está em execução, lance um browser e abra esta página: `http://localhost:8080` . Esta é a parte frontal da aplicação. Pode adicionar itens e clicar neles para votar.

   ![Aplicação de voto no Browser](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Para remover a aplicação do cluster, selecione o Tecido de **Serviço: Remover** o comando de aplicação da Paleta de **Comando**. A saída do processo de desinstalação é enviada para o terminal integrado. Pode utilizar o Service Fabric Explorer para verificar se a aplicação foi removida do cluster local.

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar as aplicações no Código VS, a aplicação deve estar a decorrer num cluster local. Os pontos de rutura podem então ser adicionados ao código.

> [!IMPORTANT]
> As aplicações Java depurativas não são suportadas em máquinas Windows.

Para preparar o VoteDataService e o pedido de votação para depurar, complete os seguintes passos:

1. Atualizar o *ficheiro Vote/VoteApplication/VoteDataServicePkg/Code/entryPoint.sh.*
Comente o comando na linha 6 (use '#') e adicione o seguinte comando à parte inferior do ficheiro:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Atualizar o ficheiro *Vote/VoteApplication/ApplicationManifest.xml.* Defina os **atributos MinReplicaSetSize** e **TargetReplicaSetSize** para "1" no elemento **StatefulService:**
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Clique no ícone Debug na Barra de **Atividade** para abrir a vista de depurar no Código VS. Clique no ícone de engrenagem na parte superior da vista de depurar e selecione **Java** a partir do menu ambiente suspenso. O launch.jsno ficheiro abre. 

   ![Ícone de depurar no espaço de trabalho do código VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. No launch.jsficheiro, detete o valor da porta na configuração denominada **Debug (Anexação)** para **8001**. Guarde o ficheiro.

   ![Configuração de depurar para o launch.jsem](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implemente a aplicação para o cluster local utilizando o **comando De Serviço: Implementar aplicação (Localhost).** Verifique se a aplicação está em execução no Explorador de Tecidos de Serviço. A sua candidatura está agora pronta para ser depurada.

Para definir um ponto de rutura, complete os seguintes passos:

1. No Explorer, abra o ficheiro */Vote/VoteDataService/src/statefulservice/VoteDataService.java.* Desaponte um ponto de rutura na primeira linha de código do `try` bloco no `addItem` método (linha 80).
   
   ![Definir ponto de rutura no Serviço de Dados de Voto](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Certifique-se de que define pontos de rutura em linhas de código executáveis. Por exemplo, os pontos de rutura definidos em declarações, `try` declarações ou declarações do método `catch` serão ignorados pelo depurador.
2. Para começar a depurar, clique no ícone Debug na Barra de **Atividades,** selecione a configuração **Debug (Anexar)** a partir do menu de depuração e clique no botão de funcionamento (seta verde).

   ![Configuração de depurg (anexação)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Num browser, aceda a `http://localhost:8080`. Digite um novo item na caixa de texto e clique **+ Adicionar**. O seu ponto de rutura deve ser atingido. Pode utilizar a barra de ferramentas Debug no topo do Código VS para continuar a execução, pisar linhas, entrar em métodos ou sair do método atual. 
   
   ![Ponto de rutura de impacto](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Para terminar a sessão de depurar, clique no ícone de ficha na barra de ferramentas Debug na parte superior do Código VS.
   
   ![Desconexão do depurador](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Quando terminar de depurar, pode utilizar o **comando De Serviço: Remover** o comando de Aplicação para remover a aplicação de votação do seu cluster local. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar aplicações de tecido de serviço C# com código VS](./service-fabric-develop-csharp-applications-with-vs-code.md).
