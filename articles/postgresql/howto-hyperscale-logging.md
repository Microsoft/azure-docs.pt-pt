---
title: Registos - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como aceder aos registos de bases de dados para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: 4ca64a7793d229e7910fb122fb33996b1ac0c4fe
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87099046"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Registos na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Os registos PostgreSQL estão disponíveis em todos os nós de um grupo de servidores De Hiperescala (Citus). Pode enviar registos para um servidor de armazenamento ou para um serviço de análise. Os registos podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e desempenho subóptima.

## <a name="accessing-logs"></a>Aceder a registos

Para aceder aos registos PostgreSQL para um coordenador de Hiperescala (Citus) ou nó do trabalhador, abra o nó no portal Azure:

![lista de nosdes](media/howto-hyperscale-logging/choose-node.png)

Para o nó selecionado, abra **as definições de Diagnóstico**e clique + Adicione a **definição de diagnóstico**.

![Adicione botão de definições de diagnóstico](media/howto-hyperscale-logging/diagnostic-settings.png)

Escolha um nome para as novas definições de diagnóstico e verifique a caixa **PostgreSQLLogs.**  Escolha qual destino(s) deve receber os registos.

![Escolha registos PostgreSQL](media/howto-hyperscale-logging/diagnostic-create-setting.png)

## <a name="next-steps"></a>Passos seguintes

- [Começar com consultas de analítica de log](/azure/azure-monitor/log-query/get-started-portal)
- Saiba mais sobre [os centros de eventos da Azure](/azure/event-hubs/event-hubs-about)
