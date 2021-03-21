---
title: Visualizações da grelha de trabalho do Azure Monitor
description: Saiba mais sobre todas as visualizações da grelha de trabalho do Azure Monitor.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: lagayhar
ms.openlocfilehash: 114245030ba2356861b83a580f103d3b84c0eae3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100615612"
---
# <a name="grid-visualizations"></a>Visualizações de grelha

As redes ou tabelas são uma forma comum de apresentar dados aos utilizadores. Os livros de trabalho permitem que os utilizadores modelem individualmente as colunas da grelha para fornecer uma UI rica para os seus relatórios.

O exemplo abaixo mostra uma grelha que combina ícones, máscaras de calor e barras de faísca para apresentar informações complexas. O livro também fornece triagem, uma caixa de pesquisa e um botão de ir-to-analytics.

[![Screenshot da grelha baseada em log](./media/workbooks-grid-visualizations/grid.png)](./media/workbooks-grid-visualizations/grid.png#lightbox)

## <a name="adding-a-log-based-grid"></a>Adicionar uma grelha baseada em log

1. Mude o livro para editar o modo clicando no item da barra de ferramentas **Editar.**
2. Utilize o link **de consulta Adicionar** para adicionar um controlo de consulta de registo ao livro de trabalho.
3. Selecione o tipo de consulta como **Log**, tipo de recurso (por exemplo, Insights de Aplicação) e os recursos a direcionar.
4. Utilize o editor de consulta para introduzir o KQL para a sua análise (por exemplo, VMs com memória abaixo de um limiar)
5. Definir a visualização para **Grid**
6. Desa ajuste outros parâmetros se necessário - como intervalo de tempo, tamanho, paleta de cores e lenda.

[![Screenshot da consulta de grelha baseada em log](./media/workbooks-grid-visualizations/grid-query.png)](./media/workbooks-grid-visualizations/grid-query.png#lightbox)

## <a name="log-chart-parameters"></a>Parâmetros do gráfico de registo

| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `Query Type` | O tipo de consulta a utilizar. | Log, Azure Resource Graph, etc. |
| `Resource Type` | O tipo de recurso para o alvo. | Insights de Aplicação, Log Analytics ou Azure-first |
| `Resources` | Um conjunto de recursos para obter o valor das métricas. | MyApp1 |
| `Time Range` | A janela de tempo para ver o gráfico de registo. | Última hora, últimas 24 horas, etc. |
| `Visualization` | A visualização a usar. | Grelha |
| `Size` | O tamanho vertical do controlo. | Pequeno, médio, grande ou cheio |
| `Query` | Qualquer consulta KQL que retorne dados no formato esperado pela visualização do gráfico. | _pedidos \| resumir Pedidos = contagem() por nome_ |

## <a name="simple-grid"></a>Grelha Simples

Os livros de trabalho podem tornar os resultados do KQL como uma tabela simples. A grelha abaixo mostra a contagem de pedidos e utilizadores únicos por tipo de pedidos numa aplicação.

```kusto
requests
| where name !endswith('.eot')
| summarize Requests = count(), Users = dcount(user_Id) by name
| order by Requests desc
```

[![Screenshot de uma grelha baseada em log no modo de edição](./media/workbooks-grid-visualizations/log-chart-simple-grid.png)](./media/workbooks-grid-visualizations/log-chart-simple-grid.png#lightbox)

## <a name="grid-styling"></a>Estilo de grelha

Enquanto uma tabela simples mostra dados, é difícil de ler e insights nem sempre serão aparentes. O modelar a grelha pode ajudar a facilitar a leitura e a interpretação dos dados.

Abaixo está a mesma grelha da secção anterior, estilo de máscaras de calor.

[![Screenshot de uma grelha baseada em log com colunas modeladas como heatmaps](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png)](./media/workbooks-grid-visualizations/log-chart-grid-heatmap.png#lightbox)

Aqui está a mesma grelha estilo barras: [ ![ Screenshot de uma grelha baseada em log com colunas estilo como barras](./media/workbooks-grid-visualizations/log-chart-grid-bar.png)](./media/workbooks-grid-visualizations/log-chart-grid-bar.png#lightbox)

### <a name="styling-a-grid-column"></a>Estilizar uma coluna de grelha

1. Selecione o botão **de definição de coluna** na barra de ferramentas de controlo de consultas.
2. Nas *definições* da coluna Editar, selecione a coluna para o estilo.
3. Escolha um renderizador de colunas (por exemplo, mapa térmico, barra, barra por baixo, etc.) e definições relacionadas para modelar a sua coluna.

Abaixo está um exemplo que modela a coluna *Request* como um bar:

[![Screenshot de uma grelha baseada em log com coluna de pedidos estilo como barra.](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png)](./media/workbooks-grid-visualizations/log-chart-grid-column-settings-start.png#lightbox)

### <a name="column-renderers"></a>Renderizadores de colunas

| Renderizador de colunas | Explicação | Opções Adicionais |
|:------------- |:-------------|:-------------|
| `Automatic` | O padrão - utiliza o renderizador mais adequado com base no tipo de coluna.  |  |
| `Text` | Torna os valores da coluna como texto. | |
| `Right Aligned` | Semelhante ao texto, exceto que está alinhado. | |
| `Date/Time` | Torna uma cadeia de tempo de data legível. | |
| `Heatmap` | Colore as células da grelha com base no valor da célula. | Paleta de cores e valor min/max usado para dimensionamento. |
| `Bar` | Faz uma barra ao lado da célula com base no valor da célula. | Paleta de cores e valor min/max usado para dimensionamento. |
| `Bar underneath` | Faz uma barra perto do fundo da célula com base no valor da célula. | Paleta de cores e valor min/max usado para dimensionamento. |
| `Composite bar` | Torna-se uma barra composta utilizando as colunas especificadas nessa linha. Consulte [a Barra Composta](workbooks-composite-bar.md) para mais detalhes. | Colunas com cores correspondentes para tornar a barra e um rótulo para exibir na parte superior da barra. |
| `Spark bars` | Torna uma barra de faísca na célula com base nos valores de uma matriz dinâmica na célula. Por exemplo, a coluna Trend a partir da imagem no topo. | Paleta de cores e valor min/max usado para dimensionamento. |
| `Spark lines` | Faz uma linha de faísca na célula com base nos valores de uma matriz dinâmica na célula. | Paleta de cores e valor min/max usado para dimensionamento. |
| `Icon` | Torna os ícones baseados nos valores de texto na célula. Os valores apoiados incluem: `cancelled` `critical` , , `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` , `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` , `Blank`|  |
| `Link` | Torna um link que quando clicado ou executa uma ação configurável. Utilize isto se *quiser que* o item seja apenas um link.  Qualquer um dos outros tipos *também* pode ser um link utilizando a `Make this item a link` definição. Para mais informações consulte [Link Actions](#link-actions) abaixo. |  |
| `Location` | Torna um nome amigável da região de Azure com base em uma região ids. |  |
| `Resource type` | Torna uma cadeia de tipo de recurso amigável baseada num id tipo de recurso  |  |
| `Resource` | Torna um nome de recurso amigável e link baseado em um id de recursos  | Opção para mostrar o ícone do tipo de recurso  |
| `Resource group` | Torna um nome de grupo de recursos amigável e link com base num id de grupo de recursos. Se o valor da célula não for um grupo de recursos, será convertido para um.  | Opção para mostrar o ícone do grupo de recursos  |
| `Subscription` | Torna um nome de subscrição amigável e link com base num id de subscrição.  se o valor da célula não for uma subscrição, será convertido para um.  | Opção para mostrar o ícone de subscrição.  |
| `Hidden` | Esconde a coluna na grelha. Útil quando a consulta padrão devolve mais colunas do que o necessário, mas um projeto-away não é desejado |  |

### <a name="link-actions"></a>Ações de ligação

Se o `Link` renderizador for selecionado ou se o item for selecionado uma caixa de *verificação de ligação,* então o autor pode configurar uma ação de ligação que ocorrerá na seleção da célula. O THis costuma levar o utilizador a outra visão com contexto proveniente da célula ou pode abrir um url.

### <a name="custom-formatting"></a>Formatação personalizada

Os livros de trabalho também permitem que os utilizadores dediformem o número de valores das suas células. Podem fazê-lo clicando na caixa de *verificação de formatação personalizada* quando disponível.

| Opção formatação | Explicação |
|:------------- |:-------------|
| `Units` | As unidades para a coluna - várias opções para percentagem, contagem, tempo, byte, contagem/tempo, bytes/time, etc. Por exemplo, a unidade por um valor de 1234 pode ser configurada para milissegundos e é renderizada como 1.234 s. |
| `Style` | O formato para torná-lo como - decimal, moeda, por cento. |
| `Show group separator` | Caixa de verificação para mostrar separadores de grupo. Renderiza 1234 como 1.234 nos EUA. |
| `Minimum integer digits` | Número mínimo de números inteiros a utilizar (predefinição 1). |
| `Minimum fractional digits` | Número mínimo de dígitos fracionados a utilizar (predefinição 0).  |
| `Maximum fractional digits` | Número máximo de dígitos fracionados para usar. |
| `Minimum significant digits` | Número mínimo de dígitos significativos a utilizar (predefinição 1). |
| `Maximum significant digits` | Número máximo de dígitos significativos a utilizar. |
| `Custom text for missing values` | Quando um ponto de dados não tiver um valor, mostre este texto personalizado em vez de um vazio. |

### <a name="custom-date-formatting"></a>Formatação de data personalizada

Quando o autor tiver especificado que uma coluna está definida para o renderizador data/hora, o autor pode especificar opções de formatação de datas personalizadas utilizando a caixa de *verificação de formatação de datas personalizadas.*

| Opção formatação | Explicação |
|:------------- |:-------------|
| `Style` | O formato para tornar uma data como formatos curtos, longos, completos, ou um tempo como formatos curtos ou longos. |
| `Show time as` | Permite ao autor decidir entre mostrar a hora no horário local (padrão), ou como UTC. Dependendo do estilo de formato de data selecionado, a informação UTC/fuso horário não pode ser apresentada. |

## <a name="custom-column-width-setting"></a>Definição de largura de coluna personalizada

O autor pode personalizar a largura de qualquer coluna na grelha utilizando o campo *de largura da coluna personalizada* nas *definições de colunas*.

![Screenshot das definições da coluna com o campo de largura da coluna personalizado indicado numa caixa vermelha](./media/workbooks-grid-visualizations/custom-column-width-setting.png)

Se o campo for deixado preto, a largura será automaticamente determinada com base no número de caracteres na coluna e no número de colunas visíveis. A unidade predefinida é "ch" (caracteres).

A seleção do botão azul **(Largura de Corrente)** na etiqueta preencherá o campo de texto com a largura de corrente da coluna selecionada. Se um valor estiver presente no campo de largura personalizado sem unidade de medição, então o padrão será utilizado.

As unidades de medição disponíveis são:

| Unidade de medição | Definição           |
|:--------------------|:---------------------|
| ch                  | caracteres (padrão) |
| px                  | pixels               |
| fr                  | unidades fracionárias     |
| %                   | percentage           |

Validação de entrada - se a validação falhar, uma mensagem de orientação vermelha aparecerá abaixo do campo, mas o utilizador ainda pode aplicar a largura. Se um valor estiver presente na entrada, será analisado. Se não for encontrada uma unidade de medida válida, o padrão será utilizado.

Não há largura mínima/máxima, uma vez que esta é deixada à discrição do autor. O campo de largura da coluna personalizada é desativado para colunas ocultas.

## <a name="examples"></a>Exemplos

### <a name="spark-lines-and-bar-underneath"></a>Linhas de faísca e barra por baixo

O exemplo abaixo mostra as contagens de pedidos e a sua tendência por nome de pedido.

```kusto
requests
| make-series Trend = count() default = 0 on timestamp from ago(1d) to now() step 1h by name
| project-away timestamp
| join kind = inner (requests
    | summarize Requests = count() by name
    ) on name
| project name, Requests, Trend
| order by Requests desc
```

[![Screenshot de uma grelha baseada em log com uma barra por baixo e uma linha de faísca](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png)](./media/workbooks-grid-visualizations/log-chart-grid-spark-line.png#lightbox)

### <a name="heatmap-with-shared-scales-and-custom-formatting"></a>Mapa de calor com escamas partilhadas e formatação personalizada

Este exemplo mostra várias métricas de duração do pedido e as suas contagens. O renderizador de mapas térmicos utiliza os valores mínimos definidos em configurações ou calcula um valor mínimo e máximo para a coluna, e atribui uma cor de fundo da paleta selecionada para a célula com base no valor da célula relativamente ao valor mínimo e máximo da coluna.

```
requests
| summarize Mean = avg(duration), (Median, p80, p95, p99) = percentiles(duration, 50, 80, 95, 99), Requests = count() by name
| order by Requests desc
```

[![Screenshot de uma grelha baseada em log com um mapa de calor com uma escala partilhada através de colunas](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale.png#lightbox)

No exemplo acima, uma paleta partilhada (verde ou vermelho) e escala é usada para colorir as colunas (média, mediana, p80, p95 e p99). Uma paleta separada (azul) utilizada para a coluna de pedido.

#### <a name="shared-scale"></a>Escala partilhada

Para obter uma escala partilhada:

1. Utilize expressões regulares para selecionar mais de uma coluna para aplicar uma definição. Por exemplo, desa um nome de coluna `Mean|Median|p80|p95|p99` para selecionar todos.
2. Elimine as definições predefinidos para as colunas individuais.

Isto fará com que a nova definição multi-colunas aplique as suas definições para incluir uma escala partilhada.

[![Screenshot de uma definição de grelha baseada em log para obter uma escala partilhada através de colunas](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png)](./media/workbooks-grid-visualizations/log-chart-grid-shared-scale-settings.png#lightbox)

### <a name="icons-to-represent-status"></a>Ícones para representar o estado

O exemplo abaixo mostra o estado personalizado dos pedidos com base na duração do p95.

```
requests
| summarize p95 = percentile(duration, 95) by name
| order by p95 desc
| project Status = case(p95 > 5000, 'critical', p95 > 1000, 'error', 'success'), name, p95
```

[![Screenshot de uma grelha baseada em log com um mapa de calor com uma escala partilhada através de colunas usando a consulta acima.](./media/workbooks-grid-visualizations/log-chart-grid-icons.png)](./media/workbooks-grid-visualizations/log-chart-grid-icons.png#lightbox)

Os nomes dos ícones apoiados incluem: `cancelled` `critical` , `disabled` `error` `failed` `info` `none` `pending` `stopped` `question` `success` `unknown` `warning` `uninitialized` `resource` `up` `down` `left` `right` `trendup` `trenddown` `4` `3` `2` `1` `Sev0` `Sev1` `Sev2` `Sev3` `Sev4` `Fired` `Resolved` `Available` `Unavailable` `Degraded` `Unknown` , `Blank`

### <a name="using-thresholds-with-links"></a>Utilização de limiares com ligações

As instruções abaixo mostrar-lhe-ão como utilizar limiares com links para atribuir ícones e abrir diferentes livros de trabalho. Cada ligação na grelha abrirá um modelo de livro diferente para esse recurso Application Insights.

1. Mude o livro para editar o modo selecionando editar o item da barra de ferramentas *Editar.*
2. **Selecione Adicionar** e adicione *consulta*.
3. Altere a *fonte de dados* para "JSON" e *Visualização* para "Grid".
4. Insira a seguinte consulta.

```json
[ 
    { "name": "warning", "link": "Community-Workbooks/Performance/Performance Counter Analysis" },
    { "name": "info", "link": "Community-Workbooks/Performance/Performance Insights" },
    { "name": "error", "link": "Community-Workbooks/Performance/Apdex" }
]
```

5. Fazer consulta.
6. Selecione **Definições de colunas** para abrir as definições.
7. Selecione "nome" de *Colunas*.
8. Sob *o renderizador da Coluna,* escolha "Limiares".
9.  Introduza e escolha as *seguintes Definições de limiar*.
   
    | Operador | Valor   | Ícones   |
    |----------|---------|---------|
    | ==       | aviso | Aviso |
    | ==       | erro   | Com falhas  |

    ![Screenshot do separador de definições de coluna Editar com as definições acima.](./media/workbooks-grid-visualizations/column-settings.png)

    Mantenha a linha predefinida como está. Pode introduzir o sms que quiser. A coluna Text toma um formato de corda como uma entrada e povoa-a com o valor da coluna e unidade, se especificado. Por exemplo, se o aviso for o valor da coluna o texto pode ser " {0} {1} link!", será apresentado como "ligação de aviso!".
10. Selecione o Artigo fazer deste item uma caixa *de ligação.*
    1. Em *Vista para abrir,* escolha "Workbook (Modelo)".
    2. No *valor link vem de,* escolha "link".
    3. Selecione o link Open na caixa *de lâmina de contexto.*
    4. Escolha as seguintes definições nas *Definições de Link do Livro*
        1. No *Modelo Id vem,* escolha "Coluna".
        2. Em *Coluna* escolha "link".

    ![Screenshot das definições de ligação com as definições acima.](./media/workbooks-grid-visualizations/make-this-item-a-link.png)

11. Selecione "link" de *Colunas*. Em Definições ao lado *do renderizador de colunas,* selecione **(Ocultar coluna)**.
1. Para alterar o nome de visualização da coluna "nome" selecione o **separador Etiquetas.** Na linha com "nome" como *iD da coluna*, em *Etiqueta de coluna introduza o nome que deseja visualizar.
2. Selecione **Aplicar**

![Screenshot de limiares na grelha com as definições acima](./media/workbooks-grid-visualizations/thresholds-workbooks-links.png)

## <a name="fractional-units-percentages"></a>Percentagens de unidades fracionadas

A unidade fracionada (fr) é uma unidade dinâmica de medição comumente utilizada em vários tipos de grelhas. À medida que o tamanho/resolução da janela muda, a largura fr também muda.

A imagem abaixo mostra uma tabela com oito colunas com 1fr de largura cada e todas as larguras iguais. À medida que o tamanho da janela muda, a largura de cada coluna muda proporcionalmente.

[![Screenshot de colunas na grelha com valor de largura de coluna de 1fr cada](./media/workbooks-grid-visualizations/custom-column-width-fr.png)](./media/workbooks-grid-visualizations/custom-column-width-fr.png#lightbox)

A imagem abaixo mostra a mesma tabela, exceto que a primeira coluna está definida para 50% de largura. Isto irá definir a coluna para metade da largura total da grelha dinamicamente. O redimensionamento da janela continuará a manter a largura de 50%, a menos que o tamanho da janela fique demasiado pequeno. Estas colunas dinâmicas têm uma largura mínima com base no seu conteúdo. Os restantes 50% da rede são divididos pelas oito unidades fracionadas totais. A coluna "tipo" abaixo está definida para 2fr, por isso ocupa um quarto do espaço restante. Como as outras colunas são 1fr cada, cada uma toma um oitavo da metade direita da grelha.

[![Screenshot de colunas em grelha com 1 valor de largura de coluna de 50% e o resto como 1fr cada](./media/workbooks-grid-visualizations/custom-column-width-fr2.png)](./media/workbooks-grid-visualizations/custom-column-width-fr2.png#lightbox)

Combinando as larguras fr, %, px e ch é possível e funciona de forma semelhante aos exemplos anteriores. As larguras definidas pelas unidades estáticas (ch e px) são constantes duras que não se alteram mesmo que a janela/resolução seja alterada. As colunas definidas em % assumirão a sua percentagem com base na largura total da grelha (pode não ser exata devido a larguras mínimas anteriormente). As colunas definidas com fr apenas dividirão o espaço restante da grelha com base no número de unidades fracionadas que são atribuídas.

[![Screenshot de colunas na grelha com variedade de diferentes unidades de largura utilizadas](./media/workbooks-grid-visualizations/custom-column-width-fr3.png)](./media/workbooks-grid-visualizations/custom-column-width-fr3.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* Saiba como criar uma [árvore em livros.](workbooks-tree-visualizations.md)
* Saiba como criar [parâmetros de texto de livro.](workbooks-text.md)