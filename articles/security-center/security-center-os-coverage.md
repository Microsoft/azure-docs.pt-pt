---
title: Recursos e plataformas suportadas pelo centro de segurança do Azure | Documentos da Microsoft
description: Este documento fornece uma lista de recursos e plataformas suportadas pelo centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: monhaber
ms.openlocfilehash: c5b5b88ee1334ac6d7b39b8ad53bd020e6042454
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480532"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plataformas e funcionalidades suportadas pelo centro de segurança do Azure

Monitorização de estado de segurança e recomendações, estão disponíveis para máquinas virtuais (VMs), criadas usando o clássico e modelos de implementação do Resource Manager e computadores.

> [!NOTE]
> Saiba mais sobre o [clássico e modelos de implementação do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plataformas que suportam o agente de recolha de dados 

Esta secção lista as plataformas em que pode executar o agente do Centro de segurança do Azure e de que esta possa recolher dados.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plataformas suportadas para VMs e computadores do Windows
São suportados os seguintes sistemas operativos Windows:

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Integração com o Windows Defender ATP suporta apenas o Windows Server 2012 R2 e o Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plataformas suportadas para VMs e computadores Linux

São suportados os seguintes sistemas operativos Linux:

> [!NOTE]
> Uma vez que a lista de sistemas operativos Linux suportados forem alterados constantemente, se preferir, clique em [aqui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) para ver a lista mais atualizada das versões suportadas, no caso de ter sido efetuadas alterações uma vez que este tópico foi publicado pela última vez.

64 bits
* CentOS 6 e 7
* Amazon Linux 2017.09
* Oracle Linux 6 e 7
* Red Hat Enterprise Linux Server 6 e 7
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS e 18.04 LTS
* SUSE Linux Enterprise Server 12

32 bits
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 e 9
* Ubuntu Linux 14.04 LTS e 16.04 LTS

## <a name="vms-and-cloud-services"></a>VMs e serviços Cloud
VMs que são executadas num serviço cloud também são suportadas. Apenas funções na nuvem serviços web e de trabalho que são executados nas ranhuras de produção são monitorizadas. Para saber mais sobre os serviços cloud, veja [descrição geral dos serviços Cloud do Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Recursos de IaaS suportados

> [!div class="mx-tableFixed"]
> 

|Servidor|Windows||Linux||||Preços|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**Azure**||**Non-Azure**|**Azure**||**Non-Azure**||
||**Máquina Virtual**|**Conjunto de dimensionamento de máquina virtual**||**Máquina Virtual**|**Conjunto de dimensionamento de máquina virtual**|
|Alertas de deteção de ameaças VMBA|✔|✔|✔|✔ (em versões suportadas)|✔ (em versões suportadas)|✔|Deteção de ameaças (gratuito) de recomendações (padrão)|
|Alertas de deteção de ameaças baseada em rede|✔|✔|X|✔|✔|X|Standard|
|Integração do Windows Defender ATP|✔ (em versões suportadas)|✔ (em versões suportadas)|✔|X|X|X|Standard|
|Patches em falta|✔|✔|✔|✔|✔|✔|Livre|
|Configurações de segurança|✔|✔|✔|✔|✔|✔|Livre|
|Avaliação de proteção de ponto final|✔|✔|✔|X|X|X|Livre|
|Acesso JIT da VM|✔|X|X|✔|X|X|Standard|
|Controlos de aplicações adaptáveis|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|Avaliação de encriptação de disco|✔|✔|X|✔|✔|X|Livre|
|Implantação de terceiros|✔|X|X|✔|X|X|Livre|
|Avaliação de NSG|✔|✔|X|✔|✔|X|Livre|
|Deteção de ameaças fileless|✔|✔|✔|X|X|X|Standard|
|Mapa de rede|✔|✔|X|✔|✔|X|Standard|
|Controlos de rede adaptável|✔|✔|X|✔|✔|X|Standard|
|Dashboard de conformidade regulamentar e relatórios|✔|✔|✔|✔|✔|✔|Standard|
|Recomendações e deteção de ameaças em contentores do Docker alojado IaaS|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade suportados

A tabela seguinte fornece uma matriz de:
 - Se é possível utilizar o Centro de segurança do Azure para instalar cada solução.
 - Pode descobrir qual Centro de segurança de soluções de proteção de ponto final. Se uma destas soluções de proteção de ponto final for detetada, o Centro de segurança irá recomendar não instalando uma.

Para obter informações sobre o quando as recomendações são geradas para cada uma dessas proteções, veja [avaliação de proteção de ponto final e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (ver nota abaixo) | Através de Extensão | Sim |
| Trend Micro – Todas as versões | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| Kaspersky| Família Windows Server  | Não | Não  |
| Sophos| Família Windows Server  | Não | Não  |

> [!NOTE]
> - A deteção do System Center Endpoint Protection (SCEP) numa máquina virtual do Windows Server 2008 R2 requer SCEP ser instaladas após PowerShell 3.0 (ou uma versão superior).

## <a name="supported-paas-features"></a>Recursos de PaaS suportados


|Serviço|Recomendações (gratuitas)|Deteção de ameaças (padrão)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Contas de armazenamento de Blobs do Azure|✔| ✔|
|Serviços aplicacionais|✔| ✔|
|Serviços Cloud|✔| X|
|VNets|✔| N/D|
|Sub-redes|✔| N/D|
|NICs|✔| N/D|
|NSGs|✔| N/D|
|Subscrição|✔ **| ✔|
|Batch|✔| N/D|
|Service fabric|✔| N/D|
|Conta de automatização|✔| N/D|
|Load balancer|✔| N/D|
|Pesquisa|✔| N/D|
|Service Bus|✔| N/D|
|Stream Analytics|✔| N/D|
|Hub de eventos|✔| N/D|
|Aplicações lógicas|✔| N/D|
|Conta de armazenamento|✔| N/D|
|Redis|✔| N/D|
|O Data lake analytics|✔| N/D|
|Key Vault|✔| N/D|




\* Estas funcionalidades são suportadas atualmente em pré-visualização pública.

\*\* Recomendações de AAD só estão disponíveis para as subscrições Standard



## <a name="next-steps"></a>Passos Seguintes

- Saiba como [planear e compreender as considerações de conceção para adoção do Centro de segurança do Azure](security-center-planning-and-operations-guide.md).
- Saiba mais sobre [análise comportamental de máquinas virtuais e despejo de memória análise de memória no Centro de segurança](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Encontrar [perguntas frequentes sobre o Centro de segurança do Azure](security-center-faq.md).
- Encontrar [mensagens do Blogue acerca da segurança do Azure e a conformidade](https://blogs.msdn.com/b/azuresecurity/).
