---
title: Começar com o Azure Monitor Log Analytics  Microsoft Docs
description: Este artigo fornece um tutorial para usar log analytics no portal Azure para escrever consultas.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 07/19/2019
ms.openlocfilehash: 1cf1695db50e6aee2a5dae24ed5231fdda7c12de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79239589"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Começar com Log Analytics no Monitor Azure

> [!NOTE]
> Pode trabalhar através deste exercício no seu próprio ambiente se estiver a recolher dados de pelo menos uma máquina virtual. Se não utilizar o nosso [ambiente de demonstração,](https://portal.loganalytics.io/demo)que inclui muitos dados da amostra.

Neste tutorial, você aprenderá a usar log analytics no portal Azure para escrever consultas de log Do Monitor Azure. Vai ensinar-te a:

- Use log analytics para escrever uma consulta simples
- Compreenda o esquema dos seus dados
- Filtrar, classificar e resultados do grupo
- Aplicar um intervalo de tempo
- Criar gráficos
- Guardar e carregar consultas
- Consultas de exportação e partilha

Para um tutorial sobre a escrita de consultas de registo, consulte [Começar com consultas de log no Monitor Azure](get-started-queries.md).<br>
Para mais detalhes sobre consultas de registo, consulte [a visão geral das consultas de registo no Monitor Azure](log-query-overview.md).

## <a name="meet-log-analytics"></a>Conheça o Log Analytics
Log Analytics é uma ferramenta web usada para escrever e executar consultas de registo do Monitor Azure. Abra-o selecionando **Registos** no menu Do Monitor Azure. Começa com uma nova consulta em branco.

![Página de boas-vindas](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>Requisitos de firewall
Para utilizar o Log Analytics, o seu navegador necessita de acesso aos seguintes endereços. Se o seu navegador estiver a aceder ao portal Azure através de uma firewall, tem de ativar o acesso a estes endereços.

| Uri | IP | Portas |
|:---|:---|:---|
| portal.loganalytics.io | Dinâmica | 80,443 |
| api.loganalytics.io | Dinâmica | 80,443 |
| docs.loganalytics.io | Dinâmica | 80,443 |

## <a name="basic-queries"></a>Consultas básicas
As consultas podem ser usadas para pesquisar termos, identificar tendências, analisar padrões e fornecer muitas outras informações com base nos seus dados. Comece com uma consulta básica:

```Kusto
Event | search "error"
```

Esta consulta procura na tabela _Evento_ por registos que contenham o _erro_ de termo em qualquer propriedade.

As consultas podem começar com um nome de mesa ou um comando de [pesquisa.](/azure/kusto/query/searchoperator) O exemplo acima começa com o nome de mesa _Evento_, que recupera todos os registos da tabela Evento. O caracteres do tubo (/) separa os comandos, pelo que a saída do primeiro serve como entrada do seguinte comando. Pode adicionar qualquer número de comandos a uma única consulta.

Outra forma de escrever a mesma consulta seria:

```Kusto
search in (Event) "error"
```

Neste exemplo, **a pesquisa** é remeto para a tabela _Evento,_ e todos os registos nessa tabela são pesquisados para o _erro_de termo .

## <a name="running-a-query"></a>Executando uma consulta
Execute uma consulta clicando no botão **Executar** ou premindo **Shift+Enter**. Considere os seguintes detalhes que determinam o código que será executado e os dados que são devolvidos:

- Quebras de linha: Uma única pausa torna a sua consulta mais fácil de ler. Várias quebras de linha dividem-na em consultas separadas.
- Cursor: Coloque o cursor em algum lugar dentro da consulta para executá-lo. A consulta atual é considerada o código até que uma linha em branco seja encontrada.
- Intervalo de tempo - Um intervalo de tempo das _últimas 24 horas_ é definido por defeito. Para utilizar uma gama diferente, utilize o marcador de tempo ou adicione um filtro de intervalo de tempo explícito à sua consulta.


## <a name="understand-the-schema"></a>Compreender o esquema
O esquema é uma coleção de mesas agruparadas visualmente sob uma categoria lógica. Várias das categorias são de soluções de monitorização. A categoria _LogManagement_ contém dados comuns como eventos Windows e Syslog, dados de desempenho e batimentos cardíacos do agente.

![Esquema](media/get-started-portal/schema.png)

Em cada tabela, os dados são organizados em colunas com diferentes tipos de dados, conforme indicado por ícones ao lado do nome da coluna. Por exemplo, a tabela _Evento_ mostrada na imagem contém colunas como _Computador,_ Categoria de _Eventos,_ que é um número, e _TimeGenerated_ que é data/hora.

## <a name="filter-the-results"></a>Filtrar os resultados
Comece por colocar tudo na mesa do _Evento._

```Kusto
Event
```

Log Analytics aplica automaticamente os resultados:

- Intervalo de tempo: Por defeito, as consultas limitam-se às últimas 24 horas.
- Número de resultados: Os resultados estão limitados a um máximo de 10.000 registos.

Esta consulta é muito geral, e devolve muitos resultados para ser útil. Pode filtrar os resultados através dos elementos da tabela ou adicionando explicitamente um filtro à consulta. Os resultados de filtragem através dos elementos da tabela aplicam-se ao conjunto de resultados existente, enquanto um filtro à própria consulta devolverá um novo conjunto de resultados filtrado e, portanto, poderá produzir resultados mais precisos.

### <a name="add-a-filter-to-the-query"></a>Adicione um filtro à consulta
Há uma flecha à esquerda de cada registo. Clique nesta seta para abrir os detalhes de um registo específico.

Pairar acima de um nome de coluna para os ícones "+" e "-" para exibir. Para adicionar um filtro que devolverá apenas registos com o mesmo valor, clique no sinal "+". Clique em "-" para excluir registos com este valor e, em seguida, clique em **Executar** para executar a consulta novamente.

![Adicione filtro à consulta](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Filtrar através dos elementos da tabela
Agora vamos focar-nos em eventos com uma gravidade de _erro._ Isto é especificado numa coluna chamada _EventLevelName_. Tens de rolar para a direita para ver esta coluna.

Clique no ícone Filter ao lado do título da coluna e na janela pop-up selecione valores que _Começa com_ o _erro_de texto:

![Filtro](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Classificar e resultados de grupo
Os resultados são agora reduzidos para incluir apenas eventos de erro do SQL Server, criados nas últimas 24 horas. No entanto, os resultados não são classificados de forma alguma. Para classificar os resultados por uma coluna específica, como o carimbo de _tempo,_ por exemplo, clique no título da coluna. Um clique classifica em ordem ascendente enquanto um segundo clique vai classificar em descida.

![Ordenar coluna](media/get-started-portal/sort-column.png)

Outra forma de organizar resultados é por grupos. Para agrupar os resultados por uma coluna específica, basta arrastar o cabeçalho da coluna acima das outras colunas. Para criar subgrupos, arraste outras colunas também na barra superior.

![Grupos](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Selecione colunas para exibir
A tabela de resultados inclui frequentemente muitas colunas. Pode descobrir que algumas das colunas devolvidas não são apresentadas por defeito, ou pode querer remover algumas das colunas que são apresentadas. Para selecionar as colunas para mostrar, clique no botão Colunas:

![Selecionar colunas](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Selecione uma faixa de tempo
Por predefinição, o Log Analytics aplica o intervalo de tempo _das últimas 24 horas._ Para utilizar uma gama diferente, selecione outro valor através do marcador de tempo e clique em **Executar**. Além dos valores predefinidos, pode utilizar a opção de intervalo de _tempo Personalizado_ para selecionar uma gama absoluta para a sua consulta.

![Selecionador de tempo](media/get-started-portal/time-picker.png)

Ao selecionar uma gama de tempo personalizada, os valores selecionados estão em UTC, que pode ser diferente do seu fuso horário local.

Se a consulta contiver explicitamente um filtro para _TimeGenerated,_ o título do selecionador de tempo mostrará _definido em consulta_. A seleção manual será desativada para evitar um conflito.


## <a name="charts"></a>Gráficos
Além de devolver resultados numa tabela, os resultados da consulta podem ser apresentados em formatos visuais. Utilize a seguinte consulta como exemplo:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Por predefinição, os resultados são apresentados numa tabela. Clique em _Gráfico_ para ver os resultados numa vista gráfica:

![Gráfico de barras](media/get-started-portal/bar-chart.png)

Os resultados são mostrados num gráfico de barras empilhado. Clique na _Coluna Empilhada_ e selecione _Pie_ para mostrar outra visão dos resultados:

![Gráfico de torta](media/get-started-portal/pie-chart.png)

Diferentes propriedades da vista, tais como eixos x e y, ou preferências de agrupamento e divisão, podem ser alteradas manualmente a partir da barra de controlo.

Também pode definir a visão preferida na própria consulta, utilizando o operador de renderização.

### <a name="smart-diagnostics"></a>Diagnósticos inteligentes
Num gráfico de tempo, se houver um pico repentino ou um passo nos seus dados, poderá ver um ponto realçado na linha. Isto indica que o _Smart Diagnostics_ identificou uma combinação de propriedades que filtram a mudança súbita. Clique no ponto para obter mais detalhes sobre o filtro e para ver a versão filtrada. Isto pode ajudá-lo a identificar o que causou a mudança:

![Diagnósticos inteligentes](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Afixar ao dashboard
Para fixar um diagrama ou tabela num dos seus painéis Azure partilhados, clique no ícone do pino. Note que este ícone passou para o topo da janela Log Analytics, diferente da imagem abaixo.

![Afixar ao dashboard](media/get-started-portal/pin-dashboard.png)

Certas simplificações são aplicadas a um gráfico quando o prende a um painel de instrumentos:

- Colunas e linhas de mesa: Para fixar uma mesa no tablier, deve ter quatro ou menos colunas. Apenas são exibidas as sete primeiras filas.
- Restrição de tempo: As consultas limitam-se automaticamente aos últimos 14 dias.
- Restrição de contagem de contentores: Se apresentar um gráfico que tem muitos caixotes discretos, os caixotes menos povoados são automaticamente agrupados num _único_ caixote do lixo.

## <a name="save-queries"></a>Guardar consultas
Uma vez criado uma consulta útil, talvez queira salvá-la ou partilhar com os outros. O ícone **Save** está na barra superior.

Pode guardar toda a página de consulta, ou uma única consulta em função. Funções são consultas que também podem ser referenciadas por outras consultas. Para guardar uma consulta em função, deve fornecer um pseudónimo de função, que é o nome usado para chamar a esta consulta quando referenciado por outras consultas.

![Salvar a função](media/get-started-portal/save-function.png)

>[!NOTE]
>Os seguintes caracteres são suportados - `a–z, A–Z, 0-9, -, _, ., <space>, (, ), |` no campo **Nome** ao guardar ou editar a consulta guardada.

As consultas de Log Analytics são sempre guardadas para um espaço de trabalho selecionado e partilhadas com outros utilizadores desse espaço de trabalho.

## <a name="load-queries"></a>Consultas de carga
O ícone do Explorador de Consulta está na área superior direita. Isto lista todas as consultas guardadas por categoria. Também permite que marque consultas específicas como Favoritos para encontrá-las rapidamente no futuro. Clique duas vezes numa consulta guardada para adicioná-la à janela atual.

![Explorador de consulta](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportação e partilha como link
O Log Analytics suporta vários métodos de exportação:

- Excel: Guarde os resultados como um ficheiro CSV.
- Power BI: Exporte os resultados para Power BI. Consulte os dados de [registo do Import Azure Monitor no Power BI](../../azure-monitor/platform/powerbi.md) para obter mais detalhes.
- Partilhar um link: A consulta em si pode ser partilhada como um link que pode ser enviado e executado por outros utilizadores que tenham acesso ao mesmo espaço de trabalho.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a escrita de consultas de [registo do Monitor Azure.](get-started-queries.md)
