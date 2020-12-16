---
title: Advanced features of Azure Metrics Explorer (Funcionalidades avançadas do Explorador de Métricas do Azure)
description: Conheça as funcionalidades avançadas do Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: be3d3f11e90c17bd8c4792418500da651039e480
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562808"
---
# <a name="advanced-features-of-azure-metrics-explorer"></a>Advanced features of Azure Metrics Explorer (Funcionalidades avançadas do Explorador de Métricas do Azure)

> [!NOTE]
> Este artigo assume que está familiarizado com as características básicas do Metrics Explorer. Se é um novo utilizador e quer aprender a criar o seu primeiro gráfico métrico, consulte [Começar com o Azure Metrics Explorer](metrics-getting-started.md).

## <a name="metrics-in-azure"></a>Métricas em Azure

[As métricas no Azure Monitor](data-platform-metrics.md) são a série de valores e contagens medidos que são recolhidos e armazenados ao longo do tempo. Existem métricas padrão (ou "plataforma") e métricas personalizadas. As métricas padrão são fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de saúde e utilização dos seus recursos Azure. Enquanto as métricas personalizadas são enviadas para a Azure pelas suas aplicações utilizando a [API de Insights de Aplicação para eventos e métricas personalizadas,](../app/api-custom-events-metrics.md)  [extensão de Diagnóstico do Windows Azure (WAD)](./diagnostics-extension-overview.md)ou pela [Azure Monitor REST API](./metrics-store-custom-rest-api.md).

## <a name="create-views-with-multiple-metrics-and-charts"></a>Criar vistas com várias métricas e gráficos

Pode criar gráficos que desfiem várias linhas de métricas ou mostrem vários gráficos métricos ao mesmo tempo. Esta funcionalidade permite-lhe:

- correlacionar métricas relacionadas no mesmo gráfico para ver como um valor está relacionado com outro
- mostrar métricas com diferentes unidades de medida em proximidade
- visualmente agregado e compare métricas de múltiplos recursos

Por exemplo, se tiver 5 contas de armazenamento e quiser saber quanto espaço total é consumido entre elas, pode criar um gráfico de área (empilhado) que mostra o indivíduo e a soma de todos os valores em determinados pontos do tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Métricas múltiplas no mesmo gráfico

Primeiro, [criar um novo gráfico.](metrics-getting-started.md#create-your-first-metric-chart) Clique **em Adicionar Métrica** e repita os passos para adicionar outra métrica no mesmo gráfico.

   > [!NOTE]
   > Normalmente não se quer ter métricas com diferentes unidades de medida (isto é, "milissegundos" e "kilobytes") ou com escala significativamente diferente num gráfico. Em vez disso, considere usar vários gráficos. Clique no botão Adicionar Gráfico para criar vários gráficos no explorador de métricas.

### <a name="multiple-charts"></a>Vários gráficos

Clique na **tabela Add** e crie outro gráfico com uma métrica diferente.

### <a name="order-or-delete-multiple-charts"></a>Encomendar ou apagar vários gráficos

Para encomendar ou eliminar vários gráficos, clique no símbolo das elipses **(...** ) para abrir o menu do gráfico e escolha o item de menu apropriado de **Move up**, **Move down**, ou **Delete**.

## <a name="changing-aggregation"></a>Alteração da agregação

Quando adiciona uma métrica a um gráfico, o explorador de métricas pré-selecciona automaticamente a sua agregação padrão. O padrão faz sentido nos cenários básicos, mas você pode usar uma agregação diferente para obter insights adicionais sobre a métrica. Ver agregações diferentes num gráfico requer que compreenda como o explorador de métricas as trata. 

As métricas são a série de medições (ou "valores métricos") capturadas ao longo do período de tempo. Quando se traça um gráfico, os valores da métrica selecionada são agregados separadamente ao longo do grão de *tempo*. Selecione o tamanho do grão de tempo utilizando o painel de recolha de tempo do [Metrics Explorer](metrics-getting-started.md#select-a-time-range). Se não fizer uma seleção explícita do grão de tempo, a granularidade do tempo é selecionada automaticamente com base no intervalo de tempo atualmente selecionado. Uma vez determinado o grão de tempo, os valores métricos que foram capturados durante cada intervalo de grão são agregados e colocados na tabela - um ponto de dados por grão de tempo.

Por exemplo, suponha que o gráfico esteja a mostrar a métrica **do Tempo de Resposta** do Servidor utilizando a agregação **média** ao longo das **últimas 24 horas:**

- Se a granularidade do tempo estiver definida para 30 minutos, o gráfico é desenhado a partir de 48 pontos de dados agregados (por exemplo, o gráfico de linha liga 48 pontos na área do enredo do gráfico). Ou seja, 24 horas x 2 pontos de dados por hora. Cada ponto de dados representa a *média* de todos os tempos de resposta capturados para pedidos de servidor que ocorreram durante cada um dos períodos de tempo de 30 minutos relevantes.
- Se mudar a granularidade de tempo para 15 minutos, obtém 96 pontos de dados agregados.  Ou seja, 24 horas x 4 pontos de dados por hora.

Existem cinco tipos básicos de agregação de estatísticas disponíveis no explorador de métricas: **Soma,** **Conde,** **Min,** **Max** e **Média.** A **agregação** da Soma é por vezes referida como **agregação total.** Para muitas métricas, o Metrics Explorer esconderá as agregações que são totalmente irrelevantes e não podem ser usadas.

- **Soma** – a soma de todos os valores capturados ao longo do intervalo de agregação
- **Count** – o número de medições capturadas durante o intervalo de agregação. Note que **o Conde** será igual a **Sum** no caso em que a métrica é sempre capturada com o valor de 1. Isto é comum quando a métrica acompanha a contagem de eventos distintos, e cada medição representa um evento (ou seja, o código dispara um registo métrico cada vez que um novo pedido entra)
- **Média** – a média dos valores métricos capturados ao longo do intervalo de agregação
- **Min** – o menor valor capturado ao longo do intervalo de agregação
- **Max** – o maior valor capturado ao longo do intervalo de agregação

## <a name="apply-filters-to-charts"></a>Aplicar filtros em gráficos

Pode aplicar filtros nos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica "Contagem de Transações" tiver uma dimensão, "Tipo de resposta", que indique se a resposta das transações foi bem sucedida ou falhada, então a filtragem nesta dimensão traçaria uma linha de gráfico apenas para transações bem sucedidas (ou apenas falhadas). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro

1. **Selecione Adicionar filtro** acima do gráfico

2. Selecione qual a dimensão (propriedade) que pretende filtrar

   ![Screenshot que mostra as dimensões (propriedades) que pode filtrar.](./media/metrics-charts/028.png)

3. Selecione quais os valores de dimensão que pretende incluir ao traçar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem sucedidas):

   ![Screenshot que mostra a filtragem das transações de armazenamento bem sucedidas.](./media/metrics-charts/029.png)

4. Depois de selecionar os valores do filtro, clique para longe do Seletor de Filtro para o fechar. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![Screenshot que mostra quantas transações de armazenamento falharam](./media/metrics-charts/030.png)

5. Pode repetir os passos 1-4 para aplicar vários filtros nas mesmas tabelas.



## <a name="apply-splitting-to-a-chart"></a>Aplicar divisão a um gráfico

Pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam entre si, e identificar os segmentos de uma dimensão.

### <a name="apply-splitting"></a>Aplicar divisão

1. Clique em **Aplicar a divisão** acima da tabela.
 
   > [!NOTE]
   > A divisão não pode ser usada com gráficos que têm várias métricas. Além disso, pode ter vários filtros, mas apenas uma dimensão de divisão aplicada a qualquer gráfico.

2. Escolha uma dimensão na qual pretende segmentar o seu gráfico:

   ![Screenshot que mostra a dimensão selecionada na qual segmenta a sua tabela.](./media/metrics-charts/031.png)

   Agora o gráfico mostra agora várias linhas, uma para cada segmento de dimensão:

   ![Screenshot que mostra várias linhas, uma para cada segmento de dimensão.](./media/metrics-charts/032.png)

3. Clique para longe do **Seletor de Agrupamento** para fechá-lo.

   > [!NOTE]
   > Utilize filtragem e divisão na mesma dimensão para ocultar os segmentos que são irrelevantes para o seu cenário e tornar os gráficos mais fáceis de ler.

## <a name="lock-boundaries-of-chart-y-axis"></a>Limites de bloqueio do eixo gráfico y

Bloquear o alcance do eixo y torna-se importante quando o gráfico mostra flutuações menores de valores maiores. 

Por exemplo, quando o volume de pedidos bem sucedidos desce de 99,99% para 99,5%, pode representar uma redução significativa na qualidade do serviço. No entanto, notar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível a partir das definições do gráfico predefinido. Neste caso, poderia bloquear o limite mais baixo da tabela para 99%, o que tornaria esta pequena queda mais aparente. 

Outro exemplo é uma flutuação na memória disponível, onde o valor tecnicamente nunca chegará a 0. Fixar o intervalo a um valor mais elevado pode facilitar a posição das gotas na memória disponível. 

Para controlar a gama de eixos y, use o "..." menu de gráficos e selecione **definições de Gráfico** para aceder a definições avançadas do gráfico.

![Screenshot que realça a opção de definições de gráfico.](./media/metrics-charts/033.png)

 Modifique os valores na secção Y-Axis Range ou use o botão **Auto** para reverter para os predefinidos.
 
 ![Screenshot que destaca a secção de alcance do eixo Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Bloquear os limites do eixo y para as tabelas que acompanham várias contagens ou somas ao longo de um período de tempo (e assim utilizar a contagem, a soma, o mínimo ou as agregações máximas) geralmente requer especificar uma granularidade de tempo fixo em vez de depender dos predefinidos automáticos. Isto é necessário porque os valores nos gráficos mudam quando a granularidade é automaticamente modificada pelo utilizador redimensionando a janela do navegador ou passando de uma resolução de ecrã para outra. A alteração resultante da granularidade temporal afeta o aspeto do gráfico, invalidando a seleção atual da gama y-axis.

## <a name="change-colors-of-chart-lines"></a>Alterar cores das linhas de gráfico

Depois de configurar os gráficos, as linhas de gráfico são automaticamente atribuídas uma cor a partir de uma paleta padrão. Pode mudar as cores.

Para alterar a cor de uma linha de gráfico, clique na barra colorida na legenda que corresponde à tabela. O diálogo do apanhador de cores abrir-se-á. Utilize o apanhador de cores para configurar a cor da linha.

![Screenshot que mostra como mudar de cor](./media/metrics-charts/035.png)

Depois de configuradas as cores do gráfico, elas permanecerão assim quando fixar a tabela num dashboard. A seguinte secção mostra como fixar um gráfico.

## <a name="pin-charts-to-dashboards"></a>Gráficos de pinos para painéis

Depois de configurar as tabelas, pode querer adicioná-lo aos dashboards para que possa vê-lo novamente, possivelmente no contexto de outra telemetria de monitorização, ou partilhar com a sua equipa.

Para fixar um gráfico configurado num painel de instrumentos:

Depois de configurar a sua tabela, clique em Pin para painel de **instrumentos** no canto superior direito da tabela.

![Screenshot que mostra como pin to chart](./media/metrics-charts/036.png)

## <a name="create-alert-rules"></a>Criar regras de alertas

Pode utilizar os critérios definidos para visualizar as suas métricas como base de uma regra de alerta baseada em métricas. A nova regra de alerta incluirá as dimensões do seu recurso alvo, métrica, divisão e filtragem da sua tabela. Poderá modificar estas definições mais tarde no painel de criação de regras de alerta.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Para criar uma nova regra de alerta, clique na **regra de Alerta Novo**

![Novo botão de regra de alerta realçado no vermelho](./media/metrics-charts/042.png)

Você será levado para o painel de criação de regras de alerta com as dimensões métricas subjacentes do seu gráfico pré-povoado para facilitar a geração de regras de alerta personalizados.

![Criar regra de alerta](./media/metrics-charts/041.png)

Confira este [artigo](alerts-metric.md) para saber mais sobre a configuração de alertas métricos.

## <a name="troubleshooting"></a>Resolução de problemas

*Não vejo nenhum dado na minha ficha.*

* Os filtros aplicam-se a todas as tabelas do painel. Certifique-se de que, enquanto se concentra num gráfico, não definiu um filtro que exclui todos os dados de outro.

* Se pretender definir filtros diferentes em diferentes gráficos, crie-os em lâminas diferentes, guarde-os como favoritos separados. Se quiser, pode fixá-los no painel de instrumentos para que possa vê-los ao lado um do outro.

* Se segmentar um gráfico por uma propriedade que não está definida na métrica, então não haverá nada na tabela. Tente limpar a segmentação (divisão), ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Passos seguintes

  Leia [A criação de dashboards KPI personalizados](../learn/tutorial-app-dashboards.md) para aprender sobre as melhores práticas para criar dashboards accuáveis com métricas.
