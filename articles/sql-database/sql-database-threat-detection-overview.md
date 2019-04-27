---
title: Proteção avançada contra ameaças - base de dados SQL do Azure | Documentos da Microsoft
description: Proteção avançada contra ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança na base de dados do Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: monhaber
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 03/31/2019
ms.openlocfilehash: 710a94c919f4262c3f572f28d03c79b77e658287
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614606"
---
# <a name="advanced-threat-protection-for-azure-sql-database"></a>Proteção avançada contra ameaças para a base de dados SQL do Azure

Proteção avançada contra ameaças para [Azure SQL Database](sql-database-technical-overview.md) e [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de bases de dados.

Proteção avançada contra ameaças faz parte do [segurança de dados avançada](sql-database-advanced-data-security.md) (ADS) oferta, que é um pacote unificado para funções de segurança avançadas do SQL. Proteção avançada contra ameaças podem ser acessada e gerenciada através do portal de anúncios de SQL central.

> [!NOTE]
> Este tópico aplica-se ao servidor SQL do Azure, bem como às bases de dados da Base de Dados SQL e do SQL Data Warehouse que são criadas no servidor SQL do Azure. Para simplificar, a Base de Dados SQL é utilizada para referenciar a Base de Dados SQL e o SQL Data Warehouse.

## <a name="what-is-advanced-threat-protection"></a>O que é a proteção avançada contra ameaças

 Proteção avançada contra ameaças fornece uma nova camada de segurança, o que permite aos clientes detetar e responder a potenciais ameaças à medida que ocorrem ao fornecer alertas de segurança relativamente a atividades anómalas. Os utilizadores recebem um alerta após a atividades suspeitas da base de dados, potenciais vulnerabilidades, e injeção de SQL, ataques, bem como de acesso de base de dados anómalas e padrões de consulta. Proteção avançada contra ameaças integra alertas no [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/), que incluem detalhes de atividade suspeita e ação investigar e mitigar a ameaça recomendada. Proteção avançada contra ameaças facilita lidar com potenciais ameaças à base de dados sem a necessidade de ser um especialista em segurança ou gerir sistemas de monitorização de segurança avançada.

Para uma experiência de investigação completo, é recomendado que ative [a auditoria de base de dados SQL](sql-database-auditing.md), que escreve o registo de eventos de base de dados para uma auditoria na sua conta de armazenamento do Azure.  

## <a name="advanced-threat-protection-alerts"></a>Alertas do Advanced Threat Protection

Proteção avançada contra ameaças para a base de dados do Azure SQL Deteta atividades anómalas que indiquem tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de bases de dados e ele pode acionar os seguintes alertas:

- **Vulnerabilidade a injeção de SQL**: Este alerta é acionado quando uma aplicação gera uma instrução SQL defeituosa na base de dados. Este alerta pode indicar uma possível vulnerabilidade a ataques de injeção de SQL. Existem dois motivos possíveis para a geração de uma instrução defeituosa:

  - Um defeito no código da aplicação que constitui a instrução SQL defeituosa
  - O código de aplicação ou os procedimentos armazenados não saneiam a entrada de utilizador ao criar a instrução SQL defeituosa, o que pode ser explorado para Injeção SQL
- **Potencial injeção de SQL**: Este alerta é acionado quando uma exploração ativa ocorre contra uma vulnerabilidade de aplicação identificada a injeção de SQL. Significa que o atacante está a tentar injetar instruções SQL maliciosas através dos procedimentos armazenados ou código de aplicação com vulnerabilidade.
- **Acesso a partir de uma localização invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso ao SQL server, em que alguém iniciou sessão para o SQL server a partir de uma localização geográfica invulgar. Em alguns casos, o alerta deteta uma ação legítima (uma nova manutenção de programador ou aplicação). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir do Centro de dados do Azure invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso ao SQL server, em que alguém iniciou sessão para o SQL server a partir de um centro de dados do Azure invulgar que foi visto neste servidor durante o período recente. Em alguns casos, o alerta deteta uma ação legítima (a sua nova aplicação no Azure, Power BI, Editor de Consultas SQL do Azure). Noutros casos, o alerta deteta uma ação maliciosa de um recurso/serviço do Azure (ex-funcionário, atacante externo).
- **Acesso a partir de principal invulgar**: Este alerta é acionado quando ocorre uma alteração no padrão de acesso ao SQL server, onde alguém fez logon no SQL server utilizar um principal invulgar (utilizador SQL). Em alguns casos, o alerta deteta uma ação legítima (nova aplicação, manutenção de programador). Noutros casos, o alerta deteta uma ação maliciosa (ex-funcionário, atacante externo).
- **Acesso a partir de uma aplicação potencialmente prejudicial**: Este alerta é acionado quando uma aplicação potencialmente prejudicial é utilizada para aceder a base de dados. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque através de ferramentas de ataque comuns.
- **Credenciais SQL de força bruta**: Este alerta é acionado quando existe um número anormalmente elevado de inícios de sessão falhados com credenciais diferentes. Em alguns casos, o alerta deteta testes de penetração em ação. Noutros casos, o alerta deteta um ataque de força bruta.

## <a name="explore-anomalous-database-activities-upon-detection-of-a-suspicious-event"></a>Explore a atividades anómalas da base de dados após a deteção de um evento suspeita

Receber uma notificação por e-mail após a deteção de atividades anómalas da base de dados. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, o nome de base de dados, nome do servidor, o nome da aplicação e a hora do evento. Além disso, o e-mail fornece informações sobre as causas possíveis e as ações recomendadas para investigar e mitigar a potencial ameaça à base de dados.

![Relatório de atividade anómala](./media/sql-database-threat-detection/anomalous_activity_report.png)

1. Clique nas **ver alertas recentes do SQL** ligação no e-mail para iniciar o portal do Azure e mostrar a página de alertas do Centro de segurança do Azure, que fornece uma visão geral do Active Directory ameaças detetadas na base de dados SQL.

   ![Ameaças de atividade](./media/sql-database-threat-detection/active_threats.png)

2. Clique num alerta específico para obter detalhes adicionais e ações para esta ameaça a investigar e remediar ameaças futuras.

   Por exemplo, injeção de SQL é uma dos problemas mais comuns de segurança de aplicação Web na Internet que é utilizado para atacar aplicações condicionadas por dados. Os atacantes tiram partido das vulnerabilidades das aplicações para injetar instruções SQL maliciosas nos campos de entrada do aplicativo, violar ou modificar os dados na base de dados. Para os alertas de Injeção de SQL, os detalhes do alerta incluem a instrução SQL vulnerável, que foi explorada.

   ![Alerta específico](./media/sql-database-threat-detection/specific_alert.png)

## <a name="explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal"></a>Explore os alertas de proteção avançada contra ameaças para a base de dados no portal do Azure

Proteção avançada contra ameaças integra seus alertas no [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/). Blocos dinâmicos de proteção do SQL avançada contra ameaças na base de dados e os painéis de anúncios de SQL no portal do Azure rastrear o status de ameaças ativas.

Clique em **alerta de proteção avançada contra ameaças** para iniciar o Centro de segurança do Azure a alertas de página e obter uma visão geral do Active Directory SQL ameaças detetadas no armazém de dados ou base de dados.

   ![Alerta de proteção contra ameaças avançada](./media/sql-database-threat-detection/threat_detection_alert.png)

   ![Advanced Threat Protection alert2](./media/sql-database-threat-detection/threat_detection_alert_atp.png)

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [proteção avançada contra ameaças em bases de dados individuais e em pool](sql-database-threat-detection.md).
- Saiba mais sobre [proteção avançada contra ameaças na instância gerida](sql-database-managed-instance-threat-detection.md).
- Saiba mais sobre [segurança de dados avançada](sql-database-advanced-data-security.md).
- Saiba mais sobre [auditoria de base de dados do Azure SQL](sql-database-auditing.md)
- Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
- Para obter mais informações sobre os preços, consulte o [base de dados SQL página de preços](https://azure.microsoft.com/pricing/details/sql-database/)  
