---
title: Características avançadas do explorador de métricas Azure
description: Saiba mais sobre os usos avançados do explorador de métricas Azure.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.openlocfilehash: 444185dc41532bfa55c271e2f0027eb8b71de42f
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959920"
---
# <a name="advanced-features-of-the-azure-metrics-explorer"></a>Características avançadas do explorador de métricas Azure

> [!NOTE]
> Este artigo pressupõe que está familiarizado com as características básicas da funcionalidade exploradora de métricas Azure do Azure Monitor. Se é um novo utilizador e quer aprender a criar o seu primeiro gráfico métrico, veja [como começar com o explorador de métricas](./metrics-getting-started.md).

No Azure Monitor, [as métricas](data-platform-metrics.md) são uma série de valores medidos e contagens que são recolhidas e armazenadas ao longo do tempo. As métricas podem ser padrão (também chamada de "plataforma") ou personalizadas. 

As métricas padrão são fornecidas pela plataforma Azure. Refletem as estatísticas de saúde e utilização dos seus recursos Azure. 

## <a name="resource-scope-picker"></a>Selecionador de âmbito de recursos
O selecionador de recursos permite-lhe visualizar métricas através de recursos únicos e múltiplos recursos. As seguintes secções explicam como utilizar o selecionador de âmbito de recursos. 

### <a name="select-a-single-resource"></a>Selecione um único recurso
Selecione **métricas** do menu **Azure Monitor** ou da secção de **monitorização** do menu de um recurso. Em seguida, escolha **Selecionar um âmbito** para abrir o selecionador de âmbito. 

Utilize o selecionador de âmbito para selecionar os recursos cujas métricas pretende ver. O âmbito deve ser preenchido se abrir o explorador de métricas Azure a partir do menu de um recurso. 

![Screenshot mostrando como abrir o selecionador de âmbito de recurso.](./media/metrics-charts/scope-picker.png)

Para alguns recursos, só pode ver as métricas de um recurso de cada vez. No menu **de tipos de recursos,** estes recursos estão na secção **Todos os tipos de recursos.**

![Screenshot mostrando um único recurso.](./media/metrics-charts/single-resource-scope.png)

Depois de selecionar um recurso, vê todas as subscrições e grupos de recursos que contêm esse recurso.

![Screenshot mostrando recursos disponíveis.](./media/metrics-charts/available-single-resource.png)

> [!TIP]
> Se quiser a capacidade de visualizar as métricas de múltiplos recursos ao mesmo tempo, ou de visualizar métricas através de uma subscrição ou grupo de recursos, selecione **Upvote**.

Quando estiver satisfeito com a sua seleção, **selecione Apply**.

### <a name="view-metrics-across-multiple-resources"></a>Ver métricas em vários recursos
Alguns tipos de recursos podem consultar métricas sobre vários recursos. Os recursos devem estar dentro da mesma subscrição e local. Encontre estes tipos de recursos no topo do menu **de tipos de recursos.** 

Para obter mais informações, consulte [Selecione vários recursos.](./metrics-dynamic-scope.md#select-multiple-resources)

![Screenshot mostrando tipos de recursos cruzados.](./media/metrics-charts/multi-resource-scope.png)

Para tipos compatíveis com múltiplos recursos, pode consultar métricas através de uma subscrição ou de vários grupos de recursos. Para obter mais informações, consulte [Selecione um grupo de recursos ou subscrição](./metrics-dynamic-scope.md#select-a-resource-group-or-subscription).

## <a name="multiple-metric-lines-and-charts"></a>Múltiplas linhas métricas e gráficos

No explorador de métricas Azure, pode criar gráficos que desfiem várias linhas métricas ou mostrem vários gráficos métricos ao mesmo tempo. Esta funcionalidade permite-lhe:

- Correlacionar métricas relacionadas no mesmo gráfico para ver como um valor se relaciona com o outro.
- Vise métricas que utilizem diferentes unidades de medida em proximidade.
- Visualmente agregado e compare métricas de múltiplos recursos.

Por exemplo, imagine que tem cinco contas de armazenamento, e quer saber quanto espaço consomem juntos. Pode criar um gráfico de área (empilhado) que mostre os valores individuais e a soma de todos os valores em determinados pontos do tempo.

### <a name="multiple-metrics-on-the-same-chart"></a>Métricas múltiplas no mesmo gráfico

Para ver várias métricas no mesmo gráfico, primeiro [crie um novo gráfico](./metrics-getting-started.md#create-your-first-metric-chart). Em seguida, **selecione Adicionar métrica**. Repita este passo para adicionar outra métrica no mesmo gráfico.

> [!NOTE]
> Normalmente, os seus gráficos não devem misturar métricas que usam diferentes unidades de medida. Por exemplo, evite misturar uma métrica que usa milissegundos com outra que usa quilobytes. Evite também misturar métricas cujas escamas diferem significativamente. 
>
> Nestes casos, considere usar vários gráficos em vez disso. No explorador de métricas, **selecione Adicionar gráfico** para criar um novo gráfico.

### <a name="multiple-charts"></a>Vários gráficos

Para criar outro gráfico que utilize uma métrica diferente, **selecione Add chart**.

Para reencomendar ou eliminar vários gráficos, selecione o botão elipse **(...)** para abrir o menu do gráfico. Em seguida, escolha **Mover-se,** **mover-se para baixo**, ou **eliminar**.

## <a name="time-range-controls"></a>Controlos do intervalo de tempo

Além de alterar o intervalo de tempo utilizando o painel de recolha de [tempo,](metrics-getting-started.md#select-a-time-range)também pode fazer panorâmica e fazer zoom utilizando os controlos na área do gráfico.
### <a name="pan"></a>Pan

Para fazer uma panela, clique nas setas esquerda e direita na borda da tabela.  Isto irá mover o intervalo de tempo selecionado para trás e para a frente por metade do tempo da tabela.  Por exemplo, se estiver a ver as últimas 24 horas, clicar na seta esquerda fará com que o intervalo de tempo se desloque para um dia e meio a 12 horas atrás.

A maioria das métricas suporta 93 dias de retenção, mas só lhe permite ver 30 dias de cada vez.  Utilizando os controlos da panela, você olha para os últimos 30 dias e, em seguida, facilmente volta 15 dias de cada vez para ver o resto do período de retenção.

![Gif animado mostrando os controlos da panela esquerda e direita.](./media/metrics-charts/metrics-pan-controls.gif)

### <a name="zoom"></a>Zoom

Pode clicar e arrastar na tabela para fazer zoom numa secção de um gráfico.  O zooming atualizará o intervalo de tempo do gráfico para abranger a sua seleção e selecionará um grão de tempo menor se o grão de tempo estiver definido para "Automático".  O novo intervalo de tempo aplicar-se-á a todos os gráficos em Métricas.

![Gif animado mostrando a característica de zoom métricas.](./media/metrics-charts/metrics-zoom-control.gif)

## <a name="aggregation&quot;></a>Agregação

Quando se adiciona uma métrica a um gráfico, o explorador de métricas aplica automaticamente uma agregação padrão. O padrão faz sentido em cenários básicos. Mas pode usar uma agregação diferente para obter mais informações sobre a métrica. 

Antes de usar agregações diferentes num gráfico, deve entender como o explorador de métricas as trata. As métricas são uma série de medições (ou &quot;valores métricos") que são capturadas ao longo de um período de tempo. Quando se traça um gráfico, os valores da métrica selecionada são agregados separadamente ao longo do grão de *tempo*. 

Selecione o tamanho do grão de tempo utilizando o painel de recolha de tempo do explorador de [métricas](./metrics-getting-started.md#select-a-time-range). Se não selecionar explicitamente o grão de tempo, o intervalo de tempo atualmente selecionado é utilizado por predefinição. Após o tempo em que os grãos são determinados, os valores métricos que foram capturados durante cada vez que os grãos são agregados na tabela, um ponto de dados por grão de tempo.

Por exemplo, suponha que um gráfico mostre a métrica do tempo de resposta do *Servidor.* Utiliza a agregação *média* ao longo do tempo das *últimas 24 horas.* Neste exemplo:

- Se a granularidade do tempo estiver definida para 30 minutos, o gráfico é extraído de 48 pontos de dados agregados. Ou seja, o gráfico de linha conecta 48 pontos na área do enredo do gráfico (24 horas x 2 pontos de dados por hora). Cada ponto de dados representa a *média* de todos os tempos de resposta capturados para pedidos de servidor que ocorreram durante cada um dos períodos de tempo de 30 minutos relevantes.
- Se mudar a granularidade de tempo para 15 minutos, obtém 96 pontos de dados agregados.  Ou seja, obtém-se 24 horas x 4 pontos de dados por hora.

O explorador de métricas tem cinco tipos básicos de agregação estatística: soma, contagem, min, máx e média. A *agregação* da soma é por vezes chamada de agregação *total.* Para muitas métricas, o explorador de métricas esconde as agregações que são irrelevantes e não podem ser usadas.

* **Soma**: Soma de todos os valores capturados durante o intervalo de agregação.

    ![Screenshot de um pedido de soma.](./media/metrics-charts/request-sum.png)

* **Contagem**: O número de medições capturadas durante o intervalo de agregação. 
    
    Quando a métrica é sempre capturada com o valor de 1, a agregação da contagem é igual à agregação de soma. Este cenário é comum quando a métrica acompanha a contagem de eventos distintos e cada medida representa um evento. O código emite um registo métrico sempre que um novo pedido chega.

    ![Screenshot de um pedido de contagem.](./media/metrics-charts/request-count.png)

* **Média**: A média dos valores métricos capturados durante o intervalo de agregação.

    ![Screenshot de um pedido médio.](./media/metrics-charts/request-avg.png)

* **Min**: O menor valor capturado durante o intervalo de agregação.

    ![Screenshot de um pedido mínimo.](./media/metrics-charts/request-min.png)

* **Max**: O maior valor capturado durante o intervalo de agregação.

    ![Screenshot de um pedido máximo.](./media/metrics-charts/request-max.png)

## <a name="filters"></a>Filtros

Pode aplicar filtros em gráficos cujas métricas têm dimensões. Por exemplo, imagine uma métrica de "contagem de transações" que tenha uma dimensão de "tipo de resposta". Esta dimensão indica se a resposta das transações foi bem sucedida ou falhada. Se filtrar esta dimensão, verá uma linha de gráfico apenas para transações bem sucedidas (ou apenas falhadas). 

### <a name="add-a-filter"></a>Adicionar um filtro

1. Acima da tabela, **selecione Adicionar filtro**.

2. Selecione uma dimensão (propriedade) para filtrar.

   ![Screenshot que mostra as dimensões (propriedades) que pode filtrar.](./media/metrics-charts/028.png)

3. Selecione o operador que pretende aplicar contra a dimensão (propriedade). O operador predefinido é = (é igual)

   ![Screenshot que mostra o operador que pode usar com o filtro.](./media/metrics-charts/filter-operator.png)

4. Selecione quais os valores de dimensão que pretende aplicar ao filtro ao traçar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem sucedidas):

   ![Screenshot que mostra as transações de armazenamento filtradas bem sucedidas.](./media/metrics-charts/029.png)

5. Depois de selecionar os valores do filtro, clique para longe do Seletor de Filtro para o fechar. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![Screenshot que mostra quantas transações de armazenamento falharam.](./media/metrics-charts/030.png)

6. Pode repetir os passos 1-5 para aplicar vários filtros nas mesmas tabelas.


## <a name="metric-splitting"></a>Divisão métrica

Pode dividir uma métrica por dimensão para visualizar como diferentes segmentos da métrica se comparam. A divisão também pode ajudá-lo a identificar os segmentos de uma dimensão.

### <a name="apply-splitting"></a>Aplicar divisão

1. Acima da tabela, **selecione Aplicar a divisão**.
 
   > [!NOTE]
   > Gráficos que têm várias métricas não podem usar a funcionalidade de divisão. Além disso, embora um gráfico possa ter vários filtros, pode ter apenas uma dimensão de divisão.

2. Escolha uma dimensão para segmentar o seu gráfico:

   ![Screenshot que mostra a dimensão selecionada para segmentar o gráfico.](./media/metrics-charts/031.png)

   O gráfico agora mostra várias linhas, uma para cada segmento de dimensão:

   ![Screenshot que mostra várias linhas, uma para cada segmento de dimensão.](./media/metrics-charts/segment-dimension.png)
   
3. Escolha um limite para o número de valores a apresentar após a divisão por dimensão selecionada. O limite de predefinição é de 10, como mostrado no gráfico acima. O intervalo de limite é de 1 a 50.
   
   ![Screenshot que mostra limite de divisão, o que restringe o número de valores após a divisão.](./media/metrics-charts/segment-dimension-limit.png)
   
4. Escolha a ordem de classificação em segmentos: Ascendente ou Descendente. A seleção padrão está a descer.
   
   ![Screenshot que mostra ordem de classificação em valores divididos.](./media/metrics-charts/segment-dimension-sort.png)

5. Clique para longe do **Seletor de Agrupamento** para fechá-lo.
   

   > [!NOTE]
   > Para ocultar segmentos que são irrelevantes para o seu cenário e para facilitar a leitura dos seus gráficos, utilize tanto a filtragem como a divisão na mesma dimensão.

## <a name="locking-the-range-of-the-y-axis"></a>Bloqueando o alcance do eixo y

Bloquear a gama do eixo de valor (y) torna-se importante em gráficos que mostram pequenas flutuações de grandes valores. 

Por exemplo, uma redução do volume de pedidos bem sucedidos de 99,99% para 99,5% pode representar uma redução significativa na qualidade do serviço. Mas notar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível se estiver a usar as definições de gráfico padrão. Neste caso, pode bloquear o limite mais baixo da tabela para 99% para tornar uma pequena gota mais aparente. 

Outro exemplo é uma flutuação na memória disponível. Neste cenário, o valor tecnicamente nunca chegará a 0. Fixar o intervalo a um valor mais elevado pode tornar as quedas na memória disponível mais fáceis de detetar. 

Para controlar a gama de eixos y, abra o menu do gráfico **(...**). Em seguida, selecione **definições de Gráfico** para aceder a definições de gráficos avançados.

![Screenshot que realça a seleção de definições de gráfico.](./media/metrics-charts/033.png)

Modifique os valores na secção **de alcance do eixo Y** ou selecione **Auto** para reverter para os valores predefinidos.
 
 ![Screenshot que destaca a secção de alcance do eixo Y.](./media/metrics-charts/034.png)

> [!WARNING]
> Se precisar de bloquear os limites do eixo y para gráficos que rastreiam contagens ou somas ao longo de um período de tempo (utilizando agregações de contagem, soma, min ou máximo), deve normalmente especificar uma granularidade de tempo fixo. Neste caso, não deve confiar nos predefinidos automáticos. 
>
> Você escolhe uma granularidade de tempo fixo porque os valores do gráfico mudam quando a granularidade é modificada automaticamente após um utilizador redimensionar uma janela do navegador ou alterar a resolução do ecrã. A alteração resultante da granularidade temporal afeta o aspeto do gráfico, invalidando a seleção atual da gama y-axis.

## <a name="line-colors"></a>Cores de linha

Depois de configurar os gráficos, as linhas de gráfico são automaticamente atribuídas uma cor a partir de uma paleta padrão. Pode mudar as cores.

Para alterar a cor de uma linha de gráfico, selecione a barra colorida na legenda que corresponde à tabela. Abre-se a caixa de diálogo do apanhador de cores. Utilize o apanhador de cores para configurar a cor da linha.

![Screenshot que mostra como mudar de cor.](./media/metrics-charts/035.png)

As suas cores personalizadas são preservadas quando fixa o gráfico num painel de instrumentos. A seguinte secção mostra como fixar um gráfico.

## <a name="pinning-to-dashboards"></a>Fixação em painéis 

Depois de configurar um gráfico, é melhor adicioná-lo a um dashboard. Ao fixar um gráfico num dashboard, pode torná-lo acessível à sua equipa. Também pode obter insights visualizando-o no contexto de outra telemetria de monitorização.

Para fixar um gráfico configurado a um painel de instrumentos, no canto superior direito da tabela, selecione Pin para painel de **instrumentos**.

![Screenshot mostrando como fixar um gráfico a um painel de instrumentos.](./media/metrics-charts/036.png)

## <a name="alert-rules"></a>Regras de alertas

Pode utilizar os seus critérios de visualização para criar uma regra de alerta baseada em métricas. A nova regra de alerta incluirá as dimensões do recurso-alvo, métrica, divisão e filtragem do seu gráfico. Pode modificar estas definições utilizando o painel de criação de regras de alerta.

Para começar, selecione **Nova regra de alerta**.

![Screenshot que mostra o novo botão de regra de alerta realçado a vermelho.](./media/metrics-charts/042.png)

O painel de criação de regras de alerta abre. No painel, vê-se as dimensões métricas do gráfico. Os campos no painel são pré-expulsados para ajudá-lo a personalizar a regra.

![Screenshot mostrando o painel de criação de regras.](./media/metrics-charts/041.png)

Para obter mais informações, consulte [Criar, ver e gerir alertas métricos.](../alerts/alerts-metric.md)

## <a name="correlate-metrics-to-logs"></a>Correlacionar métricas com registos
Para ajudar o cliente a diagnosticar a causa principal de anomalias na sua tabela de métricas, criámos a Drill em Logs. Perfurar em Logs permite que os clientes correlacionem picos na sua tabela de métricas com registos e consultas. 

Antes de mergulharmos na experiência, queremos primeiro introduzir os diferentes tipos de registos e consultas fornecidas. 

| Termo             | Definição  | 
|------------------|-------------|
| Registos de atividade    | Fornece informações sobre as operações de cada recurso Azure na subscrição a partir do exterior (o plano de gestão) para além de atualizações sobre eventos de Saúde de Serviço. Utilize o Registo de Atividades, para determinar o quê, quem e quando para quaisquer operações de escrita (PUT, POST, DELETE) assumiu os recursos na sua subscrição. Há um único registo de Atividade para cada subscrição do Azure.  |   
| Registo de diagnósticos   | Forneça informações sobre as operações que foram realizadas dentro de um recurso Azure (o plano de dados), por exemplo, obter um segredo de um Cofre-Chave ou fazer um pedido para uma base de dados. O conteúdo dos registos de recursos varia consouros e de tipo de recurso. **Nota:** Deve ser prestado por serviço e habilitado pelo cliente  | 
| Tronco recomendado | Consultas baseadas em cenários que o cliente pode aproveitar para investigar anomalias no seu explorador de métricas.  |

Atualmente, a Drill in Logs está disponível para fornecedores de recursos selecionados. Os fornecedores de recursos que possuem a experiência drill in Logs completa são: 

* Application Insights 
* Dimensionamento Automático 
* Serviços Aplicacionais  
* Armazenamento  

Abaixo está uma amostra de experiências para o fornecedor de recursos Application Insights.

![Pico em falhas na lâmina de insights de apps](./media/metrics-charts/drill-into-log-ai.png)

Para diagnosticar o pico em pedidos falhados, clique em "Drill in Logs".

![Screenshot da broca em troncos dropdown](./media/metrics-charts/drill-into-logs-dropdown.png)

Ao clicar na opção de falha, você será levado a uma lâmina de falha personalizada que lhe fornece as operações de funcionamento falhadas, tipos de exceções de topo e dependências. 

![Screenshot da lâmina de falha de insights de aplicativos](./media/metrics-charts/ai-failure-blade.png)

### <a name="common-problems-with-drill-into-logs"></a>Problemas comuns com perfuração em troncos

* Os registos e consultas estão desativados - Para visualizar registos e consultas recomendados, tem de encaminhar os seus registos de diagnóstico para Registar Analytics. Leia [este documento](./diagnostic-settings.md) para aprender a fazer isto. 
* Os registos de atividade só são fornecidos - A funcionalidade Drill in Logs só está disponível para fornecedores de recursos selecionados. Por predefinição, são fornecidos registos de atividade. 

 
## <a name="troubleshooting"></a>Resolução de problemas

Se não vir nenhum dado na sua ficha, reveja as seguintes informações de resolução de problemas:

* Os filtros aplicam-se a todas as tabelas do painel. Enquanto se foca num gráfico, certifique-se de que não define um filtro que exclui todos os dados de outro gráfico.

* Para definir filtros diferentes em diferentes gráficos, crie as tabelas em lâminas diferentes. Em seguida, guarde as tabelas como favoritos separados. Se quiser, pode fixar as fichas no painel de instrumentos para que possa vê-las juntas.

* Se segmentar um gráfico por uma propriedade que a métrica não define, o gráfico não apresenta nenhum conteúdo. Tente limpar a segmentação (divisão), ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Passos seguintes

Para criar painéis de instrumentos através da utilização de métricas, consulte [criar painéis KPI personalizados](../app/tutorial-app-dashboards.md).