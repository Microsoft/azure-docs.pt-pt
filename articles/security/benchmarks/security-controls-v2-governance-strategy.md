---
title: Azure Security Benchmark V2 - Governação e Estratégia
description: Azure Security Benchmark V2 Governação e Estratégia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 277033e41ec7e02b89eca8cf74fe6854acb51cc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727027"
---
# <a name="security-control-v2-governance-and-strategy"></a>Controlo de Segurança V2: Governação e Estratégia

A governação e a estratégia fornecem orientações para assegurar uma estratégia de segurança coerente e uma abordagem de governação documentada para orientar e manter a garantia de segurança, incluindo o estabelecimento de funções e responsabilidades para as diferentes funções de segurança na nuvem, estratégia técnica unificada e apoio a políticas e normas.

## <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definir a gestão dos ativos e a estratégia de proteção de dados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e a monitorização de dados e sistemas críticos para a empresa.

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Norma para a classificação de dados, de acordo com os riscos da atividade

- Visibilidade da organização de segurança para os riscos e inventário de ativos

- Aprovação da organização de segurança dos serviços do Azure a utilizar

- Segurança dos ativos ao longo do ciclo de vida

- Estratégia de controlo de acesso obrigatória, de acordo com a classificação dos dados organizacionais

- Utilização das capacidades de proteção de dados nativas do Azure e de terceiros

- Requisitos de encriptação de dados para casos de utilização de dados em trânsito e inativos

- Normas criptográficas adequadas

Para obter mais informações, veja as seguintes referências:
- [Recomendação de Arquitetura de Segurança do Azure - Armazenamento, dados e encriptação](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Noções Básicas da Segurança do Azure - Segurança, encriptação e armazenamento de dados do Azure](../fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Melhores práticas de segurança e encriptação de dados do Azure](../fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Referência de Segurança do Azure - Gestão de ativos](security-controls-v2-asset-management.md)

- [Referência de Segurança do Azure - Proteção de dados](security-controls-v2-data-protection.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definir a estratégia de segmentação da empresa

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-2 | 4, 9, 16 | AC, CA, SC |

Estabeleça uma estratégia em toda a empresa para segmentar o acesso a ativos utilizando uma combinação de identidade, rede, aplicação, subscrição, grupo de gestão e outros controlos.

Equilibre cuidadosamente a necessidade de separação da segurança com a necessidade de permitir o funcionamento diário dos sistemas que têm de comunicar entre si e aceder a dados.

Certifique-se de que a estratégia de segmentação está implementada de forma consistente em todos os tipos de controlos, incluindo segurança de rede, modelos de identidade e acesso e modelos de permissão/acesso a aplicações e controlos de processos humanos.

- [Orientação para a estratégia de segmentação no Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Orientação para a estratégia de segmentação no Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alinhar a segmentação de rede com a estratégia de segmentação empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definir a estratégia de gestão da postura de segurança

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-3 | 20, 3, 5 | RA, CM, SC |

Medindo e atenuando continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Dê prioridade aos ativos de valor alto e a superfícies de ataque muito expostas, como aplicações publicadas, pontos de entrada e saída de rede, pontos finais de utilizador e administrador, etc.

- [Referência de Segurança do Azure - Gestão da postura e das vulnerabilidades](security-controls-v2-posture-vulnerability-management.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alinhar as funções, as responsabilidades e as responsabilizações na organização

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-4 | N/D | PL, PM |

Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Estabeleça prioridades ao indicar de forma clara a responsabilização quanto às decisões de segurança, explicando o modelo de responsabilização partilhada a todos e explicando às equipas técnicas a tecnologia para proteger a cloud.

- [Melhor Prática de Segurança do Azure 1 – Pessoas: Explicar às Equipas o Percurso da Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Melhor Prática de Segurança do Azure 2 – Pessoas: Explicar às Equipas a Tecnologia de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Melhor Prática de Segurança do Azure 3 – Processo: Atribuir Responsabilização Quanto às Decisões de Segurança da Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-network-security-strategy"></a>GS-5: Definir uma estratégia de segurança de rede

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-5 | 9 | CA |

Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos:

- Gestão centralizada da rede e responsabilidade quanto à segurança

- Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação empresarial

- Estratégia de remediação em diferentes cenários de ameaças e ataques

- Estratégia de entrada e saída do edge da Internet

- Estratégia de interconectividade entre a cloud híbrida e o ambiente no local

- Artefactos de segurança de rede atualizados (tais como diagramas de rede, arquitetura de rede de referência)

Para obter mais informações, veja as seguintes referências:

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Referência de Segurança do Azure - Segurança de Rede](security-controls-v2-network-security.md)

- [Descrição geral da segurança de rede do Azure](../fundamentals/network-overview.md)

- [Estratégia de arquitetura da rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definir a estratégia de identidades e acessos privilegiados

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-6 | 16, 4 | AC, AU, SC |

Estabeleça uma identidade Azure e abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização.

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos:

- Um sistema de identidades e autenticação centralizado e respetiva interconectividade com outros sistemas de identidades internas e externas

- Métodos de autenticação fortes em diferentes casos de utilização e condições

- Proteção de utilizadores com muitos privilégios

- Monitorização e processamento de atividades anómalas de utilizadores

- Processo de revisão e reconciliação de identidades e acessos dos utilizadores

Para obter mais informações, veja as seguintes referências:

- [Referência de Segurança do Azure - Gestão de identidades](security-controls-v2-identity-management.md)

- [Referência de Segurança do Azure - Acesso privilegiado](security-controls-v2-privileged-access.md)

- [Melhor Prática de Segurança do Azure 11 – Arquitetura. Estratégia de segurança única e unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Descrição geral da segurança da gestão de identidades do Azure](../fundamentals/identity-management-overview.md)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definir a estratégia de resposta a ameaças e registos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-7 | 19 | IR, AU, RA, SC |

Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Estabeleça prioridades ao disponibilizar aos analistas alertas de alta qualidade e experiências práticas para que se possam concentrar nas ameaças e não na integração e em passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- O papel e as responsabilidades da organização das operações de segurança (SecOps) 

- Processo de resposta a incidentes bem definido e alinhado com as normas da agência norte-americana NIST ou outro framework da indústria 

- Captura e retenção de registos para suportar a deteção de ameaças, a resposta a incidentes e as necessidades de conformidade

- Visibilidade centralizada e correlação de informações sobre ameaças, mediante a utilização de SIEM, das capacidades nativas do Azure e de outras origens 

- Plano de comunicação e notificação com os seus clientes, fornecedores e partes interessadas públicas

- Utilização de plataformas nativas do Azure e de terceiros para processamento de incidentes, como registo e deteção de ameaças, análises forenses e remediação e erradicação de ataques

- Processos para lidar com incidentes e atividades pós-incidentes, como lições aprendidas e retenção de provas

Para obter mais informações, veja as seguintes referências:
- [Referência de Segurança do Azure - Registos e deteção de ameaças](security-controls-v2-logging-threat-detection.md)

- [Referência de Segurança do Azure - Resposta a incidentes](security-controls-v2-incident-response.md)

- [Melhor Prática de Segurança do Azure 4 – Processo. Atualizar os Processos de Resposta a Incidentes para a Cloud](/azure/cloud-adoption-framework/security/security-top-10#3-process-assign-accountability-for-cloud-security-decisions)

- [Azure Adoption Framework, registos e guia de decisão de relatórios](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização empresarial do Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-8-define-backup-and-recovery-strategy"></a>GS-8: Definir estratégia de backup e recuperação

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP 800-53 r4 ID(s) |
|--|--|--|--|
| GS-8 | 10 | CP |

Estabeleça uma estratégia de backup e recuperação da Azure para a sua organização. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

- Definições objetivas do tempo de recuperação (RTO) e ponto de recuperação (RPO) de acordo com os objetivos de resiliência do seu negócio

- Design de redundância nas suas aplicações e configuração de infraestruturas

- Proteção de cópias de segurança utilizando controlo de acesso e encriptação de dados

Para obter mais informações, veja as seguintes referências:
- [Benchmark de segurança Azure - Backup e recuperação](security-controls-v2-backup-recovery.md)

- [Azure Well-Architecture Framework - Backup e recuperação de desastres para aplicações do Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Quadro de Adoção Azure - continuidade das empresas e recuperação de desastres](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilidade**: Cliente

**Stakeholders de Segurança do Cliente** [(Saiba mais):](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)