---
title: Um guia de referência para os blocos do designer de exibição no Azure Monitor | Microsoft Docs
description: Usando o designer de exibição no Azure Monitor, você pode criar exibições personalizadas que são exibidas na portal do Azure e conter uma variedade de visualizações nos dados no espaço de trabalho Log Analytics. Este artigo é um guia de referência para as configurações dos blocos que estão disponíveis em suas exibições personalizadas.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 4141a4728ef5a7dd8896e4e3f21ee6d1d4dce958
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931962"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Guia de referência para exibir blocos do designer no Azure Monitor
Usando o designer de exibição no Azure Monitor, você pode criar uma variedade de exibições personalizadas na portal do Azure que podem ajudá-lo a Visualizar dados em seu espaço de trabalho do Log Analytics. Este artigo é um guia de referência para as configurações dos blocos que estão disponíveis em suas exibições personalizadas.

Para obter mais informações sobre o designer de exibição, consulte:

* [Designer de exibição](view-designer.md): fornece uma visão geral do designer de exibição e dos procedimentos para criar e editar exibições personalizadas.
* [Referência de parte de visualização](view-designer-parts.md): fornece um guia de referência para as configurações para as partes de visualização que estão disponíveis em suas exibições personalizadas.


Os blocos do designer de exibição disponíveis são descritos na tabela a seguir:  

| Bloco | Descrição |
|:--- |:--- |
| [Automática](#number-tile) |A contagem de registros de uma consulta. |
| [Dois números](#two-numbers-tile) |As contagens de registros de duas consultas diferentes. |
| [Rosca](#donut-tile) | Um gráfico baseado em uma consulta, com um valor de resumo no centro. |
| Gráfico de linhas e texto explicativo | Um gráfico de linhas baseado em uma consulta e um texto explicativo com um valor de resumo. |
| [Gráfico de linhas](#line-chart-tile) |Um gráfico de linhas baseado em uma consulta. |
| [Duas linhas do tempo](#two-timelines-tile) | Um gráfico de colunas com duas séries, cada uma com base em uma consulta separada. |

As seções a seguir descrevem os tipos de bloco e suas propriedades em detalhes.

> [!NOTE]
> Blocos em exibições são baseados em [consultas de log](../log-query/log-query-overview.md) em seu espaço de trabalho log Analytics. Atualmente, eles não dão suporte a [consultas entre recursos](../log-query/cross-workspace-query.md) para recuperar dados de Application insights.

## <a name="number-tile"></a>Bloco número
O bloco **número** exibe a contagem de registros de uma consulta de log e um rótulo.

![Bloco número](media/view-designer-tiles/tile-number.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido na parte superior do bloco. |
| Descrição |O texto que é exibido sob o nome do bloco. |
| **Bloco** | |
| Legenda |O texto que é exibido sob o valor. |
| Consulta |A consulta executada. A contagem dos registros retornados pela consulta é exibida. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione este link se a verificação de fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, você usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados ficam disponíveis. |
| Consulta |A consulta executada para determinar se os dados estão disponíveis para a exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que será exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status de *avaliação em execução* será exibida. |


## <a name="two-numbers-tile"></a>Bloco de dois números
Esse bloco exibe a contagem de registros de duas consultas de log diferentes e um rótulo para cada uma.

![Bloco de dois números](media/view-designer-tiles/tile-two-numbers.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido na parte superior do bloco. |
| Descrição |O texto que é exibido sob o nome do bloco. |
| **Primeiro bloco** | |
| Legenda |O texto que é exibido sob o valor. |
| Consulta |A consulta executada. A contagem dos registros retornados pela consulta é exibida. |
| **Segundo bloco** | |
| Legenda |O texto que é exibido sob o valor. |
| Consulta |A consulta executada. A contagem dos registros retornados pela consulta é exibida. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione este link se a verificação de fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, você usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados ficam disponíveis. |
| Consulta |A consulta executada para determinar se os dados estão disponíveis para a exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que será exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status de *avaliação em execução* será exibida. |


## <a name="donut-tile"></a>Bloco de rosca
O bloco de **rosca** exibe um único número que resume uma coluna de valor em uma consulta de log. A rosca exibe graficamente os resultados dos três principais registros.

![Bloco de rosca](media/view-designer-tiles/tile-donut.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido na parte superior do bloco. |
| Descrição |O texto que é exibido sob o nome do bloco. |
| **Rosca** | |
| Consulta |A consulta executada para a rosca. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Essa consulta normalmente usa a palavra-chave *Measure* para resumir os resultados. |
| **Rosca** |**Centro de >** |
| Texto |O texto que é exibido sob o valor dentro da rosca. |
| Operação |A operação executada na propriedade Value para resumir como um único valor.<ul><li>Sum: Adicione os valores de todos os registros com o valor da propriedade.</li><li>Porcentagem: porcentagem dos valores somados de registros com o valor da propriedade em comparação com os valores somados de todos os registros.</li></ul> |
| Valores de resultado usados na operação do centro |Opcionalmente, selecione o sinal de adição (+) para adicionar um ou mais valores. Os resultados da consulta são limitados a registros com os valores de propriedade que você especificar. Se nenhum valor for adicionado, todos os registros serão incluídos na consulta. |
| **Rosca** |**> Opções adicionais** |
| Cores |A cor que é exibida para cada uma das três propriedades principais. Para especificar cores alternativas para valores de propriedade específicos, use o *mapeamento de cores avançado*. |
| Mapeamento de cores avançado |Exibe uma cor que representa valores de propriedade específicos. Se o valor especificado estiver nos três principais, a cor alternativa será exibida em vez da cor padrão. Se a propriedade não estiver nos três principais, a cor não será exibida. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione este link se a verificação de fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, você usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados ficam disponíveis. |
| Consulta |A consulta executada para determinar se os dados estão disponíveis para a exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que será exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status de *avaliação em execução* será exibida. |


## <a name="line-chart-tile"></a>Bloco do gráfico de linhas
Esse bloco é um gráfico de linhas que exibe várias séries de uma consulta de log ao longo do tempo. 

![Gráfico de linhas e bloco de texto explicativo](media/view-designer-tiles/tile-line-chart.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido na parte superior do bloco. |
| Descrição |O texto que é exibido sob o nome do bloco. |
| **Gráfico de linhas** | |
| Consulta |A consulta que é executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Essa consulta normalmente usa a palavra-chave *Measure* para resumir os resultados. Se a consulta usar a palavra-chave *Interval* , o eixo x usará esse intervalo de tempo. Se a consulta não usar a palavra-chave *Interval* , o eixo x usará intervalos por hora. |
| **Gráfico de linhas** |**Eixo Y >** |
| Usar escala logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores retornados pela consulta. Essas informações são usadas para exibir rótulos no gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O **tipo de unidade** especifica a categoria da unidade e define os valores do **tipo de unidade atual** que estão disponíveis. Se você selecionar um valor em **converter para** , os valores numéricos serão convertidos do tipo de **unidade atual** para o tipo **converter para** . |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo do tipo de *unidade* . Se nenhum rótulo for especificado, somente o tipo de *unidade* será exibido. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione este link se a verificação de fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, você usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados ficam disponíveis. |
| Consulta |A consulta executada para determinar se os dados estão disponíveis para a exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que será exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status de *avaliação em execução* será exibida. |


## <a name="line-chart-and-callout-tile"></a>Gráfico de linhas e bloco de texto explicativo
Esse bloco tem um gráfico de linhas que exibe várias séries de uma consulta de log ao longo do tempo e um texto explicativo com um valor resumido. 

![Gráfico de linhas e bloco de texto explicativo](media/view-designer-tiles/tile-line-chart-callout.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido na parte superior do bloco. |
| Descrição |O texto que é exibido sob o nome do bloco. |
| **Gráfico de linhas** | |
| Consulta |A consulta que é executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Essa consulta normalmente usa a palavra-chave *Measure* para resumir os resultados. Se a consulta usar a palavra-chave *Interval* , o eixo x usará esse intervalo de tempo. Se a consulta não usar a palavra-chave *Interval* , o eixo x usará intervalos por hora. |
| **Gráfico de linhas** |**> Texto explicativo** |
| Título do balão | O texto que é exibido acima do valor do texto explicativo. |
| Nome da série |O valor da propriedade da série a ser usado como o valor do texto explicativo. Se nenhuma série for fornecida, todos os registros da consulta serão usados. |
| Operação |A operação executada na propriedade Value para resumir como um valor único para o texto explicativo.<ul><li>Média: a média dos valores de todos os registros.</li><li>Contagem: a contagem de todos os registros retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Max: o valor máximo dos intervalos que são incluídos no gráfico.</li><li>Min: o valor mínimo dos intervalos que são incluídos no gráfico.</li><li>Sum: a soma dos valores de todos os registros.</li></ul> |
| **Gráfico de linhas** |**Eixo Y >** |
| Usar escala logarítmica |Selecione este link para usar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Essas informações são usadas para exibir rótulos de gráfico que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo de *unidade* especifica a categoria da unidade e define os valores de tipo de *unidade atual* disponíveis. Se você selecionar um valor em *converter para*, os valores numéricos serão convertidos do tipo de *unidade atual* para o tipo *converter para* . |
| Rótulo personalizado |O texto que é exibido para o eixo y ao lado do rótulo do tipo de *unidade* . Se nenhum rótulo for especificado, somente o tipo de *unidade* será exibido. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione este link se a verificação de fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, você usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados ficam disponíveis. |
| Consulta |A consulta executada para determinar se os dados estão disponíveis para a exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que será exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status de *avaliação em execução* será exibida. |


## <a name="two-timelines-tile"></a>Bloco de duas linhas do tempo
O bloco **dois cronogramas** exibe os resultados de duas consultas de log ao longo do tempo como gráficos de colunas. Um texto explicativo é exibido para cada série. 

![Bloco de duas linhas do tempo](media/view-designer-tiles/tile-two-timelines.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido na parte superior do bloco. |
| Descrição |O texto que é exibido sob o nome do bloco. |
| Primeiro gráfico | |
| Legenda |O texto que é exibido sob o texto explicativo para a primeira série. |
| Cor |A cor que é usada para as colunas na primeira série. |
| Consulta de gráfico |A consulta que é executada para a primeira série. A contagem dos registros em cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação executada na propriedade Value para resumir como um valor único para o texto explicativo.<ul><li>Média: a média dos valores de todos os registros.</li><li>Contagem: a contagem de todos os registros retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Max: o valor máximo dos intervalos que são incluídos no gráfico.</li></ul> |
| **Segundo gráfico** | |
| Legenda |O texto que é exibido sob o texto explicativo para a segunda série. |
| Cor |A cor que é usada para as colunas na segunda série. |
| Consulta de gráfico |A consulta que é executada para a segunda série. A contagem dos registros em cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação executada na propriedade Value para resumir como um valor único para o texto explicativo.<ul><li>Média: a média dos valores de todos os registros.</li><li>Contagem: a contagem de todos os registros retornados pela consulta.</li><li>Última amostra: o valor do último intervalo que é incluído no gráfico.</li><li>Max: o valor máximo dos intervalos que são incluídos no gráfico. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione este link se a verificação de fluxo de dados deve ser habilitada para o bloco. Essa abordagem fornecerá uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, você usa a abordagem para fornecer uma mensagem durante o período temporário quando a exibição é instalada e os dados ficam disponíveis. |
| Consulta |A consulta executada para determinar se os dados estão disponíveis para a exibição. Se a consulta não retornar nenhum resultado, uma mensagem será exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que será exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se você não fornecer nenhuma mensagem, uma mensagem de status de *avaliação em execução* será exibida. |


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [consultas de log](../log-query/log-query-overview.md) para dar suporte às consultas em blocos.
* Adicione [partes de visualização](view-designer-parts.md) à sua exibição personalizada.
