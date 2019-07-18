---
title: Introdução ao Azure Dev Spaces
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Introdução ao Azure Dev Spaces
keywords: Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, kubectl, K8S
manager: gwallace
ms.openlocfilehash: c96115f2eba6f8c8a028251d146ca3f766eba044
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305457"
---
# <a name="azure-dev-spaces"></a>Espaços de Programador do Azure

Azure Dev Spaces é uma experiência de desenvolvimento kubernetes rápida e iterativa para equipes em clusters do AKS (serviço kubernetes do Azure). Você pode colaborar com sua equipe em um cluster AKS compartilhado. Azure Dev Spaces também permite testar todos os componentes do seu aplicativo no AKS sem replicar ou simular dependências. Você pode executar e depurar os contêineres de forma iterativa diretamente no AKS com a configuração mínima da máquina de desenvolvimento.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como os Espaços de Programador do Azure simplificam o desenvolvimento da Kubernetes

Azure Dev Spaces ajuda as equipes a se concentrarem no desenvolvimento e na iteração rápida de seu aplicativo de microatendimento, permitindo que as equipes trabalhem diretamente com toda a sua arquitetura de microserviço ou aplicativo em execução no AKS. O Azure Dev Spaces também fornece uma maneira de atualizar partes de forma independente da arquitetura de seus microserviços isoladamente sem afetar o restante do cluster AKS ou de outros desenvolvedores. O Azure Dev Spaces é para desenvolvimento e teste em ambientes de desenvolvimento e teste de nível inferior e não deve ser executado em clusters de AKS de produção.

Como as equipes podem trabalhar com todo o aplicativo e colaborar diretamente no AKS, Azure Dev Spaces:

* Minimiza a configuração da máquina local
* Diminui o tempo de configuração para novos desenvolvedores na equipe
* Aumenta a velocidade de uma equipe por meio de iteração mais rápida
* Reduz o número de ambientes redundantes de desenvolvimento e integração, já que os membros da equipe podem compartilhar um cluster
* Elimina a necessidade de replicar ou simular dependências
* Melhora a colaboração entre equipes de desenvolvimento, bem como as equipes com as quais elas trabalham, como DevOps Teams

Azure Dev Spaces fornece ferramentas para gerar ativos do Docker e do kubernetes para seus projetos. Essas ferramentas permitem que você adicione facilmente aplicativos novos e existentes a um espaço de desenvolvimento e a outros clusters AKS.

Para obter mais informações sobre como Azure Dev Spaces funciona, consulte [como o Azure dev Spaces funciona e está configurado][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Regiões e configurações com suporte

Só há suporte para Azure Dev Spaces em clusters AKS nos **EUA Leste**, **leste dos eua 2**, **EUA Central**, **Sul EUA Central**, **oeste dos EUA 2**, **Europa setentrional**, **Europa Ocidental**, **sul do Reino Unido**, **sudeste asiático**,  **Leste da Austrália**, **central do Canadá**e regiões do **leste do Canadá** . O Azure Dev Spaces suporta a utilização da [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou do [Visual Studio Code](https://code.visualstudio.com/download) com a [extensão do Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada no Linux, MacOS ou Windows 8 ou superior para criar e executar aplicações no AKS. Ele também dá suporte ao uso do [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) instalado no Windows 8 ou superior. Para o Visual Studio 2019, você precisará da carga de trabalho de desenvolvimento do Azure. Para o Visual Studio 2017, você precisará da carga de trabalho de desenvolvimento Web e [Ferramentas do Visual Studio para kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o desenvolvimento rápido e iterativo para equipes com Azure Dev Spaces com o guia de início rápido de desenvolvimento de equipe.

> [!div class="nextstepaction"]
> [Início rápido de desenvolvimento de equipe](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
