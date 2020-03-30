---
title: Boas práticas para usar o Power BI para consultar e visualizar dados do Azure Data Explorer
description: Neste artigo, aprende-se as melhores práticas para utilizar o Power BI para consultar e visualizar dados do Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251742"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Boas práticas para usar o Power BI para consultar e visualizar dados do Azure Data Explorer

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente dimensionável para dados telemétricos e de registo. [O Power BI](https://docs.microsoft.com/power-bi/) é uma solução de análise de negócios que permite visualizar os seus dados e partilhar os resultados em toda a sua organização. O Azure Data Explorer fornece três opções para a ligação aos dados no Power BI. Utilize o [conector incorporado,](power-bi-connector.md) [importe uma consulta do Azure Data Explorer para o Power BI,](power-bi-imported-query.md)ou utilize uma [consulta SQL](power-bi-sql-query.md). Este artigo fornece-lhe dicas para consultar e visualizar os dados do Azure Data Explorer com o Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Boas práticas para usar Power BI 

Ao trabalhar com terabytes de dados crus frescos, siga estas orientações para manter os dashboards e relatórios power BI snappy e atualizados:

* **Luz de viagem** - Traga apenas os dados necessários para os seus relatórios para o Power BI. Para uma análise interativa profunda, utilize o [Azure Data Explorer Web UI](web-query-data.md) que está otimizado para exploração ad-hoc com a Linguagem de Consulta kusto.

* **Modelo composto** - Utilize o [modelo composto](https://docs.microsoft.com/power-bi/desktop-composite-models) para combinar dados agregados para dashboards de alto nível com dados brutos operacionais filtrados. Pode definir claramente quando usar dados brutos e quando utilizar uma vista agregada. 

* **Modo de importação versus modo DirectQuery** - Utilize o modo **de importação** para interação de conjuntos de dados mais pequenos. Utilize o modo **DirectQuery** para conjuntos de dados grandes e frequentemente atualizados. Por exemplo, crie tabelas de dimensão utilizando o modo **Import** uma vez que são pequenas e não mudam frequentemente. Detete o intervalo de atualização de acordo com a taxa esperada de atualizações de dados. Crie tabelas de factos utilizando o modo **DirectQuery,** uma vez que estas tabelas são grandes e contêm dados brutos. Utilize estas tabelas para apresentar dados filtrados utilizando [a perfuração](https://docs.microsoft.com/power-bi/desktop-drillthrough)Power BI .

* **Paralelismo** – O Azure Data Explorer é uma plataforma de dados línear escalável, pelo que pode melhorar o desempenho da renderização do dashboard aumentando o paralelismo do fluxo de ponta a ponta da seguinte forma:

   * Aumente o número de [ligações simultâneas no DirectQuery em Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Use [uma consistência fraca para melhorar o paralelismo.](/azure/kusto/concepts/queryconsistency) Isto pode ter um impacto na frescura dos dados.

* **Cortadores eficazes** – Utilize cortadores de sincronização para evitar que os [relatórios](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) carreguem dados antes de estar pronto. Depois de estruturar o conjunto de dados, colocar todos os visuais e marcar todos os cortadores, pode selecionar o cortador de sincronização para carregar apenas os dados necessários.

* **Utilizar filtros** - Utilize o maior número possível de filtros Power BI para concentrar a pesquisa do Azure Data Explorer nos fragmentos de dados relevantes.

* **Visuais eficientes** – Selecione os visuais mais performantes para os seus dados.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Dicas para a utilização do conector Azure Data Explorer para Power BI para consultar dados

A secção seguinte inclui dicas e truques para usar a linguagem de consulta Kusto com Power BI. Utilize o [conector Azure Data Explorer para o Power BI](power-bi-connector.md) para visualizar dados

### <a name="complex-queries-in-power-bi"></a>Consultas complexas em Power BI

Consultas complexas são mais facilmente expressas em Kusto do que em Power Query. Devem ser implementados como [funções kusto](/azure/kusto/query/functions)e invocados no Power BI. Este método é necessário ao utilizar `let` **directquery** com declarações na sua consulta Kusto. Como o Power BI se `let` junta a duas consultas, `join` e as declarações não podem ser usadas com o operador, podem ocorrer erros de sintaxe. Portanto, guarde cada parte da junta como uma função Kusto e permita que o Power BI junte estas duas funções.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Como simular um operador de data-data relativa

O Power BI não *relative* contém um operador `ago()`relativo de data-data, como .
Para `ago()`simular, utilize `DateTime.FixedLocalNow()` `#duration` uma combinação de funções e Power BI.

Em vez desta consulta `ago()` utilizando o operador:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Utilize a seguinte consulta equivalente:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Atingir os limites da consulta de Kusto 

As consultas de Kusto regressam, por defeito, até 500.000 linhas ou 64 MB, conforme descrito nos [limites](/azure/kusto/concepts/querylimits)de consulta . Pode anular estes incumprimentos utilizando **opções avançadas** na janela de ligação **Azure Data Explorer (Kusto):**

![opções avançadas](media/power-bi-best-practices/advanced-options.png)

Estas opções emitem [declarações definidas](/azure/kusto/query/setstatement) com a sua consulta para alterar os limites de consulta padrão:

  * **Limitar o número recorde** de resultados da consulta gera um`set truncationmaxrecords`
  * Limitar o tamanho dos dados de **resultados da consulta em Bytes** gera um`set truncationmaxsize`
  * **Desativar a truncação do resultado** gera um`set notruncation`

### <a name="using-query-parameters"></a>Usando parâmetros de consulta

Pode utilizar [parâmetros](/azure/kusto/query/queryparametersstatement) de consulta para modificar a sua consulta dinamicamente. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Utilização de um parâmetro de consulta nos detalhes da ligação

Utilize um parâmetro de consulta para filtrar informações na consulta e otimizar o desempenho da consulta.
 
Na janela **Editar Consultas,** **Editor Avançado** da **Casa** > 

1. Encontre a seguinte secção da consulta:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Por exemplo:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Substitua a parte relevante da consulta pelo seu parâmetro. Divida a consulta em várias partes e concatená-las de volta usando uma ampersand (&), juntamente com o parâmetro.

   Por exemplo, na consulta acima, vamos `State == 'ALABAMA'` pegar na parte e `State == '` `'` dividi-la para: `State` e colocaremos o parâmetro entre eles:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Se a sua consulta contiver aspas, codifique-as corretamente. Por exemplo, a seguinte consulta: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   aparecerá no **Editor Avançado** da seguinte forma com duas aspas:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Deve ser substituído pela seguinte consulta por três aspas:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Use um parâmetro de consulta nos passos de consulta

Você pode usar um parâmetro de consulta em qualquer passo de consulta que o suporte. Por exemplo, filtrar os resultados com base no valor de um parâmetro.

![resultados do filtro usando um parâmetro](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Não utilize o programador de atualização de dados do Power BI para emitir comandos de controlo para Kusto

O Power BI inclui um programador de atualização de dados que pode periodicamente emitir consultas contra uma fonte de dados. Este mecanismo não deve ser usado para agendar comandos de controlo para Kusto porque o Power BI assume que todas as consultas são apenas leitura.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI só pode&lt;enviar consultas curtas (2000 caracteres) para Kusto

Se executar uma consulta no Power BI resultar no seguinte erro: _"DataSource.Error: Web.O conteúdo não conseguiu obter conteúdo de..."_ a consulta é provavelmente superior a 2000 caracteres. Power BI usa **PowerQuery** para consultar Kusto, emitindo um pedido HTTP GET que codifica a consulta como parte do URI sendo recuperado. Portanto, as consultas de Kusto emitidas pelo Power BI limitam-se ao comprimento máximo de um pedido URI (2000 caracteres, menos pequena compensação). Como suposições, pode definir uma [função armazenada](/azure/kusto/query/schema-entities/stored-functions) em Kusto e ter o Power BI a usar essa função na consulta.

## <a name="next-steps"></a>Passos seguintes

[Visualizar dados utilizando o conector Azure Data Explorer para Power BI](power-bi-connector.md)




