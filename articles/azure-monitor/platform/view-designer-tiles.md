---
title: Um guia de referência para os azulejos do View Designer no Azure Monitor Microsoft Docs
description: Ao utilizar o View Designer no Azure Monitor, pode criar vistas personalizadas que são exibidas no portal Azure e conter uma variedade de visualizações de dados no espaço de trabalho Log Analytics. Este artigo é um guia de referência para as definições dos azulejos que estão disponíveis nas suas vistas personalizadas.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2018
ms.openlocfilehash: b73fa9702a95897afbca53e2de70dcabc04a4665
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168514"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Guia de referência para ver azulejos do designer no Azure Monitor
Ao utilizar o View Designer no Azure Monitor, pode criar uma variedade de visualizações personalizadas no portal Azure que podem ajudá-lo a visualizar dados no seu espaço de trabalho Log Analytics. Este artigo é um guia de referência para as definições dos azulejos que estão disponíveis nas suas vistas personalizadas.

Para mais informações sobre o View Designer, consulte:

* [Ver Designer](view-designer.md): Fornece uma visão geral do View Designer e procedimentos para criar e editar vistas personalizadas.
* [Referência da parte de visualização](view-designer-parts.md): Fornece um guia de referência às definições das peças de visualização que estão disponíveis nas suas vistas personalizadas.


Os azulejos disponíveis do View Designer são descritos na tabela seguinte:  

| Mosaico | Descrição |
|:--- |:--- |
| [Number](#number-tile) |A contagem de registos de uma consulta. |
| [Dois números](#two-numbers-tile) |As contagens de registos de duas consultas diferentes. |
| [Anel](#donut-tile) | Um gráfico que é baseado numa consulta, com um valor sumário no centro. |
| Gráfico de linha e chamada | Um gráfico de linha baseado numa consulta, e uma chamada com um valor sumário. |
| [Gráfico de linhas](#line-chart-tile) |Um gráfico de linha baseado numa consulta. |
| [Duas cronologias](#two-timelines-tile) | Um gráfico de coluna com duas séries, cada uma com base numa consulta separada. |

As secções seguintes descrevem os tipos de azulejos e as suas propriedades em detalhe.

> [!NOTE]
> Os azulejos nas vistas são [baseados](../log-query/log-query-overview.md) em consultas de registo no seu espaço de trabalho Log Analytics. Atualmente, não suportam [consultas de recursos cruzados](../log-query/cross-workspace-query.md) para obter dados da Application Insights.

## <a name="number-tile"></a>Azulejo de número
O **azulejo Número** exibe tanto a contagem de registos de uma consulta de registo como uma etiqueta.

![Azulejo de número](media/view-designer-tiles/tile-number.png)

| Definições | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido no topo do azulejo. |
| Descrição |O texto que é mostrado sob o nome de azulejo. |
| **Mosaico** | |
| Legenda |O texto que é mostrado sob o valor. |
| Consulta |A consulta que é executada. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Avançado** |**> verificação do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza-se a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não tiver resultados, é apresentada uma mensagem no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é apresentada se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado *de avaliação* de desempenho. |


## <a name="two-numbers-tile"></a>Dois números azulejos
Este azulejo apresenta a contagem de registos de duas consultas de log diferentes e uma etiqueta para cada um.

![Dois números azulejos](media/view-designer-tiles/tile-two-numbers.png)

| Definições | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido no topo do azulejo. |
| Descrição |O texto que é mostrado sob o nome de azulejo. |
| **Primeiro azulejo** | |
| Legenda |O texto que é mostrado sob o valor. |
| Consulta |A consulta que é executada. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Segundo azulejo** | |
| Legenda |O texto que é mostrado sob o valor. |
| Consulta |A consulta que é executada. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Avançado** |**> verificação do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza-se a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não tiver resultados, é apresentada uma mensagem no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é apresentada se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado *de avaliação* de desempenho. |


## <a name="donut-tile"></a>Azulejo de donut
O **azulejo Donut** apresenta um único número que resume uma coluna de valor numa consulta de log. O donut apresenta graficamente os resultados dos três melhores registos.

![Azulejo de donut](media/view-designer-tiles/tile-donut.png)

| Definições | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido no topo do azulejo. |
| Descrição |O texto que é mostrado sob o nome de azulejo. |
| **Anel** | |
| Consulta |A consulta que é executada para o donut. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a palavra-chave da *medida* para resumir os resultados. |
| **Anel** |** Centro>** |
| Texto |O texto que é mostrado sob o valor dentro do donut. |
| Operação |A operação que é realizada na propriedade de valor para resumi-la como um único valor.<ul><li>Soma: Adicione os valores de todos os registos com o valor da propriedade.</li><li>Percentagem: Percentagem dos valores somados dos registos com o valor patrimonial face aos valores somados de todos os registos.</li></ul> |
| Valores de resultados utilizados na operação central |Opcionalmente, selecione o sinal de mais (+) para adicionar um ou mais valores. Os resultados da consulta estão limitados a registos com os valores de propriedade que especifica. Se não forem adicionados valores, todos os registos estão incluídos na consulta. |
| **Anel** |**> Opções adicionais** |
| Cores |A cor que é exibida para cada uma das três propriedades de topo. Para especificar cores alternativas para valores de propriedade específicos, utilize *o Mapa de Cores Avançadas*. |
| Mapeamento de cores avançadas |Exibe uma cor que representa valores de propriedade específicos. Se o valor especificado estiver nos três primeiros, a cor alternativa é exibida em vez da cor padrão. Se a propriedade não estiver entre os três primeiros, a cor não é exibida. |
| **Avançado** |**> verificação do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza-se a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não tiver resultados, é apresentada uma mensagem no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é apresentada se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado *de avaliação* de desempenho. |


## <a name="line-chart-tile"></a>Azulejo de gráfico de linha
Este azulejo é um gráfico de linha que exibe várias séries a partir de uma consulta de log ao longo do tempo. 

![Screenshot de um azulejo de gráfico de linha no Azure Monitor View Designer.](media/view-designer-tiles/tile-line-chart.png)

| Definições | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido no topo do azulejo. |
| Descrição |O texto que é mostrado sob o nome de azulejo. |
| **Gráfico de linhas** | |
| Consulta |A consulta que é executada para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a palavra-chave da *medida* para resumir os resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo X utiliza este intervalo de tempo. Se a consulta não utilizar a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| **Gráfico de linhas** |**> eixo Y** |
| Use a escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta. Estas informações são utilizadas para exibir etiquetas no gráfico indicando os tipos de valor e opcionalmente para converter os valores. O **Tipo de Unidade** especifica a categoria da unidade e define os valores do Tipo Unidade **Atual** que estão disponíveis. Se selecionar um valor em **Converter para,** os valores numéricos são convertidos do tipo **Unidade Atual** para o **Converse para** o tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada nenhuma etiqueta, apenas é apresentado o tipo *Unidade.* |
| **Avançado** |**> verificação do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza-se a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não tiver resultados, é apresentada uma mensagem no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é apresentada se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado *de avaliação* de desempenho. |


## <a name="line-chart-and-callout-tile"></a>Gráfico de linha e azulejo de chamada
Este azulejo tem tanto um gráfico de linha que exibe várias séries a partir de uma consulta de log ao longo do tempo e uma chamada com um valor resumido. 

![Screenshot de um gráfico de linha e azulejo de chamada no Azure Monitor View Designer.A chamada aumenta o gráfico de linha mostrando um valor resumido.](media/view-designer-tiles/tile-line-chart-callout.png)

| Definições | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido no topo do azulejo. |
| Descrição |O texto que é mostrado sob o nome de azulejo. |
| **Gráfico de linhas** | |
| Consulta |A consulta que é executada para o gráfico de linha. A primeira propriedade é um valor de texto, e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a palavra-chave da *medida* para resumir os resultados. Se a consulta utilizar a palavra-chave de *intervalo,* o eixo X utiliza este intervalo de tempo. Se a consulta não utilizar a palavra-chave de *intervalo,* o eixo x utiliza intervalos de hora. |
| **Gráfico de linhas** |**> Callout** |
| Título de chamada | O texto que é apresentado acima do valor de chamada. |
| Nome da série |O valor da propriedade da série a ser usado como o valor de chamada. Se não forem fornecidas séries, todos os registos da consulta são utilizados. |
| Operação |A operação que é realizada na propriedade de valor para resumi-la como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: o valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela.</li><li>Min: O valor mínimo dos intervalos incluídos na tabela.</li><li>Soma: A soma dos valores de todos os registos.</li></ul> |
| **Gráfico de linhas** |**> eixo Y** |
| Use a escala logarítmica |Selecione este link para utilizar uma escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para que os valores sejam devolvidos pela consulta. Estas informações são utilizadas para exibir etiquetas de gráficos que indicam os tipos de valor e, opcionalmente, para converter os valores. O tipo *de Unidade* especifica a categoria da unidade e define os valores de tipo Unidade *De Corrente* disponíveis. Se selecionar um valor em *Converter para,* os valores numéricos são convertidos do tipo *Unidade Atual* para o *Converse para* o tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y ao lado da etiqueta para o tipo *Unidade.* Se não for especificada nenhuma etiqueta, apenas é apresentado o tipo *Unidade.* |
| **Avançado** |**> verificação do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza-se a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não tiver resultados, é apresentada uma mensagem no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é apresentada se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado *de avaliação* de desempenho. |


## <a name="two-timelines-tile"></a>Duas linhas do tempo azulejo
O azulejo **de duas linhas de tempo** exibe os resultados de duas consultas de registo ao longo do tempo como gráficos de colunas. É apresentada uma chamada para cada série. 

![Duas linhas do tempo azulejo](media/view-designer-tiles/tile-two-timelines.png)

| Definições | Descrição |
|:--- |:--- |
| Nome |O texto que é exibido no topo do azulejo. |
| Descrição |O texto que é mostrado sob o nome de azulejo. |
| Primeiro Gráfico | |
| Legenda |O texto que é exibido sob a chamada para a primeira série. |
| Color |A cor que é usada para as colunas da primeira série. |
| Consulta de gráfico |A consulta que é executada para a primeira série. A contagem dos registos ao longo de cada intervalo de tempo é representada pelas colunas de gráficos. |
| Operação |A operação que é realizada na propriedade de valor para resumi-la como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: o valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela.</li></ul> |
| **Segundo gráfico** | |
| Legenda |O texto que é exibido sob a chamada para a segunda série. |
| Color |A cor que é usada para as colunas da segunda série. |
| Consulta de gráficos |A consulta que é executada para a segunda série. A contagem dos registos ao longo de cada intervalo de tempo é representada pelas colunas de gráficos. |
| Operação |A operação que é realizada na propriedade de valor para resumi-la como um único valor para a chamada.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem: A contagem de todos os registos que são devolvidos pela consulta.</li><li>Última amostra: o valor do último intervalo incluído na tabela.</li><li>Max: O valor máximo dos intervalos incluídos na tabela. |
| **Avançado** |**> verificação do fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação do fluxo de dados deve ser ativada para o azulejo. Esta abordagem fornece uma mensagem alternativa se os dados não estiverem disponíveis. Normalmente, utiliza-se a abordagem para fornecer uma mensagem durante o período temporário quando a vista é instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não tiver resultados, é apresentada uma mensagem no lugar do valor da consulta principal. |
| Mensagem |A mensagem que é apresentada se a consulta de verificação do fluxo de dados não retornar nenhum dado. Se não fornecer nenhuma mensagem, é apresentada uma mensagem de estado *de avaliação* de desempenho. |


## <a name="next-steps"></a>Passos seguintes
* Saiba mais [sobre consultas de log](../log-query/log-query-overview.md) para apoiar as consultas em azulejos.
* Adicione [peças de visualização](view-designer-parts.md) à sua vista personalizada.
