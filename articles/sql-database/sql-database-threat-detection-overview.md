---
title: Proteção avançada contra ameaças-banco de dados SQL do Azure
description: A proteção avançada contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças à segurança no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
ms.date: 03/31/2019
ms.openlocfilehash: fca68fbc1ee30b1df100255e463eca2fc895980b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686964"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Advanced Threat Protection para a Base de Dados SQL do Azure

A proteção avançada contra ameaças para o [banco de dados SQL do Azure](sql-database-technical-overview.md) e o [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A proteção avançada contra ameaças faz parte da oferta do ADS ( [segurança de dados avançada](sql-database-advanced-data-security.md) ), que é um pacote unificado para recursos avançados de segurança do SQL. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do portal central de anúncios do SQL.

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>O que é proteção avançada contra ameaças

 A proteção avançada contra ameaças fornece uma nova camada de segurança, que permite que os clientes detectem e respondam a ameaças potenciais à medida que ocorrem, fornecendo alertas de segurança em atividades anormais. Os usuários recebem um alerta sobre atividades suspeitas de banco de dados, possíveis vulnerabilidades e ataques de injeção de SQL, bem como padrões de consulta e acesso de banco de dados anormais. A proteção avançada contra ameaças integra alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/), que inclui detalhes de atividades suspeitas e recomendação de ações sobre como investigar e atenuar a ameaça. A proteção avançada contra ameaças simplifica a endereçamento de ameaças potenciais ao banco de dados sem a necessidade de ser um especialista em segurança ou gerenciar sistemas de monitoramento de segurança avançados.

Para uma experiência de investigação completa, é recomendável habilitar a [auditoria do banco de dados SQL](sql-database-auditing.md), que grava eventos de banco de dados em um log de auditoria em sua conta de armazenamento do Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alertas de proteção avançada contra ameaças

A proteção avançada contra ameaças para o banco de dados SQL do Azure detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados e pode disparar os seguintes alertas:

- **Vulnerabilidade à injeção de SQL**: esse alerta é disparado quando um aplicativo gera uma instrução SQL com falha no banco de dados. Este alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Existem dois motivos possíveis para a geração de uma instrução defeituosa:

  - Um defeito no código da aplicação que constitui a instrução SQL defeituosa
  - O código de aplicação ou os procedimentos armazenados não saneiam a entrada de utilizador ao criar a instrução SQL defeituosa, o que pode ser explorado para Injeção SQL
- **Potencial injeção SQL**: este alerta é acionado quando uma exploração ativa ocorre contra uma vulnerabilidade de aplicação identificada para a injeção SQL. Significa que o atacante está a tentar injetar instruções SQL maliciosas através dos procedimentos armazenados ou código de aplicação com vulnerabilidade.
- **Acesso de uma localização invulgar**: este alerta é acionado quando ocorre uma alteração no padrão de acesso ao servidor SQL, no qual alguém iniciou sessão no servidor SQL a partir de uma localização geográfica invulgar. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de um centro de dados do Azure invulgar**: este alerta é acionado quando ocorre uma alteração no padrão de acesso ao servidor SQL, no qual alguém iniciou sessão no servidor SQL a partir de um centro de dados do Azure invulgar que foi visto neste servidor durante o período recente. Em alguns casos, o alerta deteta uma ação legítima (a sua nova aplicação no Azure, Power BI, Editor de Consultas SQL do Azure). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso de um principal invulgar**: este alerta é acionado quando ocorre uma alteração no padrão de acesso ao servidor SQL, no qual alguém iniciou sessão no servidor SQL com recurso a um principal (utilizador SQL) invulgar. Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso de uma localização potencialmente prejudicial**: este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder à base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Credenciais SQL de força bruta**: este alerta é acionado quando existe um número anormalmente elevado de inícios de sessão falhados com diferentes credenciais. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades anômalas do banco de dados após a detecção de um evento suspeito

Você recebe uma notificação por email após a detecção de atividades anormais do banco de dados. O email fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anormais, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornece informações sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça em potencial para o banco de dados.

![Relatório de atividade anômala](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Clique no link **exibir alertas recentes do SQL** no email para iniciar o portal do Azure e mostrar a página de alertas da central de segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no banco de dados SQL.

   ![Ameaças à atividade](./media/sql-database-threat-detection/active_threats.png)

2. Clique em um alerta específico para obter detalhes adicionais e ações para investigar essa ameaça e corrigir futuras ameaças.

   Por exemplo, a injeção de SQL é um dos problemas de segurança de aplicativo Web mais comuns na Internet que é usado para atacar aplicativos controlados por dados. Os invasores aproveitam as vulnerabilidades do aplicativo para injetar instruções SQL mal-intencionadas em campos de entrada de aplicativo, violando ou modificando dados no banco de dado. Para os alertas de injeção de SQL, os detalhes do alerta incluem a instrução SQL vulnerável que foi explorada.

   ![Alerta específico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Explore os alertas de proteção avançada contra ameaças para seu banco de dados no portal do Azure

A proteção avançada contra ameaças integra seus alertas à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/). Blocos de proteção avançada contra ameaças do SQL Live no banco de dados e nas folhas de anúncios do SQL no portal do Azure acompanhar o status das ameaças ativas.

Clique em **alerta de proteção avançada contra ameaças** para iniciar a página de alertas da central de segurança do Azure e obtenha uma visão geral das ameaças do SQL ativas detectadas no banco de dados ou data warehouse.

   ![Alerta de proteção avançada contra ameaças](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Alert2 de proteção avançada contra ameaças](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [proteção avançada contra ameaças em bancos de dados individuais e em pool](sql-database-threat-detection.md).
- Saiba mais sobre a [proteção avançada contra ameaças na instância gerenciada](sql-database-managed-instance-threat-detection.md).
- Saiba mais sobre a [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre a [auditoria do banco de dados SQL do Azure](sql-database-auditing.md)
- Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre preços, consulte a [página de preços do banco de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)  
