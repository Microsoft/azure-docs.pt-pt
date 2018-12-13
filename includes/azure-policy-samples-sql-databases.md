---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: 7bf885f2ab81e5d86878d2b0b53f4e98b8aedd9a
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318356"
---
### <a name="sql-databases"></a>Bases de Dados SQL

|  |  |
|---------|---------|
| [SKUs de DB SQL permitidos](../articles/governance/policy/samples/allowed-sql-db-skus.md) | Requer que as bases de dados SQL utilizem um SKU aprovado. Especifica uma matriz de IDs ou nomes de SKUs permitidos. |
| [Auditar a definição de deteção de ameaças do nível de BD](../articles/governance/policy/samples/audit-db-threat-detection-setting.md) | Faz uma auditoria às políticas de alerta de segurança da base de dados SQL se as políticas não estiverem definidas para o estado especificado. Especifica um valor que indica se a deteção de ameaças está ativada ou desativada.  |
| [Auditar a encriptação da Base de Dados SQL](../articles/governance/policy/samples/sql-database-encryption-audit.md) | Faz uma auditoria se a base de dados SQL não tiver a encriptação de dados transparente ativada. |
| [Auditar a Definição de Auditoria do Nível de DB SQL](../articles/governance/policy/samples/audit-sql-db-audit-setting.md) | Faz uma auditoria das definições de auditoria da base de dados SQL se essas definições não corresponderem a uma definição especificada. Especifica um valor que indica se as definições de auditoria devem estar ativadas ou desativadas.  |