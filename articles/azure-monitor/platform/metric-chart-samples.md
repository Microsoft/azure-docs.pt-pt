---
title: Exemplo de gráfico de métrica Azure Monitor
description: Saiba mais sobre como visualizar seus dados de Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 8cc653b8e5d2c239243bf6a09955b10011c7408b
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538971"
---
# <a name="metric-chart-examples"></a>Exemplos de gráfico de métrica 

A plataforma Azure oferece [mais de mil métricas](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported), muitas das quais têm dimensões. Usando [filtros de dimensão](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), aplicando a [divisão](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts), controlando o tipo de gráfico e ajustando as configurações de gráfico, você pode criar modos de exibição de diagnóstico e painéis avançados que fornecem informações sobre a integridade da infraestrutura e dos aplicativos. Este artigo mostra alguns exemplos dos gráficos que você pode criar usando [Metrics Explorer](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-charts) e explica as etapas necessárias para configurar cada um desses gráficos.

Deseja compartilhar seus ótimos exemplos de gráficos com o mundo? Contribuir para esta página no GitHub e compartilhar seus próprios exemplos de gráfico aqui!

## <a name="website-cpu-utilization-by-server-instances"></a>Utilização de CPU do site por instâncias de servidor

Este gráfico mostra se a CPU de um serviço de aplicativo estava dentro do intervalo aceitável e a divide por instância para determinar se a carga foi distribuída corretamente. Você pode ver no gráfico que o aplicativo estava sendo executado em uma única instância de servidor antes das 6h e, em seguida, escalar verticalmente adicionando outra instância.

![Gráfico de linhas da porcentagem média de CPU por instância de servidor](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Selecione o recurso do serviço de aplicativo e localize a métrica **percentual de CPU** . Em seguida, clique em **aplicar divisão** e selecione a dimensão de **instância** .

## <a name="application-availability-by-region"></a>Disponibilidade do aplicativo por região

Exiba a disponibilidade do aplicativo por região para identificar quais locais geográficos estão tendo problemas. Este gráfico mostra a métrica de disponibilidade Application Insights. Você pode ver que o aplicativo monitorado não tem nenhum problema com a disponibilidade do datacenter leste dos EUA, mas está enfrentando um problema de disponibilidade parcial do oeste dos EUA e Ásia Oriental.

![Gráfico de disponibilidade média por locais](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

Primeiro, você precisa ativar Application Insights monitoramento de [disponibilidade](https://docs.microsoft.com/azure/azure-monitor/app/monitor-web-app-availability) para seu site. Depois disso, escolha o recurso Application Insights e selecione a métrica de disponibilidade. Aplicar divisão na dimensão de **local de execução** .

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Volume de transações de conta de armazenamento por nome de API

O recurso da conta de armazenamento está apresentando um volume excessivo de transações. Você pode usar a métrica de transações para identificar qual API é responsável pela carga em excesso. Observe que o gráfico a seguir está configurado com a mesma dimensão (nome da API) na filtragem e divisão para restringir a exibição apenas às chamadas de API do interesse:

![Gráfico de barras de transações de API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Como configurar este gráfico?

No seletor de métrica, selecione sua conta de armazenamento e a métrica de **Transações** . Alternar tipo de gráfico para **gráfico de barras**. Clique em **aplicar divisão** e selecione **nome da API**de dimensão. Em seguida, clique em **Adicionar filtro** e selecione a dimensão **nome da API** novamente. Na caixa de diálogo filtro, selecione as APIs que você deseja plotar no gráfico.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [pastas de trabalho](../../azure-monitor/app/usage-workbooks.md) do Azure monitor
* Saiba mais sobre o [Gerenciador de métricas](metrics-charts.md)
