---
title: Práticas recomendadas para usar Power BI para consultar e Visualizar dados de Data Explorer do Azure
description: Neste artigo, você aprenderá as práticas recomendadas para usar Power BI para consultar e Visualizar dados de Data Explorer do Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024023"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Práticas recomendadas para usar Power BI para consultar e Visualizar dados de Data Explorer do Azure

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. [Power bi](https://docs.microsoft.com/power-bi/) é uma solução de análise de negócios que permite visualizar seus dados e compartilhar os resultados em toda a organização. O Azure Data Explorer fornece três opções para se conectar a dados no Power BI. Use o [conector interno](power-bi-connector.md), [importe uma consulta do Azure data Explorer para Power bi](power-bi-imported-query.md)ou use uma [consulta SQL](power-bi-sql-query.md). Este artigo fornece dicas para consultar e visualizar seus dados de Data Explorer do Azure com Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Práticas recomendadas para usar o Power BI 

Ao trabalhar com terabytes de dados brutos novos, siga estas diretrizes para manter Power BI dashboards e relatórios mais encaixados e atualizados:

* **Luz de viagem** – Traga apenas os dados de que você precisa para seus relatórios Power bi. Para análise interativa profunda, use a [interface do usuário da Web do Azure data Explorer](web-query-data.md) que é otimizada para exploração ad hoc com a linguagem de consulta Kusto.

* **Modelo composto** – use o [modelo composto](https://docs.microsoft.com/power-bi/desktop-composite-models) para combinar dados agregados para painéis de nível superior com dados brutos operacionais filtrados. Você pode definir claramente quando usar dados brutos e quando usar uma exibição agregada. 

* Modo de importação em comparação com os **modos DirectQuery** – use o modo de **importação** para interação de conjuntos de dados menores. Use o modo **DirectQuery** para conjuntos de dados grandes e frequentemente atualizados. Por exemplo, crie tabelas de dimensões usando o modo de **importação** , já que elas são pequenas e não mudam com frequência. Defina o intervalo de atualização de acordo com a taxa esperada de atualizações de dados. Crie tabelas de fatos usando o modo **DirectQuery** , pois essas tabelas são grandes e contêm dados brutos. Use essas tabelas para apresentar dados filtrados usando o [detalhamento](https://docs.microsoft.com/power-bi/desktop-drillthrough)de Power bi.

* **Paralelismo** – o Azure data Explorer é uma plataforma de dados linearmente escalonável, portanto, você pode melhorar o desempenho da renderização do painel aumentando o paralelismo do fluxo de ponta a ponta da seguinte maneira:

   * Aumente o número de [conexões simultâneas no DirectQuery no Power bi](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Use [consistência fraca para melhorar o paralelismo](/azure/kusto/concepts/queryconsistency). Isso pode afetar a atualização dos dados.

* **Segmentações de dados efetivos** – use as [segmentações de sincronização](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) para impedir que os relatórios carreguem os mesmos antes de você estar pronto. Depois de estruturar o conjunto de dados, coloque todos os visuais e marque todas as segmentações, você pode selecionar a segmentação de sincronização para carregar apenas os dados necessários.

* **Usar filtros** -use o máximo de filtros de Power bi possível para concentrar a pesquisa de data Explorer do Azure nos fragmentos de dados relevantes.

* **Visuais eficientes** – selecione os visuais de melhor desempenho para seus dados.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Dicas para usar o conector de Data Explorer do Azure para Power BI para consultar dados

A seção a seguir inclui dicas e truques para usar a linguagem de consulta Kusto com Power BI. Usar o [conector de data Explorer do Azure para Power bi](power-bi-connector.md) para visualizar dados

### <a name="complex-queries-in-power-bi"></a>Consultas complexas no Power BI

Consultas complexas são mais facilmente expressas em Kusto do que em Power Query. Eles devem ser implementados como [funções Kusto](/azure/kusto/query/functions)e invocados em Power bi. Esse método é necessário ao usar o **DirectQuery** com instruções `let` em sua consulta Kusto. Como Power BI une duas consultas e `let` instruções não podem ser usadas com o operador de `join`, podem ocorrer erros de sintaxe. Portanto, salve cada parte da junção como uma função Kusto e permita que Power BI ingresse essas duas funções juntas.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Como simular um operador de data/hora relativo

Power BI não contém um operador de data/hora *relativo* , como `ago()`.
Para simular `ago()`, use uma combinação de `DateTime.FixedLocalNow()` e `#duration` funções Power BI.

Em vez desta consulta usando o operador de `ago()`:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Use a seguinte consulta equivalente:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Alcançando limites de consulta do Kusto 

As consultas Kusto retornam, por padrão, até 500.000 linhas ou 64 MB, conforme descrito em [limites de consulta](/azure/kusto/concepts/querylimits). Você pode substituir esses padrões usando **as opções avançadas** na janela de conexão do **Data Explorer do Azure (Kusto)** :

![Opções avançadas](media/power-bi-best-practices/advanced-options.png)

Essas opções emitem [instruções SET](/azure/kusto/query/setstatement) com sua consulta para alterar os limites de consulta padrão:

  * O **número de registros de resultados de consulta Limit** gera um `set truncationmaxrecords`
  * O **tamanho dos dados do resultado da consulta de limite em bytes** gera um `set truncationmaxsize`
  * **Desabilitar o truncamento do conjunto de resultados** gera um `set notruncation`

### <a name="using-query-parameters"></a>Usando parâmetros de consulta

Você pode usar [parâmetros de consulta](/azure/kusto/query/queryparametersstatement) para modificar a consulta dinamicamente. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Usando um parâmetro de consulta nos detalhes da conexão

Use um parâmetro de consulta para filtrar informações na consulta e otimizar o desempenho da consulta.
 
Na janela **editar consultas** , > **página inicial** **Editor avançado**

1. Localize a seguinte seção da consulta:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Por exemplo:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Substitua a parte relevante da consulta pelo parâmetro. Divida a consulta em várias partes e as concatene novamente usando um e comercial (&), juntamente com o parâmetro.

   Por exemplo, na consulta acima, vamos pegar o `State == 'ALABAMA'` parte e dividi-lo em: `State == '` e `'` e colocaremos o parâmetro de `State` entre eles:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Se sua consulta contiver aspas, codifique-as corretamente. Por exemplo, a seguinte consulta: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   aparecerá na **Editor avançado** da seguinte maneira com duas aspas:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Ele deve ser substituído pela seguinte consulta com três aspas:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Usar um parâmetro de consulta nas etapas de consulta

Você pode usar um parâmetro de consulta em qualquer etapa de consulta que ofereça suporte a ele. Por exemplo, filtre os resultados com base no valor de um parâmetro.

![filtrar resultados usando um parâmetro](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Não use Power BI Agendador de atualização de dados para emitir comandos de controle para Kusto

O Power BI inclui um Agendador de atualização de dados que pode emitir consultas periodicamente em uma fonte de dados. Esse mecanismo não deve ser usado para agendar comandos de controle para Kusto porque Power BI assume que todas as consultas são somente leitura.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI pode enviar somente consultas curtas (&lt;caracteres 2000) para Kusto

Se a execução de uma consulta no Power BI resultar no seguinte erro: _"DataSource. Error: Web. Contents falhou ao obter conteúdo de..."_ a consulta provavelmente tem mais de 2000 caracteres. Power BI usa **PowerQuery** para consultar o Kusto emitindo uma solicitação HTTP Get que codifica a consulta como parte do URI que está sendo recuperado. Portanto, as consultas Kusto emitidas por Power BI são limitadas ao comprimento máximo de um URI de solicitação (2000 caracteres, menos deslocamento pequeno). Como alternativa, você pode definir uma [função armazenada](/azure/kusto/query/schema-entities/stored-functions) em Kusto e ter Power bi usar essa função na consulta.

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados usando o conector de Data Explorer do Azure para Power BI](power-bi-connector.md)




