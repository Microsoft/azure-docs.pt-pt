---
title: Utilizar o Azure Resource Health para monitorizar o estado de funcionamento da base de dados SQL | Documentos da Microsoft
description: Utilize o Azure Resource Health para monitorizar o estado de funcionamento da base de dados SQL, ajuda a diagnosticar e obter suporte quando um problema do Azure afeta os seus recursos do SQL.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aamalvea
ms.author: aamalvea
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 02/26/2019
ms.openlocfilehash: c3b9fecd3ad404385732e55a9cf3aa65a6e388b8
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56889380"
---
# <a name="use-resource-health-to-troubleshoot-connectivity-for-azure-sql-database"></a>Utilizar o Resource Health para resolver problemas de conectividade para a base de dados do Azure SQL

## <a name="overview"></a>Descrição geral

[Estado de funcionamento do recurso](../service-health/resource-health-overview.md#getting-started) para a base de dados SQL ajuda-o a diagnosticar e obter suporte quando um problema do Azure afeta os seus recursos do SQL. Este serviço informa-o do estado de funcionamento atual e antigo dos seus recursos e ajuda-o a mitigar problemas. O estado de funcionamento dos recursos fornece suporte técnico quando precisa de ajuda para resolver problemas relacionados com o serviço do Azure.

![Descrição geral](./media/sql-database-resource-health/sql-resource-health-overview.jpg)

## <a name="health-checks"></a>Verificações de estado de funcionamento

Estado de funcionamento do recurso determina o estado de funcionamento do seu recurso SQL ao examinar o êxito e falha de inícios de sessão para o recurso. Atualmente, o estado de funcionamento de recursos para o seu recurso de BD SQL examina apenas falhas de início de sessão devido a erro de sistema e não a erro de utilizador. O estado de funcionamento do recurso é atualizado a cada 1 a 2 minutos.

## <a name="health-states"></a>Estados de funcionamento

### <a name="available"></a>Disponível

Estado **disponível** significa que o estado de funcionamento do recurso não detetou falhas de inícios de sessão devido a erros de sistema no seu recurso SQL.

![Disponível](./media/sql-database-resource-health/sql-resource-health-available.jpg)

### <a name="degraded"></a>Degradado

O estado **Degradado** significa que o Resource Health detetou maioritariamente inícios de sessão com êxito, mas também algumas falhas. Esses são erros de início de sessão transitórias mais prováveis. Para reduzir o impacto dos problemas de ligação causados por erros de início de sessão transitório, implemente [lógica de repetição](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) em seu código.

![Degradado](./media/sql-database-resource-health/sql-resource-health-degraded.jpg)

### <a name="unavailable"></a>Não disponível

Estado **indisponível** significa que o estado de funcionamento do recurso detetou falhas de início de sessão consistente para o recurso do SQL. Se o seu recurso permanecer neste estado durante um período prolongado de tempo, contacte o suporte.

![Não disponível](./media/sql-database-resource-health/sql-resource-health-unavailable.jpg)

### <a name="unknown"></a>Desconhecidos

O estado de funcionamento das **desconhecido** indica que o estado de funcionamento do recurso não recebe informações sobre este recurso para mais de 10 minutos. Embora este estado não é uma indicação definitiva do Estado do recurso, é um ponto de dados importantes no processo de resolução de problemas. Se o recurso está em execução conforme esperado, o estado do recurso mudará para disponível após alguns minutos. Se estiver a ter problemas com o recurso, o estado de funcionamento desconhecido pode sugerir que um evento na plataforma está a afetar o recurso.

![Desconhecidos](./media/sql-database-resource-health/sql-resource-health-unknown.jpg)

## <a name="historical-information"></a>Informações do histórico

Pode acessar até 14 dias do histórico de estado de funcionamento na secção de histórico de estado de funcionamento do Estado de funcionamento do recurso. A secção também irá conter o motivo de tempo de inatividade (quando disponível) para os tempos de inatividade do comunicado pelo Estado de funcionamento do recurso. Atualmente, o Azure mostra o tempo de inatividade do seu recurso de base de dados SQL com uma granularidade de dois minutos. O tempo de inatividade atual é provavelmente menos do que um minuto – média é 8s.

### <a name="downtime-reasons"></a>Motivos de tempo de inatividade

Quando a base de dados SQL sofrer um período de indisponibilidade, a análise é executada para determinar um motivo. Se estiver disponível, é comunicado o motivo de tempo de inatividade na secção de histórico de estado de funcionamento do Estado de funcionamento do recurso. Geralmente, os motivos para os tempos de inatividade são publicados 30 minutos após os eventos.

#### <a name="planned-maintenance"></a>Manutenção planeada

A infraestrutura do Azure efetua periodicamente a manutenção planeada – atualização dos componentes de hardware ou software no Centro de dados. Enquanto a base de dados entra em manutenção, o SQL pode encerrar algumas ligações existentes e recusar novos. As falhas de início de sessão durante a manutenção planeada estão costumam ser transitórias e [lógica de repetição](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors) ajuda a reduzir o impacto. Se continuar a ocorrer erros de início de sessão, contacte o suporte.

#### <a name="reconfiguration"></a>Reconfiguração

Reconfigurações são consideradas condições transitórias e espera-se de tempos em tempos. Esses eventos podem ser adicionados a falhas de software/hardware ou de balanceamento de carga. Qualquer aplicativo de produção do cliente que se liga a uma base de dados na cloud deve implementar uma ligação robusta [lógica de repetição](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors), ele poderia ajudar a reduzir tais situações e deve geralmente fazem os erros transparente para o utilizador final.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [repetir a lógica para erros transitórios](./sql-database-connectivity-issues.md#retry-logic-for-transient-errors)
- [Troubleshoot, diagnose, and prevent SQL connection errors](./sql-database-connectivity-issues.md) (Resolver problemas, diagnosticar e evitar erros de ligação do SQL)
- Saiba mais sobre [configurar alertas de estado de funcionamento do recurso](../service-health/resource-health-alert-arm-template-guide.md)
- Obtenha uma visão geral de [Resource Health](../service-health/resource-health-overview.md)
- [FAQ do Estado de funcionamento do recurso](../service-health/resource-health-faq.md)
