---
title: Visão geral do OpenShift no Azure | Microsoft Docs
description: Uma visão geral do OpenShift no Azure.
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
ms.openlocfilehash: 2293eac45da6f53b0cd29fda68b0ddc76aea1a6c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390639"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

O OpenShift é uma plataforma de aplicativo de contêiner aberta e extensível que traz o Docker e o kubernetes para a empresa.  

O OpenShift inclui kubernetes para orquestração e gerenciamento de contêineres. Ele adiciona ferramentas centradas em desenvolvedores e em operações que permitem:

- Desenvolvimento rápido de aplicativos.
- Implantação e dimensionamento fáceis.
- Manutenção de ciclo de vida de longo prazo para equipes e aplicativos.

Há várias versões do OpenShift disponíveis.  Dessas versões, somente duas estão disponíveis atualmente para que os clientes implantem no Azure: plataforma de contêiner OpenShift e OKD (anteriormente OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift é uma oferta totalmente gerenciada do OpenShift em execução no Azure. Esse serviço é gerenciado em conjunto e tem suporte da Microsoft e do Red Hat. Para obter mais detalhes, consulte a documentação do [serviço do Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/) .

## <a name="openshift-container-platform"></a>OpenShift Container Platform

A plataforma de contêiner é uma [versão comercial](https://www.openshift.com) pronta para a empresa e com suporte do Red Hat. Com essa versão, os clientes compram os direitos necessários para a plataforma de contêiner OpenShift e são responsáveis pela instalação e pelo gerenciamento de toda a infraestrutura.

Como os próprios clientes "possuem" toda a plataforma, eles podem instalá-lo em seu datacenter local ou em uma nuvem pública (como o Azure).

## <a name="okd"></a>OKD

OKD [é um projeto de](https://www.okd.io/) upstream de software livre do OpenShift com suporte da Comunidade. O OKD pode ser instalado em CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passos seguintes

- [Configurar pré-requisitos comuns para o OpenShift no Azure](./openshift-container-platform-3x-prerequisites.md)
- [Implantar a plataforma de contêiner OpenShift no Azure](./openshift-container-platform-3x.md)
- [Implantar a oferta do Marketplace autogerenciado da plataforma de contêiner do OpenShift](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Implantar OpenShift no Azure Stack](./openshift-azure-stack.md)
- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Solucionar problemas de implantação do OpenShift](./openshift-container-platform-3x-troubleshooting.md)
