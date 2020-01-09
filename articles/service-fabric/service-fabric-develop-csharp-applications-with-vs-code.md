---
title: Desenvolva aplicativos .NET Core com Visual Studio Code
description: Este artigo mostra como criar, implantar e depurar aplicativos .NET Core Service Fabric usando Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614528"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Desenvolva C# Service Fabric aplicativos com Visual Studio Code

A [extensão de Reliable Services Service Fabric para vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a criação de aplicativos de Service Fabric do .NET Core em sistemas operacionais Windows, Linux e MacOS.

Este artigo mostra como criar, implantar e depurar um aplicativo de Service Fabric do .NET Core usando Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você já tenha instalado VS Code, a extensão Service Fabric Reliable Services para VS Code e quaisquer dependências necessárias para o seu ambiente de desenvolvimento. Para saber mais, consulte [introdução](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo usa o aplicativo de serviço de manutenção no [repositório GitHub Service Fabric exemplos de introdução ao .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Para clonar o repositório em seu computador de desenvolvimento, execute o seguinte comando em uma janela de terminal (janela de comando no Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Abra o aplicativo no VS Code

### <a name="windows"></a>Windows
Clique com o botão direito do mouse no ícone de VS Code no menu iniciar e escolha **Executar como administrador**. Para anexar o depurador aos seus serviços, você precisa executar VS Code como administrador.

### <a name="linux"></a>Linux
Usando o terminal, navegue até o caminho/service-fabric-dotnet-core-getting-started/Services/CounterService do diretório no qual o aplicativo foi clonado localmente.

Execute o comando a seguir para abrir VS Code como um usuário raiz para que o depurador possa ser anexado aos seus serviços.
```
sudo code . --user-data-dir='.'
```

O aplicativo agora deve aparecer no espaço de trabalho VS Code.

![Aplicativo de serviço de contador no espaço de trabalho](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Criar a aplicação
1. Pressione (Ctrl + Shift + p) para abrir a **paleta de comandos** no vs Code.
2. Pesquise e selecione o comando **Service Fabric: Compilar aplicativo** . A saída da compilação é enviada para o terminal integrado.

   ![Comando criar aplicativo no VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implantar o aplicativo no cluster local
Depois de criar o aplicativo, você pode implantá-lo no cluster local. 

1. Na **paleta de comandos**, selecione o **comando Service Fabric: implantar aplicativo (localhost)** . A saída do processo de instalação é enviada para o terminal integrado.

   ![Comando implantar aplicativo no VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implantação for concluída, inicie um navegador e abra Service Fabric Explorer: http:\//localhost: 19080/Explorer. Você deve ver que o aplicativo está em execução. Isso pode levar algum tempo, portanto, seja paciente. 

   ![Aplicativo de serviço de contador no Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Depois de verificar se o aplicativo está em execução, inicie um navegador e abra esta página: http:\//localhost: 31002. Este é o front-end da Web do aplicativo. Atualize a página para ver o valor atual do contador conforme ele é incrementado.

   ![Aplicativo de serviço de contador no navegador](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publicar o aplicativo em um cluster de Service Fabric do Azure
Juntamente com a implantação do aplicativo no cluster local, você também pode publicar o aplicativo em um cluster remoto Service Fabric do Azure. 

1. Verifique se você criou seu aplicativo usando as instruções acima. Atualize o arquivo de configuração gerado `Cloud.json` com os detalhes do cluster remoto no qual você deseja publicar.

2. Na **paleta de comandos**, selecione o **comando Service Fabric: publicar aplicativo**. A saída do processo de instalação é enviada para o terminal integrado.

   ![Comando publicar aplicativo no VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Quando a implantação for concluída, inicie um navegador e abra Service Fabric Explorer: `https:<clusterurl>:19080/Explorer`. Você deve ver que o aplicativo está em execução. Isso pode levar algum tempo, portanto, seja paciente. 

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar aplicativos no VS Code, o aplicativo deve estar em execução em um cluster local. Os pontos de interrupção podem ser adicionados ao código.

Para definir um ponto de interrupção e depurar, conclua as seguintes etapas:
1. No Explorer, abra o arquivo */src/CounterServiceApplication/CounterService/CounterService.cs* e defina um ponto de interrupção na linha 62 dentro do método `RunAsync`.
3. Clique no ícone de depuração na **barra de atividade** para abrir a exibição do depurador no vs Code. Clique no ícone de engrenagem na parte superior da exibição do depurador e selecione **.NET Core** no menu do ambiente suspenso. O arquivo launch. JSON é aberto. Você pode fechar este arquivo. Agora você deve ver opções de configuração no menu de configuração de depuração localizado ao lado do botão Executar (seta verde).

   ![Ícone de depuração no espaço de trabalho VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecione **anexação do .NET Core** no menu configuração de depuração.

   ![Ícone de depuração no espaço de trabalho VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Abra Service Fabric Explorer em um navegador: http:\//localhost: 19080/Explorer. Clique em **aplicativos** e faça uma busca detalhada para determinar o nó primário em que o serviço de manutenção está sendo executado. Na imagem abaixo, o nó primário para o com-mento de serviço é o nó 0.

   ![Nó primário para o serviço de manutenção](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. Em VS Code, clique no ícone executar (seta verde) ao lado da configuração de depuração **anexar do .NET Core** . Na caixa de diálogo seleção de processo, selecione o processo de serviço de manutenção em execução no nó primário que você identificou na etapa 4.

   ![Processo primário](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. O ponto de interrupção no arquivo *CounterService.cs* será atingido muito rapidamente. Em seguida, você pode explorar os valores das variáveis locais. Use a barra de ferramentas depurar na parte superior da VS Code para continuar a execução, passar sobre linhas, depurar métodos ou sair do método atual. 

   ![Valores de depuração](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Para encerrar a sessão de depuração, clique no ícone de plugue na barra de ferramentas de depuração na parte superior da VS Code.
   
   ![Desconectar do depurador](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Quando tiver terminado a depuração, você poderá usar o comando **Service Fabric: remover aplicativo** para remover o aplicativo de serviço de cluster do local. 

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar aplicativos Service Fabric Java com vs Code](./service-fabric-develop-java-applications-with-vs-code.md).



