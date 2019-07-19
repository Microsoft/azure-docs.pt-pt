---
title: Planejando eventos de manutenção do Azure – banco de dados SQL do Azure | Microsoft Docs
description: Saiba como se preparar para eventos de manutenção planejada para o banco de dados SQL do Azure.
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
ms.openlocfilehash: 235d29c876616948516bbe4309ddd630bc3f6dca
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67852594"
---
# <a name="planning-for-azure-maintenance-events-in-azure-sql-database"></a>Planejando eventos de manutenção do Azure no banco de dados SQL do Azure

Saiba como preparar-se para eventos de manutenção planejada no seu banco de dados SQL do Azure.

## <a name="what-is-a-planned-maintenance-event"></a>O que é um evento de manutenção planejada

Para cada banco de dados, o BD SQL do Azure mantém um quorum de réplicas de banco de dados em que uma réplica é a primária. Em todos os momentos, uma réplica primária deve estar em manutenção online e pelo menos uma réplica secundária deve estar íntegra. Durante a manutenção planejada, os membros do quorum de banco de dados ficarão offline um de cada vez, com a intenção de que haja uma réplica primária de resposta e pelo menos uma réplica secundária online para garantir que não haja tempo de inatividade do cliente. Quando a réplica primária precisar ser colocada offline, ocorrerá um processo de reconfiguração/failover no qual uma réplica secundária se tornará a nova primária.  

## <a name="what-to-expect-during-a-planned-maintenance-event"></a>O que esperar durante um evento de manutenção planejada

Reconfigurações/failovers geralmente são concluídos em 30 segundos – a média é de 8 segundos. Se já estiver conectado, seu aplicativo deverá se reconectar à cópia íntegra nova réplica primária do banco de dados. Se uma nova conexão for tentada enquanto o banco de dados estiver passando por uma reconfiguração antes que a nova réplica primária esteja online, você receberá o erro 40613 (banco de dados indisponível): "O banco de dados ' {DatabaseName} ' no servidor ' {servername} ' não está disponível no momento. Repita a conexão mais tarde. ". Se o banco de dados tiver uma consulta de execução longa, essa consulta será interrompida durante uma reconfiguração e precisará ser reiniciada.

## <a name="retry-logic"></a>Lógica de repetição

Qualquer aplicativo de produção de cliente que se conecta a um serviço de banco de dados de nuvem deve implementar uma [lógica de repetição](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)de conexão robusta. Isso ajudará a mitigar essas situações e, em geral, deve tornar os erros transparentes para o usuário final.

## <a name="frequency"></a>Frequência

Em média, 1,7 eventos de manutenção planejada ocorrem a cada mês.

## <a name="resource-health"></a>Estado de Funcionamento de Recursos

Se o banco de dados SQL estiver apresentando falhas de logon, verifique a janela [Resource Health](../service-health/resource-health-overview.md#get-started) no [portal do Azure](https://portal.azure.com) para obter o status atual. A seção Histórico de integridade contém o motivo do tempo de inatividade para cada evento (quando disponível).


## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre o [Resource Health](sql-database-resource-health.md) para o banco de dados SQL
- Para obter mais informações sobre a lógica de repetição, consulte [lógica de repetição para erros transitórios](sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
