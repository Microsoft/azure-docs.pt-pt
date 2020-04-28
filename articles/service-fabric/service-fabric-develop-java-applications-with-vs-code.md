---
title: Desenvolver aplicações Java com Código de Estúdio Visual
description: Este artigo mostra como construir, implementar e depurar aplicações java service fabric usando o Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75610051"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Desenvolver aplicações java service fabric com código de estúdio visual

A extensão de [Serviço sintetizador de serviços fiáveis para código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a construção de aplicações Java Service Fabric em windows, Linux e sistemas operativos macOS.

Este artigo mostra-lhe como construir, implementar e depurar uma aplicação Java Service Fabric usando o Visual Studio Code.

> [!IMPORTANT]
> As aplicações de Tecido de Serviço Java podem ser desenvolvidas em máquinas Windows, mas podem ser implantadas apenas em clusters Azure Linux. As aplicações Debugging Java não são suportadas no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo assume que já instalou o Código VS, a extensão de Serviços Fiáveis de Tecidos para código VS, e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Para saber mais, ver [Getting Started](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo utiliza a aplicação de votação no sistema de utilização de tecido de [serviço Java amostra de reposição gitHub](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Para clonar o repositório para a sua máquina de desenvolvimento, execute o seguinte comando a partir de uma janela de terminal (janela de comando no Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Abra a aplicação no Código VS

Código VS aberto.  Clique no ícone Explorer na Barra de **Atividade** e clique em **Abrir pasta,** ou clique em **Ficheiro -> Open Folder**. Navegue para o diretório *./service-fabric-java-quickstart/Vote* na pasta onde clonou o repositório e, em seguida, clique em **OK**. O espaço de trabalho deve conter os mesmos ficheiros indicados na imagem abaixo.

![Pedido de voto de Java no espaço de trabalho](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Criar a aplicação

1. Prima (CTRL + Shift + p) para abrir a Paleta de **Comando** no Código VS.
2. Procure e selecione o Tecido de Serviço: Construa o comando **da Aplicação.** A saída de construção é enviada para o terminal integrado.

   ![Construir comando de aplicação em código VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implementar a aplicação para o cluster local
Depois de ter construído a aplicação, pode implantá-la para o aglomerado local. 

> [!IMPORTANT]
> A implementação de aplicações Java para o cluster local não é suportada nas máquinas Windows.

1. A partir da Paleta de **Comando,** selecione o tecido de **serviço: implementação de aplicação (localde a localização)** comando . A saída do processo de instalação é enviada para o terminal integrado.

   ![Implementar comando de aplicação no código VS](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implementação estiver concluída, lance um `http://localhost:19080/Explorer`navegador e abra o Service Fabric Explorer: . Devia ver que a candidatura está a decorrer. Isto pode levar algum tempo, por isso tenha paciência. 

   ![Aplicação de voto no Explorador de Tecido de Serviço](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Depois de verificar que a aplicação está em execução, `http://localhost:8080`lance um navegador e abra esta página: . Esta é a frente web da aplicação. Pode adicionar itens e clicar neles para votar.

   ![Aplicação de voto no navegador](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Para remover a aplicação do cluster, selecione o Tecido de **Serviço: Retire** o comando da Aplicação da Paleta de **Comando**. A saída do processo de desinstalação é enviada para o terminal integrado. Pode utilizar o Service Fabric Explorer para verificar se a aplicação foi removida do cluster local.

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar aplicações no Código VS, a aplicação deve estar em execução num cluster local. Os pontos de rutura podem então ser adicionados ao código.

> [!IMPORTANT]
> Debugging Java aplicações não é suportado em máquinas Windows.

Para preparar o Serviço de Dados de Voto e a aplicação de voto para depuração, preencha os seguintes passos:

1. Atualize o ficheiro *Vote/VoteApplication/VoteDataServicePkg/Code/entryPoint.sh.*
Comente o comando na linha 6 (use '#') e adicione o seguinte comando na parte inferior do ficheiro:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Atualizar o ficheiro *Voting/VoteApplication/ApplicationManifest.xml.* Defina o **MinReplicaSetSize** e o **TargetReplicaSetSize** atribui a "1" no elemento **StatefulService:**
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Clique no ícone Debug na Barra de **Atividades** para abrir a vista de debugger no Código VS. Clique no ícone de engrenagem na parte superior da vista de debugger e selecione **Java** a partir do menu de ambiente dropdown. O ficheiro launch.json abre. 

   ![Ícone de depuração no espaço de trabalho do código VS](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. No ficheiro launch.json, detete o valor da porta na configuração denominada **Debug (Attach)** a **8001**. Guarde o ficheiro.

   ![Configuração de depuração para o lançamento.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implemente a aplicação para o cluster local utilizando o comando **Service Fabric: Deploy Application (Localhost).** Verifique se a aplicação está em execução no Service Fabric Explorer. A sua candidatura está agora pronta para ser depurada.

Para definir um ponto de rutura, complete os seguintes passos:

1. No Explorer, abra o ficheiro */Voting/VotingDataService/src/statefulservice/VoteDataService.java.* Delineie um ponto de `try` rutura na `addItem` primeira linha de código no método (linha 80).
   
   ![Definir ponto de rutura no Serviço de Dados de Votação](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Certifique-se de que define pontos de rutura em linhas de código executáveis. Por exemplo, os pontos `try` de rutura `catch` definidos nas declarações, declarações ou declarações do método serão ignorados pelo desordeiro.
2. Para começar a depurar, clique no ícone Debug na Barra de **Atividade,** selecione a configuração **Debug (Anexar)** do menu de depuração e clique no botão de execução (seta verde).

   ![Configuração de depuração (anexar)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Num browser, aceda a `http://localhost:8080`. Digite um novo item na caixa de texto e clique **+ Adicionar**. O teu ponto de rutura deve ser atingido. Pode utilizar a barra de ferramentas Debug no topo do Código VS para continuar a executar, pisar linhas, pisar métodos ou sair do método atual. 
   
   ![Breakpoint de sucesso](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Para terminar a sessão de depuração, clique no ícone da ficha na barra de ferramentas Debug na parte superior do Código VS.
   
   ![Desligar do debugger](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Quando terminar de depurar, pode utilizar o Serviço Tecido: Remover o comando **da Aplicação** para remover a aplicação de votação do seu cluster local. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar aplicações de tecido](./service-fabric-develop-csharp-applications-with-vs-code.md)de serviço C# com código VS .
