---
title: Proteção Avançada Contra Ameaças
description: A Advanced Threat Protection deteta atividades anómalas de base de dados que indicam potenciais ameaças à segurança na Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 0b231adafabff8414b1cf742e1441e938a3fa212
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848199"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection para a Base de Dados SQL do Azure

Advanced Threat Protection for [Azure SQL Database](sql-database-technical-overview.md) and [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

Advanced Threat Protection faz parte da oferta avançada de segurança de [dados](sql-database-advanced-data-security.md) (ADS), que é um pacote unificado para capacidades avançadas de segurança SQL. O Advanced Threat Protection pode ser acedido e gerido através do portal ADS SQL central.

> [!NOTE]
> Este tópico aplica-se ao servidor Azure SQL, e tanto à Base de Dados SQL como ao Synapse Azure que são criados no servidor Azure SQL. Para a simplicidade, a Base de Dados SQL é utilizada quando se refere tanto à Base de Dados SQL como ao Synapse Azure.

## <a name="what-is-advanced-threat-protection"></a>O que é Proteção Avançada de Ameaças

 A Advanced Threat Protection fornece uma nova camada de segurança, que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem, fornecendo alertas de segurança sobre atividades anómalas. Os utilizadores recebem um alerta sobre atividades suspeitas de bases de dados, potenciais vulnerabilidades e ataques de injeção SQL, bem como padrões anómalos de acesso à base de dados e consultas. A Advanced Threat Protection integra alertas com o [Azure Security Center,](https://azure.microsoft.com/services/security-center/)que incluem detalhes de atividades suspeitas e recomendam ações sobre como investigar e mitigar a ameaça. A Advanced Threat Protection torna simples abordar potenciais ameaças à base de dados sem a necessidade de ser um perito em segurança ou gerir sistemas avançados de monitorização de segurança.

Para uma experiência de investigação completa, é aconselhável ativar a Auditoria de [Bases de Dados SQL,](sql-database-auditing.md)que escreve eventos de base de dados num registo de auditoria na sua conta de armazenamento Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alertas avançados de proteção contra ameaças

A Advanced Threat Protection for Azure SQL Database deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados. Para obter uma lista de alertas para a Base de Dados Azure SQL, consulte os alertas para a Base de Dados SQL e o Armazém de [Dados SQL no Centro](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-sql-db-and-warehouse)de Segurança Azure .

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore atividades anómalas na base de dados após a deteção de um evento suspeito

Recebe uma notificação por e-mail após a deteção de atividades anómalas na base de dados. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, nome da base de dados, nome do servidor, nome da aplicação e a hora do evento. Além disso, o e-mail fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial para a base de dados.

![Relatório de atividadeanómala](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Clique no recente link de **alertas SQL** no e-mail para lançar o portal Azure e mostrar a página de alertas do Azure Security Center, que fornece uma visão geral das ameaças ativas detetadas na base de dados SQL.

   ![Ameaças de atividade](./media/sql-database-threat-detection/active_threats.png)

2. Clique num alerta específico para obter detalhes e ações adicionais para investigar esta ameaça e remediar ameaças futuras.

   Por exemplo, a injeção SQL é um dos problemas de segurança de aplicações web mais comuns na Internet que é usado para atacar aplicações baseadas em dados. Os atacantes aproveitam as vulnerabilidades da aplicação para injetar declarações SQL maliciosas nos campos de entrada de aplicações, violando ou modificando dados na base de dados. Para os alertas de injeção SQL, os detalhes do alerta incluem a declaração sql vulnerável que foi explorada.

   ![Alerta específico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Explore alertas avançados de proteção contra ameaças para a sua base de dados no portal Azure

A Advanced Threat Protection integra os seus alertas com o centro de [segurança Azure.](https://azure.microsoft.com/services/security-center/) Os azulejos de proteção contra ameaças avançadas SQL vivos dentro da base de dados e as lâminas SQL ADS no portal Azure rastreiam o estado das ameaças ativas.

Clique em alerta avançado de **proteção contra ameaças** para lançar a página de alertas do Centro de Segurança Azure e obter uma visão geral das ameaças sql ativas detetadas na base de dados.

   ![Alerta avançado de proteção contra ameaças](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alerta avançado de proteção contra ameaças2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a Proteção avançada de ameaças em bases de [dados individuais e reunidas.](sql-database-threat-detection.md)
- Saiba mais sobre [a Proteção avançada de ameaças em caso gerido](sql-database-managed-instance-threat-detection.md).
- Saiba mais sobre [a segurança avançada de dados.](sql-database-advanced-data-security.md)
- Saiba mais sobre a auditoria da Base de [Dados Azure SQL](sql-database-auditing.md)
- Saiba mais sobre o [centro de segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre os preços, consulte a página de preços da Base de [Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
