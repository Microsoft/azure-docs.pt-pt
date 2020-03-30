---
title: Criar relatórios interativos com os livros do Monitor Azure [ Microsoft docs
description: Simplificar relatórios complexos com livros pré-construídos e parametrizados personalizados
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 15543f7f761c707e8eff8e0cc0a0e4532475ddf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671006"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Criar relatórios interativos com livros do Monitor Azure

Os livros de trabalho combinam texto, [consultas de Análise,](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)Métricas Azure e parâmetros em relatórios interativos ricos. Os livros de recção são editáveis por quaisquer outros membros da equipa que tenham acesso aos mesmos recursos Do Azure.

Os livros são úteis para cenários como:

* Explorando o uso da sua app quando não conhece antecipadamente as métricas de interesse: númerode utilizadores, taxas de retenção, taxas de conversão, etc. Ao contrário de outras ferramentas de análise de uso, os livros de livros permitem combinar vários tipos de visualizações e análises, tornando-as ótimas para este tipo de exploração em forma livre.
* Explicando à sua equipa como está a ser apresentada uma funcionalidade recém-lançada, mostrando que o utilizador conta para interações chave e outras métricas.
* Partilhar os resultados de uma experiência A/B na sua aplicação com outros membros da sua equipa. Você pode explicar os objetivos para a experiência com texto, em seguida, mostrar cada métrica de uso e consulta de Analytics usada para avaliar a experiência, juntamente com chamadas claras para se cada métrica estava acima ou abaixo do alvo.
* Relatando o impacto de uma interrupção no uso da sua app, combinando dados, explicação de texto e uma discussão sobre os próximos passos para evitar interrupções no futuro.

## <a name="starting-with-a-template-or-saved-workbook"></a>Começar com um modelo ou livro guardado

Um livro é composto por secções compostas por gráficos, tabelas, texto e controlos de entrada independentes. Para melhor entender os livros, o melhor é abrir um. 

Selecione **Livros** do menu à esquerda a partir do interior da experiência Application Insights para a sua aplicação.

![Screenshot da navegação para livros de trabalho](./media/usage-workbooks/001-workbooks.png)

Isto lança uma galeria de livros com uma série de livros pré-construídos para ajudá-lo a começar.

![Screenshot da galeria do livro](./media/usage-workbooks/002-workbook-gallery.png)

Começaremos com o **Modelo Padrão,** que está localizado sob a rubrica **Arranque Rápido**.

![Screenshot da galeria do livro](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edição, reorganização, clonagem e apagamento de secções de livro

Os livros têm dois modos: **modo de edição**, e **modo de leitura**. Quando o livro de regras padrão é lançado pela primeira vez, abre no modo de **edição**. Isto mostra todo o conteúdo do livro, incluindo quaisquer passos e parâmetros que estejam escondidos de outra forma. **O modo de leitura** apresenta uma visão simplificada do estilo do relatório. Isto permite-lhe abstrair a complexidade que entrou na criação de um relatório, ao mesmo tempo que ainda tem a mecânica subjacente a poucos cliques de distância quando necessário para modificação.

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/editing-controls-new.png)

1. Quando terminar de editar uma secção, clique em **Edição Feita** no canto inferior esquerdo da secção.

2. Para criar uma duplicação de uma secção, clique no **ícone clone desta secção.** Criar secções duplicadas é um ótimo caminho para iterar numa consulta sem perder iterações anteriores.

3. Para subir uma secção num livro, clique no ícone **Mover para cima** ou **mover-se para baixo.**

4. Para remover uma secção permanentemente, clique no ícone **Remover.**

## <a name="adding-text-and-markdown-sections"></a>Adicionar secções de texto e Markdown

Adicionar títulos, explicações e comentários aos seus livros ajuda a transformar um conjunto de tabelas e gráficos numa narrativa. As secções de texto nos livros de livros suportam a [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) para formatação de texto, como títulos, ousados, itálicos e listas com balas.

Para adicionar uma secção de texto ao seu livro de trabalho, utilize o botão **Adicionar** texto na parte inferior do livro ou na parte inferior de qualquer secção.

## <a name="adding-query-sections"></a>Adicionar secções de consulta

![Secção de consulta nos Livros de Trabalho](./media/usage-workbooks/analytics-section-new.png)

Para adicionar a secção de consulta ao seu livro de trabalho, use o botão **adicionar** consulta na parte inferior do livro de trabalho, ou na parte inferior de qualquer secção.

As secções de consulta são altamente flexíveis e podem ser usadas para responder a questões como:

* Quantas exceções o seu site lançou durante o mesmo período de tempo como um declínio no uso?
* Qual foi a distribuição dos tempos de carregamento de página para os utilizadores verem alguma página?
* Quantos utilizadores viram alguns conjuntos de páginas no seu site, mas não algum outro conjunto de páginas? Isto pode ser útil para entender se tem grupos de utilizadores que usam diferentes `join` subconjuntos da funcionalidade do seu site (utilize o operador com o `kind=leftanti` modificador na linguagem de consulta [Kusto).](/azure/kusto/query/)

Também não se limita apenas a consultar o contexto da aplicação de onde lançou o livro de trabalho. Pode consultar várias aplicações monitorizadas por Insights de aplicação, bem como espaços de trabalho do Log Analytics, desde que tenha permissão de acesso a esses recursos.

Para consultar os recursos adicionais de Aplicação Externa Insights, utilize o identificador de **aplicações.**

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Esta consulta está a combinar pedidos de três aplicações diferentes. Uma aplicação chamada app01, uma aplicação chamada app02, e os pedidos do recurso Local Application Insights.

Para retirar dados de um espaço de trabalho externo do Log Analytics, utilize o identificador espaço de **trabalho.**

Para saber mais sobre consultas de recursos cruzados consulte a [orientação oficial](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Definições avançadas de consulta analítica

Cada secção tem as suas próprias definições ![avançadas, que são](./media/usage-workbooks/005-settings.png) acessíveis através do ícone de definições Aplicação Insights Comandos de edição de livros de trabalho localizados à direita do botão **Adicionar parâmetros.**

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Largura personalizada**    | Delineie isto para fazer um item de tamanho arbitrário, para que possa encaixar muitos itens numa única linha, permitindo-lhe organizar melhor as suas tabelas e tabelas em relatórios interativos ricos.  |
   | **Condicionalmente visível** | Use-o para ocultar passos baseados num parâmetro quando estiver em modo de leitura. |
   | **Exportar um parâmetro**| Isto permite que uma linha selecionada na grelha ou gráfico faça com que passos posteriores alterem valores ou se tornem visíveis.  |
   | **Mostrar consulta ao não editar** | Isto exibe a consulta acima do gráfico ou da tabela mesmo quando está em modo de leitura.
   | **Mostrar aberto no botão de análise ao não editar** | Isto adiciona o ícone Azul Analytics ao canto direito da tabela para permitir um clique de acesso.|

A maioria destas configurações são bastante intuitivas, mas para entender **Exportar um parâmetro** é melhor examinar um livro que faz uso desta funcionalidade.

Um dos livros pré-construídos fornece informações sobre utilizadores ativos.

A primeira secção do livro baseia-se em dados de consulta analítica:

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/003-active-users.png)

A segunda secção baseia-se também em dados de consulta analítica, mas a seleção de uma linha na primeira tabela irá atualizar interativamente o conteúdo do gráfico:

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/004-active-users-trend.png)

 Isto é possível através da utilização do **When a item is selected, exportar um parâmetro** avançado de configurações que estejam ativados na consulta analytics da tabela.

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/007-settings-export.png)

A segunda consulta de análise utiliza os valores exportados quando uma linha é selecionada. Se não for selecionada nenhuma linha, não se encontra na linha que representa os valores globais. 

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

## <a name="adding-metrics-sections"></a>Adicionar secções de métricas

As secções de métricas dão-lhe acesso total para incorporar dados de métricas do Monitor Azure nos seus relatórios interativos. Muitos dos livros pré-construídos conterão dados de consulta analítica e dados métricos que lhe permitam tirar o máximo partido do melhor de ambas as funcionalidades num só local. Também tem a capacidade de retirar dados métricos de recursos em qualquer uma das subscrições a que tem acesso.

Aqui está um exemplo de dados de máquinas virtuais sendo puxados para um livro para fornecer uma visualização da grelha do desempenho do CPU:

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionar secções de parâmetros

Os parâmetros do livro permitem alterar valores no livro sem ter de editar manualmente as secções de consulta ou texto.  Isto elimina a exigência de necessidade de compreender a linguagem de consulta de análise subjacente e expande consideravelmente o público potencial de relatórios baseados em livros.

Os valores dos parâmetros são substituídos em secções de consulta, texto ou outros ``{parameterName}``parâmetros colocando o nome do parâmetro em aparelhos, como .  Os nomes dos parâmetros limitam-se a regras semelhantes às dos identificadores JavaScript, basicamente caracteres alfabéticos ou sublinhados, seguidos por caracteres alfanuméricos ou sublinhados. Por exemplo, a **A1** é permitida, mas **1a** não é permitida.

Os parâmetros são lineares, partindo do topo de um livro e fluindo para passos posteriores.  Os parâmetros declarados posteriormente num livro podem sobrepor-se àqueles que foram declarados mais acima.  Isto também permite parâmetros que usam consultas para aceder aos valores a partir de parâmetros definidos mais acima.  Dentro do próprio passo de um parâmetro, os parâmetros também são lineares, da esquerda para a direita, onde os parâmetros à direita podem depender de um parâmetro declarado anteriormente nesse mesmo passo.
 
Existem quatro tipos diferentes de parâmetros que são atualmente suportados:

  |         |          |
   | ---------------- |:-----|
   | **Texto**    | o utilizador irá editar uma caixa de texto, e pode facultar opcionalmente uma consulta para preencher o valor predefinido. |
   | **Pendente** | O utilizador escolherá entre um conjunto de valores. |
   | **Picker de intervalo de tempo**| O utilizador escolherá entre um conjunto predefinido de valores de intervalo de tempo, ou escolherá a partir de um intervalo de tempo personalizado.|
   | **Picker de recursos** | O utilizador escolherá entre os recursos selecionados para o livro.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um utilizador escreve na caixa de texto é substituído diretamente na consulta, sem escapar ou citar. Se o valor que precisa é de uma corda, a consulta deve ter citações em torno do parâmetro (como **'{parâmetro}'**).

Isto permite que o valor numa caixa de texto seja utilizado em qualquer lugar. Pode ser um nome de mesa, nome de coluna, nome de função, operador, etc.

O tipo de parâmetro de texto tem uma definição **Obter valor padrão a partir de consulta de análise**, que permite ao autor do livro usar uma consulta para preencher o valor padrão para essa caixa de texto.

Ao utilizar o valor predefinido de uma consulta de análise, apenas o primeiro valor da primeira linha (linha 0, coluna 0) é usado como valor predefinido. Por isso, recomenda-se limitar a sua consulta para devolver apenas uma linha e uma coluna. Quaisquer outros dados devolvidos pela consulta são ignorados. 

Qualquer valor que a consulta devoluções seja substituído diretamente sem escapar ou citar. Se a consulta não devolver linhas, o resultado do parâmetro é uma corda vazia (se o parâmetro não for necessário) ou indefinida (se for necessário o parâmetro).

### <a name="using-a-dropdown"></a>Usando uma queda

O tipo de parâmetro de queda permite criar um controlo de dropdown, permitindo a seleção de um ou muitos valores.

A queda é povoada por uma consulta de análise. Se a consulta devolver uma coluna, os valores nessa coluna são tanto o **valor** como a **etiqueta** no controlo de queda. Se a consulta devolver duas colunas, a primeira coluna é o **valor**, e a segunda coluna é a **etiqueta** mostrada no dropdown.  Se a consulta devolver três colunas, a 3ª coluna é utilizada para indicar a seleção predefinida nessa queda.  Esta coluna pode ser qualquer tipo, mas o mais simples é usar tipos bool ou numéricos, onde 0 é falso, e 1 é verdade.

 Se a coluna for do tipo de corda, a corda nula/vazia é considerada falsa, e qualquer outro valor é considerado verdadeiro. Para downdowns de seleção simples, o primeiro valor com um valor real é usado como a seleção padrão.  Para várias descidas de seleção, todos os valores com um valor real são usados como o conjunto selecionado por padrão. Os itens no dropdown são mostrados em qualquer ordem que a consulta devolveu linhas. 

Vejamos os parâmetros presentes no relatório Utilizadores Ativos. Clique no símbolo de edição ao lado do **TimeRange**.

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/009-time-range.png)

Isto lançará o item do menu Edit Parameter:

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/010-time-range-edit.png)

A consulta usa uma característica da linguagem de consulta de análise chamada **datatable** que permite gerar uma mesa arbitrária, cheia de conteúdo, fora do ar! Por exemplo, a seguinte consulta de análise:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Gera o resultado:

![Controlos de edição de livros de trabalho de Insights de aplicação](./media/usage-workbooks/011-data-table.png)

Um exemplo mais aplicável é a utilização de uma queda para escolher de um conjunto de países/regiões pelo nome:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A consulta apresentará os resultados da seguinte forma:

![Queda do país](./media/usage-workbooks/012-country-dropdown.png)

As dropdowns são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora possa fazer o seu próprio parâmetro de intervalo de tempo personalizado através do tipo de parâmetro de dropdown, também pode utilizar o tipo de parâmetro de intervalo de tempo fora da caixa se não precisar do mesmo grau de flexibilidade. 

Os tipos de parâmetros de intervalo de tempo têm 15 gamas padrão que vão de cinco minutos para os últimos 90 dias. Existe também uma opção para permitir a seleção personalizada do intervalo de tempo, que permite ao operador do relatório escolher valores explícitos de início e paragem para o intervalo de tempo.

### <a name="resource-picker"></a>Picker de recursos

O tipo de parâmetro de recolha de recursos dá-lhe a capacidade de examinar o seu relatório a certos tipos de recursos. Um exemplo de livro pré-construído que aproveita o tipo de picker de recursos é o livro de insights **de falha.**

![Queda do país](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Guardar e partilhar livros com a equipa

Os livros de trabalho são guardados dentro de um recurso Application Insights, quer na secção **My Reports** que seja privado de si ou na secção **Relatórios Partilhados** que esteja acessível a todos os que tenham acesso ao recurso Application Insights. Para ver todos os livros de reposição do recurso, clique no botão **Open** na barra de ação.

Para partilhar um livro que está atualmente em **My Reports:**

1. Clique em **Abrir** na barra de ação
2. Clique no "..." botão ao lado do livro que você quer partilhar
3. Clique em **mover-se para relatórios partilhados**.

Para partilhar um livro com um link ou via e-mail, clique em **Partilhar** na barra de ação. Tenha em mente que os destinatários do link precisam de ter acesso a este recurso no portal Azure para visualizar o livro de trabalho. Para edição, os destinatários precisam de pelo menos permissões contributivas para o recurso.

Para fixar um link para um livro de trabalho a um Painel De Instrumentos Azure:

1. Clique em **Abrir** na barra de ação
2. Clique no "..." botão ao lado do livro que você quer fixar
3. Clique **em Pin para painel de instrumentos**.

## <a name="contributing-workbook-templates"></a>Modelos de livro contributante

Criou um modelo de livro incrível e quer partilhá-lo com a comunidade? Para saber mais, visite o nosso [repo GitHub.](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)

## <a name="next-steps"></a>Passos seguintes
- Para permitir experiências de utilização, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [visualizações de páginas.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)
- Se já envia eventos personalizados ou visualizações de página, explore as ferramentas de Utilização para saber como os utilizadores usam o seu serviço.
    - [Utilizadores, Sessões, Eventos](../../azure-monitor/app/usage-segmentation.md)
    - [Funis](../../azure-monitor/app/usage-funnels.md)
    - [Retenção](../../azure-monitor/app/usage-retention.md)
    - [Fluxos do Utilizador](../../azure-monitor/app/usage-flows.md)
    - [Adicionar contexto de utilizador](../../azure-monitor/app/usage-send-user-context.md)
