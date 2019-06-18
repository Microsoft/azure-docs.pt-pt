---
title: OpenShift na descrição geral do Azure | Documentos da Microsoft
description: Uma visão geral do OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: d9e3aa3dae81166ef91f57ea6a95087a952001ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65550989"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

OpenShift é uma plataforma de aplicação de contentor aberta e extensível que fornece o Docker e Kubernetes para a empresa.  

OpenShift inclui Kubernetes para orquestração de contentores e gestão. Ele adiciona centrado no desenvolvedor e centrado em operações de ferramentas que permitem:

- Desenvolvimento rápido de aplicativos.
- Facilitar a implementação e dimensionamento.
- Manutenção de ciclo de vida longa duração para equipes e aplicativos.

Existem várias versões do OpenShift disponíveis.  Estas versões, apenas dois estão atualmente disponíveis para os clientes implementar no Azure: OpenShift Container Platform e OKD (anteriormente conhecido como o OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift é uma oferta completamente gerida do OpenShift em execução no Azure. Este serviço em conjunto, é gerido e suportado pela Microsoft e a Red Hat. Para obter mais detalhes, consulte a [serviço do Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/) documentação.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Plataforma de contentores é uma prontas para empresas [versão comercial](https://www.openshift.com) partir e suportados pelo Red Hat. Com esta versão, os clientes comprar as elegibilidades necessárias para a plataforma de contentores do OpenShift e são responsáveis pela instalação e gestão de toda a infraestrutura.

Uma vez que os clientes "proprietário" a plataforma de toda, eles podem instalá-lo em datacenters no local ou numa nuvem pública (por exemplo, o Azure).

## <a name="okd"></a>OKD

OKD é um [aberto](https://www.okd.io/) projeto a montante do OpenShift suportada na Comunidade. OKD pode ser instalado no CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implementar o OpenShift Container Platform no Azure](./openshift-container-platform.md)
- [Implementar o OpenShift Container Platform gestão automática centrada no Marketplace oferta](./openshift-marketplace-self-managed.md)
- [Implementar o OpenShift no Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift](./openshift-troubleshooting.md)
