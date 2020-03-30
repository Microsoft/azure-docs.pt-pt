---
title: OpenShift na visão geral do Azure
description: Uma visão geral do OpenShift em Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 021ebe010a27fa155de861121e1972466c800f4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74035412"
---
# <a name="openshift-in-azure"></a>OpenShift em Azure

OpenShift é uma plataforma de aplicação de contentores aberta e extensível que traz Docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para orquestração e gestão de contentores. Adiciona ferramentas centradas no desenvolvedor e centradas em operações que permitem:

- Desenvolvimento rápido de aplicações.
- Fácil implantação e escalação.
- Manutenção de ciclode vida a longo prazo para equipas e aplicações.

Existem várias versões do OpenShift disponíveis.  Destas versões, apenas duas estão disponíveis hoje para os clientes implementarem em Azure: OpenShift Container Platform e OKD (antiga OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

O Microsoft Azure Red Hat OpenShift é uma oferta totalmente gerida do OpenShift em funcionamento em Azure. Este serviço é gerido em conjunto e suportado pela Microsoft e pela Red Hat. Para mais detalhes, consulte a documentação do [Serviço OpenShift da Chapéu Vermelha Azure.](https://docs.microsoft.com/azure/openshift/)

## <a name="openshift-container-platform"></a>OpenShift Container Platform

A Container Platform é uma [versão comercial](https://www.openshift.com) pronta para a empresa e apoiada pela Red Hat. Com esta versão, os clientes adquirem os direitos necessários para a Plataforma de Contentores OpenShift e são responsáveis pela instalação e gestão de toda a infraestrutura.

Como os clientes "possuem" toda a plataforma, podem instalá-la no seu centro de dados no local, ou numa nuvem pública (como o Azure).

## <a name="okd"></a>OKD

OKD é um projeto a montante de [openshift](https://www.okd.io/) de código aberto que é apoiado pela comunidade. OkD pode ser instalado em CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passos seguintes

- [Configure pré-requisitos comuns para OpenShift em Azure](./openshift-container-platform-3x-prerequisites.md)
- [Implementar plataforma de contentores OpenShift em Azure](./openshift-container-platform-3x.md)
- [Implementar oferta de marketplace auto-gerida plataforma de contentores openshift](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Implementar OpenShift em Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Implantação openShift de resolução de problemas](./openshift-container-platform-3x-troubleshooting.md)
