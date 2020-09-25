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
ms.openlocfilehash: cece53544bfc8287f6ef542575d92b05032991f0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318046"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para máquinas

Os dois separadores abaixo mostram as funcionalidades do Azure Security Center que estão disponíveis para máquinas e servidores virtuais Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funcionalidades suportadas para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas de windows**](#tab/features-windows)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas não-Azure**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Yes|
|[Análise comportamental de máquina virtual (e alertas de segurança)](alerts-reference.md)|✔|✔|✔|Yes|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|✔|✔|✔|Yes|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Acesso just-in-time à VM](security-center-just-in-time.md)|✔|-|-|Yes|
|[Avaliação da vulnerabilidade nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Yes|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|-|-|-|Yes|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Grátis<br><br>Non-Azure: Sim|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Não<br><br>Non-Azure: Sim|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Não<br><br>Non-Azure: Sim|
|Avaliação da encriptação do disco|✔</br>(para [cenários apoiados)](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|


### <a name="linux-machines"></a>[**Máquinas Linux**](#tab/features-linux)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas não-Azure**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Integração ATP do Microsoft Defender](security-center-wdatp.md)|-|-|-|Yes|
|[Análise comportamental de máquina virtual (e alertas de segurança)](security-center-alerts-iaas.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Yes|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|-|-|-|Yes|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Acesso just-in-time à VM](security-center-just-in-time.md)|✔|-|-|Yes|
|[Avaliação da vulnerabilidade nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Yes|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|✔|✔|✔|Yes|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Grátis<br><br>Non-Azure: Sim|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Grátis<br><br>Non-Azure: Sim|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Gratuito|
|Avaliação da encriptação do disco|✔</br>(para [cenários apoiados)](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)|✔|-|Gratuito|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Gratuito|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Gratuito|

--- 


> [!TIP]
>Para experimentar funcionalidades que só estão disponíveis com o Azure Defender, pode inscrever-se num ensaio de 30 dias. Para obter mais informações, veja a [página de preços](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="supported-endpoint-protection-solutions"></a>Soluções de proteção de pontos finais apoiadas <a name="endpoint-supported"></a>

A tabela a seguir fornece uma matriz de:

 - Se pode utilizar o Azure Security Center para instalar cada solução para si.
 - Que soluções de proteção de ponto final o Centro de Segurança pode descobrir. Se for descoberta uma solução de proteção de ponto final desta lista, o Centro de Segurança não recomendará a instalação de uma.

Para obter informações sobre quando são geradas recomendações para cada uma destas proteções, consulte [a Avaliação e Recomendações de Proteção de Pontos Finais](security-center-endpoint-protection.md).

| Endpoint Protection| Plataformas | Instalação do Centro de Segurança | Deteção do Centro de Segurança |
|------|------|-----|-----|
| Microsoft Defender Antivírus| Windows Server 2016 ou mais tarde| Não, Incorporado no SO| Yes |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (ver nota abaixo) | Através de Extensão | Yes |
| Trend Micro – Segurança Profunda | Família Windows Server  | No | Yes |
| Symantec v12.1.1100+| Família Windows Server  | No | Yes |
| McAfee v10+ | Família Windows Server  | No | Yes |
| McAfee v10+ | Família linux servidor  | No | Sim, é o seu **\*** |
| Sophos V9+| Família linux servidor  | No | Sim, é o seu  **\***  |

 **\*** O estado de cobertura e os dados de suporte só estão atualmente disponíveis no espaço de trabalho Log Analytics associado às suas subscrições protegidas. Não se reflete no portal do Centro de Segurança Azure.

> [!NOTE]
> A deteção da Proteção de Pontos Finais do Centro de Sistema (SCEP) numa máquina virtual Do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell (v3.0 ou mais recente).


## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados e o Agente de Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Security Center gere e salvaguarda dados.](security-center-data-security.md)
- Reveja as [plataformas que suportam o centro de segurança.](security-center-os-coverage.md)