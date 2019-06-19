---
title: incluir ficheiro
description: incluir ficheiro
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: c8453a2ec00a2fca107f85a23a8af1e6313a70b6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184420"
---
### <a name="sql-servers"></a>SQL Servers

|  |  |
|---------|---------|
| [Auditar se não existir um administrador do Azure Active Directory](../articles/governance/policy/samples/audit-no-aad-admin.md) | Auditar quando não existe nenhum administrador do Azure Active Directory atribuído ao servidor SQL. |
| [Auditar a definição de deteção de ameaças do nível do Servidor](../articles/governance/policy/samples/audit-sql-server-threat-detection-setting.md) | Faz uma auditoria às políticas de alerta de segurança da base de dados SQL se as políticas não estiverem definidas para o estado especificado. Especifica um valor que indica se a deteção de ameaças está ativada ou desativada.  |
| [Auditar as definições de auditoria do SQL Server](../articles/governance/policy/samples/sql-server-audit.md) | Faz uma auditoria ao SQL Server para verificar se as definições de auditoria estão ativadas. |
| [Auditar a Definição de Auditoria do Nível do SQL Server](../articles/governance/policy/samples/audit-sql-server-audit-setting.md) | Faz uma auditoria das definições de auditoria do SQL Server se essas definições não corresponderem a uma definição especificada. Especifica um valor que indica se as definições de auditoria devem estar ativadas ou desativadas. |
| [Requer a Versão do SQL Server 12.0](../articles/governance/policy/samples/require-sql-12.md) | Requer que os servidores do SQL utilizem a versão 12.0.  |