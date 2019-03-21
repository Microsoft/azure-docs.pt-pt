---
title: Azure Service Fabric com o VS Code introdução | Documentos da Microsoft
description: Este artigo é uma descrição geral da criação de aplicações do Service Fabric com o Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: JimacoMS2
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: v-jamebr
ms.openlocfilehash: 197010271518347025157968012be31c71b3b667
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121031"
---
# <a name="service-fabric-for-visual-studio-code"></a>O Service Fabric para Visual Studio Code

O [extensão de serviços fiáveis do Service Fabric para o VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fornece as ferramentas necessárias para criar, compilar e depurar aplicações do Service Fabric em sistemas operativos Windows, Linux e macOS.

Este artigo fornece uma descrição geral dos requisitos e a configuração da extensão, bem como a utilização dos vários comandos que são fornecidos pela extensão de. 

> [!IMPORTANT]
> Aplicações de Java do Service Fabric podem ser desenvolvidas em máquinas do Windows, mas poderá ser implementadas em clusters do Linux do Azure apenas. Depuração de aplicações Java não é suportada no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Os seguintes pré-requisitos devem ser instalados em todos os ambientes.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Geradores do yeoman – instalar os geradores apropriados para a sua aplicação

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Os seguintes pré-requisitos tem de estar instalados para desenvolvimento Java:

* [SDK de Java](https://aka.ms/azure-jdks) (versão 1.8)
* [Gradle](https://gradle.org/install/)
* [Depurador para a extensão do Java VS Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) necessárias para depurar serviços Java. Depuração de serviços Java é suportado no Linux apenas. Pode instalar ao clicar no ícone de extensões no **barra de atividade** no VS Code e a pesquisa para a extensão ou do Marketplace do VS Code.

Os seguintes pré-requisitos tem de estar instalados para .NET Core /C# desenvolvimento:

* [.NET core](https://www.microsoft.com/net/learn/get-started) (versão 2.0.0 ou posterior)
* [C#para a extensão do Visual Studio Code (com tecnologia da omnisharp) VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) necessárias para depurar C# services. Pode instalar ao clicar no ícone de extensões no **barra de atividade** no VS Code e a pesquisa para a extensão ou do Marketplace do VS Code.

## <a name="setup"></a>Configurar

1. Código de VS aberto.
2. Clique no ícone de extensões no **barra de atividade** no lado esquerdo do VS Code. Procure "Service Fabric". Clique em **instalar** para a extensão do Service Fabric Reliable Services.

## <a name="commands"></a>Comandos
A extensão de serviços fiáveis do Service Fabric para o VS Code fornece muitos comandos para ajudar os desenvolvedores a criar e implementar projetos do Service Fabric. Pode chamar comandos a partir do **paleta de comandos** pressionando `(Ctrl + Shift + p)`, ao escrever o nome do comando na barra de entrada e selecionar o comando pretendido na lista de linha de comandos. 

* Recursos de infraestrutura do serviço: Criar Aplicação 
* Recursos de infraestrutura do serviço: Publicar aplicação 
* Recursos de infraestrutura do serviço: Implementar aplicação 
* Recursos de infraestrutura do serviço: Remover Aplicação  
* Recursos de infraestrutura do serviço: Criar aplicação 
* Recursos de infraestrutura do serviço: Limpar aplicação 

### <a name="service-fabric-create-application"></a>Recursos de infraestrutura do serviço: Criar Aplicação

O **do Service Fabric: Criar aplicação** comando cria uma nova aplicação de Service Fabric na sua área de trabalho atual. Dependendo de qual geradores do yeoman estão instalados no computador de desenvolvimento, pode criar vários tipos de aplicação do Service Fabric, incluindo Java, C#, contentores e projetos de convidado. 

1.  Selecione o **do Service Fabric: Adicionar serviço** comando
2.  Selecione o tipo para a sua nova aplicação de Service Fabric. 
3.  Introduza o nome da aplicação que pretende criar
3.  Selecione o tipo de serviço que pretende adicionar à sua aplicação do Service Fabric. 
4.  Siga as instruções para mencionar o serviço. 
5.  A nova aplicação de Service Fabric aparece na área de trabalho.
6.  Abra a nova pasta de aplicativo para que o torna-se a pasta de raiz na área de trabalho. Pode continuar a executar comandos a partir daqui.

### <a name="service-fabric-add-service"></a>Recursos de infraestrutura do serviço: Adicionar serviço
O **do Service Fabric: Adicionar serviço** comando adiciona um novo serviço a uma aplicação de Service Fabric existente. A aplicação que o serviço será adicionado ao tem de ser o diretório de raiz da área de trabalho. 

1.  Selecione o **do Service Fabric: Adicionar serviço** comando.
2.  Selecione o tipo da sua aplicação atual do Service Fabric. 
3.  Selecione o tipo de serviço que pretende adicionar à sua aplicação do Service Fabric. 
4.  Siga as instruções para mencionar o serviço. 
5.  O novo serviço é apresentado no seu diretório do projeto. 

### <a name="service-fabric-publish-application"></a>Recursos de infraestrutura do serviço: Publicar aplicação
O **do Service Fabric: Publicar aplicação** comando implementa a sua aplicação do Service Fabric num cluster remoto. O cluster de destino pode ser segura ou um cluster inseguro. Se parâmetros não estão definidos na cloud, a aplicação é implementada no cluster local.

1.  A primeira vez que o aplicativo for criado, um arquivo de cloud é gerado no diretório do projeto.
2.  Introduza os valores para o cluster que pretende ligar no ficheiro cloud.
3.  Selecione o **do Service Fabric: Publicar aplicação** comando.
4.  Ver o cluster de destino com o Service Fabric Explorer para confirmar que o aplicativo foi instalado. 

### <a name="service-fabric-deploy-application-localhost"></a>Recursos de infraestrutura do serviço: Implementar a aplicação (Localhost)
O **do Service Fabric: Implementar aplicação** comando implementa a sua aplicação do Service Fabric no seu cluster local. Certifique-se de que o seu cluster local está em execução antes de utilizar o comando. 

1. Selecione o **do Service Fabric: Implementar aplicação** comando
2. Ver o cluster local com o Service Fabric Explorer (http://localhost:19080/Explorer) para confirmar que o aplicativo foi instalado. Esta operação pode demorar algum tempo, por isso, seja paciente.
3. Também pode utilizar **Service Fabric: Publicar aplicação** comando sem parâmetros definido no ficheiro de cloud para implementar um cluster local.

> [!NOTE]
> Implementação de aplicações Java no cluster local não é suportada em máquinas do Windows.

### <a name="service-fabric-remove-application"></a>Recursos de infraestrutura do serviço: Remover Aplicação
O **do Service Fabric: Remover aplicação** comando remove uma aplicação do Service Fabric do cluster que foi anteriormente implementado para utilizar a extensão do VS Code. 

1.  Selecione o **do Service Fabric: Remover aplicação** comando.
2.  Ver o cluster com o Service Fabric Explorer para confirmar que o aplicativo foi removido. Esta operação pode demorar algum tempo, por isso, seja paciente.

### <a name="service-fabric-build-application"></a>Recursos de infraestrutura do serviço: Criar aplicação
O **do Service Fabric: Remover aplicativo** comando pode criar qualquer um dos Java ou C# aplicações do Service Fabric. 

1.  Certifique-se de que está na pasta raiz do aplicativo antes de executar este comando. O comando identifica o tipo de aplicação (C# ou Java) e cria a sua aplicação em conformidade.
2.  Selecione o **do Service Fabric: Criar aplicação** comando.
3.  O resultado do processo de compilação é escrito para o terminal integrado.

### <a name="service-fabric-clean-application"></a>Recursos de infraestrutura do serviço: Limpar aplicação
O **do Service Fabric: Limpar aplicação** comando exclui todos os ficheiros jar e bibliotecas nativas que foram geradas por compilação. Válido para apenas aplicações de Java. 

1.  Certifique-se de que está na pasta raiz do aplicativo antes de executar este comando. 
2.  Selecione o **do Service Fabric: Limpar aplicação** comando.
3.  O resultado do processo limpo é escrito para o terminal integrado.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [desenvolver e depurar C# aplicações do Service Fabric com o VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Saiba como [desenvolver e depurar aplicações de Java do Service Fabric com o VS Code](./service-fabric-develop-java-applications-with-vs-code.md).
