---
title: Introdução aos espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
ms.author: zarhoads
ms.date: 05/07/2019
ms.topic: overview
description: Introdução aos espaços de desenvolvimento do Azure
keywords: Docker, Kubernetes, Azure, AKS, serviço Kubernetes do Azure, contentores, kubectl, k8s
manager: jeconnoc
ms.openlocfilehash: 3e887409518f5cc97238a0168213a7918e318c04
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394014"
---
# <a name="azure-dev-spaces"></a>Espaços de Programador do Azure

Os espaços de desenvolvimento do Azure são uma experiência de desenvolvimento de Kubernetes de rápido e iterativa para as equipes em clusters do Azure Kubernetes Service (AKS). Pode colaborar com sua equipe num cluster do AKS partilhado. Espaços de desenvolvimento do Azure também permite-lhe testar todos os componentes da sua aplicação no AKS sem replicar ou dependências de simulação. Iterativamente pode executar e depurar contentores diretamente no AKS com a configuração de máquina de desenvolvimento mínima.

![](media/azure-dev-spaces/collaborate-graphic.gif)


## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Como os Espaços de Programador do Azure simplificam o desenvolvimento da Kubernetes

Espaços de desenvolvimento do Azure ajuda as equipes a concentrar-se no desenvolvimento e uma rápida iteração da sua aplicação de microsserviços, ao permitir que as equipes a trabalhar diretamente com a respetiva arquitetura de microsserviços toda ou de uma aplicação em execução no AKS. Espaços de desenvolvimento do Azure também fornece uma forma de forma independente atualizar partes da sua arquitetura de microsserviços isoladamente sem afetar o resto do AKS cluster ou de outros desenvolvedores. Espaços de desenvolvimento do Azure para desenvolvimento e teste no desenvolvimento de nível inferior e ambientes de teste e não é destinado para execução em clusters do AKS de produção.

Uma vez que as equipes podem trabalhar com toda a aplicação e colaborar diretamente no AKS, espaços de desenvolvimento do Azure:

* Minimiza a configuração do computador local
* Tempo de configuração de diminuições por novos desenvolvedores da Equipe
* Aumenta a velocidade de uma equipe por meio de iteração mais rápida
* Reduz o número de ambientes de desenvolvimento e integração redundantes, uma vez que os membros da Equipe podem partilhar um cluster
* Remove a necessidade de replicar ou simular as dependências
* Melhora a colaboração entre as equipes de desenvolvimento, bem como as equipes que trabalham com, por exemplo, as equipas de DevOps

Espaços de desenvolvimento do Azure fornece as ferramentas para gerar o Docker e Kubernetes ativos para os seus projetos. Estas ferramentas permite-lhe adicionar facilmente aplicações de novas e existentes para um espaço de desenvolvimento e outros clusters do AKS.

Para obter mais informações sobre como funciona a espaços de desenvolvimento do Azure, consulte [como os espaços de desenvolvimento do Azure funciona e é configurada][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Regiões suportadas e configurações

O Azure Dev Spaces é suportado apenas por clusters do AKS nas regiões **E.U.A. Leste**, **E.U.A. Leste 2**, **E.U.A. Central**, **E.U.A. Oeste 2**, **Europa do Norte**, **Europa Ocidental**, **Sul do Reino Unido**, **Sudeste Asiático**, **Leste da Austrália**, **Canadá Central** e **Leste do Canadá**. O Azure Dev Spaces suporta a utilização da [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou do [Visual Studio Code](https://code.visualstudio.com/download) com a [extensão do Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) instalada no Linux, MacOS ou Windows 8 ou superior para criar e executar aplicações no AKS. Também suporta a utilização de [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) instalado no Windows 8 ou superior. Para Visual Studio 2019, terá da carga de trabalho de desenvolvimento do Azure. Para o Visual Studio 2017, terá da carga de trabalho de desenvolvimento para a Web e [Visual Studio Tools para o Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o desenvolvimento rápido e iterativo, para as equipes com os espaços de desenvolvimento do Azure com o início rápido de desenvolvimento de equipe.

> [!div class="nextstepaction"]
> [Início rápido de desenvolvimento de equipe](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md
