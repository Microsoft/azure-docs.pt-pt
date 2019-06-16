---
title: Como configurar a retenção no seu ambiente do Azure Time Series Insights | Documentos da Microsoft
description: Este artigo descreve como configurar a retenção no seu ambiente do Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: f5f34983d1818679450249aa40bbb325e743cc57
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239009"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurar a retenção no Time Series Insights

Este artigo descreve como configurar **período de retenção de dados** e **limite de armazenamento excedido comportamento** no Azure Time Series Insights.

## <a name="summary"></a>Resumo

Cada ambiente do Time Series Insights (TSI) tem uma definição para configurar **período de retenção de dados**. Abrange o valor de 1 para 400 dias. Os dados são eliminados com base no ambiente armazenamento capacidade ou de retenção de duração (1-400), o que ocorrer primeiro.

Cada ambiente de TSI tem uma configuração adicional **limite de armazenamento excedido comportamento**. Esta definição controla o comportamento de entrada e de remoção quando for atingida a capacidade máxima de um ambiente. Existem dois comportamentos à sua escolha:

- **Remover dados antigos** (predefinição)
- **Entrada de colocação em pausa**

Para obter informações detalhadas compreender melhor estas definições, reveja [retenção de compreensão no Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar a retenção de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o seu ambiente do Time Series Insights existente. Selecione **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

1. Sob o **configurações** cabeçalho, selecione **configurar**.

1. Selecione o **período de retenção de dados** para configurar a retenção usando a barra de controlo de deslize ou escreva um número na caixa de texto.

1. Tenha em atenção a **capacidade** definição, uma vez que esta configuração afeta a quantidade máxima de eventos de dados e a capacidade de armazenamento total para armazenar dados.

1. Ativar/desativar a **limite de armazenamento excedido comportamento** definição. Selecione **remover dados antigos** ou **colocar em pausa entrada** comportamento.

1. Selecione **guardar** para configurar as alterações.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações, consulte [retenção de compreensão no Time Series Insights](time-series-insights-concepts-retention.md).