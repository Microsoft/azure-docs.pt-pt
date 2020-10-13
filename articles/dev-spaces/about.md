---
title: O que é o Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Saiba como a Azure Dev Spaces proporciona uma experiência rápida e iterativa de desenvolvimento de Kubernetes para equipas em clusters de serviçoS Azure Kubernetes
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 9bdd6f48a8f48266b8a9aad003fe6ea43715036c
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978446"
---
# <a name="what-is-azure-dev-spaces"></a>O que é o Azure Dev Spaces?

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

A Azure Dev Spaces proporciona uma experiência rápida e iterativa de desenvolvimento de Kubernetes para equipas em clusters Azure Kubernetes Service (AKS). O Azure Dev Spaces também permite depurar e testar todos os componentes da sua aplicação em AKS com a configuração mínima da máquina de desenvolvimento, sem replicar ou ridicularizar dependências.

![O diagrama mostra duas versões de uma aplicação desenvolvida de forma independente. Depois são combinados num ambiente de desenvolvimento do Azure Dev Spaces.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como os Espaços de Programador do Azure simplificam o desenvolvimento da Kubernetes

O Azure Dev Spaces ajuda as equipas a focarem-se no desenvolvimento e iteração rápida da sua aplicação de microserviços, permitindo que as equipas trabalhem diretamente com toda a sua arquitetura de microserviços ou aplicações em execução em AKS. O Azure Dev Spaces também fornece uma forma de atualizar independentemente partes da arquitetura dos seus microserviços isoladamente sem afetar o resto do cluster AKS ou outros desenvolvedores. A Azure Dev Spaces destina-se ao desenvolvimento e teste em ambientes de desenvolvimento e teste de nível inferior e não se destina a funcionar em clusters AKS de produção.

Uma vez que as equipas podem trabalhar com toda a aplicação e colaborar diretamente na AKS, Azure Dev Spaces:

* Minimiza a configuração da máquina local
* Diminui o tempo de configuração para novos desenvolvedores na equipa
* Aumenta a velocidade de uma equipa através de uma iteração mais rápida
* Reduz o número de ambientes redundantes de desenvolvimento e integração, uma vez que os membros da equipa podem partilhar um cluster
* Elimina a necessidade de replicar ou ridicularizar dependências
* Melhora a colaboração entre as equipas de desenvolvimento, bem como as equipas com quem trabalham, como as equipas de DevOps

A Azure Dev Spaces fornece ferramentas para gerar ativos de Docker e Kubernetes para os seus projetos. Esta ferramenta permite-lhe adicionar facilmente aplicações novas e existentes tanto a um espaço dev como a outros clusters AKS.

Para obter mais informações sobre o funcionamento do Azure Dev Spaces, consulte [como funciona a Azure Dev Spaces e está configurado.][how-dev-spaces-works]

## <a name="supported-regions-and-configurations"></a>Regiões e configurações apoiadas

O Azure Dev Spaces é suportado apenas por clusters AKS em [algumas regiões.][supported-regions] O Azure Dev Spaces suporta a utilização do [Código Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Estúdio Visual](https://code.visualstudio.com/download) com a [extensão Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada no Linux, macOS ou Windows 8 ou maior para construir e executar as suas aplicações em AKS. Também suporta a utilização do [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) instalado no Windows com a carga de trabalho do Azure Development.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o funcionamento da Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como funciona o Azure Dev Spaces](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
