---
title: Recursos com suporte disponíveis na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de serviços com suporte pela central de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2019
ms.author: v-mohabe
ms.openlocfilehash: 0d2b417dd01e26f0f93722be66d82972b52aca83
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234585"
---
# <a name="supported-features-available-in-azure-security-center"></a>Recursos com suporte disponíveis na central de segurança do Azure

> [!NOTE]
>Alguns recursos estão disponíveis apenas com a camada Standard. Se você ainda não tiver se inscrito na camada Standard da central de segurança, um período de avaliação gratuita estará disponível. Veja a [página de preços do Centro de Segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/), para obter mais informações.

As seções a seguir mostram os recursos da central de segurança que estão disponíveis para suas [plataformas com suporte](security-center-os-coverage.md).

* [Máquinas virtuais/servidores](#vm-server-features)
* [Serviços de PaaS](#paas-services)


## Recursos com suporte para máquina virtual/servidor<a name="vm-server-features"></a>

> [!div class="mx-tableFixed"]

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

### Soluções de proteção de ponto de extremidade com suporte<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a central de segurança do Azure para instalar cada solução para você.
 - Que a central de segurança das soluções do Endpoint Protection pode descobrir. Se uma dessas soluções de proteção de ponto de extremidade for descoberta, a central de segurança não recomendará a instalação de uma.

Para obter informações sobre quando as recomendações são geradas para cada uma dessas proteções, consulte [Endpoint Protection avaliação e recomendações](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (veja a observação abaixo) | Através de Extensão | Sim |
| Trend Micro – todas as versões * | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família de servidores Linux  | Não | Ok **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Ok **\***  |

 **\*** O estado de cobertura e os dados de suporte estão disponíveis no momento apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas e não são refletidos no portal da central de segurança do Azure.

> [!NOTE]
>
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell 3,0 (ou uma versão superior).
> - Há suporte para a detecção da proteção Trend Micro para agentes de segurança profundas.  Não há suporte para agentes do OfficeScan.


## Recursos <a name="paas-services"></a> com suporte de serviços de PaaS

Os recursos de PaaS a seguir têm suporte na central de segurança do Azure:

|Serviço|Recomendações (gratuito)|Detecção de ameaças (padrão)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|CosmosDB|X| ✔|
|Armazenamento de blobs|✔| ✔|
|Conta de armazenamento|✔| ND|
|Serviço de aplicações|✔| ✔|
|Função|✔| X|
|Serviço em Nuvem|✔| X|
|VNET|✔| ND|
|Subnet|✔| ND|
|NIC|✔| ND|
|NSG|✔| ND|
|Subscription|✔ **| ✔|
|Conta do Batch|✔| X|
|Conta do Service Fabric|✔| X|
|Conta de automatização|✔| X|
|Balanceador de carga|✔| X|
|Pesquisa|✔| X|
|Espaço de nomes do Service Bus|✔| X|
|Stream Analytics|✔| X|
|Espaço de nomes do hub de eventos|✔| X|
|Aplicações lógicas|✔| X|
|Redis|✔| ND|
|Data Lake Analytics|✔| X|
|Data Lake Store|✔| X|
|Cofre de chaves|✔| X|

\*Atualmente, esses recursos têm suporte na visualização pública.

\*\*As recomendações do Azure Active Directory (Azure AD) estão disponíveis apenas para assinaturas padrão.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a central de segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a segurança e a conformidade do Azure](https://blogs.msdn.com/b/azuresecurity/).
