---
title: Exemplo de gráfico métrico do Monitor Azure
description: Saiba mais sobre visualizar os seus dados do Monitor Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 1aaeb853a67b36a21a09db57e015029d10cd0c36
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660875"
---
# <a name="metric-chart-examples"></a>Exemplos de gráficos métricos 

A plataforma Azure oferece [mais de mil métricas, muitas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)das quais têm dimensões. Ao utilizar filtros de [dimensão,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)aplicação de [divisão,](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts)controlo do tipo de gráficoe definições de gráficos, pode criar poderosas vistas de diagnóstico e dashboards que fornecem informações sobre a saúde da sua infraestrutura e aplicações. Este artigo mostra alguns exemplos dos gráficos que pode construir usando o [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) e explica os passos necessários para configurar cada um destes gráficos.

Quer partilhar os seus grandes exemplos com o mundo? Contribua para esta página no GitHub e partilhe aqui os seus próprios exemplos de gráficos!

## <a name="website-cpu-utilization-by-server-instances"></a>Utilização do CPU do site por instâncias de servidor

Este gráfico mostra se o CPU para um Serviço de Aplicações estava dentro do intervalo aceitável e decompõe-o por exemplo para determinar se a carga estava corretamente distribuída. Pode ver na tabela que a aplicação estava a funcionar numa única instância de servidor antes das 6 da manhã e, em seguida, ampliada adicionando outra instância.

![Gráfico de linha da percentagem média de cpu por exemplo de servidor](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Selecione o seu recurso de Serviço de Aplicações e encontre a métrica **da Percentagem cpu.** Em seguida, clique em **Aplicar a divisão** e selecione a dimensão **'Instância'.**

## <a name="application-availability-by-region"></a>Disponibilidade de candidatura por região

Veja a disponibilidade da sua aplicação por região para identificar quais as localizações geográficas que estão a ter problemas. Este gráfico mostra a métrica de disponibilidade de Insights de Aplicação. Pode ver que a aplicação monitorizada não tem qualquer problema com a disponibilidade do centro de dados dos EUA Oriental, mas está a ter um problema de disponibilidade parcial dos EUA Ocidentais e da Ásia Oriental.

![Gráfico de disponibilidade média por locais](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Primeiro, é necessário ativar a monitorização da disponibilidade de Informações de [Aplicação](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) para o seu website. Depois disso, escolha o seu recurso Application Insights e selecione a métrica de Disponibilidade. Aplique a divisão na dimensão de **localização run.**

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume de transações de conta de armazenamento por nome API

O seu recurso de conta de armazenamento está a sofrer um volume excessivo de transações. Pode utilizar a métrica das transações para identificar qual a API responsável pelo excesso de carga. Note que o gráfico seguinte está configurado com a mesma dimensão (nome API) na filtragem e divisão para reduzir a vista apenas para as chamadas api do interesse:

![Gráfico de barras de transações de API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

No picker métrico, selecione a sua conta de armazenamento e a métrica **de Transações.** Mude o tipo de gráfico para **a tabela de barras**. Clique **Em Aplicar divisão** e selecione **nome API**de dimensão . Em seguida, clique no **filtro Adicionar** e escolha novamente a dimensão do **nome API.** No diálogo do filtro, selecione as APIs que pretende traçar na tabela.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [os livros](../../azure-monitor/app/usage-workbooks.md) de trabalho do Monitor Azure
* Saiba mais sobre [o Metric Explorer](metrics-charts.md)
