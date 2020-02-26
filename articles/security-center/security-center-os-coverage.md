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
ms.date: 08/29/2019
ms.author: memildin
ms.openlocfilehash: 089fcc89f330e47a09187b5338176bf23fd801bb
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603763"
---
# <a name="supported-platforms"></a>Plataformas suportadas 

## Máquinas /servidores virtuais<a name="vm-server"></a>

O Security Center suporta máquinas/servidores virtuais em diferentes tipos de ambientes híbridos:

* Apenas Azure
* Azure e no local
* Azul e outras nuvens
* Azure, outras nuvens, e no local

Para um ambiente Azure ativado numa subscrição do Azure, o Azure Security Center descobrirá automaticamente os recursos IaaS que são implantados dentro da subscrição.

> [!NOTE]
> Para receber o conjunto completo de funcionalidades de segurança, deve ter o [Agente De Analítico](../azure-monitor/platform/agents-overview.md#log-analytics-agent)de Log, que é utilizado pelo Azure Security Center, instalado e [devidamente configurado para enviar dados para](security-center-enable-data-collection.md#manual-agent)o Azure Security Center .


As seguintes secções listam os sistemas operativos do servidor suportados nos quais o Agente de Análise de [Registo,](../azure-monitor/platform/agents-overview.md#log-analytics-agent)que é utilizado pelo Azure Security Center, pode funcionar.

### Sistemas operativos do servidor windows<a name="os-windows"></a>

|SO|Apoiado pelo Azure Security Center|Suporte para integração com o Microsoft Defender ATP|
|:---|:-:|:-:|
|Windows Server 2019|✔|X|
|Windows Server 2016|✔|✔|
|Windows Server 2012 R2|✔|✔|
|Windows Server 2008 R2|✔|✔|

Para saber mais sobre as funcionalidades suportadas para os sistemas operativos Windows, listados acima, consulte [funcionalidades suportadas por máquinas virtuais /servidor](security-center-services.md#vm-server-features).

### Sistemas operativos Windows<a name="os-windows (non-server)"></a>

O Azure Security Center integra-se com os serviços Azure para monitorizar e proteger as suas máquinas virtuais baseadas no Windows.

### Sistemas operativos Linux<a name="os-linux"></a>

64 bits

* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e Oracle Linux 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS, 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bits
* Centos 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

> [!NOTE]
> Uma vez que a lista de sistemas operativos Linux suportados está em constante mudança, se preferir, clique [aqui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) para ver a lista mais atualizada de versões suportadas, caso tenha havido alterações desde que este tópico foi publicado pela última vez.

Para saber mais sobre as funcionalidades suportadas para os sistemas operativos Linux, listados acima, consulte [funcionalidades suportadas por máquinas virtuais /servidor](security-center-services.md#vm-server-features).

### Serviços de máquinas virtuais geridos<a name="virtual-machine"></a>

As máquinas virtuais também são criadas numa subscrição de clientes como parte de alguns serviços geridos pelo Azure, como o Azure Kubernetes (AKS), o Azure Databricks, entre outros. Estas máquinas virtuais também são descobertas pelo Azure Security Center, e o agente de análise de registo pode ser instalado e configurado de acordo com os [sistemas operativos Windows/Linux](#os-windows)suportados, listados acima.

### Serviços cloud<a name="cloud-services"></a>

As máquinas virtuais que funcionam num serviço de nuvem também são suportadas. Apenas os serviços na nuvem web e funções de trabalhador que funcionam em slots de produção são monitorizados. Para saber mais sobre os serviços na nuvem, consulte a [visão geral dos Serviços Azure Cloud.](../cloud-services/cloud-services-choose-me.md)

## Serviços PaaS<a name="paas-services"></a>

Os seguintes recursos Azure PaaS são apoiados pelo Azure Security Center:

* SQL
* Postgresql
* MySQL
* CosmosDB
* Conta de armazenamento
* Serviço de aplicações
* Função
* Serviço de Nuvem
* VNet
* Subrede
* NIC
* NSG
* Conta do Batch
* Conta de tecido de serviço
* Conta de automatização
* Load balancer
* Pesquisa
* Espaço de nomes do Service Bus
* Stream Analytics
* Espaço de nomes do hub de eventos
* Aplicativos lógicos
* Redis
* Data Lake Analytics
* Data Lake Store
* Key Vault

Para saber mais sobre as funcionalidades suportadas para a lista acima dos recursos paaS, consulte [as funcionalidades suportadas pelos serviços paaS.](security-center-services.md#paas-services)

A proteção das máquinas virtuais residentes no Azure Stack também é suportada. Para obter mais informações sobre a integração do Security Center com o Azure Stack, consulte as [suas máquinas virtuais Azure Stack para o Security Center](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack).

## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados e o Agente de Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Centro de Segurança gere e salvaguarda dados.](security-center-data-security.md)
- Saiba como [planear e compreender as considerações de design para adotar o Azure Security Center.](security-center-planning-and-operations-guide.md)
- Conheça [as funcionalidades disponíveis para os diferentes ambientes em nuvem.](security-center-services.md)
- Saiba mais sobre [a deteção de ameaças para VMs e servidores no Azure Security Center](security-center-alerts-iaas.md).