---
title: Service Fabric do Azure com VS Code Introdução
description: Este artigo é uma visão geral da criação de aplicativos Service Fabric usando Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: d7d3182ad00d0ce151c6d327b29584c7e2ff1323
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457856"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric para Visual Studio Code

A [extensão de Reliable Services Service Fabric para vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fornece as ferramentas necessárias para criar, compilar e depurar aplicativos Service Fabric em sistemas operacionais Windows, Linux e MacOS.

Este artigo fornece uma visão geral dos requisitos e da configuração da extensão, bem como o uso dos vários comandos fornecidos pela extensão. 

> [!IMPORTANT]
> Service Fabric aplicativos Java podem ser desenvolvidos em computadores Windows, mas podem ser implantados somente em clusters Linux do Azure. Não há suporte para a depuração de aplicativos Java no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir devem ser instalados em todos os ambientes.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman Generators – instale os geradores apropriados para seu aplicativo

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Os seguintes pré-requisitos devem ser instalados para o desenvolvimento Java:

* [SDK do Java](https://aka.ms/azure-jdks) (versão 1,8)
* [Gradle](https://gradle.org/install/)
* [Depurador para extensão de vs Code Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Necessário para depurar os serviços Java. A depuração de serviços Java tem suporte apenas no Linux. Você pode instalar o clicando no ícone extensões na barra de **atividades** em vs Code e pesquisando a extensão ou no vs Code Marketplace.

Os seguintes pré-requisitos devem ser instalados para .NET Core/C# Development:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (versão 2.0.0 ou posterior)
* [para Visual Studio Code (de plataforma de OmniSharp) vs Code extensão C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Necessário para depurar C# serviços. Você pode instalar o clicando no ícone extensões na barra de **atividades** em vs Code e pesquisando a extensão ou no vs Code Marketplace.

## <a name="setup"></a>Configuração

1. Abra VS Code.
2. Clique no ícone extensões na **barra de atividade** no lado esquerdo da vs Code. Pesquise "Service Fabric". Clique em **instalar** para a extensão de Reliable Services de Service Fabric.

## <a name="commands"></a>Comandos
A extensão de Reliable Services Service Fabric para VS Code fornece muitos comandos para ajudar os desenvolvedores a criar e implantar projetos de Service Fabric. Você pode chamar comandos na **paleta de comandos** pressionando `(Ctrl + Shift + p)`, digitando o nome do comando na barra de entrada e selecionando o comando desejado na lista de prompts. 

* Service Fabric: criar aplicativo 
* Service Fabric: publicar aplicativo 
* Service Fabric: implantar aplicativo 
* Service Fabric: remover aplicativo  
* Service Fabric: Compilar aplicativo 
* Service Fabric: limpar aplicativo 

### <a name="service-fabric-create-application"></a>Service Fabric: criar aplicativo

O comando **Service Fabric: criar aplicativo** cria um novo aplicativo Service Fabric no seu espaço de trabalho atual. Dependendo de quais geradores Yeoman estão instalados em seu computador de desenvolvimento, você pode criar vários tipos de aplicativo Service Fabric, incluindo projetos Java C#, contêiner e convidado. 

1.  Selecione o comando **Service Fabric: Adicionar serviço**
2.  Selecione o tipo para o novo aplicativo Service Fabric. 
3.  Insira o nome do aplicativo que você deseja criar
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo aplicativo Service Fabric aparece no espaço de trabalho.
6.  Abra a nova pasta de aplicativo para que ela se torne a pasta raiz no espaço de trabalho. Você pode continuar executando comandos aqui.

### <a name="service-fabric-add-service"></a>Service Fabric: Adicionar serviço
O comando **Service Fabric: Add Service** adiciona um novo serviço a um aplicativo Service Fabric existente. O aplicativo ao qual o serviço será adicionado deve ser o diretório raiz do espaço de trabalho. 

1.  Selecione o comando **Service Fabric: Adicionar serviço** .
2.  Selecione o tipo de seu aplicativo de Service Fabric atual. 
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo serviço aparece no diretório do projeto. 

### <a name="service-fabric-publish-application"></a>Service Fabric: publicar aplicativo
O comando **Service Fabric: publicar aplicativo** implanta seu aplicativo Service Fabric em um cluster remoto. O cluster de destino pode ser um cluster seguro ou não seguro. Se os parâmetros não estiverem definidos em Cloud. JSON, o aplicativo será implantado no cluster local.

1.  Na primeira vez que o aplicativo é compilado, um arquivo Cloud. JSON é gerado no diretório do projeto.
2.  Insira os valores para o cluster ao qual você deseja se conectar no arquivo Cloud. JSON.
3.  Selecione o comando **Service Fabric: publicar aplicativo** .
4.  Exiba o cluster de destino com Service Fabric Explorer para confirmar que o aplicativo foi instalado. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: implantar aplicativo (localhost)
O comando **Service Fabric: implantar aplicativo** implanta o aplicativo Service Fabric no cluster local. Verifique se o cluster local está em execução antes de usar o comando. 

1. Selecione o comando **Service Fabric: implantar aplicativo**
2. Exiba o cluster local com Service Fabric Explorer (http:\//localhost: 19080/Explorer) para confirmar que o aplicativo foi instalado. Isso pode levar algum tempo, portanto, seja paciente.
3. Você também pode usar o comando **Service Fabric: publicar aplicativo** sem parâmetros definidos no arquivo Cloud. JSON para implantar em um cluster local.

> [!NOTE]
> Não há suporte para a implantação de aplicativos Java no cluster local em computadores Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: remover aplicativo
O comando **Service Fabric: remover aplicativo** remove um aplicativo Service Fabric do cluster em que foi implantado anteriormente usando a extensão vs Code. 

1.  Selecione o comando **Service Fabric: remover aplicativo** .
2.  Exiba o cluster com Service Fabric Explorer para confirmar que o aplicativo foi removido. Isso pode levar algum tempo, portanto, seja paciente.

### <a name="service-fabric-build-application"></a>Service Fabric: Compilar aplicativo
O comando **Service Fabric: Compilar aplicativo** pode criar aplicativos Java ou C# Service Fabric. 

1.  Verifique se você está na pasta raiz do aplicativo antes de executar este comando. O comando identifica o tipo de aplicativo (C# ou Java) e compila seu aplicativo de acordo.
2.  Selecione o comando **Service Fabric: Compilar aplicativo** .
3.  A saída do processo de compilação é gravada no terminal integrado.

### <a name="service-fabric-clean-application"></a>Service Fabric: limpar aplicativo
O comando **Service Fabric: limpar aplicativo** exclui todos os arquivos jar e bibliotecas nativas que foram gerados pela compilação. Válido somente para aplicativos Java. 

1.  Verifique se você está na pasta raiz do aplicativo antes de executar este comando. 
2.  Selecione o comando **Service Fabric: limpar aplicativo** .
3.  A saída do processo de limpeza é gravada no terminal integrado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar C# Service Fabric aplicativos com vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Saiba como [desenvolver e depurar aplicativos Service Fabric Java com vs Code](./service-fabric-develop-java-applications-with-vs-code.md).
