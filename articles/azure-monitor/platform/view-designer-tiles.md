---
title: Um guia de referência para os mosaicos do estruturador de vistas no Azure Monitor | Documentos da Microsoft
description: Ao utilizar o estruturador de vistas no Azure Monitor, pode criar vistas personalizadas que são apresentadas no portal do Azure e contêm uma variedade de visualizações nos dados na área de trabalho do Log Analytics. Este artigo é um guia de referência para as definições para os mosaicos que estão disponíveis nas suas vistas personalizadas.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: c380f0391c51628b0eb71723a0faf982a4399680
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887765"
---
# <a name="reference-guide-to-view-designer-tiles-in-azure-monitor"></a>Guia de referência para mosaicos do estruturador de vistas no Azure Monitor
Ao utilizar o estruturador de vistas no Azure Monitor, pode criar diversas exibições personalizadas no portal do Azure pode ajudá-lo a visualizar os dados na sua área de trabalho do Log Analytics. Este artigo é um guia de referência para as definições para os mosaicos que estão disponíveis nas suas vistas personalizadas.

Para obter mais informações sobre o estruturador de vistas, consulte:

* [Exibir Designer](view-designer.md): Fornece uma visão geral do estruturador de vistas e procedimentos para criação e edição de exibições personalizadas.
* [Referência de parte da visualização](view-designer-parts.md): Fornece um guia de referência para as definições para as partes de visualização que estão disponíveis em suas vistas personalizadas.


Os mosaicos do estruturador de vistas disponíveis são descritos na tabela a seguir:  

| Dispor em Mosaico | Descrição |
|:--- |:--- |
| [Número](#number-tile) |A contagem de registos de uma consulta. |
| [Dois números](#two-numbers-tile) |As contagens de registos de duas consultas diferentes. |
| [Anel](#donut-tile) | Um gráfico que se baseia numa consulta, com um valor de resumo no Centro de mensagens em fila. |
| Gráfico de linhas e nota de aviso | Um gráfico de linhas que se baseia numa consulta e uma nota de aviso com um valor de resumo. |
| [Gráfico de linhas](#line-chart-tile) |Um gráfico de linhas que se baseia numa consulta. |
| [Duas linhas cronológicas](#two-timelines-tile) | Um gráfico de colunas com dois da série, cada um com base numa consulta separada. |

As secções seguintes descrevem os tipos de mosaico e as respetivas propriedades em detalhes.

## <a name="number-tile"></a>Mosaico de número
O **número** mosaico mostra ambas as a contagem de registos a partir de uma consulta de registo e um rótulo.

![Mosaico de número](media/view-designer-tiles/tile-number.png)

| Definição | Descrição |
|:--- |:--- |
| Name |O texto que é apresentado na parte superior do mosaico. |
| Descrição |O texto que é apresentado sob o nome do mosaico. |
| **mosaico** | |
| Legenda |O texto que é apresentado no valor. |
| Consulta |A consulta que é executada. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação de fluxo de dados deve estar ativada para o mosaico. Esta abordagem fornece uma mensagem de alternativa, se os dados não estão disponíveis. Normalmente use a abordagem para fornecer uma mensagem durante o período de temporário quando a vista está instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolve nenhum resultado, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |A mensagem é apresentada se a consulta de verificação de fluxo de dados não retornará nenhum dado. Se não fornecer nenhuma mensagem, uma *a executar avaliação* é apresentada a mensagem de estado. |


## <a name="two-numbers-tile"></a>Mosaico de dois números
Este mosaico apresenta a contagem de registos de duas consultas de registo diferentes e uma etiqueta para cada um.

![Mosaico de dois números](media/view-designer-tiles/tile-two-numbers.png)

| Definição | Descrição |
|:--- |:--- |
| Name |O texto que é apresentado na parte superior do mosaico. |
| Descrição |O texto que é apresentado sob o nome do mosaico. |
| **Primeiro mosaico** | |
| Legenda |O texto que é apresentado no valor. |
| Consulta |A consulta que é executada. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Segundo mosaico** | |
| Legenda |O texto que é apresentado no valor. |
| Consulta |A consulta que é executada. É apresentada a contagem dos registos que são devolvidos pela consulta. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação de fluxo de dados deve estar ativada para o mosaico. Esta abordagem fornece uma mensagem de alternativa, se os dados não estão disponíveis. Normalmente use a abordagem para fornecer uma mensagem durante o período de temporário quando a vista está instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolve nenhum resultado, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |A mensagem é apresentada se a consulta de verificação de fluxo de dados não retornará nenhum dado. Se não fornecer nenhuma mensagem, uma *a executar avaliação* é apresentada a mensagem de estado. |


## <a name="donut-tile"></a>Mosaico de anel
O **anel** mosaico apresenta um único número que resume uma coluna de valor numa consulta de registo. Anel apresenta graficamente os resultados dos registos de três principais.

![Mosaico de anel](media/view-designer-tiles/tile-donut.png)

| Definição | Descrição |
|:--- |:--- |
| Name |O texto que é apresentado na parte superior do mosaico. |
| Descrição |O texto que é apresentado sob o nome do mosaico. |
| **Anel** | |
| Consulta |A consulta que está a ser executada para o anel. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a *medida* palavra-chave para resumir os resultados. |
| **Anel** |**> Center** |
| Texto |O texto que é apresentado no valor dentro do anel. |
| Operação |A operação é executada na propriedade de valor para resumi-lo como um único valor.<ul><li>Soma: Adicione os valores de todos os registos com o valor da propriedade.</li><li>Percentagem de: Percentagem dos valores summed de registos com o valor da propriedade em comparação comparada os valores summed de todos os registos.</li></ul> |
| Valores dos resultados utilizados na operação do centro |Opcionalmente, selecione o sinal de adição (+) para adicionar um ou mais valores. Os resultados da consulta são limitados aos registros com os valores de propriedade que especificar. Se não os valores são adicionados, todos os registos estão incluídos na consulta. |
| **Anel** |**> Opções adicionais** |
| Cores |A cor que é apresentada para cada uma das três propriedades principais. Para especificar alternativas cores dos valores de propriedade específica, utilize *Advanced mapeamento de cor*. |
| Mapeamento de Cores Avançadas |Apresenta uma cor que representa valores de propriedade específicos. Se o valor que especificar nas três principais, a cor alternativa é apresentada em vez da cor padrão. Se a propriedade não estiver a ser os três principais, a cor não é apresentada. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação de fluxo de dados deve estar ativada para o mosaico. Esta abordagem fornece uma mensagem de alternativa, se os dados não estão disponíveis. Normalmente use a abordagem para fornecer uma mensagem durante o período de temporário quando a vista está instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolve nenhum resultado, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |A mensagem é apresentada se a consulta de verificação de fluxo de dados não retornará nenhum dado. Se não fornecer nenhuma mensagem, uma *a executar avaliação* é apresentada a mensagem de estado. |


## <a name="line-chart-tile"></a>Mosaico de gráfico de linha
Este mosaico é um gráfico de linhas que exibe várias séries de uma consulta de registo ao longo do tempo. 

![Mosaico de gráfico e nota de aviso da linha](media/view-designer-tiles/tile-line-chart.png)

| Definição | Descrição |
|:--- |:--- |
| Name |O texto que é apresentado na parte superior do mosaico. |
| Descrição |O texto que é apresentado sob o nome do mosaico. |
| **Gráfico de linhas** | |
| Consulta |A consulta que está a ser executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a *medida* palavra-chave para resumir os resultados. Se a consulta utiliza a *intervalo* palavra-chave, o eixo x utiliza este intervalo de tempo. Se a consulta não utiliza a *intervalo* palavra-chave, os intervalos de hora a hora do eixo x utiliza. |
| **Gráfico de linhas** |**> Eixo y** |
| Utilizar Escala Logarítmica |Selecione esta ligação para utilizar escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores devolvidos pela consulta. Estas informações são utilizadas para apresentar as etiquetas no gráfico que indica os tipos de valor e, opcionalmente, para converter os valores. O **tipo de unidade** Especifica a categoria da unidade e define a **tipo de unidade atual** valores que estão disponíveis. Se selecionar um valor na **converter** , em seguida, os valores numéricos são convertidos da **unidade atual** escreva para o **converter** tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y junto à etiqueta para o *unidade* tipo. Se nenhuma etiqueta for especificada, apenas os *unidade* tipo é apresentado. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação de fluxo de dados deve estar ativada para o mosaico. Esta abordagem fornece uma mensagem de alternativa, se os dados não estão disponíveis. Normalmente use a abordagem para fornecer uma mensagem durante o período de temporário quando a vista está instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolve nenhum resultado, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |A mensagem é apresentada se a consulta de verificação de fluxo de dados não retornará nenhum dado. Se não fornecer nenhuma mensagem, uma *a executar avaliação* é apresentada a mensagem de estado. |


## <a name="line-chart-and-callout-tile"></a>Mosaico de gráfico e nota de aviso da linha
Este mosaico tem tanto de uma linha do gráfico que apresenta várias séries de um log de consulta ao longo do tempo e uma nota de aviso com um valor resumido. 

![Mosaico de gráfico e nota de aviso da linha](media/view-designer-tiles/tile-line-chart-callout.png)

| Definição | Descrição |
|:--- |:--- |
| Name |O texto que é apresentado na parte superior do mosaico. |
| Descrição |O texto que é apresentado sob o nome do mosaico. |
| **Gráfico de linhas** | |
| Consulta |A consulta que está a ser executada para o gráfico de linhas. A primeira propriedade é um valor de texto e a segunda propriedade é um valor numérico. Esta consulta normalmente utiliza a *medida* palavra-chave para resumir os resultados. Se a consulta utiliza a *intervalo* palavra-chave, o eixo x utiliza este intervalo de tempo. Se a consulta não utiliza a *intervalo* palavra-chave, os intervalos de hora a hora do eixo x utiliza. |
| **Gráfico de linhas** |**> Texto explicativo** |
| Título da nota de aviso | O texto que é apresentado acima do valor da nota de aviso. |
| Nome da série |O valor de propriedade de série a ser utilizado como o valor da nota de aviso. Se não for fornecido nenhum série, todos os registos da consulta são utilizados. |
| Operação |A operação é executada na propriedade de valor para resumi-lo como um valor único para a nota de aviso.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem de: Contagem de todos os registos que são devolvidos pela consulta.</li><li>Último exemplo: O valor do último intervalo que está incluído no gráfico.</li><li>Máx.: O valor máximo de intervalos que estão incluídos no gráfico.</li><li>Mínimo: O valor mínimo de intervalos que estão incluídos no gráfico.</li><li>Soma: A soma dos valores de todos os registos.</li></ul> |
| **Gráfico de linhas** |**> Eixo y** |
| Utilizar Escala Logarítmica |Selecione esta ligação para utilizar escala logarítmica para o eixo y. |
| Unidades |Especifique as unidades para os valores a serem retornados pela consulta. Estas informações são utilizadas para rótulos de gráficos de apresentação que indicam os tipos de valor e, opcionalmente, para converter os valores. O *unidade* tipo Especifica a categoria da unidade e define o disponíveis *unidade atual* tipo de valores. Se selecionar um valor na *converter*, os valores numéricos são convertidos da *unidade atual* escreva para o *converter* tipo. |
| Etiqueta personalizada |O texto que é apresentado para o eixo y junto à etiqueta para o *unidade* tipo. Se nenhuma etiqueta for especificada, apenas os *unidade* tipo é apresentado. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação de fluxo de dados deve estar ativada para o mosaico. Esta abordagem fornece uma mensagem de alternativa, se os dados não estão disponíveis. Normalmente use a abordagem para fornecer uma mensagem durante o período de temporário quando a vista está instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolve nenhum resultado, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |A mensagem é apresentada se a consulta de verificação de fluxo de dados não retornará nenhum dado. Se não fornecer nenhuma mensagem, uma *a executar avaliação* é apresentada a mensagem de estado. |


## <a name="two-timelines-tile"></a>Mosaico de duas linhas cronológicas
O **duas linhas cronológicas** mosaico apresenta os resultados de duas consultas de registo ao longo do tempo, como gráficos de colunas. Uma nota de aviso é apresentada para cada série. 

![Mosaico de duas linhas cronológicas](media/view-designer-tiles/tile-two-timelines.png)

| Definição | Descrição |
|:--- |:--- |
| Name |O texto que é apresentado na parte superior do mosaico. |
| Descrição |O texto que é apresentado sob o nome do mosaico. |
| Primeiro Gráfico | |
| Legenda |O texto que é apresentado em nota de aviso para a primeira série. |
| Cor |A cor que é utilizada para as colunas da primeira série. |
| Consulta de gráfico |A consulta que está a ser executada para a primeira série. A contagem de registos ao longo de cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação é executada na propriedade de valor para resumi-lo como um valor único para a nota de aviso.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem de: Contagem de todos os registos que são devolvidos pela consulta.</li><li>Último exemplo: O valor do último intervalo que está incluído no gráfico.</li><li>Máx.: O valor máximo de intervalos que estão incluídos no gráfico.</li></ul> |
| **Segundo gráfico** | |
| Legenda |O texto que é apresentado em nota de aviso para a segunda série. |
| Cor |A cor utilizada para as colunas na segunda série. |
| Consulta do Gráfico |A consulta que está a ser executada para a segunda série. A contagem de registos ao longo de cada intervalo de tempo é representada pelas colunas do gráfico. |
| Operação |A operação é executada na propriedade de valor para resumi-lo como um valor único para a nota de aviso.<ul><li>Média: A média dos valores de todos os registos.</li><li>Contagem de: Contagem de todos os registos que são devolvidos pela consulta.</li><li>Último exemplo: O valor do último intervalo que está incluído no gráfico.</li><li>Máx.: O valor máximo de intervalos que estão incluídos no gráfico. |
| **Avançadas** |**> Verificação de fluxo de dados** |
| Ativado |Selecione esta ligação se a verificação de fluxo de dados deve estar ativada para o mosaico. Esta abordagem fornece uma mensagem de alternativa, se os dados não estão disponíveis. Normalmente use a abordagem para fornecer uma mensagem durante o período de temporário quando a vista está instalada e os dados ficam disponíveis. |
| Consulta |A consulta que é executada para determinar se os dados estão disponíveis para a vista. Se a consulta não devolve nenhum resultado, é apresentada uma mensagem em vez do valor da consulta principal. |
| Mensagem |A mensagem é apresentada se a consulta de verificação de fluxo de dados não retornará nenhum dado. Se não fornecer nenhuma mensagem, uma *a executar avaliação* é apresentada a mensagem de estado. |


## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre [registar as consultas](../log-query/log-query-overview.md) para oferecer suporte as consultas em mosaicos.
* Adicione [partes de visualização](view-designer-parts.md) à sua vista personalizada.
