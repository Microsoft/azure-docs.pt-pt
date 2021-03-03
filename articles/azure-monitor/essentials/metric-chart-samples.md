---
title: Exemplo de gráfico métrico do Monitor Azure
description: Saiba como visualizar os seus dados do Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 882c9e50c344b792d70d09017aeca3a2874cb106
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700750"
---
# <a name="metric-chart-examples"></a>Exemplos de gráficos métricos 

A plataforma Azure oferece [mais de mil métricas, muitas](./metrics-supported.md)das quais têm dimensões. Ao utilizar [filtros de dimensão,](./metrics-charts.md)aplicando [a divisão, controlando](./metrics-charts.md)o tipo de gráfico e ajustando as definições do gráfico, pode criar vistas e dashboards de diagnóstico poderosos que fornecem informações sobre a saúde da sua infraestrutura e aplicações. Este artigo mostra alguns exemplos dos gráficos que pode construir usando o [Metrics Explorer](./metrics-charts.md) e explica os passos necessários para configurar cada um destes gráficos.

Queres partilhar os teus grandes exemplos com o mundo? Contribua para esta página no GitHub e partilhe aqui os seus próprios exemplos de gráficos!

## <a name="website-cpu-utilization-by-server-instances"></a>Utilização do CPU do site por instâncias de servidor

Este gráfico mostra se o CPU de um Serviço de Aplicações estava dentro do alcance aceitável e o decompõe por exemplo para determinar se a carga foi devidamente distribuída. Pode ver na tabela que a aplicação estava a funcionar numa única instância de servidor antes das 6 da manhã e depois aumentou adicionando outro caso.

![Gráfico de linha da percentagem média de cpu por instância de servidor](./media/metrics-charts/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Selecione o seu recurso de Serviço de Aplicações e encontre a métrica **CPU Percentagem.** Em seguida, clique em **Aplicar a divisão** e selecionar a dimensão **Exemplo.**

## <a name="application-availability-by-region"></a>Disponibilidade de candidatura por região

Veja a disponibilidade da sua aplicação por região para identificar quais as localizações geográficas que estão a ter problemas. Este gráfico mostra a métrica de disponibilidade de Insights de Aplicação. Pode ver que a aplicação monitorizada não tem qualquer problema com a disponibilidade do centro de dados dos EUA, mas está a ter um problema de disponibilidade parcial dos EUA e da Ásia Oriental.

![Gráfico de disponibilidade média por locais](./media/metrics-charts/availability-by-location.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Primeiro tem de ativar a [monitorização da disponibilidade de Insights](../app/monitor-web-app-availability.md) de Aplicação para o seu website. Depois disso, escolha o seu recurso Application Insights e selecione a métrica Disponibilidade. Aplicar divisão na dimensão **da localização do Run.**

## <a name="volume-of-failed-storage-account-transactions-by-api-name"></a>Volume de operações de conta de armazenamento falhadas por nome da API

O seu recurso de conta de armazenamento está a experimentar um volume excessivo de transações falhadas. Pode utilizar a métrica das transações para identificar qual a API responsável pelo excesso de falha. Note que o gráfico a seguir é configurado com a mesma dimensão (nome API) na divisão e filtrado pelo tipo de resposta falhado:

![Gráfico de barras de transações de API](./media/metrics-charts/split-and-filter-example.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

No selecionador métrico, selecione a sua conta de armazenamento e a métrica **de Transações.** Tipo de gráfico de switch para **gráfico de barras**. Clique **em Aplicar dividir** e selecionar o nome **API** de dimensão. Em seguida, clique no **filtro Adicionar** e escolha novamente a dimensão do **nome API.** No diálogo do filtro, selecione as APIs que pretende traçar na tabela.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre os [livros de trabalho do](../visualize/workbooks-overview.md) Monitor Azure
* Saiba mais sobre [o Explorador Métrico](metrics-charts.md)