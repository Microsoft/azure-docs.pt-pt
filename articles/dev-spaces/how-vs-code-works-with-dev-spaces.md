---
title: Como o Código do Estúdio Visual funciona com a Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Saiba como o Visual Studio Code e o Azure Dev Spaces o ajudam a depurar e a iterar rapidamente as suas aplicações Kubernetes
keywords: Espaços Azure Dev, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores
ms.openlocfilehash: decf61bd55ed15182f030bb02a2fe9c959749556
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88209885"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como o Código do Estúdio Visual funciona com a Azure Dev Spaces

Pode utilizar o Código do Estúdio Visual e a [extensão Azure Dev Spaces][azds-extension] para preparar, executar e depurar os seus serviços com a Azure Dev Spaces. Com o Código do Estúdio Visual e a extensão Azure Dev Spaces, pode:

* Gerar ativos para executar e depurar serviços em AKS
* Executar os seus serviços Java, Node.js e .NET Core em um espaço dev
* Depurar diretamente os seus serviços Java, Node.js e .NET Core em execução num espaço dev

## <a name="generate-assets"></a>Gerar ativos

O Código do Estúdio Visual e a extensão Azure Dev Spaces geram os seguintes ativos para o seu projeto:

* Dockerfiles para aplicações Java usando aplicações Maven, Node.js e .NET Core
* Gráficos de leme para quase qualquer língua com um Dockerfile
* Um `azds.yaml` ficheiro, que é o [ficheiro de configuração Azure Dev Spaces][azds-yaml] para o seu projeto
* Uma `.vscode` pasta com a configuração de lançamento do Código do Estúdio Visual do seu projeto para aplicações Java utilizando aplicações Maven, Node.js e .NET Core

O Dockerfile, o gráfico Helm e `azds.yaml` os ficheiros são os mesmos ativos gerados quando estão a `azds prep` funcionar. Esses ficheiros também podem ser usados fora do código Visual Studio para executar o seu projeto em AKS, como executar `azds up` . A `.vscode` pasta só é utilizada pelo código Visual Studio para executar o seu projeto em AKS a partir do Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Executar o seu serviço em AKS

Depois de gerar os ativos para o seu projeto, pode executar os seus serviços Java, Node.js e .NET Core num espaço dev existente a partir do Código do Estúdio Visual. Na página *Debug* do Visual Studio Code, pode invocar a configuração de lançamento do `.vscode` diretório para executar o seu projeto.

Tem de criar o seu cluster AKS e ativar os Espaços Azure Dev no seu cluster fora do Código do Estúdio Visual. Pode reutilizar os ficheiros e ficheiros existentes de Dockerfiles, Helm e `azds.yaml` ficheiros criados fora do Código do Estúdio Visual, tais como os ativos gerados pela execução `azds prep` . Se reutilizar os ativos gerados fora do Código do Estúdio Visual, ainda precisa de ter um `.vscode` diretório. Este `.vscode` diretório pode ser regenerado pelo código Visual Studio e pela extensão Azure Dev Spaces e não substituirá os seus ativos existentes.

Para projetos .NET Core, tem de ter a [extensão C#][csharp-extension] instalada para executar o seu serviço .NET a partir do Código do Estúdio Visual. Também para projetos java usando Maven, você deve ter a [extensão Java Debugger para Azure Dev Spaces][java-extension] instalada, bem como [Maven instalado e configurado][maven] para executar o seu serviço Java a partir do Código Do Estúdio Visual.

## <a name="debug-your-service-in-aks"></a>Depurar o seu serviço em AKS

Depois de lançar o seu projeto, pode desordiá-lo aos seus serviços Java, Node.js e .NET Core em execução num espaço dev diretamente do Código do Estúdio Visual. A configuração de lançamento no `.vscode` diretório fornece a informação adicional de depuragem para executar um serviço com depuragem ativada num espaço dev. O Código do Estúdio Visual também se liga ao processo de depuração no recipiente de funcionamento nos seus espaços de dev, permitindo-lhe definir pontos de rutura, inspecionar variáveis e realizar outras operações de depuração.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
