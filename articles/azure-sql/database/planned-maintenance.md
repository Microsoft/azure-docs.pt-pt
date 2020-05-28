---
title: Planeamento para eventos de manutenção azure
description: Saiba como se preparar para eventos de manutenção planeados na Base de Dados Azure SQL e na Instância Gerida Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: 2f5f69a5f145ae8bbf23aa1e5dbca07b30db0e21
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84042695"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Planeamento de eventos de manutenção Azure em Base de Dados Azure SQL e Instância Gerida Azure SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Saiba como se preparar para eventos de manutenção planeados na sua base de dados em Azure SQL Database e Azure SQL Managed Instance.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planeado

Para cada base de dados, a Base de Dados Azure SQL e a Azure SQL Managed Instance mantêm um quórum de réplicas de bases de dados onde uma réplica é a principal. Em todos os momentos, uma réplica primária deve ser feita online e pelo menos uma réplica secundária deve ser saudável. Durante a manutenção planeada, os membros do quórum da base de dados ficarão offline um de cada vez, com a intenção de que haja uma réplica primária que responda e pelo menos uma réplica secundária online para garantir que nenhum tempo de inatividade do cliente. Quando a réplica primária precisa de ser desligada, um processo de reconfiguração/falha ocorrerá no qual uma réplica secundária se tornará a nova primária.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>O que esperar durante um evento de manutenção planeado

Reconfigurações/falhas geralmente completam-se em 30 segundos – a média é de 8 segundos. Se já estiver ligado, a sua aplicação deve voltar a ligar-se à nova réplica primária da sua base de dados. Se uma nova ligação for tentada enquanto a base de dados estiver a passar por uma reconfiguração antes da nova réplica primária estar on-line, obtém-se erro 40613 (Database Inavailable): "Database '{databasename}' no servidor '{servername}' não está disponível atualmente. Por favor, tente novamente a ligação mais tarde." Se a sua base de dados tiver uma consulta de longa duração, esta consulta será interrompida durante uma reconfiguração e terá de ser reiniciada.

## <a name="retry-logic"></a>Lógica de Retry

Qualquer aplicação de produção de clientes que se conectem a um serviço de base de dados em nuvem deve implementar uma lógica robusta [de retry](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)de ligação. Isto ajudará a mitigar estas situações e, em geral, tornará os erros transparentes para o utilizador final.

## <a name="frequency"></a>Frequência

Em média, 1,7 eventos de manutenção planeados ocorrem todos os meses.

## <a name="resource-health"></a>Estado de Funcionamento de Recursos

Se a sua base de dados estiver a sofrer falhas de login, verifique a janela [de saúde](../../service-health/resource-health-overview.md#get-started) de recursos no [portal Azure](https://portal.azure.com) para obter o estado atual. A secção história da saúde contém a razão de inatividade para cada evento (quando disponível).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [a Saúde](resource-health-to-troubleshoot-connectivity.md) de Recursos para a Base de Dados Azure SQL e a Instância Gerida Azure SQL
- Para obter mais informações sobre a lógica de retry, consulte [a lógica de retry para erros transitórios](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
