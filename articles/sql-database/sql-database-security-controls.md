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
ms.openlocfilehash: ce7f3eafa57cbd993be98f4a2da3d89cb312f9b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77190683"
---
# <a name="security-controls-for-azure-sql-database"></a>Controlos de segurança para base de dados Azure SQL

Este artigo documenta os controlos de segurança incorporados na Base de Dados Azure SQL.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

A Base de Dados SQL inclui uma [única base de dados](sql-database-single-index.yml) e instância [gerida](sql-database-managed-instance.md). As seguintes entradas aplicam-se a ambas as ofertas, exceto se for em contrário.

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim | Aplica-se apenas à base de [dados.](sql-database-single-index.yml) |
| Suporte à injeção da Rede Virtual Azure| Sim | Aplica-se apenas à [instância gerida.](sql-database-managed-instance.md) |
| Isolamento da rede e suporte à firewall| Sim | Firewall tanto ao nível da base de dados como ao nível do servidor. O isolamento da rede é apenas para [casos geridos.](sql-database-managed-instance.md) |
| Apoio de túnel forçado| Sim | [Caso gerido](sql-database-managed-instance.md) através de uma VPN [ExpressRoute.](../expressroute/index.yml) |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure, tais como Log Analytics ou Application Insights| Sim | A SecureSphere, a solução SIEM da Imperva, também é suportada através da integração dos Hubs de [Eventos Azure](../event-hubs/index.yml) através da [auditoria SQL.](sql-database-auditing.md) |
| Exploração e auditoria de planos de controlo e de gestão| Sim | Sim, apenas para alguns eventos |
| Exploração e auditoria de planos de dados | Sim | Via [auditoria SQL](sql-database-auditing.md) |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Azure Active Directory (Azure AD) |
| Autorização| Sim | Nenhuma |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Chamada "encriptação em uso", como descrito no artigo [Always Encrypted](sql-database-always-encrypted.md). A encriptação do lado do servidor utiliza [encriptação de dados transparente.](transparent-data-encryption-azure-sql.md)|
| Encriptação em trânsito:<ul><li>Encriptação Azure ExpressRoute</li><li>Encriptação em uma rede virtual</li><li>Encriptação entre redes virtuais</ul>| Sim | Utilizando HTTPS. |
| Manuseamento de chaves de encriptação, como CMK ou BYOK| Sim | São oferecidos tanto o manuseamento de chaves gerido pelo serviço como gerido pelo cliente. Este último é oferecido através do [Cofre chave Azure.](../key-vault/index.yml) |
| Encriptação ao nível da coluna fornecida pelos serviços de dados do Azure| Sim | Através [de Sempre Encriptado](sql-database-always-encrypted.md). |
| Chamadas API encriptadas| Sim | Utilizando HTTPS/TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração, como versão da configuração| Não  | Nenhuma |

## <a name="additional-security-controls-for-sql-database"></a>Controlos de segurança adicionais para base de dados SQL

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Prevenção: avaliação da vulnerabilidade | Sim | Consulte o serviço de Avaliação de [VulnerabilidadeS SQL ajuda-o a identificar vulnerabilidades na base de dados](sql-vulnerability-assessment.md). |
| Prevenção: descoberta e classificação de dados  | Sim | Consulte a Base de [Dados Azure SQL e a Descoberta de dados do Armazém de Dados SQL & classificação](sql-database-data-discovery-and-classification.md). |
| Deteção: deteção de ameaças | Sim | Consulte A Proteção avançada de Ameaças para a Base de [Dados Azure SQL](sql-database-threat-detection-overview.md). |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)
