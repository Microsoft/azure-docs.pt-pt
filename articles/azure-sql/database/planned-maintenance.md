---
title: Plano para eventos de manutenção do Azure
description: Saiba como preparar eventos de manutenção planeados na Base de Dados Azure SQL e na Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
ms.date: 01/30/2019
ms.openlocfilehash: f0bda1f4b9894b1ea5a68f44a728f715676d500e
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661151"
---
# <a name="plan-for-azure-maintenance-events-in-azure-sql-database-and-azure-sql-managed-instance"></a>Plano para eventos de manutenção Azure em Azure SQL Database e Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Saiba como se preparar para eventos de manutenção planeados na sua base de dados na Base de Dados Azure SQL e na Azure SQL Managed Instance.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planeado?

Para cada base de dados, a Base de Dados Azure SQL e a Azure SQL Managed Instance mantêm um quórum de réplicas de bases de dados onde uma réplica é a principal. A todo o momento, uma réplica primária deve estar a ser a manutenção online, e pelo menos uma réplica secundária deve ser saudável. Durante a manutenção planeada, os membros do quórum da base de dados ficarão offline um de cada vez, com a intenção de que haja uma réplica primária de resposta e pelo menos uma réplica secundária on-line para garantir que nenhum cliente está em tempo de inatividade. Quando a réplica primária precisa de ser desligada, ocorrerá um processo de reconfiguração/failover no qual uma réplica secundária se tornará a nova primária.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>O que esperar durante um evento de manutenção planeado

As reconfigurações/falhas geralmente terminam dentro de 30 segundos. A média é de 8 segundos. Se já estiver ligado, a sua aplicação deve voltar a ligar-se à nova réplica primária da sua base de dados. Se uma nova ligação for tentada enquanto a base de dados está a sofrer uma reconfiguração antes da nova réplica primária estar on-line, obtém-se o erro 40613 (Base de dados Indisponível): "Base de dados '{databasename}' no servidor '{servername}' não está atualmente disponível. Tente voltar a ligar mais tarde." Se a sua base de dados tiver uma consulta de longa duração, esta consulta será interrompida durante uma reconfiguração e terá de ser reiniciada.

## <a name="retry-logic"></a>Repetir a lógica

Qualquer aplicação de produção de clientes que se conecte a um serviço de base de dados em nuvem deve implementar uma lógica robusta [de relembramento de](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors)conexão . Isto ajudará a mitigar estas situações e deverá, em geral, tornar os erros transparentes para o utilizador final.

## <a name="frequency"></a>Frequência

Em média, ocorrem 1,7 eventos de manutenção planeados todos os meses.

## <a name="resource-health"></a>Estado de funcionamento de recursos

Se a sua base de dados estiver a registar falhas de login, verifique a janela [de Saúde](../../service-health/resource-health-overview.md#get-started) de Recursos no [portal Azure](https://portal.azure.com) para obter o estado atual. A secção De Histórico de Saúde contém a razão de inatividade para cada evento (quando disponível).

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a Saúde dos Recursos](resource-health-to-troubleshoot-connectivity.md) para Azure SQL Database e Azure SQL Managed Instance.
- Para obter mais informações sobre a lógica de relemissão, consulte [a lógica retry para erros transitórios](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors).
