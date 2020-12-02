---
title: Como monitorizar piscinas SQL no Synapse Studio
description: Aprenda a monitorizar as suas piscinas SQL utilizando o Synapse Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467422"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Use o Synapse Studio para monitorizar as suas piscinas SQL

Com o Synapse Studio, pode executar scripts SQL nas piscinas SQL no seu espaço de trabalho.

Este artigo explica como monitorizar as suas piscinas SQL, permitindo-lhe manter um olho no estado e atividade das suas piscinas.

## <a name="access-sql-pools-list"></a>Acesso lista de piscinas SQL

Para ver a lista de piscinas SQL no seu espaço de trabalho, abra primeiro [o Synapse Studio](https://web.azuresynapse.net/) e selecione o seu espaço de trabalho.

![Faça login no espaço de trabalho](./media/common/login-workspace.png)

Depois de abrir o seu espaço de trabalho, selecione a secção **Monitor** à esquerda.

![Selecione Monitor hub](./media/common/left-nav.png)

Selecione **piscinas SQL** para ver a lista de piscinas SQL.

 ![Selecione piscinas SQL](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtrar as suas piscinas SQL

Pode filtrar a lista de piscinas SQL para as que lhe interessam. Os filtros na parte superior do ecrã permitem especificar um campo no qual gostaria de filtrar.

Por exemplo, pode filtrar a vista para ver apenas as piscinas SQL que contêm o nome "registos de vendas":

![Filtro de amostra](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Ver detalhes sobre uma piscina SQL específica

Para ver os detalhes sobre uma das suas piscinas SQL, selecione a piscina SQL para ver os detalhes.

![Detalhes da piscina SQL](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a monitorização do gasoduto, consulte o gasoduto Monitor que funciona no artigo [do Synapse Studio.](how-to-monitor-pipeline-runs.md) 

Para obter mais informações sobre a monitorização dos pedidos do SQL, consulte os pedidos do Monitor SQL no artigo [do Synapse Studio.](how-to-monitor-sql-requests.md)