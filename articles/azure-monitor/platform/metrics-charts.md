---
title: Advanced features of Azure Metrics Explorer (Funcionalidades avançadas do Explorador de Métricas do Azure)
description: Saiba mais sobre as funcionalidades avançadas do Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e6ff33b6a23cb85649a8811a8bef27ab455ab9e6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980903"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Advanced features of Azure Metrics Explorer (Funcionalidades avançadas do Explorador de Métricas do Azure)

> [!NOTE]
> Este artigo assume que está familiarizado com as características básicas do Metrics Explorer. Se é um novo utilizador e quer aprender a criar o seu primeiro gráfico métrico, consulte [Getting started with Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Métricas em Azure

[As métricas no Monitor Azure](data-platform-metrics.md) são a série de valores medidos e contagens que são recolhidas e armazenadas ao longo do tempo. Existem métricas padrão (ou "plataforma") e métricas personalizadas. As métricas padrão são-lhe fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de saúde e utilização dos seus recursos Azure. Enquanto as métricas personalizadas são enviadas para o Azure pelas suas aplicações utilizando a API de [Insights de Aplicação para eventos e métricas personalizadas,](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics) [extensão de Diagnóstico Salérfio Windows Azure (WAD)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview)ou pela [API REST do Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Criar vistas com múltiplas métricas e gráficos

Pode criar gráficos que traçam várias linhas métricas ou mostrar gráficos métricos múltiplos ao mesmo tempo. Esta funcionalidade permite::

- correlacionar métricas relacionadas no mesmo gráfico para ver como um valor está relacionado com outro
- métricas de exibição com diferentes unidades de medida em proximidade
- agregado visualmente e comparar métricas de vários recursos

Por exemplo, se tiver 5 contas de armazenamento e quiser saber quanto espaço total é consumido entre elas, pode criar um gráfico de área (empilhado) que mostra o indivíduo e a soma de todos os valores em determinados pontos do tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Múltiplas métricas no mesmo gráfico

Primeiro, [crie um novo gráfico.](metrics-getting-started.md#create-your-first-metric-chart) Clique em **Adicionar Métrica** e repita os passos para adicionar outra métrica no mesmo gráfico.

   > [!NOTE]
   > Normalmente não se quer ter métricas com diferentes unidades de medida (isto é, "milissegundos" e "kilobytes") ou com uma escala significativamente diferente num gráfico. Em vez disso, considere usar vários gráficos. Clique no botão Add Chart para criar vários gráficos no explorador de métricas.

### <a name="multiple-charts"></a>Gráficos múltiplos

Clique na **tabela Adicionar** e crie outro gráfico com uma métrica diferente.

### <a name="order-or-delete-multiple-charts"></a>Encomende ou elimine vários gráficos

Para encomendar ou eliminar vários gráficos, clique no símbolo das elipses **(...** ) para abrir o menu da tabela e escolha o item de menu apropriado de **Mover-se**para **cima, mover-se para baixo**ou **excluir**.

## <a name="changing-aggregation"></a>Alteração da agregação

Quando adiciona uma métrica a um gráfico, o explorador de métricas pré-seleciona automaticamente a sua agregação padrão. O padrão faz sentido nos cenários básicos, mas você pode usar uma agregação diferente para obter insights adicionais sobre a métrica. Ver diferentes agregações num gráfico requer que compreenda como o explorador de métricas as lida. 

As métricas são a série de medições (ou "valores métricos") capturadas durante o período de tempo. Quando se traça um gráfico, os valores da métrica selecionada são agregados separadamente ao longo do *grão*de tempo . Selecione o tamanho do grão de tempo utilizando o painel de recolha de [tempo Metrics Explorer](metrics-getting-started.md#select-a-time-range). Se não fizer uma seleção explícita do grão de tempo, a granularidade do tempo é automaticamente selecionada com base na faixa hordo atualmente selecionada. Uma vez determinado o grão de tempo, os valores métricos que foram capturados durante cada intervalo de grão são agregados e colocados no gráfico - um ponto de dados por vez.

Por exemplo, suponha que o gráfico está mostrando a métrica do tempo de resposta do **servidor** usando a agregação **média** ao longo das **últimas 24 horas** de tempo:

- Se a granularidade do tempo for fixada para 30 minutos, o gráfico é extraído de 48 pontos de dados agregados (por exemplo, o gráfico de linha liga 48 pontos na área do enredo do gráfico). Isto é, 24 horas x 2 pontos de dados por hora. Cada ponto de dados representa a *média* de todos os tempos de resposta capturados para pedidos de servidores que ocorreram durante cada um dos 30 períodos de tempo relevantes.
- Se mudar a granularidade do tempo para 15 minutos, obtém 96 pontos de dados agregados.  Isto é, 24 horas x 4 pontos de dados por hora.

Existem cinco tipos básicos de agregação de estatísticas disponíveis no explorador de métricas: **Soma,** **Conde,** **Min,** **Max**e **Média**. A agregação da **soma** é por vezes referida como agregação **total.** Para muitas métricas, o Metrics Explorer esconderá as agregações que são totalmente irrelevantes e não podem ser usadas.

- **Soma** – a soma de todos os valores capturados sobre o intervalo de agregação
- **Contagem** – o número de medições capturadas sobre o intervalo de agregação. Note que **o Conde** será igual a **Soma** no caso em que a métrica é sempre capturada com o valor de 1. Isto é comum quando a métrica rastreia a contagem de eventos distintos, e cada medição representa um evento (isto é, o código dispara um registo métrico cada vez que um novo pedido entra)
- **Média** – a média dos valores métricos capturados sobre o intervalo de agregação
- **Min** – o menor valor capturado sobre o intervalo de agregação
- **Max** – o maior valor capturado sobre o intervalo de agregação

## <a name="apply-filters-to-charts"></a>Aplicar filtros em gráficos

Pode aplicar filtros nos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica "Contagem de Transações" tiver uma dimensão, "tipo de resposta", o que indica se a resposta das transações foi bem sucedida ou falhada, então a filtragem desta dimensão traçaria uma linha de gráfico para apenas transações bem sucedidas (ou apenas falhadas). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro

1. **Selecione Adicionar filtro** acima do gráfico

2. Selecione qual a dimensão (propriedade) que pretende filtrar

   ![imagem da métrica](./media/metrics-charts/00006.png)

3. Selecione quais os valores de dimensão que pretende incluir ao traçar o gráfico (este exemplo mostra filtrar as transações de armazenamento bem sucedidas):

   ![imagem da métrica](./media/metrics-charts/00007.png)

4. Depois de selecionar os valores do filtro, clique longe do Seletor do Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![imagem da métrica](./media/metrics-charts/00008.png)

5. Pode repetir os passos 1-4 para aplicar vários filtros nas mesmas tabelas.



## <a name="apply-splitting-to-a-chart"></a>Aplicar divisão num gráfico

Pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam entre si, e identificar os segmentos periféricos de uma dimensão.

### <a name="apply-splitting"></a>Aplicar divisão

1. Clique em **Aplicar divisão** acima da tabela.
 
   > [!NOTE]
   > A divisão não pode ser usada com gráficos que têm múltiplas métricas. Além disso, pode ter vários filtros, mas apenas uma dimensão de divisão aplicada a qualquer gráfico.

2. Escolha uma dimensão na qual deseja segmentar o seu gráfico:

   ![imagem da métrica](./media/metrics-charts/00010.png)

   Agora o gráfico agora mostra múltiplas linhas, uma para cada segmento de dimensão:

   ![imagem da métrica](./media/metrics-charts/00012.png)

3. Clique longe do Seletor de **Agrupamentopara** fechá-lo.

   > [!NOTE]
   > Use filtragem e divisão na mesma dimensão para ocultar os segmentos que são irrelevantes para o seu cenário e tornar os gráficos mais fáceis de ler.

## <a name="lock-boundaries-of-chart-y-axis"></a>Limites de bloqueio do gráfico y-eixo

O bloqueio da gama do eixo y torna-se importante quando o gráfico mostra flutuações menores de valores maiores. 

Por exemplo, quando o volume de pedidos bem sucedidos desce de 99,99% para 99,5%, pode representar uma redução significativa da qualidade do serviço. No entanto, notar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível a partir das definições do gráfico predefinido. Neste caso, pode bloquear o limite mais baixo da tabela para 99%, o que tornaria esta pequena queda mais aparente. 

Outro exemplo é uma flutuação na memória disponível, onde o valor tecnicamente nunca chegará a 0. Fixar o intervalo a um valor mais elevado pode facilitar a identificação das gotas na memória disponível. 

Para controlar a gama do eixo y, use o "..." menu de gráficos e **selecione gráfico de Edição** para aceder a definições de gráficos avançados. Modifique os valores na secção Gama do Eixo Y ou utilize o botão **Auto** para voltar aos defeitos.

![imagem da métrica](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> O bloqueio dos limites do eixo y para os gráficos que acompanham várias contagens ou somas durante um período de tempo (e, portanto, utilização de contagem, soma, mínima ou máxima agregação) geralmente requer especificar uma granularidade de tempo fixo em vez de depender dos incumprimentos automáticos. Isto é necessário porque os valores nos gráficos mudam quando a granularidade do tempo é automaticamente modificada pelo utilizador redimensionando a janela do navegador ou passando de uma resolução de ecrã para outra. A variação resultante da granularidade do tempo afeta o aspeto do gráfico, invalidando a seleção atual da gama y-eixo.

## <a name="change-colors-of-chart-lines"></a>Alterar as cores das linhas de gráfico

Depois de configurar os gráficos, as linhas de gráfico são automaticamente atribuídas a uma cor a partir de uma paleta padrão. Podes mudar essas cores.

Para alterar a cor de uma linha de gráfico, clique na barra colorida na legenda que corresponde ao gráfico. O diálogo do apanhador de cores abre-se. Utilize o apanhador de cores para configurar a cor para a linha.

Depois de configuradas as cores do gráfico, permanecerão assim quando fixar o gráfico num dashboard. A secção seguinte mostra-lhe como fixar um gráfico.

![imagem da métrica](./media/metrics-charts/018.png)

## <a name="pin-charts-to-dashboards"></a>Gráficos de pin para dashboards

Depois de configurar as tabelas, poderá querer adicioná-lo aos dashboards para que possa vê-lo novamente, possivelmente no contexto de outra telemetria de monitorização, ou partilhar com a sua equipa.

Para fixar um gráfico configurado num painel de instrumentos:

Depois de configurar o seu gráfico, clique no menu **'Chart Actions'** no canto superior direito da tabela e clique **em Pin para dashboard**.

![imagem da métrica](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Criar regras de alertas

Pode utilizar os critérios que definiu para visualizar as suas métricas como base de uma regra de alerta baseada em métricas. A nova regra de alerta incluirá as dimensões do seu recurso alvo, métrica, divisão e filtro da sua ficha. Poderá modificar estas definições mais tarde no painel de criação de regras de alerta.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Para criar uma nova regra de alerta, clique na **regra new alert**

![Novo botão de regra de alerta realçado a vermelho](./media/metrics-charts/015.png)

Você será levado para o painel de criação de regras de alerta com as dimensões métricas subjacentes do seu gráfico pré-povoado para facilitar a geração de regras de alerta personalizadas.

![Criar regra de alerta](./media/metrics-charts/016.png)

Confira este [artigo](alerts-metric.md) para saber mais sobre a criação de alertas métricos.

## <a name="troubleshooting"></a>Resolução de problemas

*Não vejo nenhum dado na minha ficha.*

* Os filtros aplicam-se a todos os gráficos do painel. Certifique-se de que, enquanto está focado num gráfico, não definiu um filtro que exclui todos os dados de outro.

* Se quiser definir filtros diferentes em diferentes gráficos, crie-os em lâminas diferentes, guarde-os como favoritos separados. Se quiser, pode fixá-los no tablier para que possa vê-los ao lado um do outro.

* Se segmentar um gráfico por uma propriedade que não esteja definida na métrica, então não haverá nada na tabela. Tente limpar a segmentação (separação) ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Passos seguintes

  Leia [Criar dashboards KPI personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para aprender sobre as melhores práticas para criar dashboards atol com métricas.

