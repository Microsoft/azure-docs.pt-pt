---
title: Atributos de segurança para o banco de dados SQL do Azure
description: Uma lista de verificação de atributos de segurança para avaliar o banco de dados SQL do Azure
services: sql-database
author: msmbaldwin
manager: barbkess
ms.service: load-balancer
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mbaldwin
ms.openlocfilehash: 1318b3e433224b009b76458b12e82c9bcf94bb7a
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444383"
---
# <a name="security-attributes-for-azure-sql-database"></a>Atributos de segurança para o banco de dados SQL do Azure

Este artigo documenta os atributos de segurança que são criados no banco de dados SQL do Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

O banco de dados SQL inclui [um único banco de dados e uma](sql-database-single-index.yml) [instância gerenciada](sql-database-managed-instance.md). As entradas a seguir se aplicam a ambas as ofertas, exceto onde indicado de outra forma.

## <a name="preventative"></a>Preventiva

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia em repouso:<ul><li>Encriptação do lado do servidor</li><li>Criptografia do lado do servidor com chaves gerenciadas pelo cliente</li><li>Outros recursos de criptografia, como o lado do cliente ou Always Encrypted</ul>| Sim | Chamado "criptografia em uso", conforme descrito no artigo [Always Encrypted](sql-database-always-encrypted.md). A criptografia do lado do servidor usa a Transparent [Data Encryption](transparent-data-encryption-azure-sql.md).|
| Criptografia em trânsito:<ul><li>Criptografia do Azure ExpressRoute</li><li>Criptografia em uma rede virtual</li><li>Criptografia entre redes virtuais</ul>| Sim | Usando HTTPS. |
| Criptografia – manipulação de chaves, como CMK ou BYOK| Sim | O tratamento de chaves gerenciado pelo serviço e gerenciado pelo cliente é oferecido. O último é oferecido por meio de [Azure Key Vault](../key-vault/index.yml). |
| Criptografia em nível de coluna fornecida pelos serviços de dados do Azure| Sim | Por meio de [Always Encrypted](sql-database-always-encrypted.md). |
| Chamadas de API criptografadas| Sim | Usando HTTPS/SSL. |

## <a name="network-segmentation"></a>Segmentação de rede

| Atributo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Sim | Aplica-se somente a [um único banco de dados](sql-database-single-index.yml) . |
| Suporte à injeção de rede virtual do Azure| Sim | Aplica-se somente à [instância gerenciada](sql-database-managed-instance.md) . |
| Isolamento de rede e suporte a firewall| Sim | Firewall no nível do banco de dados e do servidor. O isolamento de rede é apenas para [instância gerenciada](sql-database-managed-instance.md) . |
| Suporte a túnel forçado| Sim | [Instância gerenciada](sql-database-managed-instance.md) por meio de uma VPN de [ExpressRoute](../expressroute/index.yml) . |

## <a name="detection"></a>Deteção

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure, como Log Analytics ou Application Insights| Sim | O SecureSphere, a solução SIEM da Imperva, também tem suporte por meio da integração dos [hubs de eventos do Azure](../event-hubs/index.yml) por meio da [auditoria do SQL](sql-database-auditing.md). |

## <a name="identity-and-access-management"></a>Gestão de acesso e identidades

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| Sim | Azure Active Directory (Azure AD) |
| Autorização| Sim | Nenhum |

## <a name="audit-trail"></a>Trilha de auditoria

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Log e auditoria do plano de controle e do plano de gerenciamento| Sim | Sim apenas para alguns eventos |
| Log e auditoria do plano de dados | Sim | Por meio da [auditoria do SQL](sql-database-auditing.md) |

## <a name="configuration-management"></a>Gestão de configuração

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração, como o controle de versão da configuração| Não  | Nenhum |

## <a name="additional-security-attributes-for-sql-database"></a>Atributos de segurança adicionais para o banco de dados SQL

| Atributo de segurança | Sim/Não | Notas|
|---|---|--|
| Preventivo: avaliação de vulnerabilidade | Sim | Consulte [serviço de avaliação de vulnerabilidade do SQL ajuda a identificar vulnerabilidades de banco de dados](sql-vulnerability-assessment.md). |
| Preventivo: descoberta e classificação de dados  | Sim | Consulte [classificação de & do banco de dados SQL do Azure e SQL data warehouse a descoberta de data](sql-database-data-discovery-and-classification.md). |
| Detecção: detecção de ameaças | Sim | Consulte [proteção avançada contra ameaças para o banco de dados SQL do Azure](sql-database-threat-detection-overview.md). |
