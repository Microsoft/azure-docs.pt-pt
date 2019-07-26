---
title: Introdução ao Azure Monitor Log Analytics | Microsoft Docs
description: Este artigo fornece um tutorial para usar Log Analytics no portal do Azure para escrever consultas.
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: bwren
ms.openlocfilehash: cf2aee475f5d3933421de45fa5b2ade687bed62f
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348079"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Introdução ao Log Analytics no Azure Monitor

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Neste tutorial, você aprenderá a usar Log Analytics na portal do Azure para gravar Azure Monitor consultas de log. Ele ensinará como:

- Usar Log Analytics para escrever uma consulta simples
- Entender o esquema de seus dados
- Filtrar, classificar e agrupar resultados
- Aplicar um intervalo de tempo
- Criar gráficos
- Salvar e carregar consultas
- Exportar e compartilhar consultas

Para obter um tutorial sobre como escrever consultas de log, consulte Introdução [às consultas de log em Azure monitor](get-started-queries.md).<br>
Para obter mais detalhes sobre consultas de log, consulte [visão geral das consultas de log no Azure monitor](log-query-overview.md).

## <a name="meet-log-analytics"></a>Atender Log Analytics
Log Analytics é uma ferramenta da Web usada para gravar e executar Azure Monitor consultas de log. Abra-o selecionando **logs** no menu Azure monitor. Ele começa com uma nova consulta em branco.

![Home page](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Requisitos de firewall
Para usar Log Analytics, seu navegador requer acesso aos endereços a seguir. Se o seu navegador estiver acessando o portal do Azure por meio de um firewall, você deverá habilitar o acesso a esses endereços.

| URI | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmico | 80,443 |
| api.loganalytics.io | Dinâmico | 80,443 |
| docs.loganalytics.io | Dinâmico | 80,443 |

## <a name="basic-queries"></a>Consultas básicas
As consultas podem ser usadas para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base em seus dados. Comece com uma consulta básica:

```Kusto
Event | search "error"
```

Essa consulta pesquisa a tabela de _eventos_ em busca de registros que contenham o termo _erro_ em qualquer propriedade.

As consultas podem começar com um nome de tabela ou um comando de [pesquisa](/azure/kusto/query/searchoperator) . O exemplo acima começa com o _evento_table name, que recupera todos os registros da tabela de eventos. O caractere de pipe (|) separa os comandos, de modo que a saída do primeiro serve como entrada do comando a seguir. Você pode adicionar qualquer número de comandos a uma única consulta.

Outra maneira de escrever essa mesma consulta seria:

```Kusto
search in (Event) "error"
```

Neste exemplo, a **pesquisa** tem o escopo definido para a tabela de _eventos_ , e todos os registros nessa tabela são pesquisados para o termo _erro_.

## <a name="running-a-query"></a>Executando uma consulta
Execute uma consulta clicando no botão **executar** ou pressionando **Shift + Enter**. Considere os seguintes detalhes que determinam o código que será executado e os dados retornados:

- Quebras de linha: Uma única quebra torna sua consulta mais fácil de ler. Várias quebras de linha a dividem em consultas separadas.
- Posição Coloque o cursor em algum lugar dentro da consulta para executá-lo. A consulta atual é considerada como o código até que uma linha em branco seja encontrada.
- Intervalo de tempo-um intervalo de tempo das _últimas 24 horas_ é definido por padrão. Para usar um intervalo diferente, use o seletor de tempo ou adicione um filtro de intervalo de tempo explícito à sua consulta.


## <a name="understand-the-schema"></a>Compreender o esquema
O esquema é uma coleção de tabelas agrupadas visualmente em uma categoria lógica. Várias das categorias são de monitoramento de soluções. A categoria _LogManagement_ contém dados comuns, como eventos do Windows e syslog, dados de desempenho e pulsações do agente.

![Esquema](media/get-started-portal/schema.png)

Em cada tabela, os dados são organizados em colunas com tipos de dados diferentes, conforme indicado pelos ícones ao lado do nome da coluna. Por exemplo, o _evento_ tabela mostrada na captura de ecrã contém colunas como _computador_ que é o texto, _se nos_ que é um número, e _TimeGenerated_ que é a data/hora.

## <a name="filter-the-results"></a>Filtrar os resultados
Comece obtendo tudo na tabela de _eventos_ .

```Kusto
Event
```

Log Analytics os resultados de escopos automaticamente por:

- Intervalo de tempo:  Por padrão, as consultas são limitadas às últimas 24 horas.
- Número de resultados: Os resultados são limitados ao máximo de 10.000 registros.

Essa consulta é muito geral e retorna muitos resultados para serem úteis. Você pode filtrar os resultados por meio dos elementos da tabela ou adicionando explicitamente um filtro à consulta. A filtragem de resultados por meio dos elementos da tabela aplica-se ao conjunto de resultados existente, enquanto um filtro para a consulta em si retornará um novo conjunto de resultados filtrados e, portanto, poderá produzir resultados mais precisos.

### <a name="add-a-filter-to-the-query"></a>Adicionar um filtro à consulta
Há uma seta à esquerda de cada registro. Clique nesta seta para abrir os detalhes de um registo específico.

Passe o mouse sobre um nome de coluna para os ícones "+" e "-" para exibir. Para adicionar um filtro que retornará apenas registros com o mesmo valor, clique no sinal "+". Clique em "-" para excluir registros com esse valor e clique em **executar** para executar a consulta novamente.

![Adicionar filtro à consulta](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrar pelos elementos da tabela
Agora, vamos nos concentrar nos eventos com uma severidade de _erro_. Isso é especificado em uma coluna chamada _EventLevelName_. Você precisará rolar para a direita para ver esta coluna.

Clique no ícone de filtro ao lado do título da coluna e, na janela pop-up, selecione os valores que _começam com_ o _erro_de texto:

![Filtro](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Classificar e agrupar resultados
Os resultados agora são refinados para incluir apenas eventos de erro de SQL Server, criados nas últimas 24 horas. No entanto, os resultados não são classificados de forma alguma. Para classificar os resultados por uma coluna específica, como _carimbo de data/hora_ , por exemplo, clique no título da coluna. Um clique classifica em ordem crescente, enquanto um segundo clique será classificado em decrescente.

![Classificar coluna](media/get-started-portal/sort-column.png)

Outra maneira de organizar os resultados é por grupos. Para agrupar os resultados por uma coluna específica, basta arrastar o cabeçalho da coluna para cima das outras colunas. Para criar subgrupos, arraste outras colunas também a barra superior.

![Grupos](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Selecionar colunas para exibir
A tabela de resultados geralmente inclui muitas colunas. Você pode achar que algumas das colunas retornadas não são exibidas por padrão, ou talvez você queira remover algumas das colunas que são exibidas. Para selecionar as colunas a serem mostradas, clique no botão colunas:

![Selecionar colunas](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Selecionar um intervalo de tempo
Por padrão, Log Analytics aplica o intervalo de hora das _últimas 24 horas_ . Para usar um intervalo diferente, selecione outro valor por meio do seletor de tempo e clique em **executar**. Além dos valores predefinidos, você pode usar a opção _intervalo de tempo personalizado_ para selecionar um intervalo absoluto para a consulta.

![Seletor de hora](media/get-started-portal/time-picker.png)

Ao selecionar um intervalo de tempo personalizado, os valores selecionados estão em UTC, que podem ser diferentes do seu fuso horário local.

Se a consulta contiver explicitamente um filtro para TimeGenerated, o título do seletor de tempo mostrará _definido em consulta_. A seleção manual será desabilitada para evitar um conflito.


## <a name="charts"></a>Gráficos
Além de retornar resultados em uma tabela, os resultados da consulta podem ser apresentados em formatos visuais. Use a consulta a seguir como exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por padrão, os resultados são exibidos em uma tabela. Clique em _gráfico_ para ver os resultados em uma exibição gráfica:

![Gráfico de barras](media/get-started-portal/bar-chart.png)

Os resultados são mostrados em um gráfico de barras empilhadas. Clique em _coluna_ empilhada e selecione _pizza_ para mostrar outra exibição dos resultados:

![Gráfico circular](media/get-started-portal/pie-chart.png)

Propriedades diferentes da exibição, como eixos x e y, ou preferências de agrupamento e divisão, podem ser alteradas manualmente na barra de controle.

Você também pode definir o modo de exibição preferencial na própria consulta, usando o operador render.

### <a name="smart-diagnostics"></a>Diagnóstico inteligente
Em um gráfico de tempo, se houver um pico repentino ou uma etapa em seus dados, você poderá ver um ponto realçado na linha. Isso indica que o _Smart Diagnostics_ identificou uma combinação de propriedades que filtram a alteração repentina. Clique no ponto para obter mais detalhes sobre o filtro e para ver a versão filtrada. Isso pode ajudá-lo a identificar o que causou a alteração:

![Diagnóstico inteligente](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Afixar ao dashboard
Para fixar um diagrama ou tabela em um dos seus painéis compartilhados do Azure, clique no ícone de pino.

![Afixar ao dashboard](media/get-started-portal/pin-dashboard.png)

Determinadas simplificações são aplicadas a um gráfico quando você a fixa em um painel:

- Linhas e colunas de tabela: Para fixar uma tabela no painel, ela deve ter quatro ou menos colunas. Somente as sete primeiras linhas são exibidas.
- Restrição de tempo: As consultas são limitadas automaticamente aos últimos 14 dias.
- Restrição de contagem de compartimentos: Se você exibir um gráfico com muitos compartimentos discretos, os compartimentos menos preenchidos serão automaticamente agrupados em uma única bandeja de _outros_ .

## <a name="save-queries"></a>Guardar consultas
Depois de criar uma consulta útil, talvez você queira salvá-la ou compartilhá-la com outras pessoas. O ícone **salvar** está na barra superior.

Você pode salvar a página de consulta inteira ou uma única consulta como uma função. Funções são consultas que também podem ser referenciadas por outras consultas. Para salvar uma consulta como uma função, você deve fornecer um alias de função, que é o nome usado para chamar essa consulta quando referenciado por outras consultas.

![Salvar função](media/get-started-portal/save-function.png)

>[!NOTE]
>Os caracteres a seguir têm suporte `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` -no campo **nome** ao salvar ou editar a consulta salva.

Log Analytics consultas são sempre salvas em um espaço de trabalho selecionado e compartilhadas com outros usuários desse espaço de trabalho.

## <a name="load-queries"></a>Carregar consultas
O ícone do Gerenciador de consultas está na área superior direita. Isso lista todas as consultas salvas por categoria. Ele também permite marcar consultas específicas como favoritos para encontrá-las rapidamente no futuro. Clique duas vezes em uma consulta salva para adicioná-la à janela atual.

![Explorador de consultas](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportar e compartilhar como link
O Log Analytics dá suporte a vários métodos de exportação:

- Excel: Salve os resultados como um arquivo CSV.
- Power BI: Exporte os resultados para Power BI. Consulte [importar Azure monitor dados de log no Power bi](../../azure-monitor/platform/powerbi.md) para obter detalhes.
- Compartilhar um link: A própria consulta pode ser compartilhada como um link que pode ser enviada e executada por outros usuários que têm acesso ao mesmo espaço de trabalho.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre como [gravar Azure monitor consultas de log](get-started-queries.md).
