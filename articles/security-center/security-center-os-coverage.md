---
title: Plataformas apoiadas pelo Azure Security Center Microsoft Docs
description: Este documento fornece uma lista de plataformas apoiadas pelo Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/31/2020
ms.author: memildin
ms.openlocfilehash: f02e45804234fc8f8aa2b966db7ddce5efc9cfd1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998837"
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

O Centro de Segurança depende do [Agente de Análise de Registos.](../azure-monitor/platform/agents-overview.md#log-analytics-agent) Certifique-se de que as suas máquinas estão a executar um dos sistemas operativos suportados para este agente, conforme descrito nas páginas seguintes:

* [Log Analytics agente para sistemas operativos suportados pelo Windows](../azure-monitor/platform/agents-overview.md#supported-operating-systems)
* [Log Analytics agente para sistemas operativos suportados pelo Linux](../azure-monitor/platform/agents-overview.md#supported-operating-systems)

Também certifique-se de que o seu agente Log Analytics está [devidamente configurado para enviar dados para o Centro de Segurança](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Para saber mais sobre as funcionalidades específicas do Security Center disponíveis no Windows e Linux, consulte [a cobertura de funcionalidades para máquinas.](security-center-services.md)

## <a name="managed-virtual-machine-services"></a>Serviços de máquinas virtuais geridos <a name="virtual-machine"></a>

As máquinas virtuais também são criadas numa subscrição de clientes como parte de alguns serviços geridos pela Azure, tais como Azure Kubernetes (AKS), Azure Databricks, e muito mais. O Security Center também descobre estas máquinas virtuais, e o agente Log Analytics pode ser instalado e configurado se um SISTEMA suportado estiver disponível.

## <a name="cloud-services"></a>Serviços cloud <a name="cloud-services"></a>

As máquinas virtuais que funcionam num serviço de nuvem também são suportadas. Apenas os serviços de cloud web e os papéis dos trabalhadores que funcionam em slots de produção são monitorizados. Para saber mais sobre os serviços na nuvem, consulte [a visão geral dos Serviços Azure Cloud.](../cloud-services/cloud-services-choose-me.md)

A proteção para os VMs que residem em Azure Stack também é suportada. Para obter mais informações sobre a integração do Security Center com o Azure Stack, consulte [a bordo das suas máquinas virtuais Azure Stack para o Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados utilizando o Agente De Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Security Center gere e salvaguarda dados.](security-center-data-security.md)
- Saiba como [planear e entender as considerações de design para adotar o Centro de Segurança Azure.](security-center-planning-and-operations-guide.md)