---
title: Como configurar a retenção no seu ambiente - Azure Time Series Insights | Microsoft Docs
description: Aprenda a configurar a retenção no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 468b4f7ca7b0af4abc32df5d9ef64a74154d3de1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91569408"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Configuração da retenção em Azure Time Series Insights Gen1

> [!CAUTION]
> Este é um artigo da Gen1.

Este artigo descreve como configurar o **tempo de retenção** de dados e **o limite de armazenamento excedeu o comportamento** em Azure Time Series Insights.

## <a name="summary"></a>Resumo

Cada ambiente Azure Time Series Insights tem uma definição para configurar **o tempo de retenção de dados**. O valor estende-se de 1 a 400 dias. Os dados são eliminados com base na capacidade de armazenamento do ambiente ou na duração da retenção (1-400), o que vier primeiro.

Cada ambiente Azure Time Series Insights tem um limite de armazenamento adicional **ultrapassado .** Esta definição controla o comportamento de entrada e purga quando a capacidade máxima de um ambiente é alcançada. Há dois comportamentos para escolher:

- **Purgar dados antigos** (padrão)
- **Pausa ingress**

Para obter informações detalhadas para melhor compreender estas definições, [reveja a retenção de compreensão em Azure Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar a retenção de dados

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Localize o ambiente existente da Série de TempoS Azure Insights. Selecione **Todos os recursos** no menu do lado esquerdo do portal Azure. Selecione o seu ambiente Azure Time Series Insights.

1. No título **Definições,** selecione **a configuração de armazenamento**.

    [![Em Definições, selecione a configuração de armazenamento](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selecione o **tempo de retenção de Dados (em dias)** para configurar a retenção utilizando a barra de diapositivos ou digite um número na caixa de texto.

1. Note a definição **de Capacidade,** uma vez que esta configuração impacta a quantidade máxima de eventos de dados e a capacidade total de armazenamento para armazenar dados.

1. Alternar o **limite de armazenamento excedeu a** definição de comportamento. **Selecione purgar dados antigos** ou parar o comportamento **de ingress.**

    [![Pausa ingress - aceite e poupe.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Reveja a documentação para compreender os riscos potenciais de perda de dados. **Selecione Guardar** para configurar as alterações.

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações, [reveja a retenção de compreensão em Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Saiba [como escalar o ambiente Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Saiba como [planear o seu ambiente.](time-series-insights-environment-planning.md)
