---
title: Como Visual Studio Code funciona com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Saiba como Visual Studio Code e Azure Dev Spaces ajudá-lo a depurar e iterar rapidamente seus aplicativos kubernetes
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: f7fbcdd36e9c0db74a71a50cb7cde44484e6c555
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75438388"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como Visual Studio Code funciona com Azure Dev Spaces

Você pode usar Visual Studio Code e a [extensão Azure dev Spaces][azds-extension] para preparar, executar e depurar seus serviços com o Azure dev Spaces. Com Visual Studio Code e a extensão de Azure Dev Spaces, você pode:

* Gerar ativos para executar e depurar serviços no AKS
* Executar seus serviços Java, Node. js e .NET Core em um espaço de desenvolvimento
* Depure diretamente seus serviços Java, Node. js e .NET Core em execução em um espaço de desenvolvimento

## <a name="generate-assets"></a>Gerar ativos

Visual Studio Code e a extensão Azure Dev Spaces geram os seguintes ativos para seu projeto:

* Dockerfiles para aplicativos Java usando Maven, aplicativos node. js e aplicativos .NET Core
* Gráficos Helm para quase qualquer linguagem com um Dockerfile
* Um arquivo de `azds.yaml`, que é o [arquivo de configuração de Azure dev Spaces][azds-yaml] para seu projeto
* Uma pasta `.vscode` com o Visual Studio Code iniciar a configuração do seu projeto para aplicativos Java usando Maven, aplicativos node. js e aplicativos .NET Core

Os arquivos Dockerfile, Helm Chart e `azds.yaml` são os mesmos ativos gerados durante a execução de `azds prep`. Esses arquivos também podem ser usados fora do Visual Studio Code para executar seu projeto no AKS, como a execução de `azds up`. A pasta `.vscode` só é usada pelo Visual Studio Code para executar seu projeto no AKS do Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Execute seu serviço no AKS

Depois de gerar os ativos para seu projeto, você pode executar os serviços Java, Node. js e .NET Core em um espaço de desenvolvimento existente do Visual Studio Code. Na página de *depuração* do Visual Studio Code, você pode invocar a configuração de inicialização do diretório `.vscode` para executar o projeto.

Você deve criar o cluster AKS e habilitar Azure Dev Spaces em seu cluster fora do Visual Studio Code. Por exemplo, você pode usar o CLI do Azure ou o portal do Azure para fazer essa configuração. Você pode reutilizar Dockerfiles existentes, gráficos do Helm e arquivos `azds.yaml` criados fora do Visual Studio Code, como os ativos gerados pela execução de `azds prep`. Se você reutilizar ativos gerados fora do Visual Studio Code, ainda precisará ter um diretório `.vscode`. Esse `.vscode` Directory pode ser regenerado pelo Visual Studio Code e pela extensão Azure Dev Spaces e não substituirá os ativos existentes.

Para projetos do .NET Core, você deve ter a [ C# extensão][csharp-extension] instalada para executar o serviço .net do Visual Studio Code. Além disso, para projetos Java usando o Maven, você deve ter o [depurador Java para Azure dev Spaces extensão][java-extension] instalada, bem como o [Maven instalado e configurado][maven] para executar o serviço Java do Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Depurar seu serviço no AKS

Depois de iniciar o projeto, você pode depurar os serviços Java, Node. js e .NET Core em execução em um espaço de desenvolvimento diretamente do Visual Studio Code. A configuração de inicialização no diretório `.vscode` fornece informações adicionais de depuração para executar um serviço com depuração habilitada em um espaço de desenvolvimento. O Visual Studio Code também é anexado ao processo de depuração no contêiner em execução em seus espaços de desenvolvimento, permitindo que você defina pontos de interrupção, inspecione variáveis e execute outras operações de depuração.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Usar Visual Studio Code com Azure Dev Spaces

Você pode ver Visual Studio Code e a extensão de Azure Dev Spaces trabalhando com Azure Dev Spaces nos guias de início rápido a seguir:

* [Iterar e depurar rapidamente com Visual Studio Code e Java][quickstart-java]
* [Iterar e depurar rapidamente com o Visual Studio Code e o .NET][quickstart-netcore]
* [Iterar e depurar rapidamente com Visual Studio Code e node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
