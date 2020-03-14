---
title: Tecido de serviço Azure com código VS começando
description: Este artigo é uma visão geral da criação de aplicações de Tecido de Serviço utilizando o Código do Estúdio Visual.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 17bff459a0399d39c6bfdd772ad16d0b5b2f6771
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79258489"
---
# <a name="service-fabric-for-visual-studio-code"></a>Tecido de serviço para código de estúdio visual

A extensão de [Serviço sintetizador de Serviços Fiáveis para código VS](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fornece as ferramentas necessárias para criar, construir e depurar aplicações de tecido de serviço de depuração nos sistemas operativos Windows, Linux e macOS.

Este artigo fornece uma visão geral dos requisitos e configuração da extensão, bem como a utilização dos vários comandos fornecidos pela extensão. 

> [!IMPORTANT]
> As aplicações de Tecido de Serviço Java podem ser desenvolvidas em máquinas Windows, mas podem ser implantadas apenas em clusters Azure Linux. As aplicações Debugging Java não são suportadas no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Devem ser instalados os seguintes pré-requisitos em todos os ambientes.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK de tecido de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman Generators - instale os geradores apropriados para a sua aplicação

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Devem ser instalados os seguintes pré-requisitos para o desenvolvimento de Java:

* [Java SDK](https://aka.ms/azure-jdks) (versão 1.8)
* [Gradle](https://gradle.org/install/)
* [Debugger para extensão do Código Java VS](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Precisava de depurar os serviços de Java. Os serviços de Debugging Java são suportados apenas no Linux. Pode instalar-se clicando no ícone das Extensões na Barra de **Atividade** sem Código VS e procurando a extensão, ou a partir do Mercado de Código VS.

Devem ser instalados os seguintes pré-requisitos para o .NET Core/desenvolvimento:C#

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (versão 2.0.0 ou posterior)
* [para Código de Estúdio Visual (alimentado por OmniSharp) extensão do código VS C# ](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Precisava de C# depurar serviços. Pode instalar-se clicando no ícone das Extensões na Barra de **Atividade** sem Código VS e procurando a extensão, ou a partir do Mercado de Código VS.

## <a name="setup"></a>Configurar

1. Código VS aberto.
2. Clique no ícone de Extensões na Barra de **Atividade** no lado esquerdo do Código VS. Pesquisa por "Tecido de Serviço". Clique em **instalar** para a extensão de serviço de serviço de serviço fiável serviços.

## <a name="commands"></a>Comandos
A extensão de Serviço sintetizador de Serviços Fiáveis para código VS fornece muitos comandos para ajudar os desenvolvedores a criar e implementar projetos de Tecido de Serviço. Pode ligar para comandos da Paleta de **Comando,** pressionando `(Ctrl + Shift + p)`, digitando o nome de comando na barra de entrada e selecionando o comando desejado a partir da lista de pronta. 

* Tecido de serviço: Criar aplicação 
* Tecido de Serviço: Publicar Aplicação 
* Tecido de serviço: Aplicação de implantação 
* Tecido de serviço: Remover aplicação  
* Tecido de serviço: Aplicação de construção 
* Tecido de serviço: Aplicação limpa 

### <a name="service-fabric-create-application"></a>Tecido de serviço: Criar aplicação

O **Tecido de Serviço: Criar** o comando de aplicação cria uma nova aplicação de Tecido de Serviço no seu espaço de trabalho atual. Dependendo dos geradores yeoman instalados na sua máquina de desenvolvimento, pode criar C#vários tipos de aplicação de Tecido de Serviço, incluindo Java, Contentor e Projetos De Hóspedes. 

1.  Selecione o Tecido de Serviço: Criar comando **de aplicação**
2.  Selecione o tipo para a sua nova aplicação Service Fabric. 
3.  Insira o nome da aplicação que pretende criar
3.  Selecione o tipo de serviço que pretende adicionar à sua aplicação Service Fabric. 
4.  Siga as instruções para nomear o serviço. 
5.  A nova aplicação Service Fabric aparece no espaço de trabalho.
6.  Abra a nova pasta de aplicações para que se torne a pasta raiz no espaço de trabalho. Podecontinuar a executar comandos daqui.

### <a name="service-fabric-add-service"></a>Tecido de serviço: Adicionar serviço
O Serviço **Tecido: Adicionar** o comando de serviço adiciona um novo serviço a uma aplicação de Tecido de Serviço existente. A aplicação a que o serviço será adicionado deve ser o diretório raiz do espaço de trabalho. 

1.  Selecione o **Tecido de Serviço: Adicionar** comando de serviço.
2.  Selecione o tipo da sua aplicação de Tecido de Serviço atual. 
3.  Selecione o tipo de serviço que pretende adicionar à sua aplicação Service Fabric. 
4.  Siga as instruções para nomear o serviço. 
5.  O novo serviço aparece no seu diretório de projeto. 

### <a name="service-fabric-publish-application"></a>Tecido de Serviço: Publicar Aplicação
O Serviço **Tecido: Publicar** o comando aplicação implanta a sua aplicação Service Fabric num cluster remoto. O aglomerado de alvos pode ser um aglomerado seguro ou inseguro. Se os parâmetros não estiverem definidos em Cloud.json, a aplicação é implantada para o cluster local.

1.  A primeira vez que a aplicação é construída, um ficheiro Cloud.json é gerado no diretório do projeto.
2.  Insera os valores para o cluster a que gostaria de ligar no ficheiro Cloud.json.
3.  Selecione o Tecido de Serviço: Publicar comando **de aplicação.**
4.  Consulte o cluster alvo com o Service Fabric Explorer para confirmar que a aplicação foi instalada. 

### <a name="service-fabric-deploy-application-localhost"></a>Tecido de serviço: Aplicação de implantação (local)
O Serviço **Tecido: Acionar** o comando de aplicação implementa a sua aplicação De Tecido de Serviço para o seu cluster local. Certifique-se de que o seu aglomerado local está a funcionar antes de utilizar o comando. 

1. Selecione o Tecido de **Serviço: Comando de Aplicação de implementação**
2. Consulte o cluster local com o Service Fabric Explorer (http:\//localhost:19080/Explorer) para confirmar que a aplicação foi instalada. Isto pode levar algum tempo, por isso tenha paciência.
3. Também pode utilizar **o Serviço Fabric: Publicar** o comando da aplicação sem parâmetros definidos no ficheiro Cloud.json para implantar num cluster local.

> [!NOTE]
> A implementação de aplicações Java para o cluster local não é suportada nas máquinas Windows.

### <a name="service-fabric-remove-application"></a>Tecido de serviço: Remover aplicação
O **Tecido de Serviço: Remover** o comando de aplicação remove uma aplicação de tecido de serviço do cluster que foi previamente implantado para utilizar a extensão do Código VS. 

1.  Selecione o Tecido de Serviço: Remover o comando **da aplicação.**
2.  Consulte o cluster com o Service Fabric Explorer para confirmar que a aplicação foi removida. Isto pode levar algum tempo, por isso tenha paciência.

### <a name="service-fabric-build-application"></a>Tecido de serviço: Aplicação de construção
O serviço Fabric: O comando de C# **aplicação de construção** pode construir aplicações Java ou Service Fabric. 

1.  Certifique-se de que está na pasta raiz da aplicação antes de executar este comando. O comando identifica o tipoC# de aplicação (ou Java) e constrói a sua aplicação em conformidade.
2.  Selecione o Tecido de **Serviço: Comando de Aplicação de Construção.**
3.  A saída do processo de construção está escrita para o terminal integrado.

### <a name="service-fabric-clean-application"></a>Tecido de serviço: Aplicação limpa
O comando **Service Fabric: Clean Application** elimina todos os ficheiros de frascos e bibliotecas nativas que foram gerados pela construção. Válido apenas para aplicações Java. 

1.  Certifique-se de que está na pasta raiz da aplicação antes de executar este comando. 
2.  Selecione o tecido de **serviço: comando de aplicação limpa.**
3.  A saída do processo limpo está escrita no terminal integrado.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [desenvolver e C# depurar aplicações](./service-fabric-develop-csharp-applications-with-vs-code.md)de tecido de serviço com código VS .
* Aprenda a [desenvolver e depurar aplicações](./service-fabric-develop-java-applications-with-vs-code.md)java service fabric com código VS .
