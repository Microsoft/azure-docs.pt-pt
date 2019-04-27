---
title: Planear a manutenção do Azure eventos - SQL Database do Azure | Documentos da Microsoft
description: Aprenda a preparar para eventos de manutenção planeada para a base de dados do SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: carlrab
manager: craigg
ms.date: 01/30/2019
ms.openlocfilehash: 928338a911efae051df7164239dbd19f9317338a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584612"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planejamento para eventos de manutenção do Azure na base de dados do Azure SQL

Aprenda a preparar para eventos de manutenção planeada na sua base de dados SQL do Azure.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planeada

Para cada base de dados, a BD SQL do Azure mantém um quórum de réplicas de base de dados em que uma réplica é o principal. AT sempre uma réplica primária tem de ser de manutenção online e, pelo menos uma réplica secundária tem de ser bom estado de funcionamento. Durante a manutenção planeada, os membros do quórum da base de dados passará um offline de cada vez, com a intenção de que existe uma responder a réplica primária e pelo menos uma réplica secundária online para garantir que nenhum tempo de inatividade do cliente. Quando a réplica primária tem de ser colocados offline, ocorrerá um processo de reconfiguração/ativação pós-falha no qual uma réplica secundária irá tornar-se a nova principal.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>O que esperar durante um evento de manutenção planeada

Reconfigurações/ativações pós-falha geralmente concluída no prazo de 30 segundos – a média é de 8 segundos. Se ainda estiver ligado, seu aplicativo deve voltar a ligar a nova réplica primária a cópia de bom estado de funcionamento da base de dados. Se uma nova tentativa de conexão, enquanto a base de dados está numa reconfiguração antes da nova réplica primária está online, receberá o erro 40613 (base de dados indisponível): "Base de dados"{databasename}"no servidor"{servername}"não está atualmente disponível. Tente novamente a ligação mais tarde. ". Se a sua base de dados tiver uma consulta de execução longa, esta consulta será interrompida durante uma reconfiguração e terá de ser reiniciado.

## <a name="retry-logic"></a>Lógica de repetição

Qualquer aplicativo de produção do cliente que se liga a um serviço de base de dados em nuvem deve implementar uma ligação robusta [lógica de repetição](sql-database-connectivity-issues.md#retry-logic-for-transient-errors). Isso ajudará a reduzir tais situações e deve geralmente o tornam os erros transparente para o utilizador final.

## <a name="frequency"></a>Frequência

Em média, a versão 1.7 eventos de manutenção planeada ocorrerem por mês.

## <a name="resource-health"></a>Estado de Funcionamento de Recursos

Verifique se a base de dados SQL está com falhas de início de sessão, o [Resource Health](../service-health/resource-health-overview.md#getting-started) janela no [portal do Azure](https://portal.azure.com) o status atual. A secção de histórico de estado de funcionamento contém o motivo de período de indisponibilidade para cada evento (quando disponível).


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [Resource Health](sql-database-resource-health.md) para base de dados SQL
- Para obter mais informações sobre a lógica de repetição, consulte [repetir a lógica para erros transitórios](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
