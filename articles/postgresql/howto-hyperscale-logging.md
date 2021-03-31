---
title: Registos - Hiperescala (Citus) - Base de Dados Azure para PostgreSQL
description: Como aceder aos registos de bases de dados para a Base de Dados Azure para PostgreSQL - Hiperescala (Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 7/13/2020
ms.openlocfilehash: ca3cc2873fbc6db72b10c80daecddf1471e30ff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577062"
---
# <a name="logs-in-azure-database-for-postgresql---hyperscale-citus"></a>Registos na Base de Dados Azure para PostgreSQL - Hiperescala (Citus)

Os registos PostgreSQL estão disponíveis em todos os nós de um grupo de servidores De Hiperescala (Citus). Pode enviar registos para um servidor de armazenamento ou para um serviço de análise. Os registos podem ser utilizados para identificar, resolver problemas e reparar erros de configuração e desempenho subóptima.

## <a name="accessing-logs"></a>Aceder a registos

Para aceder aos registos PostgreSQL para um coordenador de Hiperescala (Citus) ou nó do trabalhador, abra o nó no portal Azure:

:::image type="content" source="media/howto-hyperscale-logging/choose-node.png" alt-text="lista de nosdes":::

Para o nó selecionado, abra **as definições de Diagnóstico** e clique + Adicione a **definição de diagnóstico**.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-settings.png" alt-text="Adicione botão de definições de diagnóstico":::

Escolha um nome para as novas definições de diagnóstico e verifique a caixa **PostgreSQLLogs.**  Escolha qual destino(s) deve receber os registos.

:::image type="content" source="media/howto-hyperscale-logging/diagnostic-create-setting.png" alt-text="Escolha registos PostgreSQL":::

## <a name="next-steps"></a>Passos seguintes

- [Começar com consultas de analítica de log](../azure-monitor/logs/log-analytics-tutorial.md)
- Saiba mais sobre [os centros de eventos da Azure](../event-hubs/event-hubs-about.md)