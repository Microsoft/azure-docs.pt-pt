---
title: Usar Azure Resource Health para monitorar a integridade do banco de dados
description: Use Azure Resource Health para monitorar a integridade do banco de dados SQL, ajuda você a diagnosticar e obter suporte quando um problema do Azure Impacta os recursos do SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 1cf14c9e133b7e6e3e0b5219eb9e16bd3a0178dc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821163"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Usar Resource Health para solucionar problemas de conectividade do banco de dados SQL do Azure

## <a name="overview"></a>Descrição geral

O [Resource Health](../service-health/resource-health-overview.md#get-started) para o banco de dados SQL ajuda você a diagnosticar e obter suporte quando um problema do Azure Impacta os recursos do SQL. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

![Descrição geral](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificações de integridade

Resource Health determina a integridade do recurso SQL examinando o êxito e a falha dos logons no recurso. Atualmente, Resource Health para o recurso de BD SQL apenas examina as falhas de logon devido a um erro do sistema e não a um erro do usuário. O status de Resource Health é atualizado a cada 1-2 minutos.

## <a name="health-states"></a>Estados de integridade

### <a name="available"></a>Disponível

Um status de **disponível** significa que Resource Health não detectou falhas de logons devido a erros do sistema em seu recurso SQL.

![Disponível](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

O estado **Degradado** significa que o Resource Health detetou maioritariamente inícios de sessão com êxito, mas também algumas falhas. Esses são provavelmente erros de logon transitórios. Para reduzir o impacto dos problemas de conexão causados por erros de logon transitórios, implemente a [lógica de repetição](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) em seu código.

![Degradado](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Indisponível

Um status **indisponível** significa que o Resource Health detectou falhas de logon consistentes em seu recurso SQL. Se o recurso permanecer nesse estado por um longo período de tempo, entre em contato com o suporte.

![Indisponível](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Desconhecido

O status de integridade de **desconhecido** indica que Resource Health não recebeu informações sobre esse recurso por mais de 10 minutos. Embora esse status não seja uma indicação definitiva do estado do recurso, ele é um ponto de dados importante no processo de solução de problemas. Se o recurso estiver sendo executado conforme o esperado, o status do recurso será alterado para disponível após alguns minutos. Se você estiver tendo problemas com o recurso, o status de integridade desconhecido poderá sugerir que um evento na plataforma esteja afetando o recurso.

![Desconhecido](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informações históricas

Você pode acessar até 14 dias de histórico de integridade na seção Histórico de integridade de Resource Health. A seção também conterá o motivo do tempo de inatividade (quando disponível) para os tempos de inatividade relatados pelo Resource Health. Atualmente, o Azure mostra o período de indisponibilidade do recurso da base de dados SQL com uma granularidade de dois minutos. O período de indisponibilidade real é provavelmente inferior a um minuto; a média são oito segundos.

### <a name="downtime-reasons"></a>Motivos de tempo de inatividade

Quando o banco de dados SQL experimenta tempo de inatividade, a análise é executada para determinar um motivo. Quando disponível, o motivo do tempo de inatividade é relatado na seção Histórico de integridade de Resource Health. Geralmente, os motivos para os tempos de inatividade são publicados 30 minutos após os eventos.

#### <a name="planned-maintenance"></a>Manutenção planeada

A infraestrutura do Azure realiza periodicamente a manutenção planejada – atualização de componentes de hardware ou software no datacenter. Enquanto o banco de dados passa por manutenção, o SQL pode encerrar algumas conexões existentes e recusar novas. As falhas de logon experimentadas durante a manutenção planejada são normalmente transitórias e a [lógica de repetição](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se você continuar a ter erros de logon, entre em contato com o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

As reconfigurações são consideradas condições transitórias e são esperadas de tempos em tempos. Esses eventos podem ser disparados pelo balanceamento de carga ou por falhas de software/hardware. Qualquer aplicativo de produção de cliente que se conecta a um banco de dados de nuvem deve implementar uma [lógica de repetição](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)de conexão robusta, pois ajudaria a mitigar essas situações e, em geral, deve tornar os erros transparentes para o usuário final.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a lógica de repetição para erros transitórios](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Resolver problemas, diagnosticar e evitar erros de ligação do SQL)
- Saiba mais sobre como [configurar alertas de Resource Health](../service-health/resource-health-alert-arm-template-guide.md)
- Obtenha uma visão geral do [Resource Health](../service-health/resource-health-overview.md)
- [Perguntas frequentes Resource Health](../service-health/resource-health-faq.md)
