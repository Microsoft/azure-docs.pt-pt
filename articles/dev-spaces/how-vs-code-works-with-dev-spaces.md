---
title: Como funciona o Visual Studio Code com espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Como funciona o Visual Studio Code com espaços de desenvolvimento do Azure
keywords: Espaços de desenvolvimento do Azure, os espaços de desenvolvimento, Docker, Kubernetes, Azure, AKS, serviço Kubernetes do Azure, contentores
ms.openlocfilehash: a7ec20908b75ae07532c16daab8950ace9cd67ae
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712153"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como funciona o Visual Studio Code com espaços de desenvolvimento do Azure

Pode usar o Visual Studio Code e o [extensão do Azure Dev espaços][azds-extension] para preparar, executar e depurar seus serviços com os espaços de desenvolvimento do Azure. Com o Visual Studio Code e a extensão de espaços de desenvolvimento do Azure, pode:

* Gerar ativos para em execução e depuração de serviços no AKS
* Executar os serviços de Java, node. js e .NET Core num espaço de desenvolvimento
* Depurar diretamente os seus serviços de Java, node. js e .NET Core em execução num espaço de desenvolvimento

## <a name="generate-assets"></a>Gerar ativos

Visual Studio Code e a extensão de espaços de desenvolvimento do Azure geram os recursos seguintes para o seu projeto:

* Dockerfiles para aplicações de Java com o Maven, aplicações node. js e aplicações de .NET Core
* Gráficos do Helm para quase qualquer linguagem com um Dockerfile
* Uma `azds.yaml` arquivo, que é o [ficheiro de configuração de espaços de desenvolvimento do Azure][azds-yaml] para o seu projeto
* A `.vscode` pasta com a configuração de lançamento do Visual Studio Code do seu projeto para aplicações de Java com o Maven, aplicações node. js e aplicações de .NET Core

O Dockerfile, o gráfico Helm, e `azds.yaml` arquivos são os mesmo ativos gerados durante a execução `azds prep`. Esses arquivos também podem ser utilizados fora de código do Visual Studio para executar o seu projeto no AKS, por exemplo, a execução `azds up`. O `.vscode` pasta só é utilizada pelo código do Visual Studio para executar o seu projeto no AKS a partir do Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Executar o serviço no AKS

Depois de gerar os ativos para o seu projeto, pode executar os serviços de Java, node. js e .NET Core num espaço de desenvolvimento existente do Visual Studio Code. No *depurar* página do Visual Studio Code, é possível invocar a configuração de inicialização a partir do `.vscode` diretório para executar o seu projeto.

Tem de criar o cluster do AKS e ativar os espaços de desenvolvimento do Azure no seu cluster fora do Visual Studio Code. Por exemplo, pode utilizar a CLI do Azure ou o portal do Azure para fazer esta configuração. É possível reutilizar Dockerfiles existente, gráficos do Helm, e `azds.yaml` arquivos criados fora do Visual Studio Code, por exemplo, os ativos gerados pela execução `azds prep`. Se reutilizar ativos gerados fora do Visual Studio Code, ainda tem de ter um `.vscode` diretório. Isso `.vscode` directory pode ser regenerado pelo Visual Studio code e a extensão de espaços de desenvolvimento do Azure e não irá substituir os recursos existentes.

Para projetos do .NET Core, tem de ter o [ C# extensão][csharp-extension] installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] instalado, bem como [Maven instalado e configurado][maven] para executar o Java serviço do Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Depurar o seu serviço no AKS

Depois de iniciar o seu projeto, pode depurar seus serviços de Java, node. js e .NET Core em execução num espaço de desenvolvimento diretamente a partir do Visual Studio Code. A configuração de inicialização no `.vscode` directory fornece as informações de depuração adicionais para a execução de um serviço com a depuração habilitada num espaço de desenvolvimento. Código do Visual Studio anexa também para o processo de depuração no contentor em execução nos seus espaços de desenvolvimento, permitindo que defina pontos de interrupção, inspecionar as variáveis e executar outras operações de depuração.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Utilizar o Visual Studio Code com espaços de desenvolvimento do Azure

Pode ver-se ao Visual Studio Code e a extensão de espaços de desenvolvimento do Azure a funcionar com os espaços de desenvolvimento do Azure nos inícios rápidos:

* [Programar com Java][quickstart-java]
* [Programar com .NET][quickstart-netcore]
* [Programar com node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md