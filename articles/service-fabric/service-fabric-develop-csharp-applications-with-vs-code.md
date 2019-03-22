---
title: Desenvolver aplicações de .NET Core do Azure Service Fabric com o Visual Studio Code | Documentos da Microsoft
description: Este artigo mostra como criar, implementar e depurar aplicações de .NET Core do Service Fabric com o Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 9da735f10063649222a38498af17e0404137a706
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315356"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Desenvolva C# aplicações do Service Fabric com o Visual Studio Code

O [extensão de serviços fiáveis do Service Fabric para o VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) facilita a criação de aplicações do Service Fabric do .NET Core em sistemas operativos Windows, Linux e macOS.

Este artigo mostra-lhe como criar, implementar e depurar uma aplicação .NET Core do Service Fabric com o Visual Studio Code.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que já tem instalado o VS Code, a extensão de serviços fiáveis do Service Fabric para o VS Code e todas as dependências necessárias para o seu ambiente de desenvolvimento. Para obter mais informações, consulte [introdução ao](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Transferir o exemplo
Este artigo utiliza a aplicação CounterService na [repositório do GitHub de exemplos de introdução do Service Fabric .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Para clonar o repositório para o seu computador de desenvolvimento, execute o seguinte comando a partir de uma janela de terminal (janela de comando no Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Abra a aplicação no VS Code

### <a name="windows"></a>Windows
Faça duplo clique no ícone do VS Code no Menu Iniciar e escolher **executar como administrador**. Para anexar o depurador aos seus serviços, terá de executar o VS Code como administrador.

### <a name="linux"></a>Linux
Utilizar o terminal, navegue para o caminho /service-fabric-dotnet-core-getting-started/Services/CounterService do diretório ao qual a aplicação foi clonada em localmente.

Execute o seguinte comando para abrir o VS Code como um utilizador de raiz, para que o depurador pode ser anexado aos seus serviços.
```
sudo code . --user-data-dir='.'
```

A aplicação deverá agora aparecer na sua área de trabalho do VS Code.

![Aplicação de serviço de contador na área de trabalho](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Criar a aplicação
1. Prima (Ctrl + Shift + p) para abrir o **paleta de comandos** no VS Code.
2. Procure e selecione o **Service Fabric: Criar aplicação** comando. O resultado de compilação é enviado para o terminal integrado.

   ![Criar comando de aplicação no VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Implementar a aplicação no cluster local
Depois de criar a aplicação, pode implementá-la no cluster local. 

1. Partir do **paleta de comandos**, selecione o **Service Fabric: Implementar o comando de aplicação (Localhost)**. O resultado do processo de instalação é enviado para o terminal integrado.

   ![Implementar o comando de aplicação no VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Quando a implementação estiver concluída, inicie um browser e abra o Service Fabric Explorer: http:\//localhost:19080 / Explorer. Deverá ver que a aplicação está em execução. Esta operação pode demorar algum tempo, por isso, seja paciente. 

   ![Aplicação de serviço de contador no Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Depois de verificar a aplicação está em execução, inicie um browser e abra a página: http:\//localhost:31002. Esta é a web front-end da aplicação. Atualize a página para ver o valor atual do contador de como um incremento.

   ![Aplicação de serviço de contador no Browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="debug-the-application"></a>Depurar a aplicação
Ao depurar aplicativos no VS Code, a aplicação tem de estar em execução num local cluster. Pontos de interrupção, em seguida, podem ser adicionados ao código.

Para definir um ponto de interrupção e a depuração, conclua os seguintes passos:
1. No Explorer, abra a */src/CounterServiceApplication/CounterService/CounterService.cs* de ficheiros e defina um ponto de interrupção na linha 62 dentro do `RunAsync` método.
3. Clique no ícone de depuração no **barra de atividade** para abrir o modo de exibição do depurador no VS Code. Clique no ícone de engrenagem na parte superior da exibição do depurador e selecione **.NET Core** no menu de ambiente de lista pendente. Abre o ficheiro Launch JSON. Pode fechar este ficheiro. Agora deve ver opções de configuração no menu de configuração de depuração localizado junto ao botão de execução (seta verde).

   ![Depurar o ícone na área de trabalho de código de VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecione **anexar do .NET Core** no menu de configuração de depuração.

   ![Depurar o ícone na área de trabalho de código de VS](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Abra o Service Fabric Explorer num navegador: http:\//localhost:19080 / Explorer. Clique em **aplicativos** e a desagregar para determinar que o CounterService está em execução no nó principal. Na imagem abaixo do nó primário para o CounterService é nó 0.

   ![Nó principal para CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. No VS Code, clique no ícone de execução (seta verde) junto a **anexar do .NET Core** configuração de depuração. Na caixa de diálogo de seleção de processo, selecione o processo CounterService que está em execução no nó principal que identificou no passo 4.

   ![Processo principal](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Do ponto de interrupção a *CounterService.cs* ficheiros estarão pressionados muito rapidamente. Em seguida, pode explorar os valores das variáveis locais. Utilize a barra de ferramentas de depuração na parte superior do VS Code para continuar a execução, passo em linhas, passo em métodos, ou passo fora o método atual. 

   ![Valores de depuração](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Para terminar a sessão de depuração, clique no ícone de plug na barra de ferramentas de depuração na parte superior do código de VS....
   
   ![Desligar do depurador](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Quando tiver concluído a depuração, pode usar o **Service Fabric: Remover aplicação** comando para remover a aplicação CounterService do seu cluster local. 

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [desenvolver e depurar aplicações de Java do Service Fabric com o VS Code](./service-fabric-develop-java-applications-with-vs-code.md).



