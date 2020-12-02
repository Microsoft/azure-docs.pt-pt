---
title: Advanced Threat Protection
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse Analytics
description: A Advanced Threat Protection deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na Base de Dados Azure SQL, na Instância Gerida Azure SQL e na Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, sstein
ms.date: 12/01/2020
tags: azure-synapse
ms.openlocfilehash: 931e914cd3c184136395a9bb9a7e148a90e9fb91
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461939"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Proteção Avançada de Ameaças para Base de Dados Azure SQL, SqL Gerd instance e Azure Synapse Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Advanced Threat Protection for [Azure SQL Database](sql-database-paas-overview.md), [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) deteta atividades anómalas que indicam tentativas incomuns e potencialmente prejudiciais de aceder ou explorar bases de dados.

Advanced Threat Protection faz parte da oferta [Azure Defender for SQL,](azure-defender-for-sql.md) que é um pacote unificado para capacidades avançadas de segurança SQL. A Advanced Threat Protection pode ser acedida e gerida através do Azure Defender central para o portal SQL.

## <a name="overview"></a>Descrição geral

Advanced Threat Protection fornece uma nova camada de segurança, que permite que os clientes detetem e respondam a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de base de dados, potenciais vulnerabilidades e ataques de injeção de SQL, bem como padrões anómalos de acesso a bases de dados e padrões de consultas. Advanced Threat Protection integra alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/), que incluem detalhes de atividades suspeitas e recomendam ações sobre como investigar e mitigar a ameaça. A Advanced Threat Protection torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou de gerir sistemas avançados de monitorização de segurança.

Para uma experiência de investigação completa, recomenda-se permitir a auditoria, que escreve eventos de base de dados para um registo de auditoria na sua conta de armazenamento Azure.  Para permitir a auditoria, consulte [a Auditoria para a Base de Dados Azure SQL e a Azure Synapse](../../azure-sql/database/auditing-overview.md) ou Auditoria para [Azure SQL Managed Instance](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Alertas

A Advanced Threat Protection for Azure SQL Database deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados. Para obter uma lista de alertas para a Base de Dados Azure SQL, consulte os [Alertas para a Base de Dados SQL e Azure Synapse Analytics no Azure Security Center](../../security-center/alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-detection-of-a-suspicious-event"></a>Explore a deteção de um evento suspeito

Você recebe uma notificação por e-mail após a deteção de atividades de base de dados anómalas. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, nome da base de dados, nome do servidor, nome da aplicação e hora do evento. Além disso, o e-mail fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a potencial ameaça à base de dados.

![Relatório de atividades anómalas](./media/threat-detection-overview/anomalous_activity_report.png)

1. Clique no link **de alertas SQL recentes** no e-mail para lançar o portal Azure e mostrar a página de alertas do Azure Security Center, que fornece uma visão geral das ameaças ativas detetadas na base de dados.

   ![Ameaças de atividade](./media/threat-detection-overview/active_threats.png)

1. Clique num alerta específico para obter detalhes e ações adicionais para investigar esta ameaça e remediar ameaças futuras.

   Por exemplo, a injeção de SQL é um dos problemas de segurança de aplicações web mais comuns na Internet que é usado para atacar aplicações baseadas em dados. Os atacantes aproveitam-se das vulnerabilidades da aplicação para injetar declarações SQL maliciosas nos campos de entrada de aplicações, violando ou modificando dados na base de dados. Para alertas de injeção SQL, os detalhes do alerta incluem a declaração sql vulnerável que foi explorada.

   ![Alerta específico](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-the-azure-portal"></a>Explore alertas no portal Azure

Advanced Threat Protection integra os seus alertas com [o Centro de Segurança Azure](https://azure.microsoft.com/services/security-center/). Os azulejos de proteção de ameaças avançadas do SQL ao vivo dentro da base de dados e as lâminas SQL Azure Defender no portal Azure acompanham o estado das ameaças ativas.

Clique **em alerta avançado de proteção contra ameaças** para lançar a página de alertas do Azure Security Center e obtenha uma visão geral das ameaças de SQL ativas detetadas na base de dados.

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection-alerts.png" alt-text="alertas avançados de proteção de ameaças na visão geral da base de dados":::

:::image type="content" source="media/azure-defender-for-sql/advanced-threat-protection.png" alt-text="proteção de ameaças avançadas no centro de segurança":::

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Proteção Avançada de Ameaças na Base de Dados Azure SQL & Azure Synapse](threat-detection-configure.md).
- Saiba mais sobre [a Proteção Avançada de Ameaças em Azure SQL Gestd site.](../managed-instance/threat-detection-configure.md)
- Saiba mais sobre [o Azure Defender para a SQL](azure-defender-for-sql.md).
- Saiba mais sobre [a auditoria da Base de Dados Azure SQL](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre [o Centro de Segurança Azure](../../security-center/security-center-introduction.md)
- Para obter mais informações sobre preços, consulte a [página de preços da Base de Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/)