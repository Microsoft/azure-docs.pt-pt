---
title: Introdução aos espaços Azure Dev
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Saiba como a Azure Dev Spaces proporciona uma experiência rápida e iterativa de desenvolvimento de Kubernetes para equipas em clusters de Serviço Saque Dev Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço Azure Kubernetes, contentores, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 586b19070ec36517add21f7aac86ddf15121be2d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240703"
---
# <a name="introduction-to-azure-dev-spaces"></a>Introdução aos espaços Azure Dev

A Azure Dev Spaces proporciona uma experiência rápida e iterativa de desenvolvimento da Kubernetes para equipas em clusters do Serviço Azure Kubernetes (AKS). A Azure Dev Spaces também permite desbugicar e testar todos os componentes da sua aplicação em AKS com a configuração mínima da máquina de desenvolvimento, sem replicar ou ridicularizar dependências.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como os Espaços de Programador do Azure simplificam o desenvolvimento da Kubernetes

A Azure Dev Spaces ajuda as equipas a concentrarem-se no desenvolvimento e rápida iteração da sua aplicação de microserviços, permitindo que as equipas trabalhem diretamente com toda a sua arquitetura de microserviços ou aplicações em aks. A Azure Dev Spaces também fornece uma forma de atualizar independentemente partes da sua arquitetura de microserviços isoladamente sem afetar o resto do cluster AKS ou outros desenvolvedores. A Azure Dev Spaces destina-se ao desenvolvimento e teste em ambientes de desenvolvimento e teste de nível inferior e não se destina a funcionar em clusters AKS de produção.

Uma vez que as equipas podem trabalhar com toda a aplicação e colaborar diretamente na AKS, Azure Dev Spaces:

* Minimiza a configuração da máquina local
* Diminui o tempo de configuração para novos desenvolvedores na equipa
* Aumenta a velocidade de uma equipa através de uma iteração mais rápida
* Reduz o número de ambientes redundantes de desenvolvimento e integração, uma vez que os membros da equipa podem partilhar um cluster
* Remove a necessidade de replicar ou simular dependências
* Melhora a colaboração entre equipas de desenvolvimento, bem como as equipas com as que trabalham, como as equipas de DevOps

A Azure Dev Spaces fornece ferramentas para gerar ativos da Docker e da Kubernetes para os seus projetos. Esta ferramenta permite-lhe facilmente adicionar aplicações novas e existentes tanto a um espaço de v como a outros clusters AKS.

Para mais informações sobre como funciona o Azure Dev Spaces, consulte [como funciona o Azure Dev Spaces e está configurado.][how-dev-spaces-works]

## <a name="supported-regions-and-configurations"></a>Regiões e configurações suportadas

A Azure Dev Spaces é apoiada apenas por aglomerados AKS em [algumas regiões.][supported-regions] O Azure Dev Spaces suporta a utilização da [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou do [Visual Studio Code](https://code.visualstudio.com/download) com a [extensão do Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada no Linux, MacOS ou Windows 8 ou superior para criar e executar aplicações no AKS. Também suporta a utilização do [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) instalado no Windows 8 ou superior. Para o Visual Studio 2019, vai precisar da carga de trabalho do Azure Development. Para o Visual Studio 2017, você precisará da carga de trabalho de Desenvolvimento Web e [ferramentas de estúdio visual para Kubernetes.](https://aka.ms/get-vsk8stools)

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o rápido desenvolvimento iterativo para equipas com O Azure Dev Spaces com o desenvolvimento da equipa rapidamente.

> [!div class="nextstepaction"]
> [Desenvolvimento de equipas aceleradamente](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md
