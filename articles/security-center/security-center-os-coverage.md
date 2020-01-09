---
title: Plataformas com suporte na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de plataformas com suporte na central de segurança do Azure.
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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 42bd6037bcc94a83e649433e9ea0425fbdf11cd2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75353326"
---
# <a name="supported-platforms"></a>Plataformas suportadas 

## Máquinas virtuais/servidores<a name="vm-server"></a>

A central de segurança dá suporte a máquinas virtuais/servidores em diferentes tipos de ambientes híbridos:

* Somente o Azure
* Azure e local
* Azure e outras nuvens
* Azure, outras nuvens e locais

Para um ambiente do Azure ativado em uma assinatura do Azure, a central de segurança do Azure descobrirá automaticamente os recursos de IaaS implantados na assinatura.

> [!NOTE]
> Para receber o conjunto completo de recursos de segurança, você deve ter o [agente de log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), que é usado pela central de segurança do Azure, instalado e [configurado corretamente para enviar dados para a central de segurança do Azure](security-center-enable-data-collection.md#manual-agent).


As seções a seguir listam os sistemas operacionais de servidor com suporte nos quais o [agente de log Analytics](../azure-monitor/platform/agents-overview.md#log-analytics-agent), que é usado pela central de segurança do Azure, pode ser executado.

### Sistemas operacionais Windows Server<a name="os-windows"></a>

|SO|Com suporte na central de segurança do Azure|Suporte para integração com o Microsoft defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Para saber mais sobre os recursos com suporte para os sistemas operacionais Windows listados acima, consulte [recursos com suporte do servidor/máquina virtual](security-center-services.md##vm-server-features).

### Sistemas operacionais Linux<a name="os-linux"></a>

64 bits

* CentOS 6 e 7
* Amazon Linux 2017, 9
* Oracle Linux 6 e Oracle Linux 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14, 4 LTS, 16, 4 LTS e 18, 4 LTS
* SUSE Linux Enterprise Server 12

32 bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14, 4 LTS e 16, 4 LTS

> [!NOTE]
> Como a lista de sistemas operacionais Linux com suporte está em constante mudança, se você preferir, clique [aqui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) para exibir a lista mais atualizada de versões com suporte, caso haja alterações desde que este tópico foi publicado pela última vez.

Para saber mais sobre os recursos com suporte para os sistemas operacionais Linux listados acima, consulte [recursos com suporte do servidor/máquina virtual](security-center-services.md##vm-server-features).

### Serviços de máquina virtual gerenciados<a name="virtual-machine"></a>

As máquinas virtuais também são criadas em uma assinatura de cliente como parte de alguns serviços gerenciados do Azure também, como o Azure kubernetes (AKS), Azure Databricks e muito mais. Essas máquinas virtuais também são descobertas pela central de segurança do Azure e o agente do log Analytics pode ser instalado e configurado de acordo com os [sistemas operacionais Windows/Linux](#os-windows)com suporte, listados acima.

### Serviços de nuvem<a name="cloud-services"></a>

Também há suporte para máquinas virtuais que são executadas em um serviço de nuvem. Somente as funções Web e de trabalho dos serviços de nuvem que são executadas em slots de produção são monitoradas. Para saber mais sobre os serviços de nuvem, consulte [visão geral dos serviços de nuvem do Azure](../cloud-services/cloud-services-choose-me.md).

## Serviços de PaaS<a name="paas-services"></a>

Os seguintes recursos de PaaS do Azure têm suporte na central de segurança do Azure:

* SQL
* PostGreSQL
* MySQL
* CosmosDB
* Conta de armazenamento
* Serviço de aplicações
* Função
* Serviço Cloud
* VNet
* Subrede
* NIC
* NSG
* Conta do Batch
* Conta do Service Fabric
* Conta de automatização
* Balanceador de carga
* Search
* Espaço de nomes do Service Bus
* Stream Analytics
* Espaço de nomes do hub de eventos
* Aplicações lógicas
* Redis
* Data Lake Analytics
* Arquivo do Data Lake
* Key vault

Para saber mais sobre os recursos com suporte para a lista acima de recursos de PaaS, consulte [recursos compatíveis com serviços de PaaS](security-center-services.md#paas-services).

Também há suporte para a proteção para máquinas virtuais que residem no Azure Stack. Para obter mais informações sobre a integração da central de segurança com o Azure Stack, consulte integrar [suas máquinas virtuais Azure Stack à central de segurança](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Saiba mais sobre [os recursos disponíveis para os diferentes ambientes de nuvem](security-center-services.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a central de segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a segurança e a conformidade do Azure](https://blogs.msdn.com/b/azuresecurity/).
