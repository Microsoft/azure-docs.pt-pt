---
title: Atributos de segurança para a base de dados do Azure SQL
description: Uma lista de verificação de atributos de segurança para avaliar a base de dados do Azure SQL
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c495456a5a3295abe5460ff6b5586e41fab2d95
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001045"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributos de segurança para a base de dados do Azure SQL

Este artigo documenta os atributos de segurança comuns incorporados no Azure SQL Database.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Base de dados SQL do Azure inclui ambos [base de dados individual](sql-database-single-index.yml) e [instância gerida](sql-database-managed-instance.md). As entradas abaixo aplicam-se ambas as ofertas, exceto onde indicação em contrário.

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outros recursos de criptografia (por exemplo, o lado do cliente, são sempre encriptados, etc.)</ul>| Sim | Conhecido como "encriptação-em-use", conforme descrito no artigo [Always Encrypted](sql-database-always-encrypted.md). Encriptação do lado do serviço usa [encriptação de dados transparente](transparent-data-encryption-azure-sql.md) (TDE).|
| Encriptação em trânsito:<ul><li>Encriptação do ExpressRoute</li><li>Na encriptação de VNet</li><li>Encriptação de VNet a VNet</ul>| Sim | Através de HTTPS. |
| Manipulação de chave de encriptação (CMK, BYOK, etc.)| Sim | São oferecidos a manipulação de chave gerida pelo serviço tanto gerida pelo cliente (o último através de [do Azure Key Vault](../key-vault/index.yml). |
| Encriptação de nível de coluna (Serviços de dados do Azure)| Sim | Por meio [Always Encrypted](sql-database-always-encrypted.md). |
| Chamadas de API encriptadas| Sim | Através de HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Aplica-se ao [base de dados individual](sql-database-single-index.yml) apenas. |
| Suporte de injeção de VNet| Sim | Aplica-se ao [instância gerida](sql-database-managed-instance.md) apenas. |
| Isolamento de rede e o suporte de firewall| Sim | Em ambas as bases de dados - e ao nível do servidor; de firewall isolamento para de rede [instância gerida](sql-database-managed-instance.md) apenas |
| Suporte de encapsulamento de forçado| Sim | [instância gerida](sql-database-managed-instance.md) via [Azure ExpressRoute](../expressroute/index.yml) VPN |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte (do Log analytics, o App insights, etc.) do Azure| Sim | A solução SIEM de terceiros da Imperva (SecureSphere) também é suportada, através de [os Hubs de eventos do Azure](../event-hubs/index.yml) integração via [auditoria SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Azure Active Directory. |
| Autorização| Sim |  |


## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Auditoria e registo de plano de controlo e gestão| Sim | Sim, em apenas alguns eventos. |
| Auditoria e registo de plano de dados | Sim | Via [auditoria SQL](sql-database-auditing.md). |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (controle de versão de configuração, etc.)| Não  | | 

## <a name="additional-security-attributes-for-sql-database"></a>Atributos de segurança adicional para a base de dados SQL

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Preventivas: avaliação de vulnerabilidade | Sim | Ver [avaliação de vulnerabilidades do SQL service ajuda a identificar vulnerabilidades de base de dados](sql-vulnerability-assessment.md). |
| Preventivas: deteção de dados e a classificação  | Sim | Ver [deteção de dados de base de dados do Azure SQL e SQL Data Warehouse e classificação](sql-database-data-discovery-and-classification.md). |
| Deteção: deteção de ameaças | Sim | Ver [proteção avançada contra ameaças para a base de dados SQL do Azure](sql-database-threat-detection-overview.md). |
