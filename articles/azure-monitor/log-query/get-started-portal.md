---
title: Introdução ao Log Analytics do Azure Monitor | Documentos da Microsoft
description: Este artigo fornece um tutorial para utilizar o Log Analytics no portal do Azure para escrever consultas.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: ec6f3884504c94b7669df21882aeb2a1eb9d7220
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750587"
---
# <a name="get-started-with-azure-monitor-log-analytics"></a>Introdução ao Log Analytics do Azure Monitor

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Neste tutorial irá aprender como utilizar o Monitor do Azure Log Analytics no portal do Azure para escrever consultas de registo do Azure Monitor. Ele irá ensiná-lo como para:

- Escrever consultas simples
- Compreender o esquema dos seus dados
- Filtrar, ordenar e resultados de grupo
- Aplicam-se de um intervalo de tempo
- Criar gráficos
- Guardar e carregar consultas
- Exportar e partilhar consultas


## <a name="meet-log-analytics"></a>Conheça o Log Analytics
O log Analytics é uma ferramenta de web utilizada para criar e executar consultas de registo do Azure Monitor. Abri-lo selecionando **registos** no menu do Azure Monitor. Ele começa com uma nova consulta em branco.

![Página de boas-vindas](media/get-started-portal/homepage.png)



## <a name="basic-queries"></a>Consultas básicas
Consultas podem ser usadas para termos de pesquisa, identificar tendências, analisar padrões e fornecem várias outras informações com base nos seus dados. Começar com uma consulta básica:

```Kusto
Event | search "error"
```

Essa consulta pesquisa o _evento_ tabela para registos que contêm o termo "error" em qualquer propriedade.

Consultas podem começar com o nome de uma tabela ou um **pesquisa** comando. O exemplo anterior começa com o nome da tabela _evento_, que define o âmbito da consulta. O caráter de pipe (|) separa comandos, por isso, o resultado do primeiro na entrada do comando seguinte. Pode adicionar qualquer número de comandos para uma única consulta.

Outra forma de escrever essa mesma consulta deve ser:

```Kusto
search in (Event) "error"
```

Neste exemplo, **pesquisa** tem como escopo o _eventos_ tabela e todos os registos na tabela serão pesquisados o termo "erro".

## <a name="running-a-query"></a>Execução de uma consulta
Executar uma consulta ao clicar o **execute** botão ou prima **Shift + Enter**. Considere os seguintes detalhes que determinam o código que será executado e os dados que são devolvidos:

- Quebras de linha: Uma única quebra torna sua consulta mais clara. Quebras de linha de vários dividi-la em consultas separadas.
- Cursor: Coloque o cursor em algum lugar dentro da consulta para executá-lo. A consulta atual é considerada o código até que uma linha em branco é encontrada.
- Intervalo - um intervalo de hora de tempo _últimas 24 horas_ é definido por padrão. Para utilizar um intervalo diferente, utilize o Seletor de tempo ou adicionar um período de tempo explícito filtro de intervalo para a sua consulta.


## <a name="understand-the-schema"></a>Compreender o esquema
O esquema é uma coleção de tabelas visualmente agrupados numa categoria de lógica. Várias categorias são de soluções de monitorização. O _LogManagement_ categoria contém dados comuns, como Windows e eventos do Syslog, dados de desempenho e heartbeats do cliente.

![Esquema](media/get-started-portal/schema.png)

Em cada tabela, os dados são organizados em colunas com tipos de dados diferentes, conforme indicado pelo ícones junto ao nome da coluna. Por exemplo, o _evento_ tabela mostrada na captura de ecrã contém colunas como _computador_ que é o texto, _se nos_ que é um número, e _TimeGenerated_ que é a data/hora.

## <a name="filter-the-results"></a>Filtrar os resultados
Comece por obter tudo _evento_ tabela.

```Kusto
Event
```

O log Analytics examina automaticamente os resultados por:

- Intervalo de tempo:  Por predefinição, consultas estão limitadas para as últimas 24 horas.
- Número de resultados: Os resultados estão limitados ao máximo de 10 000 registos.

Esta consulta é muito geral e retorna demasiados resultados para ser útil. Pode filtrar os resultados por meio dos elementos de tabela ou adicionando explicitamente um filtro à consulta. Filtragem de resultados por meio dos elementos de tabela aplica-se para o conjunto de resultados existente, enquanto um filtro para a própria consulta irá devolver um resultado filtrado novo definido e, portanto, conseguiu produzir resultados mais precisos.

### <a name="add-a-filter-to-the-query"></a>Adicionar um filtro à consulta
Há uma seta à esquerda de cada registo. Clique nesta seta para abrir os detalhes de um registo específico.

Coloque o cursor acima um nome de coluna para o "+" e "-" ícones para exibir. Para adicionar um filtro que retorna apenas os registos com o mesmo valor, clique o sinal "+". Clique em "-" para excluir registros com este valor e, em seguida, clique em **executar** para executar a consulta novamente.

![Adicionar filtro à consulta](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrar por meio dos elementos de tabela
Agora vamos nos concentrar em eventos com uma gravidade de _erro_. Isso é especificado numa coluna chamada _EventLevelName_. Terá de deslocar para a direita para ver esta coluna.

Clique no ícone de filtro ao lado do título de coluna e, na janela de pop-up selecionar valores que _começa com_ o texto _erro_:

![Filtro](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Ordenar e agrupar resultados
Os resultados são agora reduzidos para incluir apenas os eventos de erro do SQL Server, criado nas últimas 24 horas. No entanto, os resultados não são ordenados de forma alguma. Para ordenar os resultados por uma coluna de específica, tal como _timestamp_ por exemplo, clique no título de coluna. Um clique Ordena por ordem ascendente, enquanto um segundo clique para ordenar por descendente.

![Coluna de ordenação](media/get-started-portal/sort-column.png)

Outra forma de organizar os resultados é por grupos. Para resultados de grupo por uma coluna de específico, simplesmente arraste o cabeçalho da coluna acima as outras colunas. Para criar subgrupos, arraste as outras colunas a barra superior também.

![Grupos](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Selecione as colunas a apresentar
A tabela de resultados, muitas vezes, inclui muitas colunas. Pode achar que algumas das colunas devolvidas não são apresentadas por predefinição, ou pode querer remover algumas colunas que são apresentadas. Para selecionar as colunas para mostrar, clique no botão de colunas:

![Selecionar colunas](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Selecionar um intervalo de tempo
Por predefinição, o Log Analytics aplica-se a _últimas 24 horas_ intervalo de tempo. Para utilizar um intervalo diferente, selecione outro valor através do Seletor de hora e clique em **executar**. Além dos valores predefinidos, pode utilizar o _intervalo de tempo personalizado_ opção de selecionar um intervalo de absoluto para sua consulta.

![Selecionador de hora](media/get-started-portal/time-picker.png)

Ao selecionar um intervalo de tempo personalizado, os valores selecionados estão em UTC, o que pode ser diferente de seu fuso horário local.

Se a consulta contém explicitamente um filtro para _TimeGenerated_, a hora irá mostrar o título de Seletor _definir na consulta_. Seleção manual será desativada para evitar um conflito.


## <a name="charts"></a>Gráficos
Para além de retornar resultados numa tabela, é possível apresentar os resultados da consulta formatos visual. Utilize a consulta seguinte como exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por predefinição, os resultados são exibidos numa tabela. Clique em _gráfico_ para ver os resultados numa exibição de gráfico:

![Gráfico de barras](media/get-started-portal/bar-chart.png)

Os resultados são apresentados num gráfico de barras empilhado. Clique em _colunas empilhadas_ e selecione _circular_ para mostrar a outra exibição dos resultados:

![Gráfico circular](media/get-started-portal/pie-chart.png)

Propriedades diferentes do view, como x e y eixos, ou de agrupamento e a divisão de preferências, podem ser alterados manualmente a partir da barra de controle.

Também pode definir a vista preferida na consulta em si, usando o operador de composição.

### <a name="smart-diagnostics"></a>Diagnóstico inteligente
No Pego, se houver um pico ou passo nos seus dados, poderá ver um ponto de realçado na linha. Isto indica que _diagnóstico inteligente_ identificou uma combinação de propriedades que filtrar a alteração repentina. Clique no ponto para obter mais detalhes no filtro e, para ver a versão filtrada. Isso pode ajudá-lo a identificar o que causou a alteração:

![Diagnóstico inteligente](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Afixar ao dashboard
Para afixar um diagrama ou tabela para um dos dashboards partilhados do Azure, clique no ícone de pin.

![Afixar ao dashboard](media/get-started-portal/pin-dashboard.png)

Algumas simplificações aplicadas a um gráfico quando afixá-la a um dashboard:

- Colunas de tabelas e linhas: Para poder afixar uma tabela ao dashboard, tem de ter quatro ou menos colunas. São apresentadas apenas as sete linhas superiores.
- Restrição de tempo: As consultas são automaticamente limitadas a dos últimos 14 dias.
- Restrição de contagem de bin: Se exibir um gráfico que tem muitas das discretizações discretas, menos discretizações preenchidas automaticamente são agrupadas num único _outros_ bin.

## <a name="save-queries"></a>Guardar consultas
Depois de criar uma consulta útil, pode querer guardá-lo ou partilhar com outras pessoas. O **guardar** é de ícone na barra superior.

Pode salvar a página de consulta inteira ou uma única consulta como uma função. As funções são consultas que também podem ser referenciadas por outras consultas. Para guardar uma consulta como uma função, tem de fornecer um alias de função, o que é o nome usado para chamar esta consulta quando referenciado por outras consultas.

![Guardar a função](media/get-started-portal/save-function.png)

Consultas de análise de registo são sempre guardadas para uma área de trabalho selecionada e compartilhadas com outros usuários da área de trabalho.

## <a name="load-queries"></a>Consultas de carga
O ícone do Explorador de consultas está na área superior direito. Lista todas as consultas guardadas por categoria. Ele também permite marcar consultas específicas como favoritos rapidamente encontrá-los no futuro. Clique duas vezes numa consulta guardada para adicioná-lo para a janela atual.

![Explorador de consultas](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>A exportação e partilha de ligação
O log Analytics suporta vários métodos de exportação:

- Excel: Guarde os resultados como um ficheiro CSV.
- Power BI: Exporte os resultados para o power BI. Ver [dados de registo de importação do Azure Monitor para o Power BI](../../azure-monitor/platform/powerbi.md) para obter detalhes.
- Partilhe uma ligação: A consulta em si pode ser compartilhada como um link que, em seguida, pode ser enviado e executado por outros utilizadores que têm acesso à mesma área de trabalho.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [escrever consultas de registo do Azure Monitor](get-started-queries.md).
