---
title: Azure Security Benchmark V2 - Governação e Estratégia
description: Azure Security Benchmark V2 Governação e Estratégia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: ae0930e0845e8e8bd6dc4571dc3e8e27491a7be6
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408797"
---
# <a name="security-control-v2-governance-and-strategy"></a>Controlo de Segurança V2: Governação e Estratégia

A governação e a estratégia fornecem orientações para assegurar uma estratégia de segurança coerente e uma abordagem de governação documentada para orientar e manter a garantia de segurança, incluindo o estabelecimento de funções e responsabilidades para as diferentes funções de segurança na nuvem, estratégia técnica unificada e apoio a políticas e normas.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir estratégia de gestão de ativos e proteção de dados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Norma de classificação de dados de acordo com os riscos empresariais

- Visibilidade da organização de segurança em riscos e inventário de ativos 

- Aprovação da organização de segurança dos serviços da Azure para utilização 

- Segurança dos bens através do seu ciclo de vida

- Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais

- Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

- Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso

- Padrões criptográficos adequados

Para mais informações, consulte as seguintes referências:
- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?amp;bc=%252fsecurity%252fcompass%252fbreadcrumb%252ftoc.json&toc=%252fsecurity%252fcompass%252ftoc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../fundamentals/encryption-overview.md)

- [Cloud Adopt Framework - Segurança de dados Azure e melhores práticas de encriptação](../fundamentals/data-encryption-best-practices.md?amp;bc=%252fazure%252fcloud-adoption-framework%252f_bread%252ftoc.json&toc=%252fazure%252fcloud-adoption-framework%252ftoc.json)

- [Azure Security Benchmark - Gestão de ativos](security-controls-v2-asset-management.md)

- [Benchmark de segurança Azure - Proteção de Dados](security-controls-v2-data-protection.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir estratégia de segmentação empresarial

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão e outros controlos.

Equilibra cuidadosamente a necessidade de separação de segurança com a necessidade de permitir o funcionamento diário dos sistemas que precisam de comunicar entre si e aceder aos dados.

Certifique-se de que a estratégia de segmentação é implementada de forma consistente em todos os tipos de controlo, incluindo modelos de segurança de rede, identidade e acesso, e modelos de permissão/acesso de aplicações e controlos de processos humanos.

- [Orientação sobre estratégia de segmentação em Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação sobre estratégia de segmentação em Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação da rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir estratégia de gestão da postura de segurança

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Medindo e atenuando continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, etc.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](security-controls-v2-posture-vulnerability-management.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar papéis de organização, responsabilidades e responsabilidades

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-4 | N/D | PL, PM |

Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorizar a prestação de contas claras às decisões de segurança, educar todos no modelo de responsabilidade partilhada e educar as equipas técnicas em tecnologia para garantir a nuvem.

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir estratégia de segurança de rede

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-5 | 9 | CA |

Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Responsabilidade centralizada de gestão de redes e segurança

- Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa

- Estratégia de remediação em diferentes cenários de ameaça e ataque

- Internet borda e estratégia de entrada e saída

- Estratégia híbrida de interconectividade em nuvem e no local

- Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Para mais informações, consulte as seguintes referências:

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Benchmark de segurança Azure - Segurança de rede](security-controls-v2-network-security.md)

- [Visão geral da segurança da rede Azure](../fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir estratégia de identidade e acesso privilegiado

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Estabeleça uma identidade Azure e abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa

- Métodos de autenticação forte em diferentes casos e condições de utilização

- Proteção de utilizadores altamente privilegiados

- Monitorização e manuseamento de atividades de utilizadores de anomalias  

- Revisão e reconciliação da identidade do utilizador e acesso

Para mais informações, consulte as seguintes referências:

- [Azure Security Benchmark - Gestão de identidade](security-controls-v2-identity-management.md)

- [Benchmark de Segurança Azure - Acesso privilegiado](security-controls-v2-privileged-access.md)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Visão geral da segurança da gestão de identidade Azure](../fundamentals/identity-management-overview.md)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta ao abate e à ameaça

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- O papel e as responsabilidades da organização das operações de segurança (SecOps) 

- Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria 

- Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade

- Visibilidade centralizada e correlação de informações sobre ameaças, utilizando siem, capacidades nativas de Azure, e outras fontes 

- Plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse

- Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques

- Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Para mais informações, consulte as seguintes referências:
- [Azure Security Benchmark - Registo e deteção de ameaças](security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark - Resposta a incidentes](security-controls-v2-incident-response.md)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização da empresa Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: Definir estratégia de backup e recuperação

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-8 | 10 | CP |

Estabeleça uma estratégia de backup e recuperação da Azure para a sua organização. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Definições objetivas do tempo de recuperação (RTO) e ponto de recuperação (RPO) de acordo com os objetivos de resiliência do seu negócio

- Design de redundância nas suas aplicações e configuração de infraestruturas

- Proteção de cópias de segurança utilizando controlo de acesso e encriptação de dados

Para mais informações, consulte as seguintes referências:
- [Benchmark de segurança Azure - Backup e recuperação](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework - Backup e recuperação de desastres para aplicações do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Quadro de Adoção Azure - continuidade das empresas e recuperação de desastres](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilidade** : Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)