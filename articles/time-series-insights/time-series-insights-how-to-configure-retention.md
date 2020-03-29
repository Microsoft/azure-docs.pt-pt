---
title: Como configurar a retenção no seu ambiente - Azure Time Series Insights [ Microsoft Docs
description: Aprenda a configurar a retenção no seu ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 3fdc007caaa4cb79f6083599a5bc176bc022fb03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278598"
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurar a retenção em Insights da Série Tempo

Este artigo descreve como configurar o tempo de **retenção** de dados e o limite de **armazenamento excedeu** o comportamento em Insights da Série De Tempo Azure.

## <a name="summary"></a>Resumo

Cada ambiente de Insights da Série de Tempo Azure tem uma definição para configurar o tempo de **retenção**de dados . O valor estende-se de 1 a 400 dias. Os dados são eliminados com base na capacidade de armazenamento do ambiente ou duração de retenção (1-400), o que vier em primeiro lugar.

Cada ambiente de Insights da Série Time tem um limite adicional de definição **De armazenamento excedido**comportamento . Esta definição controla o comportamento de ingresso e purga quando a capacidade máxima de um ambiente é alcançada. Há dois comportamentos para escolher:

- **Expurgar dados antigos** (padrão)
- **Pausa ingresso**

Para obter informações detalhadas para entender melhor estas configurações, reveja [a retenção compreensiva em Insights da Série De Tempo](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurar a retenção de dados

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

1. Localize o ambiente de Insights da Série Do Tempo existente. Selecione **Todos os recursos** no menu do lado esquerdo do portal Azure. Selecione o seu ambiente do Time Series Insights.

1. Sob a rubrica **Definições,** selecione **Configuração de Armazenamento**.

    [![Em Definições, selecione Configuração de Armazenamento](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selecione o tempo de **retenção de dados (em dias)** para configurar a retenção utilizando a barra de slider ou digite um número na caixa de texto.

1. Note a definição **de capacidade,** uma vez que esta configuração afeta a quantidade máxima de eventos de dados e capacidade total de armazenamento para armazenar dados.

1. Alternar o limite de **armazenamento excedeu** a definição de comportamento. **Selecione Purgar dados antigos** ou pausa no comportamento da **entrada.**

    [![Pausa ingresso - aceite e poupe.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Reveja a documentação para compreender os riscos potenciais da perda de dados. Selecione **Guardar** para configurar as alterações.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações, reveja [a retenção compreensiva em Insights](time-series-insights-concepts-retention.md)da Série Time .

- Aprenda [a escalar o seu ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Saiba mais sobre [como planear o seu ambiente.](time-series-insights-environment-planning.md)
