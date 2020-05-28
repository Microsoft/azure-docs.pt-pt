---
title: Proteção Avançada Contra Ameaças
titleSuffix: Azure SQL Database, SQL Managed Instance, & Azure Synapse
description: Advanced Threat Protection deteta atividades anómalas de base de dados que indicam potenciais ameaças à segurança na Base de Dados Azure SQL, Nainstância Gerida Azure SQL e Azure Synapse.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.custom: sqldbrb=2
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 27d44ba8be56839f248312e1bdb4b0e4b7761110
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84047021"
---
# <a name="advanced-threat-protection-for-azure-sql-database-sql-managed-instance--azure-synapse"></a>Proteção avançada de ameaças para base de dados Azure SQL, Instância Gerida SQL & Azure Synapse
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Advanced Threat Protection for [Azure SQL Database,](sql-database-paas-overview.md) [Azure SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) e [Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

Advanced Threat Protection faz parte da oferta avançada de segurança de [dados,](advanced-data-security.md) que é um pacote unificado para capacidades avançadas de segurança SQL. O Advanced Threat Protection pode ser acedido e gerido através do portal ADS SQL central.

## <a name="overview"></a>Descrição geral

A Advanced Threat Protection fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de bases de dados, potenciais vulnerabilidades e ataques de injeção SQL, bem como padrões anómalos de acesso à base de dados e consultas. A Advanced Threat Protection integra alertas com o [Azure Security Center,](https://azure.microsoft.com/services/security-center/)que incluem detalhes de atividades suspeitas e recomendam ações sobre como investigar e mitigar a ameaça. A Advanced Threat Protection torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança.

Para uma experiência de investigação completa, é aconselhável permitir a auditoria, que escreve eventos de base de dados a um registo de auditoria na sua conta de armazenamento Azure.  Para permitir a auditoria, consulte a Auditoria da Base de [Dados Azure SQL e a Azure Synapse](../../azure-sql/database/auditing-overview.md) ou a Auditoria para a [Instância Gerida Azure SQL](../managed-instance/auditing-configure.md).

## <a name="alerts"></a>Alertas

A Advanced Threat Protection for Azure SQL Database deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. Para obter uma lista de alertas para a Base de Dados Azure SQL, consulte os alertas para a Base de Dados SQL e o Armazém de [Dados SQL no Centro](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse)de Segurança Azure .

## <a name="explore-detection-of-a-suspicious-event"></a>Explore a deteção de um evento suspeito

Recebe uma notificação por e-mail após a deteção de atividades anómalas na base de dados. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, nome da base de dados, nome do servidor, nome da aplicação e a hora do evento. Além disso, o e-mail fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial para a base de dados.

![Relatório de atividadeanómala](./media/threat-detection-overview/anomalous_activity_report.png)

1. Clique no link de **alertas SQL recente** no e-mail para lançar o portal Azure e mostrar a página de alertas do Azure Security Center, que fornece uma visão geral das ameaças ativas detetadas na base de dados.

   ![Ameaças de atividade](./media/threat-detection-overview/active_threats.png)

2. Clique num alerta específico para obter detalhes e ações adicionais para investigar esta ameaça e remediar ameaças futuras.

   Por exemplo, a injeção SQL é um dos problemas de segurança de aplicações web mais comuns na Internet que é usado para atacar aplicações baseadas em dados. Os atacantes aproveitam as vulnerabilidades da aplicação para injetar declarações SQL maliciosas nos campos de entrada de aplicações, violando ou modificando dados na base de dados. Para os alertas de injeção SQL, os detalhes do alerta incluem a declaração sql vulnerável que foi explorada.

   ![Alerta específico](./media/threat-detection-overview/specific_alert.png)

## <a name="explore-alerts-in-azure-portal"></a>Explore alertas no portal Azure

A Advanced Threat Protection integra os seus alertas com o centro de [segurança Azure.](https://azure.microsoft.com/services/security-center/) Os azulejos de proteção contra ameaças avançadas SQL vivos dentro da base de dados e as lâminas SQL ADS no portal Azure rastreiam o estado das ameaças ativas.

Clique em alerta avançado de **proteção contra ameaças** para lançar a página de alertas do Centro de Segurança Azure e obter uma visão geral das ameaças sql ativas detetadas na base de dados.

   ![Alerta avançado de proteção contra ameaças](./media/threat-detection-overview/threat_detection_alert.png)

   ![Alerta avançado de proteção contra ameaças2](./media/threat-detection-overview/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre a Proteção Avançada de Ameaças na Base de [Dados Azure SQL & Azure Synapse](threat-detection-configure.md).
- Saiba mais sobre [a Proteção Avançada de Ameaças em 1º Caso Gerido Azure SQL](../managed-instance/threat-detection-configure.md).
- Saiba mais sobre [a segurança avançada de dados.](advanced-data-security.md)
- Saiba mais sobre a auditoria da Base de [Dados Azure SQL](../../azure-sql/database/auditing-overview.md)
- Saiba mais sobre o [centro de segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a página de preços da Base de [Dados Azure SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
