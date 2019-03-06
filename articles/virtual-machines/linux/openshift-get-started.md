---
title: OpenShift na descrição geral do Azure | Documentos da Microsoft
description: Uma visão geral do OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826377f7ae4803818fab22e333f7b9b89caf67cb
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444893"
---
# <a name="openshift-in-azure"></a>OpenShift no Azure

OpenShift é uma plataforma de aplicação de contentor aberta e extensível que fornece o Docker e Kubernetes para a empresa.  

OpenShift inclui Kubernetes para orquestração de contentores e gestão. Ele adiciona centrado no desenvolvedor e centrado em operações de ferramentas que permitem:

- Desenvolvimento rápido de aplicativos.
- Facilitar a implementação e dimensionamento.
- Manutenção de ciclo de vida longa duração para equipes e aplicativos.

Há várias versões do OpenShift disponíveis:

- OpenShift Container Platform
- OpenShift no Azure (OpenShift totalmente gerido, chegando à volta do final do Q1 CY2019)
- OKD (anteriormente conhecido como o OpenShift Origin)
- OpenShift dedicado
- OpenShift Online

Das cinco versões abordadas neste artigo, apenas dois estão atualmente disponíveis para os clientes implementar no Azure: Plataforma de contentores do OpenShift e OKD.

## <a name="openshift-container-platform"></a>OpenShift Container Platform

Plataforma de contentores é uma prontas para empresas [versão comercial](https://www.openshift.com) partir e suportados pelo Red Hat. Com esta versão, os clientes comprar as elegibilidades necessárias para a plataforma de contentores do OpenShift e são responsáveis pela instalação e gestão de toda a infraestrutura.

Uma vez que os clientes "proprietário" a plataforma de toda, eles podem instalá-lo em datacenters no local ou numa nuvem pública (por exemplo, o Azure).

## <a name="openshift-on-azure"></a>OpenShift no Azure

OpenShift no Azure é uma oferta completamente gerida do OpenShift em execução no Azure. Este serviço em conjunto, é gerido e suportado pela Microsoft e a Red Hat. O cluster irá implementar para a subscrição do cliente do Azure. O serviço está atualmente em pré-visualização privada e está agendado para ser GA à volta do final do Q1 CY2019. Para clientes interessados em participar na pré-visualização privada, preencha os [formulário de nomeação](http://aka.ms/openshiftazureinterest).  Irão ser fornecidas mais informações, como a oferta se aproximar GA.

## <a name="okd-formerly-openshift-origin"></a>OKD (anteriormente conhecido como o OpenShift Origin)

OKD é um [aberto](https://www.okd.io/) projeto a montante do OpenShift suportada na Comunidade. OKD pode ser instalado no CentOS ou Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>OpenShift dedicado

É dedicada, gerida de Red Hat *inquilino único* OpenShift que utiliza o OpenShift Container Platform. Red Hat gerencia todos da infraestrutura subjacente (VMs, cluster do OpenShift, rede, armazenamento, etc.). O cluster é específico para um cliente e é executado numa nuvem pública (por exemplo, o Azure). Um cluster inicial inclui quatro nós de aplicação e todos os custos são anual e pago inicialmente.

## <a name="openshift-online"></a>OpenShift Online

Online é gerida de Red Hat *multi-inquilino* OpenShift que utiliza a plataforma de contentores. Red Hat gerencia todos da infraestrutura subjacente (por exemplo, VMs, cluster de OpenShift, funcionamento em rede e armazenamento). 

Com esta versão, o cliente implementa contentores, mas não tem controle sobre os anfitriões que executam os contentores. Uma vez Online é a multi-inquilino, os contentores podem estar localizados nos anfitriões VM mesmo como contentores de outros clientes. Custo é por contentor.

## <a name="next-steps"></a>Passos Seguintes

- [Configurar a pré-requisitos comuns para OpenShift no Azure](./openshift-prerequisites.md)
- [Implementar o OpenShift Container Platform no Azure](./openshift-container-platform.md)
- [Implementar OKD no Azure](./openshift-okd.md)
- [Implementar o OpenShift no Azure Stack](./openshift-azure-stack.md)
- [Tarefas de pós-implementação](./openshift-post-deployment.md)
- [Resolver problemas de implementação do OpenShift](./openshift-troubleshooting.md)
