---
title: Criar relatórios interativos com pastas de trabalho do Azure Monitor | Documentos da Microsoft
description: Simplificar a geração de relatórios complexos com pastas de trabalho parametrizadas pré-criados e personalizadas
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6d84ad69b067f730bbfbcad9e46bdc9ae2036ead
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569615"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Criar relatórios interativos com pastas de trabalho do Azure Monitor

Pastas de trabalho do texto, de combinar [consultas de análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), métricas do Azure e parâmetros em relatórios interativos avançados. Pastas de trabalho são editáveis por quaisquer outros membros da Equipe que têm acesso aos mesmos recursos do Azure.

Pastas de trabalho são úteis para cenários como:

* Explorar a utilização da sua aplicação, quando não sabe de antemão as métricas de interesse: número de utilizadores, taxas de retenção, as taxas de conversão, etc. Ao contrário de outras ferramentas de análise de utilização, as pastas de trabalho permitem-lhe combinar vários tipos de visualizações e análises, tornando-os excelentes para esse tipo de exploração de forma livre.
* Para a sua equipa, que explica como um recurso recém-lançada está sendo executada, por utilizador que mostra contagens para interações de chave e outras métricas.
* Compartilhamento dos resultados de um A / B de experimentação na sua aplicação com outros membros da sua equipa. Pode explicar os objetivos para a experimentação com o texto e depois mostrar cada métrica de utilização e a consulta do Analytics utilizados para avaliar a experimentação, juntamente com claras explicativos para se cada uma foi acima ou abaixo-soltar.
* O impacto de uma indisponibilidade de relatórios sobre a utilização da sua aplicação, a combinação de dados, a explicação de texto e uma discussão das próximas etapas para evitar interrupções no futuro.

## <a name="starting-with-a-template-or-saved-workbook"></a>Começando com um modelo ou de pasta de trabalho guardada

Uma pasta de trabalho é constituída por secções que consiste de forma independente editáveis gráficos, tabelas, texto e os controles de entrada. Para compreender melhor as pastas de trabalho, é melhor abrir uma cópia de segurança. 

Selecione **livros** no menu esquerdo do dentro do Application Insights experiência para a sua aplicação.

![Captura de ecrã de navegação para pastas de trabalho](./media/usage-workbooks/001-workbooks.png)

Esta ação inicia uma galeria de pasta de trabalho com um número de pastas de trabalho criados previamente para ajudar a começar.

![Captura de ecrã da Galeria de livro](./media/usage-workbooks/002-workbook-gallery.png)

Vamos começar com o **modelo predefinido**, que se encontra sob o cabeçalho **guia de introdução**.

![Captura de ecrã da Galeria de livro](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Editar, reorganizar, a clonagem e excluir seções do livro

Pastas de trabalho tem dois modos: **modo de edição**, e **modo de leitura**. Quando a pasta de trabalho padrão é iniciado pela primeira vez, é aberto no **modo de edição**. Isto mostra todo o conteúdo da pasta de trabalho, inclusive todos os passos e parâmetros que caso contrário, estão ocultas. **Modo de leitura** apresenta uma vista de estilo do relatório simplificada. Isto permite-lhe abstrair a complexidade que se seguiu à criação de um relatório enquanto ainda tem o mecanismo subjacente apenas alguns cliques de distância quando necessário para modificação.

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/editing-controls-new.png)

1. Quando terminar uma seção de edição, clique em **feito editando** no canto inferior esquerdo da secção.

2. Para criar um duplicado de uma seção, clique a **clonar esta secção** ícone. A criação de secções duplicadas é uma ótima maneira de iterar numa consulta sem perder iterações anteriores.

3. Para mover para cima uma seção numa pasta de trabalho, clique nas **mover para cima** ou **mover para baixo** ícone.

4. Para remover permanentemente uma secção, clique a **remover** ícone.

## <a name="adding-text-and-markdown-sections"></a>Adicionar texto e secções do Markdown

Adicionar títulos, explicações e comentários para os livros o ajuda a transformar um conjunto de tabelas e gráficos numa narrativa. Secções de texto no suporte de pastas de trabalho do [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) para formatação de texto, como cabeçalhos, negrito, itálico e listas com marcas.

Para adicionar uma secção de texto ao seu livro, utilize o **adicionar texto** botão na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionar as secções de consulta

![Secção de consulta em pastas de trabalho](./media/usage-workbooks/analytics-section-new.png)

Para adicionar a secção de consulta ao seu livro, utilize o **adicionar consulta** botão na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

As secções de consulta são altamente flexíveis e podem ser utilizadas para responder a perguntas como:

* Como muitas exceções seu site colocado durante o período de tempo mesmo como um declínio em utilização?
* Qual era a distribuição de tempos de carregamento de página para utilizadores que visualizam alguns página?
* O número de utilizadores visualizados um conjunto de páginas em seu site, mas não de algum outro conjunto de páginas? Isso pode ser útil para entender se tiver clusters de utilizadores que utilizam diferentes subconjuntos de funcionalidade de seu site (utilizar o `join` operador com o `kind=leftanti` modificador no [linguagem de consulta de Kusto](/azure/kusto/query/)).

Também não está apenas limitadas a consulta do contexto do aplicativo iniciado o livro de. Pode consultar em várias aplicações monitorizadas do Application Insights como áreas de trabalho do Log Analytics, desde que tem a permissão de acesso a esses recursos.

A consulta de adicional utilização de recursos do Application Insights externa a **aplicação** identificador.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Esta consulta é a combinação de pedidos de três diferentes aplicações. Uma aplicação com o nome app01, uma aplicação com o nome app02 e as solicitações do recurso do Application Insights local.

Para obter dados a partir de uma utilização de área de trabalho do Log Analytics externa a **área de trabalho** identificador.

Para saber mais sobre consultas entre recursos Consulte a [obter diretrizes oficiais](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Definições de consulta de análise avançadas

Cada secção tem suas próprias definições avançadas, que são acessíveis através do ícone de definições ![controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/005-settings.png) localizado à direita do **adicionar parâmetros** botão.

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Largura personalizada**    | Defina esta opção para tornar um item a um tamanho arbitrário, pelo que pode ajustar o número de itens numa única linha, permitindo-lhe organizar melhor seus gráficos e tabelas em relatórios interativos avançados.  |
   | **Condicionalmente visível** | Utilize esta opção para ocultar os passos com base num parâmetro quando no modo de leitura. |
   | **Exportar um parâmetro**| Isso permite que uma linha selecionada na grelha ou gráfico para fazer com que os passos posteriores alterar os valores ou fiquem visíveis.  |
   | **Mostrar a consulta durante a edição não** | Esta ação apresenta a consulta acima do gráfico ou na tabela, mesmo quando no modo de leitura.
   | **Mostrar abrir no botão análise quando não a editar** | Esta ação adiciona o ícone de análise azul para o canto direito do gráfico para permitir o acesso de um clique.|

A maioria destas definições são bastante intuitiva, mas entender **exportar um parâmetro** é melhor examinar uma pasta de trabalho que utiliza essa funcionalidade.

Um dos livros criados previamente fornece informações sobre como os utilizadores ativos.

A primeira secção da pasta de trabalho baseia-se nos dados de consulta de análise:

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/003-active-users.png)

A segunda seção também se baseia nos dados de consulta de análise, mas a seleção de uma linha na primeira tabela interativamente irá atualizar o conteúdo do gráfico:

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/004-active-users-trend.png)

 Isso é possível através da utilização do **quando um item é selecionado, exportar um parâmetro** definições que são ativadas na consulta de análise da tabela avançadas.

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/007-settings-export.png)

A segunda consulta de análise, em seguida, utiliza os valores exportados quando é selecionada uma linha. Se nenhuma linha for selecionada, é assumida como predefinição para a linha que representa os valores geral. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Adicionar as secções de métricas

As secções de métricas dão-lhe acesso total ao incorporar dados de métricas do Azure Monitor para os relatórios interativos. Muitas das pastas de trabalho criados previamente irão conter dados de consulta de análise e dados de métrica que lhe permite tirar partido das funcionalidades de ambos os tudo num único local. Também tem a capacidade para enviar dados de métrica de recursos em qualquer uma das subscrições que tem acesso.

Eis um exemplo de dados de Máquina Virtual que está a ser extraídos numa pasta de trabalho para fornecer uma visualização de grade de desempenho de CPU:

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionar parâmetro secções

Parâmetros de pasta de trabalho permitem-lhe alterar valores na pasta de trabalho sem ter de editar manualmente as secções de consulta ou de texto.  Isso remove a necessidade de que precisam de compreender a linguagem de consulta do analytics subjacente e expandindo imensamente o público-alvo potencial de relatórios com base no livro.

Os valores dos parâmetros são substituídos na consulta, texto ou outras seções de parâmetro ao colocar o nome do parâmetro entre chavetas, como ``{parameterName}``.  Os nomes dos parâmetros estão limitados a regras semelhantes como identificadores de JavaScript, caracteres alfabéticos basicamente ou carateres de sublinhado, seguidos por carateres alfanuméricos ou carateres de sublinhado. Por exemplo, **a1** é permitido, mas **1a** não é permitida.

Os parâmetros são lineares, começando do início de uma pasta de trabalho e que flui para baixo para passos posteriores.  Parâmetros declarados posteriormente numa pasta de trabalho podem substituir as que foram declarados mais acima.  Isso também permite que a utilização de consultas para aceder os valores de parâmetros definidos mais acima de parâmetros.  No passo de um parâmetro em si, os parâmetros são também lineares, à esquerda para a direita, em que parâmetros para a direita podem depender de um parâmetro que declarei anteriormente nesse mesmo passo.
 
Existem quatro tipos diferentes de parâmetros que são atualmente suportados:

  |         |          |
   | ---------------- |:-----|
   | **Texto**    | o utilizador irá editar uma caixa de texto e, opcionalmente, pode fornecer uma consulta para preencher o valor predefinido. |
   | **Lista pendente** | O utilizador irá escolher a partir de um conjunto de valores. |
   | **Seletor de intervalo de tempo**| O utilizador irá escolher a partir de um conjunto predefinido de valores de intervalo de tempo ou escolha entre mais de um intervalo de tempo personalizado.|
   | **Seleccionador de recursos** | O utilizador irá escolher a partir de recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Com um parâmetro de texto

O valor de um usuário digita na caixa de texto é substituído diretamente na consulta, sem carateres de escape ou aspas. Se o valor que precisa é uma cadeia de caracteres, a consulta deve ter o parâmetro entre aspas (como **'{parameter}'** ).

Isso permite que o valor numa caixa de texto a ser utilizado em qualquer lugar. Pode ser um nome de tabela, nome da coluna, nome da função, operador, etc.

O tipo de parâmetro de texto tem uma definição **obter o valor predefinido de consulta do analytics**, que permite que o autor do livro para usar uma consulta para preencher o valor predefinido para essa caixa de texto.

Ao utilizar o valor predefinido de uma consulta do analytics, apenas o primeiro valor da primeira linha (0 de linha, coluna 0) é utilizado como o valor predefinido. Portanto, é recomendado para limitar a consulta para devolver apenas uma linha e uma coluna. Todos os dados devolvidos pela consulta são ignorados. 

O valor que a consulta devolve será substituído diretamente sem carateres de escape ou aspas. Se a consulta retornaria nenhuma linha, o resultado do parâmetro é uma cadeia vazia (se o parâmetro não for necessário) ou não definida (se o parâmetro é necessário).

### <a name="using-a-dropdown"></a>Utilizar uma lista pendente

O tipo de parâmetro de lista pendente permite-lhe criar um controle de lista pendente, que permite a seleção de um ou vários valores.

A lista pendente é preenchido por uma consulta do analytics. Se a consulta devolve uma coluna, os valores nessa coluna são ambos os **valor** e o **etiqueta** no controle de lista pendente. Se a consulta devolve duas colunas, a primeira coluna é o **valor**, e a segunda coluna é o **etiqueta** mostrado na lista pendente.  Se a consulta devolve três colunas, a coluna de 3 é usada para indicar a seleção predefinida nessa lista pendente.  Esta coluna pode ser qualquer tipo, mas o mais simples é usar bool ou tipos numéricos, em que 0 é false e 1 é verdadeiro.

 Se a coluna é um tipo de cadeia de caracteres, cadeia de caracteres nulos/vazios é considerada FALSO e qualquer outro valor é considerado verdadeira. Para listas pendentes de seleção única, o primeiro valor com um valor VERDADEIRO é utilizado como a seleção predefinida.  Para vários seleção dropdowns, todos os valores com um valor VERDADEIRO são utilizados como o conjunto predefinido selecionado. Os itens na lista pendente são apresentados em qualquer ordem, a consulta devolveu linhas. 

Vamos examinar os parâmetros presentes no relatório de utilizadores ativos. Clique no símbolo de edição junto a **TimeRange**.

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/009-time-range.png)

Isso iniciará o item de menu Edit Parameter:

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/010-time-range-edit.png)

A consulta usa um recurso da linguagem de consulta do analytics chamado um **datatable** que permite-lhe gerar uma tabela arbitrária, cheia de conteúdo, do nada! Por exemplo, a seguinte consulta do analytics:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Gera o resultado:

![Controles de edição de secção de pastas de trabalho do Application Insights](./media/usage-workbooks/011-data-table.png)

Um exemplo mais aplicável está a utilizar uma lista pendente para escolher a partir de um conjunto de países/regiões por nome:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A consulta mostra os resultados da seguinte forma:

![Lista pendente do país](./media/usage-workbooks/012-country-dropdown.png)

Listas pendentes são incrivelmente poderosas ferramentas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Enquanto pode fazer o seu parâmetro de intervalo de tempo personalizado através do tipo de parâmetro de lista pendente, também pode utilizar o tipo de parâmetro de intervalo de tempo de out-of-box, se não tiver o mesmo grau de flexibilidade. 

Tipos de parâmetros de intervalo de tempo tem 15 intervalos predefinidos que vão de cinco minutos para os últimos 90 dias. Também é uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador do relatório para escolha explícita iniciar e parar valores para o intervalo de tempo.

### <a name="resource-picker"></a>Seleccionador de recursos

O tipo de parâmetro de Seletor de recurso dá-lhe a capacidade de definir o âmbito do relatório para determinados tipos de recursos. Um exemplo de livro pré-criados que aproveita o tipo de Seletor de recurso é o **Insights falha** pasta de trabalho.

![Lista pendente do país](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>A guardar e partilhar pastas de trabalho com a sua equipa

Pastas de trabalho são salvas dentro de um recurso do Application Insights, no **os meus relatórios** secção é privada para ou na **relatórios partilhados** secção que esteja acessível para todos os utilizadores com acesso à Recurso do Application Insights. Para ver todas as pastas de trabalho no recurso, clique a **aberto** botão na barra de ação.

Para partilhar uma pasta de trabalho que se encontra atualmente num **os meus relatórios**:

1. Clique em **aberto** na barra de ação
2. Clique no botão "..." ao lado da pasta de trabalho que pretende partilhar
3. Clique em **mover para relatórios partilhados**.

Para partilhar uma pasta de trabalho com uma ligação ou através de e-mail, clique em **partilhar** na barra de ação. Tenha em atenção que os destinatários da ligação precisam de acesso a este recurso no portal do Azure para ver a pasta de trabalho. Para fazer edições, os destinatários precisam, pelo menos, permissões de Contribuidor para o recurso.

Para afixar uma ligação para uma pasta de trabalho a um Dashboard do Azure:

1. Clique em **aberto** na barra de ação
2. Clique no botão "..." ao lado da pasta de trabalho que pretende afixar
3. Clique em **afixar ao dashboard**.

## <a name="contributing-workbook-templates"></a>Contribuir com modelos de livros

Criou um modelo de pasta de trabalho formidável e queira compartilhá-lo com a Comunidade? Para saber mais, visite nosso [repositório do GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Passos Seguintes
- Para permitir experiências de utilização, começar a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [vistas de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se já tiver enviar eventos personalizados ou vistas de página, explore as ferramentas de utilização para saber como os utilizadores utilizam o seu serviço.
    - [Utilizadores, Sessões, Eventos](../../azure-monitor/app/usage-segmentation.md)
    - [Funis](../../azure-monitor/app/usage-funnels.md)
    - [Retenção](../../azure-monitor/app/usage-retention.md)
    - [Fluxos do Utilizador](../../azure-monitor/app/usage-flows.md)
    - [Adicionar contexto de utilizador](../../azure-monitor/app/usage-send-user-context.md)