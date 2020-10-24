---
title: Registos - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como aceder aos registos de bases de dados para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: fac0db7f8c94113ff87d1c0a3091e4c0cbd5ed2b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489851"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Registos na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Os registos PostgreSQL estão disponíveis em todos os nós de um grupo de servidores De Hiperescala (Citus). Pode enviar registos para um servidor de armazenamento ou para um serviço de análise. Os registos podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e desempenho subóptima.

## <a name="accessing-logs"></a>Aceder a registos

Para aceder aos registos PostgreSQL para um coordenador de Hiperescala (Citus) ou nó do trabalhador, abra o nó no portal Azure:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lista de nosdes":::

Para o nó selecionado, abra **as definições de Diagnóstico**e clique + Adicione a **definição de diagnóstico**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="lista de nosdes":::

Escolha um nome para as novas definições de diagnóstico e verifique a caixa **PostgreSQLLogs.**  Escolha qual destino(s) deve receber os registos.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="lista de nosdes":::

## <a name="next-steps"></a>Passos seguintes

- [Começar com consultas de analítica de log](../azure-monitor/log-query/get-started-portal.md)
- Saiba mais sobre [os centros de eventos da Azure](../event-hubs/event-hubs-about.md)