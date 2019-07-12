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
ms.openlocfilehash: 71e87a3295a9cd73dca2f97b3fa04a5d5ff76f84
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798693"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributos de segurança para a base de dados do Azure SQL

Este artigo documenta os atributos de segurança comuns, que estão incorporados na base de dados do Azure SQL.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

Base de dados SQL inclui ambos [base de dados individual](sql-database-single-index.yml) e [instância gerida](sql-database-managed-instance.md). As entradas a seguir se aplicam a ambas as ofertas, exceto onde indicação em contrário.

## <a name="preventative"></a>Preventivas

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação inativa:<ul><li>Encriptação do lado do servidor</li><li>Encriptação do lado do servidor com chaves geridas pelo cliente</li><li>Outras funcionalidades de encriptação, como do lado do cliente ou Always Encrypted</ul>| Sim | Chamado "encriptação-em utilização,", conforme descrito no artigo [Always Encrypted](sql-database-always-encrypted.md). Encriptação do lado do servidor usa [encriptação de dados transparente](transparent-data-encryption-azure-sql.md).|
| Encriptação em trânsito:<ul><li>Encriptação do Azure ExpressRoute</li><li>Encriptação numa rede virtual</li><li>Encriptação entre redes virtuais</ul>| Sim | Através de HTTPS. |
| Chave de encriptação, processar, tais como CMK ou BYOK| Sim | São oferecidos a manipulação de chave gerida pelo serviço tanto gerida pelo cliente. A última opção é disponibilizado através de [do Azure Key Vault](../key-vault/index.yml). |
| Encriptação de nível de coluna fornecida pelos serviços de dados do Azure| Sim | Por meio [Always Encrypted](sql-database-always-encrypted.md). |
| Encriptados chamadas de API| Sim | Através de HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte de ponto final de serviço| Sim | Aplica-se ao [base de dados individual](sql-database-single-index.yml) apenas. |
| Suporte de injeção de rede Virtual do Azure| Sim | Aplica-se ao [instância gerida](sql-database-managed-instance.md) apenas. |
| Isolamento de rede e o suporte de firewall| Sim | Firewall ao nível de servidor e ao nível da base de dados. Isolamento de rede se destina [instância gerida](sql-database-managed-instance.md) apenas. |
| Suporte de encapsulamento de forçado| Sim | [Instância gerida](sql-database-managed-instance.md) através de um [ExpressRoute](../expressroute/index.yml) VPN. |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Monitorização de suporte, como o Application Insights ou o Log Analytics do Azure| Sim | SecureSphere, a solução SIEM da Imperva, também é suportada através dos [Event Hubs do Azure](../event-hubs/index.yml) integração via [auditoria de SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Azure Active Directory (Azure AD) |
| Autorização| Sim | Nenhum |

## <a name="audit-trail"></a>Registo de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Plano de controlo e o plano de gestão de registo e auditoria| Sim | Sim, em apenas alguns eventos |
| Plano de dados de registo e auditoria | Sim | Por meio de [auditoria SQL](sql-database-auditing.md) |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração, como o controle de versão da configuração| Não  | Nenhum |

## <a name="additional-security-attributes-for-sql-database"></a>Atributos de segurança adicional para a base de dados SQL

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Preventivas: avaliação de vulnerabilidade | Sim | Ver [avaliação de vulnerabilidades do SQL service ajuda a identificar vulnerabilidades de base de dados](sql-vulnerability-assessment.md). |
| Preventivas: deteção de dados e a classificação  | Sim | Ver [deteção de dados de base de dados do Azure SQL e SQL Data Warehouse e classificação](sql-database-data-discovery-and-classification.md). |
| Deteção: deteção de ameaças | Sim | Ver [proteção avançada contra ameaças para a base de dados SQL do Azure](sql-database-threat-detection-overview.md). |
