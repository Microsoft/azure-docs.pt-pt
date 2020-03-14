---
title: Use a Azure Resource Health para monitorizar a saúde da base de dados
description: Utilize o Azure Resource Health para monitorizar o estado de funcionamento da base de dados SQL, ajuda a diagnosticar e obter suporte quando um problema do Azure afeta os seus recursos do SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
ms.date: 02/26/2019
ms.openlocfilehash: 9e19e904b47d69444b491dd88ffe49ff812aafc3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208877"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Utilizar o Resource Health para resolver problemas de conectividade para a base de dados do Azure SQL

## <a name="overview"></a>Descrição geral

[A Saúde de Recursos](../service-health/resource-health-overview.md#get-started) para a Base de Dados SQL ajuda-o a diagnosticar e a obter suporte quando um problema do Azure afeta os seus recursos SQL. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

![Descrição geral](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificações de estado de funcionamento

Estado de funcionamento do recurso determina o estado de funcionamento do seu recurso SQL ao examinar o êxito e falha de inícios de sessão para o recurso. Atualmente, o estado de funcionamento de recursos para o seu recurso de BD SQL examina apenas falhas de início de sessão devido a erro de sistema e não a erro de utilizador. O estado de funcionamento do recurso é atualizado a cada 1 a 2 minutos.

## <a name="health-states"></a>Estados de funcionamento

### <a name="available"></a>Disponível

Um estado de **Disponível** significa que a Saúde dos Recursos não detetou falhas de login devido a erros do sistema no seu recurso SQL.

![Disponível](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

O estado **Degradado** significa que o Resource Health detetou maioritariamente inícios de sessão com êxito, mas também algumas falhas. Esses são erros de início de sessão transitórias mais prováveis. Para reduzir o impacto dos problemas de ligação causados por erros transitórios de login, por favor implemente a lógica de [retry](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) no seu código.

![Degradado](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Indisponível

Um estado de **Indisponíveis** significa que a Saúde dos Recursos detetou falhas de login consistentes no seu recurso SQL. Se o seu recurso permanecer neste estado durante um período prolongado de tempo, contacte o suporte.

![Indisponível](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Desconhecido

O estado de saúde de **Unknown** indica que a Saúde de Recursos não recebe informações sobre este recurso há mais de 10 minutos. Embora este estado não é uma indicação definitiva do Estado do recurso, é um ponto de dados importantes no processo de resolução de problemas. Se o recurso está em execução conforme esperado, o estado do recurso mudará para disponível após alguns minutos. Se estiver a ter problemas com o recurso, o estado de funcionamento desconhecido pode sugerir que um evento na plataforma está a afetar o recurso.

![Desconhecido](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informações do histórico

Pode acessar até 14 dias do histórico de estado de funcionamento na secção de histórico de estado de funcionamento do Estado de funcionamento do recurso. A secção também irá conter o motivo de tempo de inatividade (quando disponível) para os tempos de inatividade do comunicado pelo Estado de funcionamento do recurso. Atualmente, o Azure mostra o tempo de inatividade do seu recurso de base de dados SQL com uma granularidade de dois minutos. O tempo de inatividade atual é provavelmente menos do que um minuto – média é 8s.

### <a name="downtime-reasons"></a>Motivos de tempo de inatividade

Quando a base de dados SQL sofrer um período de indisponibilidade, a análise é executada para determinar um motivo. Se estiver disponível, é comunicado o motivo de tempo de inatividade na secção de histórico de estado de funcionamento do Estado de funcionamento do recurso. Geralmente, os motivos para os tempos de inatividade são publicados 30 minutos após os eventos.

#### <a name="planned-maintenance"></a>Manutenção planeada

A infraestrutura do Azure efetua periodicamente a manutenção planeada – atualização dos componentes de hardware ou software no Centro de dados. Enquanto a base de dados entra em manutenção, o SQL pode encerrar algumas ligações existentes e recusar novos. As falhas de login experimentadas durante a manutenção planeada são tipicamente transitórias e a lógica de [retry](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se continuar a ocorrer erros de início de sessão, contacte o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

Reconfigurações são consideradas condições transitórias e espera-se de tempos em tempos. Esses eventos podem ser adicionados a falhas de software/hardware ou de balanceamento de carga. Qualquer aplicação de produção de clientes que se conectem a uma base de dados em nuvem deve implementar uma lógica robusta de [retry](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)de ligação, uma vez que ajudaria a mitigar estas situações e geralmente tornaria os erros transparentes para o utilizador final.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre a lógica de [retry para erros transitórios](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Resolver problemas, diagnosticar e evitar erros de ligação do SQL)
- Saiba mais sobre [configurar alertas](../service-health/resource-health-alert-arm-template-guide.md) de saúde de recursos
- Obtenha uma visão geral da [Saúde](../service-health/resource-health-overview.md) dos Recursos
- [FaQ de saúde de recursos](../service-health/resource-health-faq.md)
