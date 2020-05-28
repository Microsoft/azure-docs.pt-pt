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
ms.date: 05/27/2020
ms.author: memildin
ms.openlocfilehash: 22f66a7be27f42bfd0305ad1eaf297ca8c8029fa
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996759"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recurso para máquinas

As tabelas abaixo mostram as funcionalidades do Azure Security Center que estão disponíveis para máquinas virtuais e servidores.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funcionalidades suportadas para máquinas virtuais e servidores<a name="vm-server-features"></a>

### <a name="windows-machines"></a>[Máquinas windows](#tab/features-windows)

|||||||||
|----|:----:|:----:|:----:|:----:|:----:|:----:|:----:|
|**Funcionalidade**|**Máquinas Virtuais Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas não-Azure**|**Preços**
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Standard|
|[Análise Comportamental de Máquina Virtual (e alertas de segurança)](threat-protection.md)|✔|✔|✔|Recomendações (Grátis) </br></br> Alertas de segurança (Standard)|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa da rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlos de rede adaptativo|✔|✔|-|Standard|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
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
|**Funcionalidade**|**Máquinas Virtuais Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas não-Azure**|**Preços**
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|-|-|-|Standard|
|[Análise Comportamental de Máquina Virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Recomendações (Grátis) </br></br> Alertas de segurança (Standard)|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação de vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa da rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|Controlos de rede adaptativo|✔|✔|-|Standard|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
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


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de pontos finais suportadas<a name="endpoint-supported"></a>

A tabela seguinte fornece uma matriz de:

 - Se pode utilizar o Azure Security Center para instalar cada solução para si.
 - Que soluções de proteção de ponta saem o Centro de Segurança pode descobrir. Se for descoberta uma solução de proteção de ponto final desta lista, o Security Center não recomenda a instalação de uma.

Para obter informações sobre quando são geradas recomendações para cada uma destas proteções, consulte [avaliação e recomendações](security-center-endpoint-protection.md)de proteção do ponto final .

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (ver nota abaixo) | Através de Extensão | Sim |
| Trend Micro – Segurança Profunda | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Linux Server  | Não | Sim, é o que**\*** |
| Sophos V9+| Família Linux Server  | Não | Sim, é o que**\***  |

 **\*** Atualmente, o estado de cobertura e os dados de suporte estão apenas disponíveis no espaço de trabalho log Analytics associado às suas subscrições protegidas. Não se reflete no portal do Centro de Segurança Azure.

> [!NOTE]
> A deteção da Proteção do Ponto Final do Centro de Sistema (SCEP) numa máquina virtual Do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell 3.0 (ou uma versão superior).


## <a name="next-steps"></a>Próximos passos

- Saiba como o [Security Center recolhe dados e o Agente de Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Centro de Segurança gere e salvaguarda dados.](security-center-data-security.md)
- Reveja as plataformas que suportam o [centro de segurança.](security-center-os-coverage.md)