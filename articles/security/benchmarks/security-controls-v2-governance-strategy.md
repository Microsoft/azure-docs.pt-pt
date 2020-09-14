---
title: Azure Security Benchmark V2 - Governação e Estratégia
description: Azure Security Benchmark V2 Governação e Estratégia
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/13/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e8a5196bf71712caae1218933ed13345f4cecd99
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059276"
---
# <a name="security-control-governance-and-strategy"></a>Controlo de Segurança: Governação e Estratégia

A Cópia de Segurança e Recuperação abrange os controlos para garantir que as cópias de segurança de dados e de configuração nos diferentes níveis de serviço sejam executadas, validadas e protegidas.

## <a name="gs-1-define-asset-management-and-protection-strategy"></a>GS-1: Definir estratégia de gestão e proteção de ativos

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-1 | 2, 13 | SC, AC |

Certifique-se de que documenta e comunica uma estratégia clara de monitorização e proteção contínua de sistemas e dados. Priorize a descoberta, a avaliação, a proteção e o acompanhamento de dados e sistemas críticos do negócio. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Norma de classificação de dados de acordo com os riscos empresariais

-   Visibilidade da organização de segurança em riscos e inventário de ativos 

-   Aprovação da organização de segurança dos serviços da Azure para utilização 

-   Segurança dos bens através do seu ciclo de vida

-   Estratégia de controlo de acesso exigida de acordo com a classificação de dados organizacionais

-   Utilização de capacidades de proteção de dados nativas e de terceiros da Azure

-   Requisitos de encriptação de dados para casos de utilização em trânsito e em repouso

-   Padrões criptográficos adequados

Nota: A sua abordagem de gestão e proteção de ativos para nuvem e no local pode ser diferente dependendo de vários fatores, tais como serviço de aplicação/modelo de hospedagem, riscos de negócio e requisito de conformidade. 

- [Recomendação de arquitetura de segurança Azure - Armazenamento, dados e encriptação](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Fundamentos de Segurança Azure - Segurança de dados Azure, encriptação e armazenamento](../fundamentals/encryption-overview.md)

- [Cloud Adopt Framework - Segurança de dados Azure e melhores práticas de encriptação](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Gestão de ativos](/azure/security/benchmarks/security-controls-v2-asset-management)

- [Benchmark de segurança Azure - Proteção de Dados](/azure/security/benchmarks/security-controls-v2-data-protection)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-2-define-security-posture-management-strategy"></a>GS-2: Definir estratégia de gestão da postura de segurança

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-2 | 20, 3, 5 | RA, CM, SC |

Medindo e atenuando continuamente os riscos para os seus ativos individuais e para o ambiente em que estão hospedados. Priorize ativos de alto valor e superfícies de ataque altamente expostas, tais como aplicações publicadas, entradas de rede e pontos de saída, pontos finais de utilizador e administrador, etc.

- [Azure Security Benchmark - Postura e gestão de vulnerabilidades](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-3-align-organization-roles-responsibilities-and-accountabilities"></a>GS-3: Alinhar papéis de organização, responsabilidades e responsabilidades

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-3 | N/D | PL, PM |

Certifique-se de que documenta e comunica uma estratégia clara para funções e responsabilidades na sua organização de segurança. Priorizar a prestação de contas claras às decisões de segurança, a educação sobre o modelo de responsabilidade partilhada e a educação técnica para a segurança na nuvem. 

- [Azure Security Best Practice 1 - Pessoas: Educar equipas na jornada de segurança na nuvem](https://aka.ms/AzSec1)

- [Azure Security Best Practice 2 - Pessoas: Educar equipas sobre tecnologia de segurança na nuvem](https://aka.ms/AzSec2)

- [Azure Security Best Practice 3 - Processo: Atribuir responsabilidade para decisões de segurança na nuvem](https://aka.ms/AzSec3)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-4-define-network-security-strategy"></a>GS-4: Definir estratégia de segurança de rede

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-4 | 9 | CA |

Estabeleça uma abordagem de segurança da rede Azure como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Responsabilidade centralizada de gestão de redes e segurança

-   Modelo de segmentação de rede virtual alinhado com a estratégia de segmentação da empresa

-   Estratégia de remediação em diferentes cenários de ameaça e ataque

-   Internet borda e estratégia de entrada e saída

-   Estratégia híbrida de interconectividade em nuvem e no local

-   Artefactos de segurança da rede atualizados (por exemplo, diagramas de rede, arquitetura de rede de referência)

Nota: A sua abordagem de segurança da rede para nuvem e no local pode ser diferente dependendo de vários fatores, tais como o modelo de serviço de aplicação, exposição a ameaças e configuração da rede híbrida.

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Benchmark de segurança Azure - Segurança de rede](/azure/security/benchmarks/security-controls-v2-network-security)

- [Visão geral da segurança da rede Azure](../fundamentals/network-overview.md)

- [Estratégia de arquitetura de rede empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-5-define-identity-and-privileged-access-strategy"></a>GS-5: Definir estratégia de identidade e acesso privilegiado

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-5 | 16, 4 | AC, AU, SC |

Estabeleça uma identidade Azure e abordagens privilegiadas de acesso como parte da estratégia global de controlo de acesso à segurança da sua organização.  

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Um sistema centralizado de identidade e autenticação e a sua interconectividade com outros sistemas de identidade interna e externa

-   Métodos de autenticação forte em diferentes casos e condições de utilização

-   Proteção de utilizadores altamente privilegiados

-   Monitorização e manuseamento de atividades de utilizadores de anomalias  

-   Revisão e reconciliação da identidade do utilizador e acesso

Nota: A sua identidade e abordagem privilegiada de acesso para nuvem e no local pode ser diferente dependendo de vários fatores, tais como a trajetória de acesso a dados/aplicações, o modelo de serviço e a estratégia de acesso ao cliente/parceiro.

- [Azure Security Benchmark - Gestão de identidade](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Benchmark de Segurança Azure - Acesso privilegiado](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Azure Security Best Practice 11 - Arquitetura. Estratégia de segurança unificada única](https://aka.ms/AzSec11)

- [Visão geral da segurança da gestão de identidade Azure](../fundamentals/identity-management-overview.md) 

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-6-define-logging-and-threat-response-strategy"></a>GS-6: Definir a estratégia de resposta ao abate e à ameaça

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-6 | 19 | IR, AU, RA, SC |

Estabeleça uma estratégia de resposta à exploração madeireira e a ameaças para detetar e remediar rapidamente as ameaças, cumprindo os requisitos de conformidade. Priorize fornecer aos analistas alertas de alta qualidade e experiências perfeitas para que se concentrem em ameaças em vez de integração e passos manuais. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   O papel e as responsabilidades da organização das operações de segurança (SecOps) 

-   Um processo de resposta a incidentes bem definido, alinhado com o NIST ou outro quadro da indústria 

-   Registar captura e retenção para apoiar a deteção de ameaças, resposta a incidentes e necessidades de conformidade

-   Visibilidade centralizada e correlação de informações sobre ameaças, utilizando siem, capacidades nativas de Azure, e outras fontes 

-   Plano de comunicação e notificação com os seus clientes, fornecedores e partes públicas de interesse

-   Utilização de plataformas nativas e de terceiros da Azure para o tratamento de incidentes, tais como deteção de registos e ameaças, perícia e remediação e erradicação de ataques

-   Processos de tratamento de incidentes e atividades pós-incidente, tais como lições aprendidas e retenção de provas

Nota: A sua abordagem de deteção de registos e ameaças para nuvem e no local pode ser diferente dependendo de vários fatores, tais como requisitos de conformidade, paisagem de ameaças e capacidade de deteção e remediação. 

- [Azure Security Benchmark - Registo e deteção de ameaças](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Benchmark - Resposta a incidentes](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Azure Security Best Practice 4 - Processo. Atualizar processos de resposta a incidentes para cloud](https://aka.ms/AzSec11)

- [Quadro de adoção Azure, registo e guia de decisão de reporte](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, gestão e monitorização da empresa Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

## <a name="gs-7-define-backup-and-recovery-strategy"></a>GS-7: Definir estratégia de backup e recuperação

| Azure ID | Controlos do CIS v7.1 ID(s) | NIST SP800-53 r4 ID(s) |
|--|--|--|--|
| GS-7 | 10 | CP |

Estabeleça uma estratégia de backup e recuperação da Azure para a sua organização. 

Esta estratégia deve incluir orientações, políticas e normas documentadas para os seguintes elementos: 

-   Definições objetivas do tempo de recuperação (RTO) e ponto de recuperação (RPO) de acordo com os objetivos de resiliência do seu negócio

-   Design de redundância nas suas aplicações e configuração de infraestruturas

-   Proteção de cópias de segurança utilizando controlo de acesso e encriptação de dados

Nota: A sua abordagem de backup e recuperação para nuvem e no local pode ser diferente dependendo dos múltiplos fatores, tais como redundância de infraestrutura, serviço de aplicação/modelo de hospedagem e requisitos de conformidade.

- [Benchmark de segurança Azure - Backup e recuperação](/azure/security/benchmarks/security-controls-v2-backup-recovery)

- [Quadro Azure Well-Architecture - Backup e recuperação de desastres para aplicações Azure](/azure/architecture/framework/resiliency/backup-and-recovery)

- [Quadro de Adoção Azure - continuidade das empresas e recuperação de desastres](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery)

**Responsabilidade**: Cliente

**Partes interessadas em segurança do cliente:**

- [Todas as partes interessadas](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)

