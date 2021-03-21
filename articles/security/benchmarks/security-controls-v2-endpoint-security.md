---
title: Azure Security Benchmark V2 - Endpoint Security
description: Segurança V2 Endpoint de referência de segurança Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 48b22ba913370b27cd01319a14a2a627d7589ce4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102051519"
---
# <a name="security-control-v2-endpoint-security"></a>Controlo de Segurança V2: Segurança de ponto final

A Endpoint Security cobre os controlos na deteção e resposta do ponto final. Isto inclui a utilização de deteção e resposta de pontos finais (EDR) e serviço anti-malware para pontos finais em ambientes Azure.

Para ver a política de Azure incorporada aplicável, consulte [detalhes da iniciativa de conformidade regulamentar de referência de segurança Azure: Endpoint Security](../../governance/policy/samples/azure-security-benchmark.md#endpoint-security)

## <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: Utilizar deteção e resposta de ponto final (EDR)

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| ES-1 | 8.1 | SI-2, SI-3, SC-3 |

Ativar as capacidades de Deteção e Resposta de Pontos Finais (EDR) para servidores e clientes e integrar-se com processos siem e operações de segurança.

O Microsoft Defender for Endpoint fornece a capacidade EDR como parte de uma plataforma de segurança de ponto final da empresa para prevenir, detetar, investigar e responder a ameaças avançadas.

- [Microsoft Defender para visão geral do ponto final](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)

- [Microsoft Defender para Endpoint para servidores Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)

- [Microsoft Defender para Endpoint para servidores não Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Utilize software anti-malware moderno gerido centralmente

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| ES-2 | 8.1 | SI-2, SI-3, SC-3 |

Utilize uma solução anti-malware gerida centralmente capaz de digitalização em tempo real e periódico

O Azure Security Center pode identificar automaticamente o uso de várias soluções anti-malware populares para as suas máquinas virtuais e reportar o estado de funcionamento da proteção de ponto final e fazer recomendações. 

O Microsoft Antimalware para Azure Cloud Services é o anti-malware padrão para máquinas virtuais do Windows (VMs). Para os VMs Linux, utilize soluções antimalware de terceiros. Além disso, pode utilizar a deteção de ameaças do Azure Security Center para detetar malware enviado para contas de Armazenamento Azure. 

- [Como configurar o Microsoft Antimalware para serviços em nuvem e máquinas virtuais](../fundamentals/antimalware.md)

- [Soluções de proteção de pontos finais apoiadas](../../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Garantir que software e assinaturas anti-malware são atualizados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| ES-3 | 8.2 | SI-2, SI-3 |

Certifique-se de que as assinaturas anti-malware são atualizadas de forma rápida e consistente.

Siga as recomendações no Azure Security Center: "Compute & Apps" para garantir que todos os pontos finais estão atualizados com as assinaturas mais recentes. O Microsoft Antimalware instalará automaticamente as mais recentes assinaturas e atualizações do motor por predefinição. Para o Linux, certifique-se de que as assinaturas são atualizadas na solução antimalware de terceiros.

- [Como implementar o Microsoft Antimalware para serviços em nuvem azure e máquinas virtuais](../fundamentals/antimalware.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Segurança de infraestrutura e pontos finais](/azure/cloud-adoption-framework/organize/cloud-security)

- [Inteligência de ameaça](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

- [Gestão de Conformidade de Segurança](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Gestão de postura](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Avaliação e recomendações de proteção de pontos finais no Centro de Segurança Azure](../../security-center/security-center-endpoint-protection.md)