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
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: 7ffab200b50a7c4d1af60a230f237b18e8afc621
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754220"
---
# <a name="supported-features-available-in-azure-security-center"></a>Recursos com suporte disponíveis na central de segurança do Azure

> [!NOTE]
>Alguns recursos estão disponíveis apenas com a camada Standard. Se você ainda não tiver se inscrito na camada Standard da central de segurança, um período de avaliação gratuita estará disponível. Para obter mais informações, consulte a [página de preços da central de segurança](https://azure.microsoft.com/pricing/details/security-center/).

As seções a seguir mostram os recursos da central de segurança que estão disponíveis para suas [plataformas com suporte](security-center-os-coverage.md).

* [Máquinas virtuais/servidores](#vm-server-features)
* [Serviços de PaaS](#paas-services)


## Recursos com suporte para máquina virtual/servidor<a name="vm-server-features"></a>

### <a name="windowstabfeatures-windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Computadores não Azure**|**Preços**
|[Integração do Microsoft defender ATP](security-center-wdatp.md)|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|Padrão|
|[Alertas de detecção de ameaças de análise comportamental de máquina virtual](security-center-alerts-iaas.md)|✔|✔|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|[Alertas de detecção de ameaças com arquivo](alerts-reference.md#alerts-windows)|✔|✔|✔|Padrão|
|[Alertas de detecção de ameaças baseados em rede](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Padrão|
|[Acesso à VM just-in-time](security-center-just-in-time.md)|✔|-|-|Padrão|
|[Avaliação de vulnerabilidades nativas](built-in-vulnerability-assessment.md)|✔|-|-|Padrão|
|[Monitoramento de integridade de arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Padrão|
|[Controlos de aplicação adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Padrão|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Padrão|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Padrão|
|Controles de rede adaptáveis|✔|✔|-|Padrão|
|[Painel de conformidade regulatória & relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Padrão|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|-|-|-|Padrão|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|Gratuito|
|Avaliação de incorretas de segurança|✔|✔|✔|Gratuito|
|[Avaliação do Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuito|
|Avaliação de criptografia de disco|✔|✔|-|Gratuito|
|Avaliação de vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linuxtabfeatures-linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Computadores não Azure**|**Preços**
|[Integração do Microsoft defender ATP](security-center-wdatp.md)|-|-|-|Padrão|
|[Alertas de detecção de ameaças de análise comportamental de máquina virtual](security-center-alerts-iaas.md)|✔ (em versões com suporte)|✔ (em versões com suporte)|✔|Recomendações (gratuito) detecção de ameaças (padrão)|
|[Alertas de detecção de ameaças com arquivo](alerts-reference.md#alerts-windows)|-|-|-|Padrão|
|[Alertas de detecção de ameaças baseados em rede](security-center-alerts-service-layer.md#azure-network-layer)|✔|✔|-|Padrão|
|[Acesso à VM just-in-time](security-center-just-in-time.md)|✔|-|-|Padrão|
|[Avaliação de vulnerabilidades nativas](built-in-vulnerability-assessment.md)|✔|-|-|Padrão|
|[Monitoramento de integridade de arquivo](security-center-file-integrity-monitoring.md)|✔|✔|✔|Padrão|
|[Controlos de aplicação adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Padrão|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Padrão|
|[Proteção de rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Padrão|
|Controles de rede adaptáveis|✔|✔|-|Padrão|
|[Painel de conformidade regulatória & relatórios](security-center-compliance-dashboard.md)|✔|✔|✔|Padrão|
|Recomendações e detecção de ameaças em contêineres de IaaS hospedados pelo Docker|✔|✔|✔|Padrão|
|Avaliação de patches do sistema operacional ausente|✔|✔|✔|Gratuito|
|Avaliação de incorretas de segurança|✔|✔|✔|Gratuito|
|[Avaliação do Endpoint Protection](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Avaliação de criptografia de disco|✔|✔|-|Gratuito|
|Avaliação de vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação de segurança de rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 

## Soluções de proteção de ponto de extremidade com suporte<a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se você pode usar a central de segurança do Azure para instalar cada solução para você.
 - Que a central de segurança das soluções do Endpoint Protection pode descobrir. Se uma solução de proteção de ponto de extremidade dessa lista for descoberta, a central de segurança não recomendará a instalação de uma.

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

 **\*** O estado de cobertura e os dados de suporte estão atualmente disponíveis apenas no espaço de trabalho Log Analytics associado às suas assinaturas protegidas. Ele não é refletido no portal da central de segurança do Azure.

> [!NOTE]
> - A detecção do System Center Endpoint Protection (SCEP) em uma máquina virtual do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell 3,0 (ou uma versão superior).
> - Há suporte para a detecção da proteção Trend Micro para agentes de segurança profundas.  Não há suporte para agentes do OfficeScan.


## Recursos <a name="paas-services"></a> com suporte de serviços de PaaS

Os recursos de PaaS a seguir têm suporte na central de segurança do Azure:

|Serviço|Recomendações (gratuito)|Alertas de detecção de ameaças (Standard)|Avaliação de vulnerabilidade (padrão)|
|----|:----:|:----:|:----:|
|Bases de Dados SQL|✔|✔|✔|
|Azure Container Registry|-|-|✔|
|Serviço Kubernetes do Azure|✔|✔|-|
|Banco de dados do Azure para PostgreSQL *|✔|✔|-|
|Banco de dados do Azure para MySQL *|✔|✔|-|
|CosmosDB do Azure *|-|✔|-|
|Contas de Armazenamento|✔|-|-|
|Blob Storage|✔|✔|-|
|Serviço de Aplicações|✔|✔|-|
|Function app|✔|-|-|
|Serviços em Nuvem|✔|-|-|
|Rede Virtual|✔|-|-|
|Subrede|✔|-|-|
|NIC|✔|-|-|
|Grupos de Segurança de Rede|✔|-|-|
|Subscrição|✔ **|✔|-|
|Conta do Batch|✔|-|-|
|Conta de Service Fabric|✔|-|-|
|Conta de automatização|✔|-|-|
|Load Balancer|✔|-|-|
|Pesquisa Cognitiva|✔|-|-|
|Espaço de nomes do Service Bus|✔|-|-|
|Stream Analytics|✔|-|-|
|Espaço de nomes do hub de eventos|✔|-|-|
|Aplicações lógicas|✔|-|-|
|Cache para Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Armazenamento do Azure Data Lake|✔|-|-|
|Key Vault|✔|✔ *|-|

\* esses recursos têm suporte no momento na versão prévia.

as recomendações do \*\* Azure Active Directory (AD do Azure) estão disponíveis somente para assinaturas padrão.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [a central de segurança coleta dados e o agente de log Analytics](security-center-enable-data-collection.md).
- Saiba como a [central de segurança gerencia e protege os dados](security-center-data-security.md).
- Saiba como [planejar e entender as considerações de design para adotar a central de segurança do Azure](security-center-planning-and-operations-guide.md).
- Examine as [plataformas que dão suporte à central de segurança](security-center-os-coverage.md).
- Saiba mais sobre [a detecção de ameaças para VMs & servidores na central de segurança do Azure](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre como usar a central de segurança do Azure](security-center-faq.md).
- Encontre [postagens no blog sobre a segurança e a conformidade do Azure](https://blogs.msdn.com/b/azuresecurity/).