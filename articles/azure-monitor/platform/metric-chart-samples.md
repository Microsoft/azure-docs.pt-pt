---
title: Exemplo de gráfico métrico do Monitor Azure
description: Saiba como visualizar os seus dados do Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 9b2ab664f319de07fd70bd1a22b1ba6d64ac208f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320260"
---
# <a name="metric-chart-examples"></a>Exemplos de gráficos métricos 

A plataforma Azure oferece [mais de mil métricas, muitas](./metrics-supported.md)das quais têm dimensões. Ao utilizar [filtros de dimensão,](./metrics-charts.md)aplicando [a divisão, controlando](./metrics-charts.md)o tipo de gráfico e ajustando as definições do gráfico, pode criar vistas e dashboards de diagnóstico poderosos que fornecem informações sobre a saúde da sua infraestrutura e aplicações. Este artigo mostra alguns exemplos dos gráficos que pode construir usando o [Metrics Explorer](./metrics-charts.md) e explica os passos necessários para configurar cada um destes gráficos.

Queres partilhar os teus grandes exemplos com o mundo? Contribua para esta página no GitHub e partilhe aqui os seus próprios exemplos de gráficos!

## <a name="website-cpu-utilization-by-server-instances"></a>Utilização do CPU do site por instâncias de servidor

Este gráfico mostra se o CPU de um Serviço de Aplicações estava dentro do alcance aceitável e o decompõe por exemplo para determinar se a carga foi devidamente distribuída. Pode ver na tabela que a aplicação estava a funcionar numa única instância de servidor antes das 6 da manhã e depois aumentou adicionando outro caso.

![Gráfico de linha da percentagem média de cpu por instância de servidor](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Selecione o seu recurso de Serviço de Aplicações e encontre a métrica **CPU Percentagem.** Em seguida, clique em **Aplicar a divisão** e selecionar a dimensão **Exemplo.**

## <a name="application-availability-by-region"></a>Disponibilidade de candidatura por região

Veja a disponibilidade da sua aplicação por região para identificar quais as localizações geográficas que estão a ter problemas. Este gráfico mostra a métrica de disponibilidade de Insights de Aplicação. Pode ver que a aplicação monitorizada não tem qualquer problema com a disponibilidade do centro de dados dos EUA, mas está a ter um problema de disponibilidade parcial dos EUA e da Ásia Oriental.

![Gráfico de disponibilidade média por locais](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Primeiro tem de ativar a [monitorização da disponibilidade de Insights](../app/monitor-web-app-availability.md) de Aplicação para o seu website. Depois disso, escolha o seu recurso Application Insights e selecione a métrica Disponibilidade. Aplicar divisão na dimensão **da localização do Run.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume de operações de conta de armazenamento por nome API

O seu recurso de conta de armazenamento está a experimentar um volume excessivo de transações. Pode utilizar a métrica das transações para identificar qual a API responsável pelo excesso de carga. Note que o gráfico a seguir é configurado com a mesma dimensão (nome API) na filtragem e divisão para reduzir a vista apenas às chamadas API dos juros:

![Gráfico de barras de transações de API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

No selecionador métrico, selecione a sua conta de armazenamento e a métrica **de Transações.** Tipo de gráfico de switch para **gráfico de barras**. Clique **em Aplicar dividir** e selecionar o nome **API**de dimensão. Em seguida, clique no **filtro Adicionar** e escolha novamente a dimensão do **nome API.** No diálogo do filtro, selecione as APIs que pretende traçar na tabela.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [livros de trabalho do](./workbooks-overview.md) Monitor Azure
* Saiba mais sobre [o Explorador Métrico](metrics-charts.md)

