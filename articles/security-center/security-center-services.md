---
title: Funcionalidades suportadas disponíveis no Azure Security Center [ Centro de Segurança Do Azure] Microsoft Docs
description: Este documento fornece uma lista de serviços apoiados pelo Azure Security Center.
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
ms.openlocfilehash: 8d905da733ea0573b91b289da43684ed8083c804
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2020
ms.locfileid: "77616376"
---
# <a name="supported-features-available-in-azure-security-center"></a>Funcionalidades suportadas disponíveis no Azure Security Center

> [!NOTE]
>Algumas funcionalidades só estão disponíveis com o nível Standard. Se ainda não se inscreveu no nível Standard do Security Center, está disponível um período de teste gratuito. Para mais informações, consulte a página de preços do Centro de [Segurança](https://azure.microsoft.com/pricing/details/security-center/).

As seguintes secções mostram funcionalidades do Centro de Segurança que estão disponíveis para as suas [plataformas suportadas.](security-center-os-coverage.md)

* [Máquinas /servidores virtuais](#vm-server-features)
* [Serviços PaaS](#paas-services)


## Funcionalidades suportadas por máquina virtual / servidor<a name="vm-server-features"></a>

### <a name="windows"></a>[Windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Máquinas Virtuais do Azure**|**Conjuntos de escala de máquina seleção azul**|**Máquinas não-Azure**|**Preços**
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|✔ (em versões suportadas)|✔ (em versões suportadas)|✔|Standard|
|[Alertas de deteção de ameaças de análise comportamental de máquina virtual](threat-protection.md)|✔|✔|✔|Recomendações (Grátis) Deteção de Ameaças (Standard)|
|[Alertas de deteção de ameaças sem ficheiro](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alertas de deteção de ameaças baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicação adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa da rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Endurecimento da rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlos de rede adaptativo|✔|✔|-|Standard|
|[Dashboard e relatórios de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e deteção de ameaças em contentores IaaS hospedados pelo Docker|-|-|-|Standard|
|Avaliação de patches de OS em falta|✔|✔|✔|Gratuito|
|Avaliação de configurações erradas de segurança|✔|✔|✔|Gratuito|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuito|
|Avaliação da encriptação do disco|✔|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux"></a>[Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
||**Máquinas Virtuais do Azure**|**Conjuntos de escala de máquina seleção azul**|**Máquinas não-Azure**|**Preços**
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|-|-|-|Standard|
|[Alertas de deteção de ameaças de análise comportamental de máquina virtual](security-center-alerts-iaas.md)|✔ (em versões suportadas)|✔ (em versões suportadas)|✔|Recomendações (Grátis) Deteção de Ameaças (Standard)|
|[Alertas de deteção de ameaças sem ficheiro](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alertas de deteção de ameaças baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicação adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa da rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Endurecimento da rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlos de rede adaptativo|✔|✔|-|Standard|
|[Dashboard e relatórios de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e deteção de ameaças em contentores IaaS hospedados pelo Docker|✔|✔|✔|Standard|
|Avaliação de patches de OS em falta|✔|✔|✔|Gratuito|
|Avaliação de configurações erradas de segurança|✔|✔|✔|Gratuito|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Avaliação da encriptação do disco|✔|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 

## Soluções de proteção de pontos finais suportadas<a name="endpoint-supported"></a>

A tabela seguinte fornece uma matriz de:

 - Se pode utilizar o Azure Security Center para instalar cada solução para si.
 - Que soluções de proteção de ponta saem o Centro de Segurança pode descobrir. Se for descoberta uma solução de proteção de ponto final desta lista, o Security Center não recomenda a instalação de uma.

Para obter informações sobre quando são geradas recomendações para cada uma destas proteções, consulte [avaliação e recomendações](security-center-endpoint-protection.md)de proteção do ponto final .

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (ver nota abaixo) | Através de Extensão | Sim |
| Trend Micro – Todas as versões* | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Linux Server  | Não | Sim, **\*** |
| Sophos V9+| Família Linux Server  | Não | Sim, **\***  |

 **\*** Atualmente, o estado de cobertura e os dados de suporte estão apenas disponíveis no espaço de trabalho log Analytics associado às suas subscrições protegidas. Não se reflete no portal do Centro de Segurança Azure.

> [!NOTE]
> - A deteção da Proteção do Ponto Final do Centro de Sistema (SCEP) numa máquina virtual Do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).
> - A deteção da proteção Trend Micro é suportada para agentes de Segurança Profunda.  Os agentes do OfficeScan não são apoiados.


## Serviços PaaS <a name="paas-services"></a> suportados recursos

Os seguintes recursos PaaS são apoiados pelo Azure Security Center:

|Serviço|Recomendações (Grátis)|Alertas de deteção de ameaças (Standard)|Avaliação de vulnerabilidade (Standard)|
|----|:----:|:----:|:----:|
|Bases de Dados SQL|✔|✔|✔|
|Registo de Contentores do Azure|-|-|✔|
|Azure Kubernetes Service|✔|✔|-|
|Base de Dados Azure para PostgreSQL*|✔|✔|-|
|Base de Dados Azure para MySQL*|✔|✔|-|
|Azure CosmosDB*|-|✔|-|
|Contas de Armazenamento|✔|-|-|
|Armazenamento de Blobs|✔|✔|-|
|Serviço de Aplicações|✔|✔|-|
|Function app|✔|-|-|
|Serviços Cloud|✔|-|-|
|Rede Virtual|✔|-|-|
|Subrede|✔|-|-|
|NIC|✔|-|-|
|Grupos de Segurança de Rede|✔|-|-|
|Subscrição|✔ **|✔|-|
|Conta do Batch|✔|-|-|
|Conta de Tecido de Serviço|✔|-|-|
|Conta de automatização|✔|-|-|
|Balanceador de Carga|✔|-|-|
|Cognitive Search|✔|-|-|
|Espaço de nomes do Service Bus|✔|-|-|
|Stream Analytics|✔|-|-|
|Espaço de nomes do hub de eventos|✔|-|-|
|Aplicativos lógicos|✔|-|-|
|Cache para Redis|✔|-|-|
|Data Lake Analytics|✔|-|-|
|Armazenamento do Azure Data Lake|✔|-|-|
|Cofre de Chaves|✔|✔|-|

\* Estas funcionalidades são atualmente suportadas na pré-visualização.

\*\* recomendações do Azure Ative Directory (Azure AD) estão disponíveis apenas para subscrições Standard.

## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados e o Agente de Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Centro de Segurança gere e salvaguarda dados.](security-center-data-security.md)
- Saiba como [planear e compreender as considerações de design para adotar o Azure Security Center.](security-center-planning-and-operations-guide.md)
- Reveja as plataformas que suportam o [centro de segurança.](security-center-os-coverage.md)
- Saiba mais sobre [a deteção de ameaças para VMs e servidores no Azure Security Center](security-center-alerts-iaas.md).
- Encontre [perguntas frequentes sobre o Azure Security Center.](faq-general.md)
- Encontre posts de [blog sobre segurança e conformidade do Azure.](https://blogs.msdn.com/b/azuresecurity/)