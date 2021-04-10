---
title: Visão geral do Azure Security Benchmark V2
description: Visão geral do Azure Security Benchmark V2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369162"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Visão geral do Azure Security Benchmark (V2)

O Azure Security Benchmark (ASB) fornece boas práticas e recomendações prescritivas para ajudar a melhorar a segurança das cargas de trabalho, dados e serviços em Azure.

Este benchmark faz parte de um conjunto de orientações holísticas de segurança que também inclui:

- **Cloud Adopt Framework** – Orientação sobre segurança, incluindo [estratégia,](/azure/cloud-adoption-framework/strategy/define-security-strategy) [funções e responsabilidades,](/azure/cloud-adoption-framework/organize/cloud-security) [Azure Top 10 Boas Práticas de Segurança,](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)e [implementação de referência.](/azure/cloud-adoption-framework/ready/enterprise-scale/)
- **Azure Well-Architected Framework** – Orientação para [garantir as suas cargas de trabalho](/assessments/?mode=pre-assessment&session=local) no Azure.
- **Microsoft Security Best Practices** – [recomendações](/security/compass/microsoft-security-compass-introduction) com exemplos sobre o Azure.

 O Azure Security Benchmark foca-se em áreas de controlo centradas na nuvem. Estes controlos são consistentes com referências de segurança bem conhecidas, como as descritas pelo Center for Internet Security (CIS) Controls Version 7.1 e National Institute of Standards and Technology (NIST) SP 800-53.
Os seguintes controlos estão incluídos no Benchmark de Segurança Azure:

| Domínios de controlo ASB | Description 
|--|--|
| [Segurança &nbsp; da rede &nbsp; (NS)](security-controls-v2-network-security.md) | A Segurança da Rede cobre controlos para proteger e proteger redes Azure, incluindo a segurança de redes virtuais, o estabelecimento de ligações privadas, a prevenção e mitigação de ataques externos e a segurança de DNS. |
| [Gestão de Identidade &nbsp; &nbsp; (IM)](security-controls-v2-identity-management.md) | A Gestão de Identidade abrange controlos para estabelecer uma identidade segura e controlos de acesso utilizando o Azure Ative Directory, incluindo a utilização de autenticações únicas, autenticações fortes, identidades geridas (e princípios de serviço) para aplicações, acesso condicional e monitorização de anomalias de conta. |
| [Acesso Privilegiado &nbsp; &nbsp; (PA)](security-controls-v2-privileged-access.md) | O Acesso Privilegiado abrange controlos para proteger o acesso privilegiado ao seu inquilino e recursos Azure, incluindo uma série de controlos para proteger o seu modelo administrativo, contas administrativas e estações de trabalho privilegiadas de acesso contra riscos deliberados e inadvertidos. |
| [Proteção de Dados &nbsp; &nbsp; (PD)](security-controls-v2-data-protection.md) | A Proteção de Dados cobre o controlo da proteção de dados em repouso, em trânsito e através de mecanismos de acesso autorizados, incluindo descobrir, classificar, proteger e monitorizar ativos de dados sensíveis usando controlo de acesso, encriptação e registo em Azure. |
| [Gestão de Ativos &nbsp; &nbsp; (AM)](security-controls-v2-asset-management.md) | A Gestão de Ativos cobre controlos para garantir a visibilidade e governação de segurança sobre os recursos da Azure, incluindo recomendações sobre permissões para pessoal de segurança, acesso de segurança ao inventário de ativos e gestão de aprovações para serviços e recursos (inventário, pista e correto). |
| [Registo &nbsp; e &nbsp; Deteção de Ameaças &nbsp; (LT)](security-controls-v2-logging-threat-detection.md) | A deteção de registos e deteção de ameaças abrange os controlos de deteção de ameaças no Azure e a capacitação, recolha e armazenamento de registos de auditoria para serviços Azure, incluindo processos de deteção, investigação e reparação com controlos para gerar alertas de alta qualidade com deteção de ameaças nativas nos serviços Azure; inclui também a recolha de registos com o Azure Monitor, a centralização da análise de segurança com o Azure Sentinel, a sincronização do tempo e a retenção de registos. |
| [&nbsp;Resposta a &nbsp; Incidentes (IR)](security-controls-v2-incident-response.md) | Incident Response abrange controlos no ciclo de vida de resposta a incidentes - preparação, deteção e análise, contenção e atividades pós-incidente, incluindo a utilização de serviços Azure, como o Azure Security Center e o Sentinel, para automatizar o processo de resposta a incidentes. |
| [Gestão de Postura &nbsp; e &nbsp; Vulnerabilidade &nbsp; &nbsp; (PV)](security-controls-v2-posture-vulnerability-management.md) | A Gestão de Postura e Vulnerabilidade centra-se nos controlos para avaliar e melhorar a postura de segurança do Azure, incluindo a digitalização de vulnerabilidades, testes de penetração e reparação, bem como o rastreio, reporte e correção de configuração de segurança nos recursos do Azure. |
| [Segurança no ponto &nbsp; &nbsp; final (ES)](security-controls-v2-endpoint-security.md) | A Endpoint Security cobre controlos na deteção e resposta de pontos finais, incluindo a utilização de deteção e resposta de pontos finais (EDR) e serviço anti-malware para pontos finais em ambientes Azure. |
| [Backup &nbsp; e &nbsp; Recuperação &nbsp; (BR)](security-controls-v2-backup-recovery.md) | A Cópia de Segurança e Recuperação abrange os controlos para garantir que as cópias de segurança de dados e de configuração nos diferentes níveis de serviço sejam executadas, validadas e protegidas. |
| [Governação &nbsp; e &nbsp; Estratégia &nbsp; (SG)](security-controls-v2-governance-strategy.md) | A governação e a estratégia fornecem orientações para assegurar uma estratégia de segurança coerente e uma abordagem de governação documentada para orientar e manter a garantia de segurança, incluindo o estabelecimento de funções e responsabilidades para as diferentes funções de segurança na nuvem, estratégia técnica unificada e apoio a políticas e normas. |

## <a name="azure-security-benchmark-recommendations"></a>Recomendações de benchmark de segurança Azure

Cada recomendação inclui as seguintes informações:

- **Azure ID**: O ID de benchmark de segurança Azure que corresponde à recomendação.
- **Controlos do CIS v7.1 ID:** Os controlos do CIS v7.1 que correspondem a esta recomendação.
- **NIST SP 800-53 r4 ID**: O controlo NIST SP 800-53 r4 (moderado) que corresponde a esta recomendação.
- **Pormenores**: A lógica da recomendação e as ligações à orientação sobre como implementá-la. Se a recomendação for apoiada pelo Azure Security Center, essa informação também será listada.
- **Responsabilidade**: Quer o cliente, o prestador de serviços, ou ambos são responsáveis pela implementação desta recomendação. As responsabilidades de segurança são partilhadas na nuvem pública. Alguns controlos de segurança só estão disponíveis para o prestador de serviços na nuvem e, por isso, o fornecedor é responsável por endereçá-los. Estas são observações gerais – para alguns serviços individuais, a responsabilidade será diferente da listada no Azure Security Benchmark. Estas diferenças são descritas nas recomendações de base para o serviço individual.
- **Partes interessadas** em Segurança do Cliente : [As funções de segurança](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) na organização do cliente que podem ser responsáveis, responsáveis ou consultados para o respetivo controlo. Pode ser diferente de organização para organização dependendo da estrutura da organização de segurança da sua empresa, e os papéis e responsabilidades que estabeleceu relacionados com a segurança da Azure.

> [!NOTE]
> Os mapeamentos de controlo entre as referências asb e a indústria (tais como NIST e CIS) apenas indicam que uma característica Azure específica pode ser utilizada para responder total ou parcialmente a um requisito de controlo definido no NIST ou na CIS. Deve estar ciente de que esta implementação não se traduz necessariamente no cumprimento integral do controlo correspondente no CEI ou no NIST.

Congratulamo-nos com o seu feedback detalhado e participação ativa no esforço de Benchmark de Segurança Azure. Se você gostaria de fornecer a entrada direta da equipe Azure Security Benchmark, preencha o formulário em https://aka.ms/AzSecBenchmark

## <a name="download"></a>Download

Pode baixar o Azure Security Benchmark no [formato de folha de cálculo](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark).

## <a name="next-steps"></a>Passos seguintes 
- Consulte o primeiro controlo de segurança: [Segurança da rede](security-control-network-security.md)
- Leia a introdução do [Azure Security Benchmark](introduction.md)
- Conheça os [Fundamentos de Segurança do Azure](../fundamentals/index.yml)