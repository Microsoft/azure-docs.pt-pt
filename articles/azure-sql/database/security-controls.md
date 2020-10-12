---
title: Controlos de segurança
description: Uma lista de controlos de segurança para avaliar a Base de Dados Azure SQL
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: eec032ad56d00778627fc147761f61c03ba8bafd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89442093"
---
# <a name="security-controls-for-azure-sql-database-and-sql-managed-instance"></a>Controlos de segurança para Azure SQL Database e SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo documenta os controlos de segurança que são incorporados na Base de Dados Azure SQL e na Azure SQL Managed Instance.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]



## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Sim | Aplica-se apenas à [Base de Dados SQL.](../index.yml) |
| Suporte à injeção de rede virtual Azure| Sim | Aplica-se apenas a [SQL Caso Gerido.](../managed-instance/sql-managed-instance-paas-overview.md) |
| Isolamento de rede e suporte a firewall| Sim | Firewall tanto no nível da base de dados como no nível do servidor. O isolamento da rede é apenas para [sql Caso gerido.](../managed-instance/sql-managed-instance-paas-overview.md) |
| Suporte de túneis forçados| Sim | [SQL Caso Gerido](../managed-instance/sql-managed-instance-paas-overview.md) através de uma VPN [ExpressRoute.](../expressroute/../index.yml) |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure, como Log Analytics ou Application Insights| Sim | A SecureSphere, a solução SIEM da Imperva, também é suportada através da integração [do Azure Event Hubs](../event-hubs/../index.yml) através da [auditoria SQL.](../../azure-sql/database/auditing-overview.md) |
| Registo e auditoria de controlo-avião e de gestão-avião| Sim | Sim apenas para alguns eventos |
| Registo e auditoria de data-plane | Sim | Através da [auditoria SQL](../../azure-sql/database/auditing-overview.md) |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Azure Active Directory (Azure AD) |
| Autorização| Sim | Nenhum |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | Denominado "encriptação em uso", como descrito no artigo [Sempre Encriptado](always-encrypted-certificate-store-configure.md). A encriptação do lado do servidor utiliza [encriptação de dados transparentes.](transparent-data-encryption-tde-overview.md)|
| Encriptação em trânsito:<ul><li>Encriptação Azure ExpressRoute</li><li>Encriptação numa rede virtual</li><li>Encriptação entre redes virtuais</ul>| Sim | Utilizando HTTPS. |
| Tratamento de chaves de encriptação, tais como CMK ou BYOK| Sim | Tanto o tratamento de chaves gerido pelo serviço como o manuseamento de chaves gerido pelo cliente são oferecidos. Este último é oferecido através do [Azure Key Vault](../key-vault/../index.yml). |
| Encriptação ao nível da coluna fornecida pelos serviços de dados Azure| Sim | Através [de Always Encrypted](always-encrypted-certificate-store-configure.md). |
| Chamadas encriptadas da API| Sim | Utilização HTTPS/TLS. |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte à gestão de configurações, como a versão da configuração| Não  | Nenhum |

## <a name="additional-security-controls-for-sql-database"></a>Controlos de segurança adicionais para base de dados SQL

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Prevenção: avaliação da vulnerabilidade | Sim | Consulte [o serviço de avaliação de vulnerabilidades SQL ajuda-o a identificar vulnerabilidades de base de dados.](sql-vulnerability-assessment.md) |
| Preventiva: descoberta e classificação de dados  | Sim | Consulte [a Base de Dados Azure SQL e a descoberta de dados Azure Synapse Analytics & classificação](data-discovery-and-classification-overview.md). |
| Deteção: deteção de ameaças | Sim | Consulte a [Proteção Avançada de Ameaças para a Base de Dados Azure SQL](threat-detection-overview.md). |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)
