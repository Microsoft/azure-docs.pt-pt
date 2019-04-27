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
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: 53bed2131e81ee5ed0f46bde389262ee8349339a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542429"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

OpenShift é uma plataforma de aplicação de contentor aberta e extensível que fornece o Docker e Kubernetes para a empresa.  

OpenShift inclui Kubernetes para orquestração de contentores e gestão. Ele adiciona centrado no desenvolvedor e centrado em operações de ferramentas que permitem:

- Desenvolvimento rápido de aplicativos.
- Facilitar a implementação e dimensionamento.
- Manutenção de ciclo de vida longa duração para equipes e aplicativos.

Existem várias versões do OpenShift disponíveis.  Estas versões, apenas dois estão atualmente disponíveis para os clientes implementar no Azure: OpenShift Container Platform e OKD (anteriormente conhecido como o OpenShift Origin).

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Plataforma de contentores é uma prontas para empresas [versão comercial](https://www.openshift.com) partir e suportados pelo Red Hat. Com esta versão, os clientes comprar as elegibilidades necessárias para a plataforma de contentores do OpenShift e são responsáveis pela instalação e gestão de toda a infraestrutura.

Uma vez que os clientes "proprietário" a plataforma de toda, eles podem instalá-lo em datacenters no local ou numa nuvem pública (por exemplo, o Azure).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Red Hat OpenShift do Azure é uma oferta completamente gerida do OpenShift em execução no Azure. Este serviço em conjunto, é gerido e suportado pela Microsoft e a Red Hat. O cluster irá implementar para a subscrição do cliente do Azure. O serviço está agendado para ser a disponibilidade geral em torno de Maio de 2019. Documentação separada para o serviço gerido vai estar disponível assim que o serviço estiver GA.

## <a name="okd"></a>OKD

OKD é um [aberto](https://www.okd.io/) projeto a montante do OpenShift suportada na Comunidade. OKD pode ser instalado no CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implementar o OpenShift Container Platform no Azure](./openshift-container-platform.md)
- [Implementar o OpenShift Container Platform gestão automática centrada no Marketplace oferta](./openshift-marketplace-self-managed.md)
- [Implementar o OpenShift no Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift](./openshift-troubleshooting.md)
