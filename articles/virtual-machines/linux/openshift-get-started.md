---
title: OpenShift em visão geral do Azure
description: Uma visão geral do OpenShift em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 989c67d0d1b1cd8257da089ec9c31f247228018e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87367950"
---
# <a name="openshift-in-azure"></a>OpenShift em Azure

OpenShift é uma plataforma de aplicação de contentores aberta e extensível que traz Docker e Kubernetes para a empresa.  

O OpenShift inclui Kubernetes para orquestração e gestão de contentores. Adiciona ferramentas centradas no programador e centradas em operações que permitem:

- Desenvolvimento rápido de aplicações.
- Fácil implantação e escalonamento.
- Manutenção de ciclo de vida a longo prazo para equipas e aplicações.

Existem várias versões de OpenShift disponíveis.  Destas versões, apenas duas estão disponíveis hoje para os clientes implementarem no Azure: OpenShift Container Platform e OKD (anteriormente OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift é uma oferta totalmente gerida de OpenShift em execução em Azure. Este serviço é gerido e suportado conjuntamente pela Microsoft e pela Red Hat. Para mais detalhes, consulte a documentação do [Serviço De Abertura do Chapéu Vermelho Azure.](../../openshift/index.yml)

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Container Platform é uma [versão comercial](https://www.openshift.com) pronta para a empresa e suportada pela Red Hat. Com esta versão, os clientes adquirem os direitos necessários para a Plataforma de Contentores OpenShift e são responsáveis pela instalação e gestão de toda a infraestrutura.

Como os clientes "possuem" toda a plataforma, podem instalá-la no seu datacenter no local, ou numa nuvem pública (como o Azure).

## <a name="okd"></a>OKD

OKD é um projeto a montante [aberto](https://www.okd.io/) do OpenShift que é apoiado pela comunidade. OKD pode ser instalado no CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Passos seguintes

- [Configurar pré-requisitos comuns para OpenShift em Azure](./openshift-container-platform-3x-prerequisites.md)
- [Implementar plataforma de contentores openshift em Azure](./openshift-container-platform-3x.md)
- [Implementar plataforma de recipientes abertos Self-Managed oferta de mercado](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Implementar OpenShift em Azure Stack](./openshift-azure-stack.md)
- [Tarefas pós-implantação](./openshift-container-platform-3x-post-deployment.md)
- [Resolução de problemas OpenShift deployment](./openshift-container-platform-3x-troubleshooting.md)
