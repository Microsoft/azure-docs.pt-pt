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
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 9d3fa1e0b62ea6f4762c3df6ac7da310d5703807
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245242"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recurso para máquinas

As tabelas abaixo mostram as funcionalidades do Azure Security Center que estão disponíveis para máquinas virtuais e servidores.

## Funcionalidades suportadas para máquinas virtuais e servidores<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Máquinas windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de escala de máquina seleção azul**|**Máquinas não-Azure**|**Preços**
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Standard|
|[Análise Comportamental de Máquina Virtual (e alertas de segurança)](threat-protection.md)|✔|✔|✔|Recomendações (Grátis) </br></br> Alertas de segurança (Standard)|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicação adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa da rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Endurecimento da rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlos de rede adaptativo|✔|✔|-|Standard|
|[Dashboard e relatórios de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados pelo Docker|-|-|-|Standard|
|Avaliação de patches de OS em falta|✔|✔|✔|Gratuito|
|Avaliação de configurações erradas de segurança|✔|✔|✔|Gratuito|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Gratuito|
|Avaliação da encriptação do disco|✔|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux-machines"></a>[Máquinas Linux](#tab/features-linux)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de escala de máquina seleção azul**|**Máquinas não-Azure**|**Preços**
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|-|-|-|Standard|
|[Análise Comportamental de Máquina Virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Recomendações (Grátis) </br></br> Alertas de segurança (Standard)|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicação adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa da rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Endurecimento da rede adaptável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlos de rede adaptativo|✔|✔|-|Standard|
|[Dashboard e relatórios de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados pelo Docker|✔|✔|✔|Standard|
|Avaliação de patches de OS em falta|✔|✔|✔|Gratuito|
|Avaliação de configurações erradas de segurança|✔|✔|✔|Gratuito|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Avaliação da encriptação do disco|✔|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 


> [!TIP]
>Para experimentar funcionalidades que só estão disponíveis no nível de preços padrão, os utilizadores de nível gratuito podem inscrever-se num ensaio de 30 dias. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).


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


## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados e o Agente de Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Centro de Segurança gere e salvaguarda dados.](security-center-data-security.md)
- Saiba como [planear e compreender as considerações de design para adotar o Azure Security Center.](security-center-planning-and-operations-guide.md)
- Reveja as plataformas que suportam o [centro de segurança.](security-center-os-coverage.md)
- Saiba mais sobre [a proteção contra ameaças para máquinas Windows e Linux no Azure Security Center](threat-protection.md#windows-machines).
- Encontre [perguntas frequentes sobre o Azure Security Center.](faq-general.md)