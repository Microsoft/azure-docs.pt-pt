---
title: Criar relatórios interativos com Azure Monitor pastas de trabalho | Microsoft docs
description: Simplifique relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bbbf511286c63acf4a939e0a0e7d9c3dc9efa75b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899375"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Criar relatórios interativos com Azure Monitor pastas de trabalho

As pastas de trabalho combinam texto, [consultas de análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), métricas do Azure e parâmetros em relatórios interativos sofisticados. As pastas de trabalho são editáveis por outros membros da equipe que têm acesso aos mesmos recursos do Azure.

As pastas de trabalho são úteis para cenários como:

* Explorando o uso de seu aplicativo quando você não conhece as métricas de interesse com antecedência: números de usuários, taxas de retenção, taxas de conversão, etc. Ao contrário de outras ferramentas de análise de uso, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
* Explicando à sua equipe como um recurso recém-lançado está sendo executado, mostrando as contagens de usuários para as principais interações e outras métricas.
* Compartilhando os resultados de um experimento A/B em seu aplicativo com outros membros de sua equipe. Você pode explicar as metas para o experimento com texto e, em seguida, mostrar cada métrica de uso e consulta de análise usada para avaliar o experimento, juntamente com as chamadas claras para se cada métrica estava acima ou abaixo do destino.
* Relatar o impacto de uma interrupção no uso de seu aplicativo, combinar dados, explicação de texto e uma discussão das próximas etapas para evitar interrupções no futuro.

## <a name="starting-with-a-template-or-saved-workbook"></a>Iniciando com um modelo ou pasta de trabalho salva

Uma pasta de trabalho é composta por seções que consistem em gráficos, tabelas, texto e controles de entrada independentemente de serem editados. Para entender melhor as pastas de trabalho, é melhor abrir uma. 

Selecione **pastas de trabalho** no menu à esquerda de dentro da experiência de Application insights para seu aplicativo.

![Captura de tela de navegação para pastas de trabalho](./media/usage-workbooks/001-workbooks.png)

Isso inicia uma galeria de pastas de trabalho com várias pastas de trabalho predefinidas para ajudá-lo a começar.

![Captura de tela da Galeria de pastas de trabalho](./media/usage-workbooks/002-workbook-gallery.png)

Começaremos com o **modelo padrão**, localizado no **início rápido**do título.

![Captura de tela da Galeria de pastas de trabalho](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Editando, reorganizando, clonando e excluindo seções da pasta de trabalho

As pastas de trabalho têm dois modos: **modo de edição**e **modo de leitura**. Quando a pasta de trabalho padrão é iniciada pela primeira vez, ela é aberta no **modo de edição**. Isso mostra todo o conteúdo da pasta de trabalho, incluindo quaisquer etapas e parâmetros ocultos. O **modo de leitura** apresenta uma exibição de estilo de relatório simplificada. Isso permite abstrair a complexidade da criação de um relatório e ainda ter a mecânica subjacente apenas alguns cliques quando necessário para modificação.

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/editing-controls-new.png)

1. Quando terminar de editar uma seção, clique em **edição concluída** no canto inferior esquerdo da seção.

2. Para criar uma duplicata de uma seção, clique no ícone **clonar esta seção** . A criação de seções duplicadas é uma ótima maneira de iterar em uma consulta sem perder as iterações anteriores.

3. Para mover uma seção para cima em uma pasta de trabalho, clique no ícone **mover para cima** ou **mover para baixo** .

4. Para remover uma seção permanentemente, clique no ícone **remover** .

## <a name="adding-text-and-markdown-sections"></a>Adicionando seções de texto e redução

Adicionar títulos, explicações e comentários às suas pastas de trabalho ajuda a transformar um conjunto de tabelas e gráficos em uma narração. As seções de texto em pastas de trabalho dão suporte à [sintaxe de redução](https://daringfireball.net/projects/markdown/) para formatação de texto, como títulos, negrito, itálico e listas com marcadores.

Para adicionar uma seção de texto à pasta de trabalho, use o botão **Adicionar texto** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionando seções de consulta

![Seção de consulta em pastas de trabalho](./media/usage-workbooks/analytics-section-new.png)

Para adicionar a seção de consulta à sua pasta de trabalho, use o botão **Adicionar consulta** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

As seções de consulta são altamente flexíveis e podem ser usadas para responder a perguntas como:

* Quantas exceções o site emitiu durante o mesmo período de tempo como um declínio no uso?
* Qual foi a distribuição de tempos de carregamento de página para usuários que visualizam alguma página?
* Quantos usuários exibiram algum conjunto de páginas no seu site, mas não algum outro conjunto de páginas? Isso pode ser útil para entender se você tem clusters de usuários que usam subconjuntos diferentes da funcionalidade do site (use o operador de `join` com o modificador `kind=leftanti` na [linguagem de consulta Kusto](/azure/kusto/query/)).

Você também não está limitado a consultar o contexto do aplicativo do qual iniciou a pasta de trabalho. Você pode consultar vários Application Insights aplicativos monitorados, bem como espaços de trabalho de Log Analytics, contanto que tenha permissão de acesso a esses recursos.

Para consultar recursos de Application Insights externos adicionais, use o identificador do **aplicativo** .

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Essa consulta combina solicitações de três aplicativos diferentes. Um aplicativo chamado app01, um aplicativo chamado app02 e as solicitações do recurso de Application Insights local.

Para efetuar pull de dados de um espaço de trabalho de Log Analytics externo, use o identificador de **espaço de trabalho** .

Para saber mais sobre consultas entre recursos, consulte a [orientação oficial](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Configurações avançadas de consulta analítica

Cada seção tem suas próprias configurações avançadas, que podem ser acessadas por meio do ícone de configurações ![seção Application Insights pastas de trabalho editar controles](./media/usage-workbooks/005-settings.png) localizados à direita do botão **adicionar parâmetros** .

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Largura personalizada**    | Defina isso para tornar um item um tamanho arbitrário, para que você possa ajustar muitos itens em uma única linha, permitindo que você organize melhor seus gráficos e tabelas em relatórios interativos sofisticados.  |
   | **Condicionalmente visível** | Use isso para ocultar as etapas com base em um parâmetro no modo de leitura. |
   | **Exportar um parâmetro**| Isso permite que uma linha selecionada na grade ou no gráfico cause etapas posteriores para alterar valores ou tornar-se visível.  |
   | **Mostrar consulta quando não estiver editando** | Isso exibe a consulta acima do gráfico ou tabela, mesmo quando estiver no modo de leitura.
   | **Mostrar botão abrir no Analytics quando não estiver editando** | Isso adiciona o ícone de análise azul ao canto direito do gráfico para permitir o acesso com um clique.|

A maioria dessas configurações é bastante intuitiva, mas para entender a **exportação de um parâmetro** , é melhor examinar uma pasta de trabalho que usa essa funcionalidade.

Uma das pastas de trabalho predefinidas fornece informações sobre usuários ativos.

A primeira seção da pasta de trabalho baseia-se nos dados de consulta analítica:

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/003-active-users.png)

A segunda seção também é baseada em dados de consulta analítica, mas a seleção de uma linha na primeira tabela atualizará interativamente o conteúdo do gráfico:

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/004-active-users-trend.png)

 Isso é possível por meio do uso de **quando um item é selecionado, exportar** configurações avançadas de parâmetro que estão habilitadas na consulta de análise da tabela.

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/007-settings-export.png)

Em seguida, a segunda consulta de análise utiliza os valores exportados quando uma linha é selecionada. Se nenhuma linha for selecionada, o padrão será a linha que representa os valores gerais. 

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

## <a name="adding-metrics-sections"></a>Adicionando seções de métricas

As seções de métricas oferecem acesso completo para incorporar Azure Monitor dados de métricas em seus relatórios interativos. Muitas das pastas de trabalho predefinidas conterão dados de consulta analítica e dados de métrica, permitindo que você aproveite ao máximo os dois recursos em um só lugar. Você também tem a capacidade de extrair dados de métrica de recursos em qualquer uma das assinaturas às quais você tem acesso.

Aqui está um exemplo de dados da máquina virtual que estão sendo obtidos em uma pasta de trabalho para fornecer uma visualização em grade do desempenho da CPU:

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionando seções de parâmetro

Os parâmetros da pasta de trabalho permitem alterar valores na pasta de trabalho sem precisar editar manualmente as seções de consulta ou texto.  Isso elimina a necessidade de entender a linguagem de consulta de análise subjacente e expandir muito o possível público de relatórios baseados em pastas de trabalho.

Os valores dos parâmetros são substituídos em seções de consulta, texto ou outro parâmetro, colocando o nome do parâmetro entre chaves, como ``{parameterName}``.  Os nomes de parâmetro são limitados a regras semelhantes como identificadores JavaScript, basicamente caracteres alfabéticos ou sublinhados, seguidos de caracteres alfanuméricos ou sublinhados. Por exemplo, **a1** é permitido, mas **1a** não é permitido.

Os parâmetros são lineares, começando na parte superior de uma pasta de trabalho e fluindo para etapas posteriores.  Os parâmetros declarados posteriormente em uma pasta de trabalho podem substituir aqueles que foram declarados mais detalhadamente.  Isso também permite que os parâmetros que usam consultas acessem os valores dos parâmetros definidos mais adiante.  Dentro da própria etapa de um parâmetro, os parâmetros também são lineares, da esquerda para a direita, em que os parâmetros à direita podem depender de um parâmetro declarado anteriormente na mesma etapa.
 
Há quatro tipos diferentes de parâmetros com suporte no momento:

  |         |          |
   | ---------------- |:-----|
   | **Text** (Texto)    | o usuário Editará uma caixa de texto e, opcionalmente, você poderá fornecer uma consulta para preencher o valor padrão. |
   | **Lista suspensa** | O usuário escolherá um conjunto de valores. |
   | **Seletor de intervalo de tempo**| O usuário escolherá um conjunto predefinido de valores de intervalo de tempo ou escolherá um intervalo de tempo personalizado.|
   | **Seletor de recursos** | O usuário escolherá os recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um usuário digita na caixa de texto é substituído diretamente na consulta, sem escape ou quot. Se o valor necessário for uma cadeia de caracteres, a consulta deverá ter aspas em volta do parâmetro (como **' {Parameter} '** ).

Isso permite que o valor em uma caixa de texto seja usado em qualquer lugar. Pode ser um nome de tabela, nome de coluna, nome de função, operador, etc.

O tipo de parâmetro de texto tem uma configuração **obter valor padrão da consulta de análise**, que permite que o autor da pasta de trabalho use uma consulta para popular o valor padrão dessa caixa de texto.

Ao usar o valor padrão de uma consulta de análise, somente o primeiro valor da primeira linha (linha 0, coluna 0) é usado como o valor padrão. Portanto, é recomendável limitar sua consulta para retornar apenas uma linha e uma coluna. Todos os outros dados retornados pela consulta são ignorados. 

Qualquer valor que a consulta retornar será substituído diretamente sem escape ou quot. Se a consulta não retornar nenhuma linha, o resultado do parâmetro será uma cadeia de caracteres vazia (se o parâmetro não for necessário) ou indefinido (se o parâmetro for necessário).

### <a name="using-a-dropdown"></a>Usando uma lista suspensa

O tipo de parâmetro DropDown permite criar um controle suspenso, permitindo a seleção de um ou vários valores.

A lista suspensa é preenchida por uma consulta de análise. Se a consulta retornar uma coluna, os valores nessa coluna serão o **valor** e o **rótulo** no controle suspenso. Se a consulta retornar duas colunas, a primeira coluna será o **valor**e a segunda coluna será o **rótulo** mostrado na lista suspensa.  Se a consulta retornar três colunas, a 3ª coluna será usada para indicar a seleção padrão nesse menu suspenso.  Essa coluna pode ser qualquer tipo, mas a mais simples é usar tipos bool ou numéricos, em que 0 é false e 1 é verdadeiro.

 Se a coluna for um tipo de cadeia de caracteres, uma cadeia de caracteres nula/vazia será considerada falsa e qualquer outro valor será considerado verdadeiro. Para listas suspensas de seleção única, o primeiro valor com um valor true é usado como a seleção padrão.  Para listas suspensas de seleção múltipla, todos os valores com um valor true são usados como o conjunto selecionado padrão. Os itens na lista suspensa são mostrados em qualquer ordem em que a consulta retorna linhas. 

Vamos examinar os parâmetros presentes no relatório de usuários ativos. Clique no símbolo de edição ao lado de **timerange**.

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/009-time-range.png)

Isso abrirá o item de menu Editar parâmetro:

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/010-time-range-edit.png)

A consulta usa um recurso da linguagem de consulta do Analytics chamado **DataTable** que permite gerar uma tabela arbitrária, cheia de conteúdo, sem ar fino! Por exemplo, a seguinte consulta de análise:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Gera o resultado:

![Controles de edição da seção Application Insights pastas de trabalho](./media/usage-workbooks/011-data-table.png)

Um exemplo mais aplicável é usar uma lista suspensa para escolher um conjunto de países/regiões por nome:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A consulta exibirá os resultados da seguinte maneira:

![Lista suspensa de país](./media/usage-workbooks/012-country-dropdown.png)

As listas suspensas são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora seja possível criar seu próprio parâmetro de intervalo de tempo personalizado por meio do tipo de parâmetro DropDown, você também pode usar o tipo de parâmetro de intervalo de tempo pronto para uso se não precisar do mesmo grau de flexibilidade. 

Os tipos de parâmetro de intervalo de tempo têm 15 intervalos padrão que vão de cinco minutos até os últimos 90 dias. Há também uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador do relatório escolha valores explícitos de início e parada para o intervalo de tempo.

### <a name="resource-picker"></a>Seletor de recursos

O tipo de parâmetro do seletor de recursos oferece a capacidade de fazer o escopo do relatório para determinados tipos de recursos. Um exemplo de pasta de trabalho predefinida que utiliza o tipo de seletor de recursos é a pasta de trabalho de **informações de falha** .

![Lista suspensa de país](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

As pastas de trabalho são salvas em um recurso de Application Insights, na seção **meus relatórios** , que é particular para você ou na seção **relatórios compartilhados** , que é acessível a todos com acesso ao recurso de Application insights. Para exibir todas as pastas de trabalho no recurso, clique no botão **abrir** na barra de ação.

Para compartilhar uma pasta de trabalho que está atualmente em **meus relatórios**:

1. Clique em **abrir** na barra de ação
2. Clique no botão "..." botão ao lado da pasta de trabalho que você deseja compartilhar
3. Clique em **mover para relatórios compartilhados**.

Para compartilhar uma pasta de trabalho com um link ou por email, clique em **compartilhar** na barra de ação. Tenha em mente que os destinatários do link precisam de acesso a esse recurso no portal do Azure para exibir a pasta de trabalho. Para fazer edições, os destinatários precisam de pelo menos permissões de colaborador para o recurso.

Para fixar um link em uma pasta de trabalho em um painel do Azure:

1. Clique em **abrir** na barra de ação
2. Clique no botão "..." botão ao lado da pasta de trabalho que você deseja fixar
3. Clique em **fixar no painel**.

## <a name="contributing-workbook-templates"></a>Modelos de pasta de trabalho de contribuição

Você criou um modelo de pasta de trabalho incrível e deseja compartilhá-lo com a Comunidade? Para saber mais, visite nosso [repositório GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Passos seguintes
- Para habilitar as experiências de uso, comece a enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já enviar eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários usam seu serviço.
    - [Utilizadores, Sessões, Eventos](../../azure-monitor/app/usage-segmentation.md)
    - [Funis](../../azure-monitor/app/usage-funnels.md)
    - [Retenção](../../azure-monitor/app/usage-retention.md)
    - [Fluxos do Utilizador](../../azure-monitor/app/usage-flows.md)
    - [Adicionar contexto de usuário](../../azure-monitor/app/usage-send-user-context.md)