---
title: Plataformas apoiadas pelo Azure Security Center | Microsoft Docs
description: Este documento fornece uma lista de plataformas apoiadas pelo Azure Security Center.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: 88dc0760f320a99b0cbc99b7637dc34dd11dfecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103465451"
---
# <a name="supported-platforms"></a>Plataformas suportadas 

Esta página mostra as plataformas e ambientes suportados pelo Azure Security Center.

## <a name="combinations-of-environments"></a>Combinações de ambientes <a name="vm-server"></a>

O Azure Security Center suporta máquinas virtuais e servidores em diferentes tipos de ambientes híbridos:

* Apenas Azure
* Azul e no local
* Azul e outras nuvens
* Azul, outras nuvens, e no local

Para um ambiente Azure ativado numa subscrição do Azure, o Azure Security Center irá automaticamente descobrir os recursos iaaS que são implantados dentro da subscrição.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

O Centro de Segurança depende do [agente Log Analytics.](../azure-monitor/agents/agents-overview.md#log-analytics-agent) Certifique-se de que as suas máquinas estão a executar um dos sistemas operativos suportados para este agente, conforme descrito nas páginas seguintes:

* [Log Analytics agente para sistemas operativos suportados pelo Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems)
* [Log Analytics agente para sistemas operativos suportados pelo Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems)

Também certifique-se de que o seu agente Log Analytics está [devidamente configurado para enviar dados para o Centro de Segurança](security-center-enable-data-collection.md#manual-agent)

Para saber mais sobre as funcionalidades específicas do Security Center disponíveis no Windows e Linux, consulte [a cobertura de funcionalidades para máquinas.](security-center-services.md)

> [!NOTE]
> Apesar de o Azure Defender ter sido concebido para proteger os servidores, a maioria das capacidades do **Azure Defender para servidores** são suportadas por máquinas windows 10. Uma das funcionalidades que não é suportada atualmente é [a solução EDR integrada do Security Center: Microsoft Defender for Endpoint](security-center-wdatp.md).

## <a name="managed-virtual-machine-services"></a>Serviços de máquinas virtuais geridos <a name="virtual-machine"></a>

As máquinas virtuais também são criadas numa subscrição de clientes como parte de alguns serviços geridos pela Azure, tais como Azure Kubernetes (AKS), Azure Databricks, e muito mais. O Security Center também descobre estas máquinas virtuais, e o agente Log Analytics pode ser instalado e configurado se um SISTEMA suportado estiver disponível.

## <a name="cloud-services"></a>Serviços cloud <a name="cloud-services"></a>

As máquinas virtuais que funcionam num serviço de nuvem também são suportadas. Apenas os serviços de cloud web e os papéis dos trabalhadores que funcionam em slots de produção são monitorizados. Para saber mais sobre os serviços na nuvem, consulte [a visão geral dos Serviços Azure Cloud.](../cloud-services/cloud-services-choose-me.md)

A proteção para os VMs que residem no Azure Stack Hub também é suportada. Para obter mais informações sobre a integração do Security Center com o Azure Stack Hub, consulte [a bordo das suas máquinas virtuais Azure Stack Hub para o Security Center](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms). 

## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados utilizando o Agente De Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Security Center gere e salvaguarda dados.](security-center-data-security.md)
- Saiba como [planear e entender as considerações de design para adotar o Centro de Segurança Azure.](security-center-planning-and-operations-guide.md)