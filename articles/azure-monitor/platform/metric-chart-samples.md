---
title: Exemplos de gráfico de métricas do Azure Monitor
description: Saiba mais sobre como visualizar os dados do Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: bbfeb428d38c23955df4497242184499349aecf9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60256740"
---
# <a name="metric-chart-samples"></a>Exemplos de gráfico de métricas

As ofertas de plataforma do Azure [sobre as métricas de mais do que mil](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), muitas das quais têm dimensões. Usando [filtros de dimensão](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), aplicando [divisão](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), controlar o tipo de gráfico e ajustar as configurações de gráfico, pode criar vistas de diagnóstico poderosas e dashboards que forneçam informações sobre o estado de funcionamento sua infraestrutura e aplicações. Este artigo mostra alguns exemplos dos gráficos que pode criar usando [Explorador de métricas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) e explica os passos necessários para configurar cada um deles.

Quer partilhar os seus exemplos excelentes gráficos com o mundo? Contribuir para esta página no GitHub e partilhe os seus próprios exemplos de gráfico aqui!

## <a name="website-cpu-utilization-by-server-instances"></a>Utilização de Web site da CPU por instâncias de servidor

Este gráfico mostra se a CPU para um serviço de aplicações estava dentro do intervalo aceitável e divide-os por instância para determinar se a carga foi devidamente distribuída. Pode ver no gráfico de que a aplicação estava em execução numa instância de servidor único antes das 06:00 e, em seguida, dimensionáveis pela adição de outra instância.

![Gráfico de linhas de percentagem de cpu média pela instância do servidor](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Selecione o recurso de serviço de aplicações e localizar o **percentagem de CPU** métrica. Em seguida, clique em **aplicam-se de que a divisão** e selecione o **instância** dimensão.

## <a name="application-availability-by-region"></a>Disponibilidade de aplicações por região

Ver a disponibilidade da aplicação por região para identificar as localizações geográficas estão tendo problemas. Este gráfico mostra a métrica de disponibilidade do Application Insights. Pode ver que a aplicação monitorizada não tem problemas com disponibilidade entre o datacenter E.U.A. leste, mas ele está com um problema de disponibilidade parcial do Oeste dos E.U.A. e Ásia Oriental.

![Gráfico de disponibilidade média por localizações](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Primeiro tem de ativar [disponibilidade do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) monitorização para o seu site. Depois disso, escolha o recurso do Application Insights e selecione a métrica de disponibilidade. Aplicam-se de que a divisão no **localização de execução** dimensão.

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume de transações de conta de armazenamento pelo nome da API

O recurso de conta de armazenamento está a ter um volume em excesso de transações. Pode utilizar a métrica de transações para identificar que API é responsável pela carga em excesso. Tenha em atenção que o gráfico a seguir está configurado com a mesma dimensão (nome da API) na filtragem e a divisão para limitar a vista para apenas as chamadas à API do interesse:

![Gráfico de barras das transações de API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

No selecionador de métrico, selecione a sua conta de armazenamento e o **transações** métrica. Mude o tipo de gráfico para **gráfico de barras**. Clique em **aplicam-se de que a divisão** e selecione dimensão **nome da API**. Em seguida, clique no **Adicionar filtro** e escolha o **nome da API** dimensão mais uma vez. Na caixa de diálogo Filtro, selecione as APIs que deseja representar no gráfico.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre o Azure Monitor [pastas de trabalho](../../azure-monitor/app/usage-workbooks.md)
* Saiba mais sobre [Explorador de métricas](metrics-charts.md)