---
title: Plataformas apoiadas pelo Azure Security Center Microsoft Docs
description: Este documento fornece uma lista de plataformas suportadas pelo Azure Security Center.
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
ms.openlocfilehash: 3c8bf69b745f5dba8c08556908df4d4ae5b5769f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80521908"
---
# <a name="supported-platforms"></a>Plataformas suportadas 

Esta página mostra as plataformas e ambientes suportados pelo Azure Security Center.

## <a name="combinations-of-environments"></a>Combinações de ambientes<a name="vm-server"></a>

O Azure Security Center suporta máquinas e servidores virtuais em diferentes tipos de ambientes híbridos:

* Apenas Azure
* Azure e no local
* Azul e outras nuvens
* Azure, outras nuvens, e no local

Para um ambiente Azure ativado numa subscrição do Azure, o Azure Security Center descobrirá automaticamente os recursos IaaS que são implantados dentro da subscrição.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

O Centro de Segurança depende do [Agente de Análise](../azure-monitor/platform/agents-overview.md#log-analytics-agent)de Registos. Certifique-se de que as suas máquinas estão a funcionar com um dos sistemas operativos suportados para este agente, conforme descrito nas páginas seguintes:

* [Agente de Log Analytics para sistemas operativos suportados pelo Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)
* [Agente de Log Analytics para sistemas operativos suportados pelo Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)

Certifique-se também de que o seu agente Log Analytics está [devidamente configurado para enviar dados para o Security Center](security-center-enable-data-collection.md#manual-agent)

> [!TIP]
> Para saber mais sobre as funcionalidades específicas do Security Center disponíveis no Windows e Linux, consulte a [cobertura de funcionalidades para máquinas](security-center-services.md).

## <a name="managed-virtual-machine-services"></a>Serviços de máquinas virtuais geridos<a name="virtual-machine"></a>

As máquinas virtuais também são criadas numa subscrição de clientes como parte de alguns serviços geridos pelo Azure, como o Azure Kubernetes (AKS), o Azure Databricks, entre outros. O Security Center também descobre estas máquinas virtuais, e o agente Log Analytics pode ser instalado e configurado se estiver disponível um SISTEMA suportado.

## <a name="cloud-services"></a>Serviços cloud<a name="cloud-services"></a>

As máquinas virtuais que funcionam num serviço de nuvem também são suportadas. Apenas os serviços na nuvem web e funções de trabalhador que funcionam em slots de produção são monitorizados. Para saber mais sobre os serviços na nuvem, consulte a [visão geral dos Serviços Azure Cloud.](../cloud-services/cloud-services-choose-me.md)

A proteção dos VMs residentes em Azure Stack também é apoiada. Para obter mais informações sobre a integração do Security Center com o Azure Stack, consulte as [suas máquinas virtuais Azure Stack para o Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados utilizando o Agente de Análise de Registos](security-center-enable-data-collection.md).
- Saiba como o [Centro de Segurança gere e salvaguarda dados.](security-center-data-security.md)
- Saiba como [planear e compreender as considerações de design para adotar o Azure Security Center.](security-center-planning-and-operations-guide.md)