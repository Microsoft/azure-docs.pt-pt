---
title: Integrar a Analytics de Registos e Excel
description: Obtenha uma consulta log analytics no Excel e atualize os resultados dentro do Excel.
ms.subservice: logs
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: 6fa181a35c46ed16e4e8c1884e66c54984c418ca
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619252"
---
# <a name="integrate-log-analytics-and-excel"></a>Integrar a Analytics de Registos e Excel

Pode integrar o Azure Monitor Log Analytics e o Microsoft Excel utilizando a consulta M e a API do Log Analytics. Esta integração permite-lhe enviar até 500.000 registos para o Excel, desde que o volume total dos resultados não exceda 61MiB.

> [!NOTE]
> Como o Excel é uma aplicação de cliente local, as limitações de hardware e software locais têm impacto no seu desempenho e capacidade de processar grandes conjuntos de dados.

## <a name="create-your-m-query-in-log-analytics"></a>Crie a sua consulta M no Log Analytics 

1. **Crie e execute a sua consulta** em analítica de Log como normalmente faria. Não se preocupe se atingir a limitação de 10.000 registos na interface do utilizador.  Recomendamos que utilize datas relativas - como a função 'ago' ou o selecionador de tempo de UI - para que o Excel refresque o conjunto de dados certo.
  
2. **Consulta de exportação** - Uma vez satisfeito com a consulta e os seus resultados, exporte a consulta para M utilizando a escolha do menu De **exportação para Power Bi (consulta M)** no menu *Exportação:*

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics consulta com os dados e opção de exportação" border="true":::



A escolha desta opção descarrega um ficheiro .txt que contém o código M que pode utilizar no Excel.

A consulta acima apresentada exporta o seguinte código M. Aqui está um exemplo do código M exportado para a consulta no nosso exemplo:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Conecte consulta ao Excel 

Para importar a consulta. 

1. Abra o Microsoft Excel. 
1. Na fita, vá ao menu **Data.** Selecione **obter dados**. De **outras fontes,** selecione **consulta em branco:**
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Importar do branco na opção Excel" border="true":::

1. Na janela de consulta de energia selecione **editor avançado**:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Excel Advanced editor de consulta" border="true":::

 
1. Substitua o texto no editor avançado pela consulta exportada da Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Criar uma consulta em branco" border="true":::
 
1. Selecione **Fazer**, e depois **Carregar e fechar**. O Excel executa a consulta utilizando a API de análise de registo e o conjunto de resultados mostrado.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Resultados de consulta no Excel" border="true":::

> [!Note]
> Se o número de registos for inferior ao esperado, o volume dos resultados poderá exceder o limite de 61MiB. Tente utilizar `project` ou na sua consulta limitar as `project-away` colunas à que necessita.

##  <a name="refreshing--data"></a>Dados refrescantes

Pode atualizar os seus dados diretamente do Excel. No grupo de **menus Data** na fita Excel, selecione o botão **Refresh.**
 
## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as integrações do Excel com fontes de dados externas, consulte [dados de importação de fontes de dados externas (Consulta de Energia)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)