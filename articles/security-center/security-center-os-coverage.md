---
title: Recursos e plataformas com suporte na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de recursos e plataformas com suporte pela central de segurança do Azure.
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
ms.author: v-mohabe
ms.openlocfilehash: 7cd1d451b49faf2f8e3ad38f4ff780256ef2dc5d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883607"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Plataformas e recursos com suporte na central de segurança do Azure

O monitoramento e as recomendações de estado de segurança estão disponíveis para VMs (máquinas virtuais), criadas usando os modelos de implantação clássico e do Resource Manager e os computadores.

> [!NOTE]
> Saiba mais sobre os [modelos de implantação clássico e do Resource Manager](../azure-classic-rm.md) para recursos do Azure.
>

## <a name="platforms-that-support-the-data-collection-agent"></a>Plataformas que dão suporte ao agente de coleta de dados 

Esta seção lista as plataformas nas quais o agente da central de segurança do Azure pode ser executado e a partir da qual ele pode coletar dados.

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Plataformas com suporte para computadores Windows e VMs
Há suporte para os seguintes sistemas operacionais Windows:

* Windows Server de 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> A integração com o Windows Defender ATP dá suporte apenas ao Windows Server 2012 R2 e ao Windows Server 2016.

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Plataformas com suporte para computadores Linux e VMs

Há suporte para os seguintes sistemas operacionais Linux:

> [!NOTE]
> Como a lista de sistemas operacionais Linux com suporte está em constante mudança, se você preferir, clique [aqui](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems) para exibir a lista mais atualizada de versões com suporte, caso haja alterações desde que este tópico foi publicado pela última vez.

64 bits
* CentOS 6 e 7
* Amazon Linux 2017, 9
* Oracle Linux 6 e 7
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

## <a name="vms-and-cloud-services"></a>VMs e serviços de nuvem
Também há suporte para VMs que são executadas em um serviço de nuvem. Somente as funções Web e de trabalho dos serviços de nuvem que são executadas em slots de produção são monitoradas. Para saber mais sobre os serviços de nuvem, consulte [visão geral dos serviços de nuvem do Azure](../cloud-services/cloud-services-choose-me.md).


## <a name="supported-iaas-features"></a>Recursos de IaaS com suporte

> [!div class="mx-tableFixed"]
> 

|Servidor|Windows|||Linux|||Preços|
|----|----|----|----|----|----|----|----|
|**Ambiente**|**Azure**||**Não Azure**|**Azure**||**Não Azure**||
||**Máquina Virtual**|**Conjunto de dimensionamento de máquinas virtuais**||**Máquina Virtual**|**Conjunto de dimensionamento de máquinas virtuais**|
|Alertas de detecção de ameaças do VMBA|✔|✔|✔|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|Alertas de detecção de ameaças baseados em rede|✔|✔|X|✔|✔|X|Standard|
|Integração do Windows Defender ATP|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|X|X|X|Standard|
|Patches ausentes|✔|✔|✔|✔|✔|✔|Livre|
|Configurações de segurança|✔|✔|✔|✔|✔|✔|Livre|
|Avaliação do Endpoint Protection|✔|✔|✔|X|X|X|Livre|
|Acesso JIT da VM|✔|X|X|✔|X|X|Standard|
|Controlos de aplicação adaptável|✔|X|✔|✔|X|✔|Standard|
|SELO|✔|✔|✔|✔|✔|✔|Standard|
|Avaliação de criptografia de disco|✔|✔|X|✔|✔|X|Livre|
|Implantação de terceiros|✔|X|X|✔|X|X|Livre|
|Avaliação do NSG|✔|✔|X|✔|✔|X|Livre|
|Detecção de ameaças de arquivo|✔|✔|✔|X|X|X|Standard|
|Mapa de rede|✔|✔|X|✔|✔|X|Standard|
|Controles de rede adaptáveis|✔|✔|X|✔|✔|X|Standard|
|Painel de conformidade regulatória & relatórios|✔|✔|✔|✔|✔|✔|Standard|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de ponto de extremidade com suporte

A tabela a seguir fornece uma matriz de:
 - Se você pode usar a central de segurança do Azure para instalar cada solução para você.
 - Que a central de segurança das soluções do Endpoint Protection pode descobrir. Se uma dessas soluções de proteção de ponto de extremidade for descoberta, a central de segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (veja a observação abaixo) | Através de Extensão | Sim |
| Trend Micro – Todas as versões | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Ok **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Ok **\***  |

 **\*** O estado de cobertura e os dados de suporte estão disponíveis no momento apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas e não são refletidos no portal da central de segurança do Azure.

> [!NOTE]
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell 3,0 (ou uma versão superior).

## <a name="supported-paas-features"></a>Recursos de PaaS com suporte


|Serviço|Recomendações (gratuito)|Detecção de ameaças (padrão)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Contas de armazenamento de BLOBs do Azure|✔| ✔|
|Serviços aplicacionais|✔| ✔|
|Serviços Cloud|✔| X|
|VNets|✔| ND|
|Sub-redes|✔| ND|
|NICs|✔| ND|
|NSGs|✔| ND|
|Subscription|✔ **| ✔|
|Batch|✔| ND|
|Service fabric|✔| ND|
|Conta de automatização|✔| ND|
|Balanceador de carga|✔| ND|
|Pesquisa|✔| ND|
|Service Bus|✔| ND|
|Stream Analytics|✔| ND|
|Hub de eventos|✔| ND|
|Aplicações lógicas|✔| ND|
|Conta de armazenamento|✔| ND|
|Redis|✔| ND|
|Análise data Lake|✔| ND|
|Cofre de chaves|✔| ND|




\*Atualmente, esses recursos têm suporte na visualização pública.

\*\*As recomendações do AAD estão disponíveis somente para assinaturas padrão



## <a name="next-steps"></a>Passos seguintes

- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Saiba mais sobre [análise comportamental de máquina virtual e análise de memória de despejo de falha na central de segurança](security-center-alerts-type.md#virtual-machine-behavioral-analysis).
- Encontre [perguntas frequentes sobre como usar a central de segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a segurança e a conformidade do Azure](https://blogs.msdn.com/b/azuresecurity/).
