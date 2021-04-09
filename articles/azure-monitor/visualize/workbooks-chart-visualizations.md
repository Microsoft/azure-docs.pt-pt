---
title: Visualizações de gráficos de livros do Azure Monitor
description: Saiba mais sobre todas as visualizações do gráfico de livros do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 139c8cdb1a227595d728d2acc0b09cf1eb210715
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100618902"
---
# <a name="chart-visualizations"></a>Visualizações de gráfico

Os livros permitem que os dados de monitorização sejam apresentados como gráficos. Os tipos de gráficos suportados incluem linha, bar, bar categórico, área, parcelas de dispersão, torta e tempo. Os autores podem optar por personalizar a altura, largura, paleta de cores, lenda, títulos, mensagem sem dados, etc. do gráfico e personalizar tipos de eixos e cores de série usando configurações de gráficos.

Os livros de trabalho suportam gráficos tanto para registos como para fontes de dados métricas.

## <a name="log-charts"></a>Gráficos de registo

Os registos do Azure Monitor dão aos proprietários de recursos informações detalhadas sobre o funcionamento das suas apps e infraestruturas. Ao contrário das métricas, a informação de registo não é recolhida por padrão e requer algum tipo de recolha no embarque. No entanto, quando os registos presentes fornecem muita informação sobre o estado do recurso e dados úteis para diagnósticos. Os livros de trabalho permitem apresentar dados de registo como gráficos visuais para análise do utilizador.

### <a name="adding-a-log-chart"></a>Adicionar um gráfico de registo

O exemplo abaixo mostra a tendência dos pedidos para uma aplicação nos dias anteriores.

1. Mude o livro para editar o modo selecionando o item da barra de ferramentas **Editar.**
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o [KQL](/azure/kusto/query/) para a sua análise (por exemplo, tendência de pedidos).
5. Coloque a visualização numa das: **Área,** **Bar,** **Bar (categórica)**, **Linha,** **Tarte,** **Dispersão,** ou **Hora**.
6. Desa ajuste outros parâmetros se necessário - como intervalo de tempo, visualização, tamanho, paleta de cores e lenda.

[![Screenshot do gráfico de registo no modo de edição](./media/workbooks-chart-visualizations/log-chart.png)](./media/workbooks-chart-visualizations/log-chart.png#lightbox)

### <a name="log-chart-parameters"></a>Parâmetros do gráfico de registo

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a utilizar. | Log, Azure Resource Graph, etc. |
| `Resource Type` | O tipo de recurso para o alvo. | Insights de Aplicação, Log Analytics ou Azure-first |
| `Resources` | Um conjunto de recursos para obter o valor das métricas. | MyApp1 |
| `Time Range` | A janela de tempo para ver o gráfico de registo. | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a usar. | Área, Bar, Linha, Torta, Dispersão, Tempo, Bar Categórico |
| `Size` | O tamanho vertical do controlo. | Pequeno, médio, grande ou cheio |
| `Color palette` | A paleta de cores para usar na tabela. Ignorado em modo multi-métrico ou segmentado. | Azul, verde, vermelho, etc. |
| `Legend` | A função de agregação para usar para a lenda. | Soma ou Média de valores ou Max, Min, Primeiro, Último valor |
| `Query` | Qualquer consulta KQL que retorne dados no formato esperado pela visualização do gráfico. | _solicita \| pedidos de séries de pedidos = contagem() predefinição = 0 em tempotamp de ago(1d) to now() passo 1h_ |

### <a name="time-series-charts"></a>Gráficos de séries de tempo

Gráficos de séries de tempo como área, barra, linha, dispersão e tempo podem ser facilmente criados usando o controlo de consultas em Livros de Trabalho. A chave é ter tempo e informação métrica no conjunto de resultados.

#### <a name="simple-time-series"></a>Séries-tempo simples

A consulta abaixo devolve uma tabela com duas colunas: *timetamp* e *Requests*. O controlo de consulta utiliza *o tempotampício* para o eixo X e *solicitações* para o eixo Y.

```kusto
requests
| summarize Requests = count() by bin(timestamp, 1h)
```

[![Screenshot de um simples gráfico de linha de registo de séries de tempo.](./media/workbooks-chart-visualizations/log-chart-line-simple.png)](./media/workbooks-chart-visualizations/log-chart-line-simple.png#lightbox)

#### <a name="time-series-with-multiple-metrics"></a>Séries de tempo com várias métricas

A consulta abaixo devolve uma tabela com três colunas: *timetamp,* *Requests* e *Users*. O controlo de consultas utiliza *o tempotamp* para o eixo X e *solicita*  &  *aos utilizadores* como série separada no eixo Y.

```kusto
requests
| summarize Requests = count(), Users = dcount(user_Id) by bin(timestamp, 1h)
```

[![Screenshot de uma série de tempo com gráfico de linha de registo de várias métricas.](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png)](./media/workbooks-chart-visualizations/log-chart-line-multi-metric.png#lightbox)

#### <a name="segmented-time-series"></a>Séries de tempo segmentadas

A consulta abaixo devolve uma tabela com três colunas: *timetamp*, *Requests* e *RequestName* onde *o RequestName* é uma coluna categórica com os nomes dos pedidos. O controlo de consulta aqui utiliza *o tempotamp para* o eixo X e adiciona uma série por valor de *RequestName*.

```
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

[![Screenshot de um gráfico de linha de registo de séries de tempo segmentada.](./media/workbooks-chart-visualizations/log-chart-line-segmented.png)](./media/workbooks-chart-visualizations/log-chart-line-segmented.png#lightbox)

### <a name="summarize-vs-make-series"></a>Resumir vs. make-series

Os exemplos na secção anterior utilizam o `summarize` operador porque é mais fácil de entender. No entanto, resumir tem uma grande limitação, pois omite a linha de resultados se não houver itens no balde. Pode ter o efeito de deslocar a janela do tempo do gráfico dependendo se os baldes vazios estão na parte da frente ou na parte de trás do intervalo de tempo.

Normalmente é melhor utilizar o `make-series` operador para criar dados da série de tempo, que tem a opção de fornecer valores predefinidos para baldes vazios.

A seguinte consulta utiliza o `make-series` operador.

```kusto
requests
| make-series Requests = count() default = 0 on timestamp from ago(1d) to now() step 1h by RequestName = name
```

A consulta abaixo mostra um gráfico semelhante com o `summarize` operador

```kusto
requests
| summarize Request = count() by bin(timestamp, 1h), RequestName = name
```

Mesmo que o conjunto de resultados subjacente seja diferente. Tudo o que um utilizador tem de fazer é definir a visualização para área, linha, bar ou tempo e os livros de trabalho cuidarão do resto.

[![Screenshot de um gráfico de linha de log feito a partir de uma consulta de séries de fazer](./media/workbooks-chart-visualizations/log-chart-line-make-series.png)](./media/workbooks-chart-visualizations/log-chart-line-make-series.png#lightbox)

### <a name="categorical-bar-chart-or-histogram"></a>Gráfico de barras categórico ou histograma

Os gráficos categóricos permitem que os utilizadores representem uma dimensão ou coluna no eixo X de um gráfico, o que é especialmente útil nos histogramas. O exemplo abaixo mostra a distribuição de pedidos pelo seu código de resultados.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

A consulta devolve duas colunas: *métrica de pedidos* e categoria *resultado.* Cada valor da coluna *Resultado* terá a sua própria barra na tabela com altura proporcional à *métrica pedidos*.

[![Screenshot de um gráfico de barras categóricos para pedidos por código de resultado](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png)](./media/workbooks-chart-visualizations/log-chart-categorical-bar.png#lightbox)

### <a name="pie-charts"></a>Gráficos circulares

Os gráficos de tartes permitem a visualização de proporção numérica. O exemplo abaixo mostra a proporção de pedidos pelo seu código de resultados.

```kusto
requests
| summarize Requests = count() by Result = strcat('Http ', resultCode)
| order by Requests desc
```

A consulta devolve duas colunas: *métrica de pedidos* e categoria *resultado.* Cada valor da coluna *Resultado* obterá a sua própria fatia na tarte com tamanho proporcional à métrica *Solicitações.*

[![Screenshot de um gráfico de torta com fatias representando código de resultado](./media/workbooks-chart-visualizations/log-chart-pie-chart.png)](./media/workbooks-chart-visualizations/log-chart-pie-chart.png#lightbox)

## <a name="metric-charts"></a>Gráficos métricos

A maioria dos recursos Azure emitem dados métricos sobre estado e saúde (por exemplo, utilização de CPU, disponibilidade de armazenamento, contagem de transações de bases de dados, pedidos de aplicações em falha, etc.). Os livros permitem a visualização destes dados como gráficos de séries de tempo.)

### <a name="adding-a-metric-chart"></a>Adicionar um gráfico métrico

O exemplo seguinte mostrará o número de transações numa conta de armazenamento durante a hora anterior. Isto permite ao proprietário do armazenamento ver a tendência de transação e procurar anomalias no comportamento.

1. Mude o livro para editar o modo selecionando o item da barra de ferramentas **Editar.**
2. Utilize a ligação **métrica Adicionar** para adicionar um controlo métrico ao livro.
3. Selecione um tipo de recurso (por exemplo, Conta de Armazenamento), os recursos para o destino, o espaço de nome métrico e o nome, e a agregação a utilizar.
4. Desa ajuste outros parâmetros se necessário - como intervalo de tempo, split-by, visualização, tamanho e paleta de cores.

[![Screenshot do gráfico métrico no modo de edição](./media/workbooks-chart-visualizations/metric-chart.png)](./media/workbooks-chart-visualizations/metric-chart.png#lightbox)

### <a name="metric-chart-parameters"></a>Parâmetros de gráfico métrico

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Resource Type` | O tipo de recurso para o alvo. | Armazenamento ou Máquina Virtual. |
| `Resources` | Um conjunto de recursos para obter o valor das métricas. | MyStorage1 |
| `Namespace` | O espaço com o nome com a métrica. | Bloco de > de armazenamento |
| `Metric` | A métrica para visualizar. | Operações > blob > de armazenamento |
| `Aggregation` | A função de agregação a aplicar à métrica. | Soma, Contagem, Média, etc. |
| `Time Range` | A janela do tempo para ver a métrica. | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a usar. | Área, Bar, Linha, Dispersão, Grelha |
| `Split By` | Opcionalmente divida a métrica numa dimensão. | Transações por Geo tipo |
| `Size` | O tamanho vertical do controlo. | Pequeno, médio ou grande |
| `Color palette` | A paleta de cores para usar na tabela. Ignorado se o `Split by` parâmetro for utilizado. | Azul, verde, vermelho, etc. |

### <a name="examples"></a>Exemplos

Transações divididas pelo nome da API como um gráfico de linha:

[![Screenshot de um gráfico de linha métrica para transações de armazenamento divididos pelo nome API](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png)](./media/workbooks-chart-visualizations/metric-chart-storage-split-line.png#lightbox)

Transações divididas por tipo de resposta como um grande gráfico de barras:

[![Screenshot de um grande gráfico de barras métricas para transações de armazenamento divididos por tipo de resposta](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png)](./media/workbooks-chart-visualizations/metric-chart-storage-bar-large.png#lightbox)

Latência média como um gráfico de dispersão:

[![Screenshot de um gráfico de dispersão métrica para a latência do armazenamento](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png)](./media/workbooks-chart-visualizations/metric-chart-storage-scatter.png#lightbox)

## <a name="chart-settings"></a>Definições de gráficos

Os autores podem usar configurações de gráficos para personalizar quais os campos que são usados nos eixos de gráficos, nas unidades do eixo, na formatação personalizada, nas gamas, nos comportamentos de agrupamento, nas lendas e nas cores da série.

### <a name="the-settings-tab"></a>O separador de definições

Os separadores de definições controlam:

- As definições do eixo, incluindo quais os campos, formatação personalizada que permite aos utilizadores definir o número formando os valores do eixo e as gamas personalizadas.
- Definições de agrupamento, incluindo qual o campo, os limites antes da criação de um grupo "Outros".
- Definições de lendas, incluindo métricas de exibição (nome da série, cores e números) na parte inferior, e/ou uma lenda (nomes e cores da série).

![Screenshot das definições do gráfico.](./media/workbooks-chart-visualizations/chart-settings.png)

#### <a name="custom-formatting"></a>Formatação personalizada

As opções de formatação de números incluem:

| Opção formatação             | Explicação                                                                                           |
|:---------------------------- |:-------------------------------------------------------------------------------------------------------|
| `Units`                      | As unidades para a coluna - várias opções para percentagem, contagem, tempo, byte, contagem/tempo, bytes/time, etc. Por exemplo, a unidade por um valor de 1234 pode ser configurada para milissegundos e é renderizada como 1.234s.                                  |
| `Style`                      | O formato para torná-lo como - decimal, moeda, por cento.                                               |
| `Show group separator`       | Caixa de verificação para mostrar separadores de grupo. Renderiza 1234 como 1.234 nos EUA.                                    |
| `Minimum integer digits`     | Número mínimo de números inteiros a utilizar (predefinição 1).                                                   |
| `Minimum fractional digits`  | Número mínimo de dígitos fracionados a utilizar (predefinição 0).                                                |
| `Maximum fractional digits`  | Número máximo de dígitos fracionados para usar.                                                            |
| `Minimum significant digits` | Número mínimo de dígitos significativos a utilizar (predefinição 1).                                               |
| `Maximum significant digits` | Número máximo de dígitos significativos a utilizar.                                                           |

![Screenshot das definições do eixo X.](./media/workbooks-chart-visualizations/number-format-settings.png)

### <a name="the-series-tab"></a>O separador da série

O separador de definição da série permite ajustar as etiquetas e cores mostradas para as séries na tabela.

- O `Series name` campo é utilizado para corresponder a uma série nos dados e, se combinado, será apresentada a etiqueta e a cor do visor.
- O `Comment` campo é útil para autores de modelos, uma vez que este comentário pode ser usado por tradutores para localizar as etiquetas de exibição.

![Screenshot das definições da série.](./media/workbooks-chart-visualizations/series-settings.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como criar um azulejo em livros de [trabalho.](workbooks-tile-visualizations.md)
- Saiba como criar [livros interativos.](workbooks-interactive.md)