---
title: Service Fabric do Azure com VS Code Introdução | Microsoft Docs
description: Este artigo é uma visão geral da criação de aplicativos Service Fabric usando Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 9662ebd26a263fa006c8fccf877fdc950e9014c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102964"
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

## <a name="setup"></a>Configurar

1. Abra VS Code.
2. Clique no ícone extensões na **barra de atividade** no lado esquerdo da vs Code. Pesquise "Service Fabric". Clique em **instalar** para a extensão de Reliable Services de Service Fabric.

## <a name="commands"></a>Comandos
A extensão de Reliable Services Service Fabric para VS Code fornece muitos comandos para ajudar os desenvolvedores a criar e implantar projetos de Service Fabric. Você pode chamar comandos na **paleta de comandos** pressionando `(Ctrl + Shift + p)`, digitando o nome do comando na barra de entrada e selecionando o comando desejado na lista de prompts. 

* Service Fabric: Criar Aplicação 
* Service Fabric: Publicar aplicativo 
* Service Fabric: Implantar aplicativo 
* Service Fabric: Remover Aplicação  
* Service Fabric: Compilar aplicativo 
* Service Fabric: Limpar aplicativo 

### <a name="service-fabric-create-application"></a>Service Fabric: Criar Aplicação

O **Service Fabric: O comando** criar aplicativo cria um novo aplicativo Service Fabric no seu espaço de trabalho atual. Dependendo de quais geradores Yeoman estão instalados em seu computador de desenvolvimento, você pode criar vários tipos de aplicativo Service Fabric, incluindo projetos Java C#, contêiner e convidado. 

1.  Selecione o **Service Fabric: Adicionar comando** de serviço
2.  Selecione o tipo para o novo aplicativo Service Fabric. 
3.  Insira o nome do aplicativo que você deseja criar
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo aplicativo Service Fabric aparece no espaço de trabalho.
6.  Abra a nova pasta de aplicativo para que ela se torne a pasta raiz no espaço de trabalho. Você pode continuar executando comandos aqui.

### <a name="service-fabric-add-service"></a>Service Fabric: Adicionar serviço
O **Service Fabric: O comando** adicionar serviço adiciona um novo serviço a um aplicativo Service Fabric existente. O aplicativo ao qual o serviço será adicionado deve ser o diretório raiz do espaço de trabalho. 

1.  Selecione o **Service Fabric: Adicionar comando** de serviço.
2.  Selecione o tipo de seu aplicativo de Service Fabric atual. 
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo serviço aparece no diretório do projeto. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publicar aplicativo
O **Service Fabric: O comando** publicar aplicativo implanta seu aplicativo Service Fabric em um cluster remoto. O cluster de destino pode ser um cluster seguro ou não seguro. Se os parâmetros não estiverem definidos em Cloud. JSON, o aplicativo será implantado no cluster local.

1.  Na primeira vez que o aplicativo é compilado, um arquivo Cloud. JSON é gerado no diretório do projeto.
2.  Insira os valores para o cluster ao qual você deseja se conectar no arquivo Cloud. JSON.
3.  Selecione o **Service Fabric: Comando publicar** aplicativo.
4.  Exiba o cluster de destino com Service Fabric Explorer para confirmar que o aplicativo foi instalado. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Implantar aplicativo (localhost)
O **Service Fabric: O comando** implantar aplicativo implanta seu aplicativo Service Fabric em seu cluster local. Verifique se o cluster local está em execução antes de usar o comando. 

1. Selecione o **Service Fabric: Comando implantar** aplicativo
2. Exiba o cluster local com Service Fabric Explorer (http:\//localhost: 19080/Explorer) para confirmar que o aplicativo foi instalado. Isso pode levar algum tempo, portanto, seja paciente.
3. Você também pode usar **Service Fabric: Publicar o** comando do aplicativo sem parâmetros definidos no arquivo Cloud. JSON para implantar em um cluster local.

> [!NOTE]
> Não há suporte para a implantação de aplicativos Java no cluster local em computadores Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Remover Aplicação
O **Service Fabric: O comando** remover aplicativo remove um aplicativo Service Fabric do cluster em que ele foi implantado anteriormente usando a extensão vs Code. 

1.  Selecione o **Service Fabric: Comando Remover** aplicativo.
2.  Exiba o cluster com Service Fabric Explorer para confirmar que o aplicativo foi removido. Isso pode levar algum tempo, portanto, seja paciente.

### <a name="service-fabric-build-application"></a>Service Fabric: Compilar aplicativo
O **Service Fabric: O comando** compilar aplicativo pode criar aplicativos Java C# ou Service Fabric. 

1.  Verifique se você está na pasta raiz do aplicativo antes de executar este comando. O comando identifica o tipo de aplicativo (C# ou Java) e compila seu aplicativo de acordo.
2.  Selecione o **Service Fabric: Comando compilar** aplicativo.
3.  A saída do processo de compilação é gravada no terminal integrado.

### <a name="service-fabric-clean-application"></a>Service Fabric: Limpar aplicativo
O **Service Fabric: O comando** limpar aplicativo exclui todos os arquivos jar e bibliotecas nativas que foram gerados pela compilação. Válido somente para aplicativos Java. 

1.  Verifique se você está na pasta raiz do aplicativo antes de executar este comando. 
2.  Selecione o **Service Fabric: Comando limpar** aplicativo.
3.  A saída do processo de limpeza é gravada no terminal integrado.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [desenvolver e depurar C# Service Fabric aplicativos com vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Saiba como [desenvolver e depurar aplicativos Service Fabric Java com vs Code](./service-fabric-develop-java-applications-with-vs-code.md).
