---
title: Controlos de segurança
description: Uma lista de controlos de segurança para avaliar a Base de Dados Azure SQL
services: sql-database
author: msmbaldwin
manager: rkalrin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 09045e01ad4d40ab770dd6203f2dd4b299317a55
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84050010"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Controlos de segurança para base de dados Azure SQL e Instância Gerida SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo documenta os controlos de segurança incorporados na Base de Dados Azure SQL e na Instância Gerida Azure SQL.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim | Aplica-se apenas à Base de [Dados SQL.](../index.yml) |
| Suporte à injeção da Rede Virtual Azure| Sim | Aplica-se apenas à [Instância Gerida SQL.](../managed-instance/sql-managed-instance-paas-overview.md) |
| Isolamento da rede e suporte à firewall| Sim | Firewall tanto ao nível da base de dados como ao nível do servidor. O isolamento da rede é apenas para a [Instância Gerida sQL.](../managed-instance/sql-managed-instance-paas-overview.md) |
| Apoio de túnel forçado| Sim | [Instância Gerida SQL](../managed-instance/sql-managed-instance-paas-overview.md) através de uma VPN [ExpressRoute.](../expressroute/../index.yml) |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure, tais como Log Analytics ou Application Insights| Sim | A SecureSphere, a solução SIEM da Imperva, também é suportada através da integração dos Hubs de [Eventos Azure](../event-hubs/../index.yml) através da [auditoria SQL.](../../azure-sql/database/auditing-overview.md) |
| Exploração e auditoria de planos de controlo e de gestão| Sim | Sim, apenas para alguns eventos |
| Exploração e auditoria de planos de dados | Sim | Via [auditoria SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Azure Active Directory (Azure AD) |
| Autorização| Sim | Nenhum |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Chamada "encriptação em uso", como descrito no artigo [Always Encrypted](always-encrypted-certificate-store-configure.md). A encriptação do lado do servidor utiliza [encriptação de dados transparente.](transparent-data-encryption-tde-overview.md)|
| Encriptação em trânsito:<ul><li>Encriptação Azure ExpressRoute</li><li>Encriptação em uma rede virtual</li><li>Encriptação entre redes virtuais</ul>| Sim | Utilizando HTTPS. |
| Manuseamento de chaves de encriptação, como CMK ou BYOK| Sim | São oferecidos tanto o manuseamento de chaves gerido pelo serviço como gerido pelo cliente. Este último é oferecido através do [Cofre chave Azure.](../key-vault/../index.yml) |
| Encriptação ao nível da coluna fornecida pelos serviços de dados do Azure| Sim | Através [de Sempre Encriptado](always-encrypted-certificate-store-configure.md). |
| Chamadas API encriptadas| Sim | Utilizando HTTPS/TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração, como versão da configuração| Não  | Nenhum |

## <a name="additional-security-controls-for-sql-database"></a>Controlos de segurança adicionais para base de dados SQL

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Prevenção: avaliação da vulnerabilidade | Sim | Consulte o serviço de Avaliação de [VulnerabilidadeS SQL ajuda-o a identificar vulnerabilidades na base de dados](sql-vulnerability-assessment.md). |
| Prevenção: descoberta e classificação de dados  | Sim | Consulte a Base de [Dados Azure SQL e a Descoberta de dados do Armazém de Dados SQL & classificação](data-discovery-and-classification-overview.md). |
| Deteção: deteção de ameaças | Sim | Consulte A Proteção avançada de Ameaças para a Base de [Dados Azure SQL](threat-detection-overview.md). |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../../security/fundamentals/security-controls.md)
