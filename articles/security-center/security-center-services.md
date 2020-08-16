---
title: Funcionalidades suportadas disponíveis no Centro de Segurança Azure Microsoft Docs
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
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 3055d8c31307cfacad575d892db0ed812a03a9e4
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258611"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para máquinas

Os dois separadores abaixo mostram as funcionalidades do Azure Security Center que estão disponíveis para máquinas e servidores virtuais Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funcionalidades suportadas para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas de windows**](#tab/features-windows)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas não-Azure**|**Preços**
|----|:----:|:----:|:----:|:----:|
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Standard|
|[Análise Comportamental de Máquina Virtual (e alertas de segurança)](threat-protection.md)|✔|✔|✔|Standard|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|✔|✔|✔|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação da vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|-|-|-|Standard|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Grátis<br><br>Não-Azure: Standard|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Grátis<br><br>Não-Azure: Standard|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Grátis<br><br>Não-Azure: Standard|
|Avaliação da encriptação do disco|✔|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux-machines"></a>[**Máquinas Linux**](#tab/features-linux)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas não-Azure**|**Preços**
|----|:----:|:----:|:----:|:----:|
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|-|-|-|Standard|
|[Análise Comportamental de Máquina Virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Standard|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|-|-|-|Standard|
|[Alertas de segurança baseados em rede](threat-protection.md#network-layer)|✔|✔|-|Standard|
|[Acesso VM just-in-time](security-center-just-in-time.md)|✔|-|-|Standard|
|[Avaliação da vulnerabilidade nativa](built-in-vulnerability-assessment.md)|✔|-|-|Standard|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Standard|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Standard|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Standard|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Standard|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Standard|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|✔|✔|✔|Standard|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Grátis<br><br>Não-Azure: Standard|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Grátis<br><br>Não-Azure: Standard|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Avaliação da encriptação do disco|✔|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 


> [!TIP]
>Para experimentar funcionalidades que só estão disponíveis no nível de preços padrão, os utilizadores de nível livre podem inscrever-se num ensaio de 30 dias. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de pontos finais apoiadas <a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se pode utilizar o Azure Security Center para instalar cada solução para si.
 - Que soluções de proteção de ponto final o Centro de Segurança pode descobrir. Se for descoberta uma solução de proteção de ponto final desta lista, o Centro de Segurança não recomendará a instalação de uma.

Para obter informações sobre quando são geradas recomendações para cada uma destas proteções, consulte [a Avaliação e Recomendações de Proteção de Pontos Finais](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016 ou mais tarde| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (ver nota abaixo) | Através de Extensão | Sim |
| Trend Micro – Segurança Profunda | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família linux servidor  | Não | Sim, é o seu **\*** |
| Sophos V9+| Família linux servidor  | Não | Sim, é o seu  **\***  |

 **\*** O estado de cobertura e os dados de suporte só estão atualmente disponíveis no espaço de trabalho Log Analytics associado às suas subscrições protegidas. Não se reflete no portal do Centro de Segurança Azure.

> [!NOTE]
> A deteção da Proteção de Pontos Finais do Centro de Sistema (SCEP) numa máquina virtual Do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell (v3.0 ou mais recente).


## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados e o Agente de Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Security Center gere e salvaguarda dados.](security-center-data-security.md)
- Reveja as [plataformas que suportam o centro de segurança.](security-center-os-coverage.md)