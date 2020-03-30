---
title: Um guia de referência para os azulejos do View Designer no Monitor Azure [ Microsoft Docs
description: Ao utilizar o View Designer no Monitor Azure, pode criar vistas personalizadas que são exibidas no portal Azure e conter uma variedade de visualizações sobre dados no espaço de trabalho log Analytics. Este artigo é um guia de referência para as definições para os azulejos que estão disponíveis nas suas vistas personalizadas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: 0320be3a2cfbb96367799577a6e56bcf5da87dcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658511"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Guia de referência para ver azulejos designer no Monitor Azure
Ao utilizar o View Designer no Monitor Azure, pode criar uma variedade de vistas personalizadas no portal Azure que podem ajudá-lo a visualizar dados no seu espaço de trabalho Log Analytics. Este artigo é um guia de referência para as definições para os azulejos que estão disponíveis nas suas vistas personalizadas.

Para mais informações sobre o View Designer, consulte:

* [Ver Designer](view-designer.md): Fornece uma visão geral do View Designer e procedimentos para criar e editar vistas personalizadas.
* [Referência da parte](view-designer-parts.md)de visualização : Fornece um guia de referência para as definições para as peças de visualização que estão disponíveis nas suas vistas personalizadas.


Os azulejos disponíveis do View Designer são descritos na tabela seguinte:  

| Mosaico | Descrição |
|:--- |:--- |
| [Número](#number-tile) |A contagem de registos de uma consulta. |
| [Dois números](#two-numbers-tile) |As contagens de registos de duas consultas diferentes. |
| [Donut](#donut-tile) | Um gráfico que é baseado numa consulta, com um valor resumo no centro. |
| Gráfico de linha e chamada | Um gráfico de linha que é baseado numa consulta, e uma chamada com um valor resumo. |
| [Gráfico de linhas](#line-chart-tile) |Um gráfico de linha baseado numa consulta. |
| [Duas cronologias](#two-timelines-tile) | Um gráfico de colunas com duas séries, cada uma baseada numa consulta separada. |

As secções seguintes descrevem os tipos de azulejos e as suas propriedades em detalhe.

> [!NOTE]
> Os azulejos em vistas [baseiam-se](../log-query/log-query-overview.md) em consultas de registo no seu espaço de trabalho Log Analytics. Atualmente, não suportam [consultas de recursos cruzados](../log-query/cross-workspace-query.md) para obter dados de Insights de Aplicação.

## <a name="number-tile"></a>Azulejo sumido
O azulejo **Número** apresenta tanto a contagem de registos de uma consulta de registo como de uma etiqueta.

![Azulejo sumido](media/view-designer-tiles/tile-number.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que está exposto no topo do azulejo. |
| Descrição |O texto que está exposto sob o nome do azulejo. |
| **Azulejo** | |
| Legenda |O texto que é exibido sob o valor. |
| Consulta |A consulta que é executada. A contagem dos registos que são devolvidos pela consulta é exibida. |
| **Avançado** |**verificação> do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolver resultados, uma mensagem é exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado de *avaliação de desempenho.* |


## <a name="two-numbers-tile"></a>Dois números de azulejo
Este azulejo apresenta a contagem de registos de duas consultas de registo diferentes e uma etiqueta para cada um.

![Dois números de azulejo](media/view-designer-tiles/tile-two-numbers.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que está exposto no topo do azulejo. |
| Descrição |O texto que está exposto sob o nome do azulejo. |
| **Primeiro azulejo** | |
| Legenda |O texto que é exibido sob o valor. |
| Consulta |A consulta que é executada. A contagem dos registos que são devolvidos pela consulta é exibida. |
| **Segundo azulejo** | |
| Legenda |O texto que é exibido sob o valor. |
| Consulta |A consulta que é executada. A contagem dos registos que são devolvidos pela consulta é exibida. |
| **Avançado** |**verificação> do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolver resultados, uma mensagem é exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado de *avaliação de desempenho.* |


## <a name="donut-tile"></a>Azulejo de donut
O azulejo **Donut** apresenta um único número que resume uma coluna de valor numa consulta de registo. O donut apresenta graficamente os resultados dos três primeiros registos.

![Azulejo de donut](media/view-designer-tiles/tile-donut.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que está exposto no topo do azulejo. |
| Descrição |O texto que está exposto sob o nome do azulejo. |
| **Donut** | |
| Consulta |A consulta que corre para o donut. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta usa normalmente a palavra-chave de *medida* para resumir resultados. |
| **Donut** |**Centro>** |
| Texto |O texto que é exibido sob o valor dentro do donut. |
| Operação |A operação que é realizada na propriedade de valor para resumir como um único valor.<ul><li>Soma: Adicione os valores de todos os registos com o valor patrimonial.</li><li>Percentagem: Percentagem dos valores resumidos dos registos com o valor patrimonial em comparação com os valores resumidos de todos os registos.</li></ul> |
| Valores de resultadoutilizados no funcionamento do centro |Opcionalmente, selecione o sinal de mais (+) para adicionar um ou mais valores. Os resultados da consulta limitam-se a registos com os valores de propriedade que especifica. Se não forem adicionados valores, todos os registos estão incluídos na consulta. |
| **Donut** |**> opções adicionais** |
| Cores |A cor que é exibida para cada uma das três propriedades de topo. Para especificar cores alternativas para valores de propriedade específicos, utilize *o Advanced Color Mapping*. |
| Mapeamento de cores avançadas |Exibe uma cor que representa valores de propriedade específicos. Se o valor que especifica estiver nos três primeiros, a cor alternativa é exibida em vez da cor padrão. Se a propriedade não estiver entre os três primeiros, a cor não é exibida. |
| **Avançado** |**verificação> do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolver resultados, uma mensagem é exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado de *avaliação de desempenho.* |


## <a name="line-chart-tile"></a>Azulejo de gráfico de linha
Este azulejo é um gráfico de linha que exibe várias séries de uma consulta de log ao longo do tempo. 

![Gráfico de linha e azulejo de chamada](media/view-designer-tiles/tile-line-chart.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que está exposto no topo do azulejo. |
| Descrição |O texto que está exposto sob o nome do azulejo. |
| **Gráfico de linhas** | |
| Consulta |A consulta que corre para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta usa normalmente a palavra-chave de *medida* para resumir resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x utiliza este intervalo de tempo. Se a consulta não usar a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| **Gráfico de linhas** |**> eixo Y** |
| Utilizar escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta. Estas informações são utilizadas para exibir etiquetas no gráfico indicando os tipos de valor e opcionalmente para a conversão dos valores. O **Tipo Unidade** especifica a categoria da unidade e define os valores do Tipo unidade **atual** que estão disponíveis. Se selecionar um valor em **Converter para** então os valores numéricos são convertidos do tipo **Unidade Atual** para o Converter **para** escrever. |
| Etiqueta personalizada |O texto apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada a etiqueta, apenas o tipo *Unidade* é apresentado. |
| **Avançado** |**verificação> do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolver resultados, uma mensagem é exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado de *avaliação de desempenho.* |


## <a name="line-chart-and-callout-tile"></a>Gráfico de linha e azulejo de chamada
Este azulejo tem tanto um gráfico de linha que exibe várias séries de uma consulta de log ao longo do tempo e uma chamada com um valor resumido. 

![Gráfico de linha e azulejo de chamada](media/view-designer-tiles/tile-line-chart-callout.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que está exposto no topo do azulejo. |
| Descrição |O texto que está exposto sob o nome do azulejo. |
| **Gráfico de linhas** | |
| Consulta |A consulta que corre para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta usa normalmente a palavra-chave de *medida* para resumir resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo x utiliza este intervalo de tempo. Se a consulta não usar a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| **Gráfico de linhas** |**> Callout** |
| Título de callout | O texto que é exibido acima do valor de chamada. |
| Nome da série |O valor da propriedade da série a ser usado como o valor de chamada. Se não for fornecida nenhuma série, todos os registos da consulta são utilizados. |
| Operação |A operação que é realizada na propriedade de valor para resumir como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: O valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela.</li><li>Min: O valor mínimo dos intervalos incluídos na tabela.</li><li>Soma: A soma dos valores de todos os registos.</li></ul> |
| **Gráfico de linhas** |**> eixo Y** |
| Utilizar escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Esta informação é utilizada para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *Unidade* especifica a categoria da unidade e define os valores do tipo *Unidade Atual* disponíveis. Se selecionar um valor em *Converter para*, os valores numéricos são convertidos do tipo *Unidade Atual* para o Converter *para* escrever. |
| Etiqueta personalizada |O texto apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada a etiqueta, apenas o tipo *Unidade* é apresentado. |
| **Avançado** |**verificação> do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolver resultados, uma mensagem é exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado de *avaliação de desempenho.* |


## <a name="two-timelines-tile"></a>Dois azulejos de linha do tempo
O azulejo **de duas linhas de tempo** exibe os resultados de duas consultas de registo ao longo do tempo como gráficos de colunas. É apresentado um callout para cada série. 

![Dois azulejos de linha do tempo](media/view-designer-tiles/tile-two-timelines.png)

| Definição | Descrição |
|:--- |:--- |
| Nome |O texto que está exposto no topo do azulejo. |
| Descrição |O texto que está exposto sob o nome do azulejo. |
| Primeira Tabela | |
| Legenda |O texto que é exibido sob o callout para a primeira série. |
| Cor |A cor que é usada para as colunas na primeira série. |
| Consulta de gráfico |A consulta que corre para a primeira série. A contagem dos registos em cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação que é realizada na propriedade de valor para resumir como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: O valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela.</li></ul> |
| **Segundo gráfico** | |
| Legenda |O texto que é exibido sob a chamada para a segunda série. |
| Cor |A cor que é usada para as colunas da segunda série. |
| Consulta de gráfico |A consulta que corre para a segunda série. A contagem dos registos em cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação que é realizada na propriedade de valor para resumir como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: O valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela. |
| **Avançado** |**verificação> do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolver resultados, uma mensagem é exibida no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é exibida se a consulta de verificação de fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado de *avaliação de desempenho.* |


## <a name="next-steps"></a>Passos seguintes
* Aprenda sobre consultas de [log](../log-query/log-query-overview.md) para suportar as consultas em azulejos.
* Adicione peças de [visualização](view-designer-parts.md) à sua vista personalizada.
