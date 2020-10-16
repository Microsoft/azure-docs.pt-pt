---
title: Azure Security Benchmark V2 - Endpoint Security
description: Segurança V2 Endpoint de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 452b1a33520309ae696ab318c034f0186c993fdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91402927"
---
# <a name="security-control-v2-endpoint-security"></a>Controlo de Segurança V2: Segurança de ponto final

A Endpoint Security cobre os controlos na deteção e resposta do ponto final. Isto inclui a utilização de deteção e resposta de pontos finais (EDR) e serviço anti-malware para pontos finais em ambientes Azure.

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Utilizar deteção e resposta de ponto final (EDR)

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

Ativar as capacidades de Deteção e Resposta de Pontos Finais (EDR) para servidores e clientes e integrar-se com processos siem e operações de segurança.

O Microsoft Defender Advanced Threat Protection fornece a capacidade EDR como parte de uma plataforma de segurança de ponto final da empresa para prevenir, detetar, investigar e responder a ameaças avançadas. 

- [Visão geral da proteção de ameaças avançadas do Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Serviço ATP Microsoft Defender para servidores Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Serviço ATP microsoft Defender para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Utilize software anti-malware moderno gerido centralmente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Utilize uma solução anti-malware gerida centralmente capaz de digitalização em tempo real e periódico

O Azure Security Center pode identificar automaticamente o uso de várias soluções anti-malware populares para as suas máquinas virtuais e reportar o estado de funcionamento da proteção de ponto final e fazer recomendações. 

O Microsoft Antimalware para Azure Cloud Services é o anti-malware padrão para máquinas virtuais do Windows (VMs). Para os VMs Linux, utilize soluções antimalware de terceiros.  Além disso, pode utilizar a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de Armazenamento Azure. 

- [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../fundamentals/antimalware.md)

- [Soluções de proteção de pontos finais apoiadas](https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows#supported-endpoint-protection-solutions-)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Garantir que software e assinaturas anti-malware são atualizados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| ES-3 | 8.2 | SI-2, SI-3 |

Certifique-se de que as assinaturas anti-malware são atualizadas de forma rápida e consistente. 

Siga as recomendações no Azure Security Center: "Compute &amp; Apps" para garantir que todos os pontos finais estão atualizados com as assinaturas mais recentes. O Microsoft Antimalware instalará automaticamente as mais recentes assinaturas e atualizações do motor por predefinição. Para o Linux, utilize uma solução antimalware de terceiros.

- [Como implementar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais](../fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security)

- [Informações sobre ameaças](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Avaliação e recomendações de proteção de pontos finais no Centro de Segurança Azure](../../security-center/security-center-endpoint-protection.md)
