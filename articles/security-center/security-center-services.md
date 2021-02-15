---
title: As funcionalidades do Azure Security Center de acordo com o SO, tipo de máquina e nuvem
description: Saiba quais as funcionalidades do Azure Security Center disponíveis de acordo com o seu sistema operativo, tipo e implementação em nuvem.
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
ms.date: 02/08/2021
ms.author: memildin
ms.openlocfilehash: 7034d1b5f06fcbcead79d85df10042eee3581b0b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369361"
---
# <a name="feature-coverage-for-machines"></a>Cobertura de recursos para máquinas

Os dois separadores abaixo mostram as funcionalidades do Azure Security Center que estão disponíveis para máquinas e servidores virtuais Windows e Linux.

## <a name="supported-features-for-virtual-machines-and-servers"></a>Funcionalidades suportadas para máquinas virtuais e servidores <a name="vm-server-features"></a>

### <a name="windows-machines"></a>[**Máquinas de windows**](#tab/features-windows)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas habilittadas a Azure Arc**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender para integração endpoint](security-center-wdatp.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Yes|
|[Análise comportamental de máquina virtual (e alertas de segurança)](alerts-reference.md)|✔|✔|✔|Yes|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|✔|✔|✔|Yes|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Acesso just-in-time à VM](security-center-just-in-time.md)|✔|-|-|Yes|
|[Avaliação da vulnerabilidade nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Yes|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|-|-|-|Yes|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|Avaliação da encriptação do disco|✔</br>(para [cenários apoiados)](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)|✔|-|No|
|Avaliação da vulnerabilidade de terceiros|✔|-|✔|No|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|No|


### <a name="linux-machines"></a>[**Máquinas Linux**](#tab/features-linux)

|**Funcionalidade**|**Máquinas Virtuais do Azure**|**Conjuntos de Dimensionamento de Máquinas Virtuais do Azure**|**Máquinas habilittadas a Azure Arc**|**Azure Defender necessário**
|----|:----:|:----:|:----:|:----:|
|[Microsoft Defender para integração endpoint](security-center-wdatp.md)|-|-|-|-|
|[Análise comportamental de máquina virtual (e alertas de segurança)](./azure-defender.md)|✔</br>(em versões suportadas)|✔</br>(em versões suportadas)|✔|Yes|
|[Alertas de segurança sem ficheiros](alerts-reference.md#alerts-windows)|-|-|-|Yes|
|[Alertas de segurança baseados em rede](other-threat-protections.md#network-layer)|✔|✔|-|Yes|
|[Acesso just-in-time à VM](security-center-just-in-time.md)|✔|-|-|Yes|
|[Avaliação da vulnerabilidade nativa](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner)|✔|-|✔|Yes|
|[Monitorização da integridade do ficheiro](security-center-file-integrity-monitoring.md)|✔|✔|✔|Yes|
|[Controlos de aplicações adaptáveis](security-center-adaptive-application.md)|✔|-|✔|Yes|
|[Mapa de rede](security-center-network-recommendations.md#network-map)|✔|✔|-|Yes|
|[Proteção de rede ajustável](security-center-adaptive-network-hardening.md)|✔|-|-|Yes|
|[Relatórios de & do painel de conformidade regulamentar](security-center-compliance-dashboard.md)|✔|✔|✔|Yes|
|Recomendações e proteção contra ameaças em contentores IaaS hospedados em Docker|✔|✔|✔|Yes|
|Avaliação de patches de SO em falta|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|Avaliação de erros de segurança|✔|✔|✔|Azure: Não<br><br>Ativado pelo arco: Sim|
|[Avaliação da proteção do ponto final](security-center-services.md#supported-endpoint-protection-solutions-)|-|-|-|No|
|Avaliação da encriptação do disco|✔</br>(para [cenários apoiados)](../virtual-machines/windows/disk-encryption-windows.md#unsupported-scenarios)|✔|-|No|
|Avaliação da vulnerabilidade de terceiros|✔|-|✔|No|
|[Avaliação da segurança da rede](security-center-network-recommendations.md)|✔|✔|-|No|

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
| McAfee v10+ | Família linux servidor  | No | Yes |
| Sophos V9+| Família linux servidor  | No | Yes |

> [!NOTE]
> A deteção da Proteção de Pontos Finais do Centro de Sistema (SCEP) numa máquina virtual Do Windows Server 2008 R2 requer que o SCEP seja instalado após o PowerShell (v3.0 ou mais recente).



## <a name="feature-support-in-government-clouds"></a>Suporte de recurso em nuvens do governo

| Serviço / Recurso | US Gov | China Gov |
|------|:----:|:----:|
|[Acesso VM just-in-time](security-center-just-in-time.md) (1)|✔|✔|
|[Monitorização da integridade dos ficheiros](security-center-file-integrity-monitoring.md) (1)|✔|✔|
|[Controlos de aplicação adaptativos](security-center-adaptive-application.md) (1)|✔|✔|
|[Endurecimento adaptativo da rede](security-center-adaptive-network-hardening.md) (1)|-|-|
|[Endurecimento do anfitrião Docker](harden-docker-hosts.md) (1)|✔|✔|
|[Avaliação integrada da vulnerabilidade das máquinas](deploy-vulnerability-assessment-vm.md) (1)|-|-|
|[Microsoft Defender para Endpoint](harden-docker-hosts.md) (1)|✔|-|
|[Ligue a conta AWS](quickstart-onboard-aws.md) (1)|-|-|
|[Conecte a conta GCP](quickstart-onboard-gcp.md) (1)|-|-|
|[Exportação contínua](continuous-export.md)|✔|✔ (2)|
|[Automatização do fluxo de trabalho](workflow-automation.md)|✔|✔|
|[Regras de isenção de recomendação](exempt-resource.md)|-|-|
|[Regras de supressão de alertas](alerts-suppression-rules.md)|✔|✔|
|[Notificações de email para alertas de segurança](security-center-provide-security-contact-details.md)|✔|✔|
|[Inventário de ativos](asset-inventory.md)|✔|✔|
|[Azure Defender para Serviço de Aplicações](defender-for-app-service-introduction.md)|-|-|
|[Azure Defender para Armazenamento](defender-for-storage-introduction.md)|✔|-|
|[Azure Defender para SQL](defender-for-sql-introduction.md)|✔|✔ (2)|
|[Azure Defender para Key Vault](defender-for-key-vault-introduction.md)|-|-|
|[Azure Defender para Resource Manager](defender-for-resource-manager-introduction.md)|-|-|
|[Azure Defender para DNS](defender-for-dns-introduction.md)|-|-|
|[Azure Defender para registos de contentor](defender-for-container-registries-introduction.md)|✔ (2)|✔ (2)|
|[Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)|✔|✔|
|[Proteção da carga de trabalho de Kubernetes](kubernetes-workload-protections.md)|-|-|
|||

(1) Requer **Azure Defender para servidores**

(2) Parcial


## <a name="next-steps"></a>Passos seguintes

- Saiba como o [Security Center recolhe dados utilizando o Agente De Análise de Registos.](security-center-enable-data-collection.md)
- Saiba como o [Security Center gere e salvaguarda dados.](security-center-data-security.md)
- Reveja as [plataformas que suportam o centro de segurança.](security-center-os-coverage.md)