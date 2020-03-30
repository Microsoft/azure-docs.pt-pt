---
title: Planeamento para eventos de manutenção azure
description: Saiba como se preparar para eventos de manutenção planeados para a sua Base de Dados Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: ba882176fbe17f7b74c786f421dde8fadd58d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821307"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planear eventos de manutenção do Azure na Base de Dados SQL do Azure

Saiba como se preparar para eventos de manutenção planeados na sua base de dados Azure SQL.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planeado

Para cada base de dados, o Azure SQL DB mantém um quórum de réplicas de bases de dados onde uma réplica é a principal. Em todos os momentos, uma réplica primária deve ser feita online e pelo menos uma réplica secundária deve ser saudável. Durante a manutenção planeada, os membros do quórum da base de dados ficarão offline um de cada vez, com a intenção de que haja uma réplica primária que responda e pelo menos uma réplica secundária online para garantir que nenhum tempo de inatividade do cliente. Quando a réplica primária precisa de ser desligada, um processo de reconfiguração/falha ocorrerá no qual uma réplica secundária se tornará a nova primária.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>O que esperar durante um evento de manutenção planeado

Reconfigurações/falhas geralmente completam-se em 30 segundos – a média é de 8 segundos. Se já estiver ligado, a sua aplicação deve voltar a ligar-se à nova réplica primária da sua base de dados. Se uma nova ligação for tentada enquanto a base de dados estiver a passar por uma reconfiguração antes da nova réplica primária estar on-line, obtém-se erro 40613 (Database Inavailable): "Database '{databasename}' no servidor '{servername}' não está disponível atualmente. Por favor, tente novamente a ligação mais tarde." Se a sua base de dados tiver uma consulta de longa duração, esta consulta será interrompida durante uma reconfiguração e terá de ser reiniciada.

## <a name="retry-logic"></a>Lógica de Retry

Qualquer aplicação de produção de clientes que se conectem a um serviço de base de dados em nuvem deve implementar uma lógica robusta [de retry](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)de ligação. Isto ajudará a mitigar estas situações e, em geral, tornará os erros transparentes para o utilizador final.

## <a name="frequency"></a>Frequência

Em média, 1,7 eventos de manutenção planeados ocorrem todos os meses.

## <a name="resource-health"></a>Estado de Funcionamento de Recursos

Se a sua base de dados SQL estiver a registar falhas de login, verifique a janela [de Saúde](../service-health/resource-health-overview.md#get-started) de Recursos no [portal Azure](https://portal.azure.com) para obter o estado atual. A secção história da saúde contém a razão de inatividade para cada evento (quando disponível).


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Saúde dos Recursos](sql-database-resource-health.md) para a Base de Dados SQL
- Para mais informações sobre a lógica de retry, consulte [a lógica de retry para erros transitórios](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
