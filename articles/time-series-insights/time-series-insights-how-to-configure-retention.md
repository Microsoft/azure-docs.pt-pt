---
title: Como configurar a retenção em seu ambiente-Azure Time Series Insights | Microsoft Docs
description: Saiba como configurar a retenção em seu ambiente de Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: aa905d1686939346827d07419a62fd6f299387ec
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012600"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurando a retenção no Time Series Insights

Este artigo descreve como configurar o **tempo de retenção de dados** e o **comportamento de limite de armazenamento excedido** no Azure Time Series insights.

## <a name="summary"></a>Resumo

Cada ambiente de Azure Time Series Insights tem uma configuração para configurar o **tempo de retenção de dados**. O valor se estende de 1 a 400 dias. Os dados são excluídos com base na capacidade de armazenamento do ambiente ou na duração da retenção (1-400), o que vier primeiro.

Cada ambiente de Time Series Insights tem um comportamento de limite de armazenamento de configuração adicional **excedido**. Essa configuração controla o comportamento de entrada e limpeza quando a capacidade máxima de um ambiente é atingida. Há dois comportamentos para escolher:

- **Limpar dados antigos** (padrão)
- **Pausar entrada**

Para obter informações detalhadas para entender melhor essas configurações, consulte [noções básicas sobre retenção em time Series insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar a retenção de dados

1. Iniciar sessão no [portal do Azure](https://portal.azure.com).

1. Localize o seu ambiente do Time Series Insights existente. Selecione **todos os recursos** no menu no lado esquerdo do portal do Azure. Selecione o seu ambiente do Time Series Insights.

1. No título **configurações** , selecione **Configurar**.

    [![selecionar configurações e, em seguida, configurar](media/data-retention/1-configure-data-retention.png)](media/data-retention/1-configure-data-retention.png#lightbox)

1. Selecione o **tempo de retenção de dados (em dias)** para configurar a retenção usando a barra deslizante ou digite um número na caixa de texto.

1. Observe a configuração de **capacidade** , pois essa configuração afeta a quantidade máxima de eventos de dados e a capacidade de armazenamento total para armazenar dados.

1. Alterne a configuração de **comportamento de limite de armazenamento excedido** . Selecione **limpar dados antigos** ou **Pausar** comportamento de entrada.

    [retenção de dados ![-aceite e salve.](media/data-retention/2-accept-and-save.png)](media/data-retention/2-accept-and-save.png#lightbox)

1. Aceite a caixa de seleção indicando que você analisou a documentação e entendeu os riscos potenciais de perda de dados. Selecione **salvar** para configurar as alterações.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, consulte [noções básicas sobre retenção em time Series insights](time-series-insights-concepts-retention.md).

- Saiba [como dimensionar seu ambiente de time Series insights](time-series-insights-how-to-scale-your-environment.md).

- Saiba mais sobre como [planejar o seu ambiente](time-series-insights-environment-planning.md).