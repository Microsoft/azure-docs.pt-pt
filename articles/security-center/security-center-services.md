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
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 67c52728b72f0941ae3c9b39552ca398c12c67a2
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425154"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para máquinas

Os dois separadores abaixo mostram as funcionalidades do Azure Security Center que estão disponíveis para máquinas e servidores virtuais Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funcionalidades suportadas para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas de windows**](#tab/features-windows)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas habilittadas a Azure Arc**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender para integração endpoint](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](alerts-reference.md)|✔|✔|✔|Sim|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|✔|✔|✔|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso just-in-time à VM](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação da vulnerabilidade nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sim|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|-|-|-|Sim|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|Avaliação da encriptação do disco|✔</br>(para [cenários apoiados)](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)|✔|-|Não|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Não|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Não|


### <a name="linux-machines"></a>[**Máquinas Linux**](#tab/features-linux)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas habilittadas a Azure Arc**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender para integração endpoint](security-center-wdatp.md)|-|-|-|Sim|
|[Análise comportamental de máquina virtual (e alertas de segurança)](./azure-defender.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Sim|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|-|-|-|Sim|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Sim|
|[Acesso just-in-time à VM](security-center-just-in-time.md)|✔|-|-|Sim|
|[Avaliação da vulnerabilidade nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|-|Sim|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Sim|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Sim|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Sim|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Sim|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Sim|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|✔|✔|✔|Sim|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|Não|
|Avaliação da encriptação do disco|✔</br>(para [cenários apoiados)](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)|✔|-|Não|
|Avaliação da vulnerabilidade de terceiros|✔|-|-|Não|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|Não|

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
| Microsoft Defender Antivírus| Windows Server 2016 ou mais tarde| Não, Incorporado no SO| Sim |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2, 2012, 2008 R2 (ver nota abaixo) | Através de Extensão | Sim |
| Trend Micro – Segurança Profunda | Família Windows Server  | Não | Sim |
| Symantec v12.1.1100+| Família Windows Server  | Não | Sim |
| McAfee v10+ | Família Windows Server  | Não | Sim |
| McAfee v10+ | Família linux servidor  | Não | Sim *\** , |
| Sophos V9+| Família linux servidor  | Não | Sim, é o seu  _*\**_  |

 _ *\** * O estado de cobertura e os dados de suporte só estão atualmente disponíveis no espaço de trabalho Log Analytics associado às suas subscrições protegidas. Não se reflete no portal do Centro de Segurança Azure.

> [!NOTE]
> A deteção da Proteção de Pontos Finais do Centro de Sistema (SCEP) numa máquina virtual Do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell (v3.0 ou mais recente).


## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados utilizando o Agente De Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Security Center gere e salvaguarda dados.](security-center-data-security.md)
- Reveja as [plataformas que suportam o centro de segurança.](security-center-os-coverage.md)
