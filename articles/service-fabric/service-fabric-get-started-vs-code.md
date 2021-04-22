---
title: Tecido de serviço Azure com código VS começando
description: Este artigo é uma visão geral da criação de aplicações de Tecido de Serviço usando o Código do Estúdio Visual.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: 7d54b4b048632324a58708f893a4778a56137916
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876084"
---
# <a name="service-fabric-for-visual-studio-code"></a>Tecido de serviço para código de estúdio visual

A [extensão de Serviços Fiáveis de Serviços de Tecido de Serviço para O Código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fornece as ferramentas necessárias para criar, construir e depurar aplicações de tecido de serviço nos sistemas operativos Windows, Linux e macOS.

Este artigo fornece uma visão geral dos requisitos e configuração da extensão, bem como a utilização dos vários comandos fornecidos pela extensão. 

> [!IMPORTANT]
> As aplicações de Service Fabric Java podem ser desenvolvidas em máquinas Windows, mas podem ser implementadas apenas em clusters Azure Linux. As aplicações de depuragem java não são suportadas no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Devem ser instalados em todos os ambientes os seguintes pré-requisitos.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK de tecido de serviço](./service-fabric-get-started.md)
* Geradores Yeoman - instale os geradores apropriados para a sua aplicação

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Devem ser instalados os seguintes pré-requisitos para o desenvolvimento de Java:

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (versão 1.8)
* [Gradle](https://gradle.org/install/)
* [Debugger para extensão do Código Java VS](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Precisava de depurar os serviços java. Os serviços de depuragem java são suportados apenas no Linux. Pode instalar-se clicando no ícone Extensões na Barra de **Atividade** no Código VS e procurando a extensão, ou a partir do Mercado de Código VS.

Os seguintes pré-requisitos devem ser instalados para o desenvolvimento .NET Core/C#:

* [.NET Core](https://dotnet.microsoft.com/download) (versão 2.0.0 ou posterior)
* [C# para Código de Estúdio Visual (alimentado por OmniSharp) extensão do código VS](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Precisava de depurar os serviços C#. Pode instalar-se clicando no ícone Extensões na Barra de **Atividade** no Código VS e procurando a extensão, ou a partir do Mercado de Código VS.

## <a name="setup"></a>Configuração

1. Abra o VS Code.
2. Clique no ícone Extensões na **Barra de Atividade** no lado esquerdo do Código VS. Procure por "Service Fabric". Clique **em Instalar** para a extensão de Serviços Fiáveis de Tecido de Serviço.

## <a name="commands"></a>Comandos
A extensão de Serviços Fiáveis de Tecido de Serviço para O Código VS fornece muitos comandos para ajudar os desenvolvedores a criar e implementar projetos de Tecido de Serviço. Pode chamar os comandos da Paleta de **Comando** premindo, `(Ctrl + Shift + p)` digitando o nome de comando na barra de entrada e selecionando o comando pretendido a partir da lista de solicitação. 

* Tecido de serviço: Criar aplicação 
* Tecido de Serviço: Publicar Aplicação 
* Tecido de serviço: Implementação de aplicação 
* Tecido de serviço: Remover aplicação  
* Tecido de serviço: Aplicação de construção 
* Tecido de serviço: Aplicação limpa 

### <a name="service-fabric-create-application"></a>Tecido de serviço: Criar aplicação

O **Tecido de Serviço: Criar** o comando De Aplicação cria uma nova aplicação de Tecido de Serviço no seu espaço de trabalho atual. Dependendo dos geradores yeoman instalados na sua máquina de desenvolvimento, pode criar vários tipos de aplicações de Tecido de Serviço, incluindo projetos java, C#, Contentor e Guest. 

1.  Selecione o **Tecido de Serviço: Criar** comando de aplicação
2.  Selecione o tipo para a sua nova aplicação De Tecido de Serviço. 
3.  Insira o nome da aplicação que pretende criar
3.  Selecione o tipo de serviço que pretende adicionar à sua aplicação Service Fabric. 
4.  Siga as instruções para nomear o serviço. 
5.  A nova aplicação Service Fabric aparece no espaço de trabalho.
6.  Abra a nova pasta de aplicação de modo a que se torne a pasta raiz no espaço de trabalho. Pode continuar a executar comandos daqui.

### <a name="service-fabric-add-service"></a>Tecido de serviço: Adicionar Serviço
O **comando Serviço: Adicionar** o comando adiciona um novo serviço a uma aplicação de Tecido de Serviço existente. A aplicação a que o serviço será adicionado deve ser o diretório de raiz do espaço de trabalho. 

1.  Selecione o **Tecido de Serviço: Adicione** o comando de serviço.
2.  Selecione o tipo da sua aplicação atual de Tecido de Serviço. 
3.  Selecione o tipo de serviço que pretende adicionar à sua aplicação Service Fabric. 
4.  Siga as instruções para nomear o serviço. 
5.  O novo serviço aparece no seu diretório de projeto. 

### <a name="service-fabric-publish-application"></a>Tecido de Serviço: Publicar Aplicação
O **Tecido de Serviço: O** comando de aplicação de publicação implementa a sua aplicação De Tecido de Serviço num cluster remoto. O cluster alvo pode ser um aglomerado seguro ou inseguro. Se os parâmetros não forem definidos em Cloud.js, a aplicação é implantada no cluster local.

1.  A primeira vez que a aplicação é construída, uma Cloud.jsem arquivo é gerada no diretório do projeto.
2.  Insira os valores do cluster a que gostaria de ligar no Cloud.jsficheiro.
3.  Selecione o Tecido de Serviço: Publicar o comando **de aplicação.**
4.  Consulte o cluster alvo com o Service Fabric Explorer para confirmar que a aplicação foi instalada. 

### <a name="service-fabric-deploy-application-localhost"></a>Tecido de serviço: Aplicação de implantação (localidade)
O Tecido de Serviço: O comando **de aplicação de implementação** implementa a sua aplicação de Tecido de Serviço para o seu cluster local. Certifique-se de que o seu cluster local está a funcionar antes de utilizar o comando. 

1. Selecione o **Tecido de Serviço: Implementar** o comando de aplicação
2. Consulte o cluster local com o Service Fabric Explorer (http: \/ /localhost:19080/Explorer) para confirmar que a aplicação foi instalada. Isto pode levar algum tempo, por isso, seja paciente.
3. Também pode utilizar **o Service Fabric: Publicar** o comando de aplicação sem parâmetros definidos no Cloud.jsno ficheiro para implantar num cluster local.

> [!NOTE]
> A implementação de aplicações Java no cluster local não é suportada em máquinas Windows.

### <a name="service-fabric-remove-application"></a>Tecido de serviço: Remover aplicação
O **tecido de serviço: Remover o** comando de aplicação remove uma aplicação de Tecido de Serviço do cluster que foi previamente implantada para usar a extensão do Código VS. 

1.  Selecione o **Tecido de Serviço: Remova o comando de aplicação.**
2.  Consulte o cluster com o Service Fabric Explorer para confirmar que a aplicação foi removida. Isto pode levar algum tempo, por isso, seja paciente.

### <a name="service-fabric-build-application"></a>Tecido de serviço: Aplicação de construção
O Tecido de Serviço: O comando **de aplicação de construção** pode construir aplicações java ou C# Service Fabric. 

1.  Certifique-se de que está na pasta raiz da aplicação antes de executar este comando. O comando identifica o tipo de aplicação (C# ou Java) e constrói a sua aplicação em conformidade.
2.  Selecione o **Tecido de Serviço: Constroem** o comando de aplicação.
3.  A saída do processo de construção é escrita para o terminal integrado.

### <a name="service-fabric-clean-application"></a>Tecido de serviço: Aplicação limpa
O **comando 'Service Fabric: Clean Application'** elimina todos os ficheiros de frascos e bibliotecas nativas que foram gerados pela construção. Válido apenas para aplicações Java. 

1.  Certifique-se de que está na pasta raiz da aplicação antes de executar este comando. 
2.  Selecione o **Tecido de Serviço: Comando de aplicação limpa.**
3.  A saída do processo de limpeza é escrita para o terminal integrado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e depurar aplicações de tecido de serviço C# com código VS](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Saiba como [desenvolver e depurar aplicações de Tecido de Serviço Java com código VS.](./service-fabric-develop-java-applications-with-vs-code.md)