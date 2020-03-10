---
title: Como o Código visual do estúdio funciona com os espaços Azure Dev
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Saiba como visual Studio Code e Azure Dev Spaces ajudam a depurar e a iterar rapidamente as suas aplicações Kubernetes
keywords: Espaços Azure Dev, Espaços Dev, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: b6ab1330399ab2eb7afc9be61a7767a22ca82320
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942486"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como o Código visual do estúdio funciona com os espaços Azure Dev

Pode utilizar o Visual Studio Code e a [extensão Azure Dev Spaces][azds-extension] para preparar, executar e desinver os seus serviços com a Azure Dev Spaces. Com o Visual Studio Code e a extensão Azure Dev Spaces, pode:

* Gerar ativos para serviços de funcionamento e depuração na AKS
* Gerea os seus serviços Java, Node.js e .NET Core num espaço de v
* Depura diretamente os seus serviços Java, Node.js e .NET Core em funcionamento num espaço de v

## <a name="generate-assets"></a>Gerar ativos

O Visual Studio Code e a extensão Azure Dev Spaces geram os seguintes ativos para o seu projeto:

* Dockerfiles para aplicações Java utilizando aplicações Maven, Node.js e .NET Core aplicações
* Gráficos de leme para quase qualquer língua com um Dockerfile
* Um ficheiro `azds.yaml`, que é o ficheiro de [configuração do Azure Dev Spaces][azds-yaml] para o seu projeto
* Uma pasta `.vscode` com a configuração de lançamento do Visual Studio Code do seu projeto para aplicações Java utilizando aplicações Maven, Node.js e .NET Core

Os ficheiros Dockerfile, Helm e `azds.yaml` são os mesmos ativos gerados ao executar `azds prep`. Esses ficheiros também podem ser usados fora do código do Estúdio Visual para executar o seu projeto em AKS, tais como executar `azds up`. A pasta `.vscode` só é utilizada pelo código Visual Studio para executar o seu projeto em AKS a partir do Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Executar o seu serviço na AKS

Depois de gerar os ativos para o seu projeto, pode executar os seus serviços Java, Node.js e .NET Core num espaço de v existente a partir do Visual Studio Code. Na página *Debug* do Visual Studio Code, pode invocar a configuração de lançamento do diretório `.vscode` para executar o seu projeto.

Você deve criar o seu cluster AKS e ativar o Azure Dev Spaces no seu cluster fora do Visual Studio Code. Por exemplo, pode utilizar o Azure CLI ou o portal Azure para fazer esta configuração. Pode reutilizar os ficheiros Docker, Helm e `azds.yaml` ficheiros existentes criados fora do Visual Studio Code, tais como os ativos gerados pela execução `azds prep`. Se reutilizar os ativos gerados fora do Visual Studio Code, ainda precisa de ter um diretório `.vscode`. Este diretório `.vscode` pode ser regenerado pelo código Visual Studio e pela extensão do Azure Dev Spaces e não irá substituir os seus ativos existentes.

Para projetos .NET Core, tem de ter a [ C# extensão][csharp-extension] instalada para executar o seu serviço .NET a partir do Visual Studio Code. Também para os projetos Java que utilizam a Maven, deve ter a [extensão Java Debugger para Os Espaços Azure Dev][java-extension] instalada, bem como a [Maven instalada e configurada][maven] para executar o seu serviço Java a partir do Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Desinvejo o seu serviço na AKS

Depois de lançar o seu projeto, pode desmarcar os seus serviços Java, Node.js e .NET Core a funcionar num espaço de v diretamente do Visual Studio Code. A configuração de lançamento no diretório `.vscode` fornece a informação adicional de depuração para executar um serviço com depuração ativada num espaço de v. O Visual Studio Code também se liga ao processo de depuração no recipiente de funcionamento nos seus espaços de dev, permitindo-lhe definir pontos de rutura, inspecionar variáveis e realizar outras operações de depuração.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Use o Código de Estúdio Visual com Espaços Azure Dev

Pode ver o Visual Studio Code e a extensão Azure Dev Spaces a trabalhar com a Azure Dev Spaces nos seguintes arranques:

* [Iterado e depurado rapidamente com Código de Estúdio Visual e Java][quickstart-java]
* [Iterado e depurado rapidamente com Código de Estúdio Visual e .NET][quickstart-netcore]
* [Iterado e depurado rapidamente com Código de Estúdio Visual e Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
