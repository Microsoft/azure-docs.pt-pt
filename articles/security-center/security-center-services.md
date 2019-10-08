---
title: Recursos com suporte disponíveis na central de segurança do Azure | Microsoft Docs
description: Este documento fornece uma lista de serviços com suporte pela central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 870ebc8d-1fad-435b-9bf9-c477f472ab17
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: d756f9dfbd0012f884bb0c4a1e27efc76d613234
ms.sourcegitcommit: 387da88b8262368c1b67fffea58fe881308db1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71982828"
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
|Alertas de detecção de ameaças de análise comportamental de máquina virtual|✔|✔|✔|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|Alertas de detecção de ameaças baseados em rede|✔|✔|X|✔|✔|X|Standard|
|Integração do Microsoft defender ATP|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|X|X|X|Standard|
|Patches ausentes|✔|✔|✔|✔|✔|✔|Livre|
|Configurações de segurança|✔|✔|✔|✔|✔|✔|Livre|
|Avaliação do Endpoint Protection|✔|✔|✔|X|X|X|Livre|
|Acesso à VM just-in-time|✔|X|X|✔|X|X|Standard|
|Controlos de aplicação adaptável|✔|X|✔|✔|X|✔|Standard|
|Monitorização da Integridade do Ficheiro|✔|✔|✔|✔|✔|✔|Standard|
|Avaliação de criptografia de disco|✔|✔|X|✔|✔|X|Livre|
|Implantação de terceiros|✔|X|X|✔|X|X|Livre|
|Avaliação do NSG|✔|✔|X|✔|✔|X|Livre|
|Detecção de ameaças de arquivo|✔|✔|✔|X|X|X|Standard|
|Mapa de rede|✔|✔|X|✔|✔|X|Standard|
|Proteção de rede ajustável|✔|X|X|✔|X|X|Standard|
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
| McAfee v10+ | Família de servidores Linux  | Não | Sim **\*** |
| Sophos v9 +| Família de servidores Linux  | Não | Sim **\***  |

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
|VNet|✔| ND|
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

\* esses recursos têm suporte atualmente na visualização pública.

as recomendações \* @ no__t-1 Azure Active Directory (Azure AD) estão disponíveis somente para assinaturas padrão.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a central de segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a segurança e a conformidade do Azure](https://blogs.msdn.com/b/azuresecurity/).
