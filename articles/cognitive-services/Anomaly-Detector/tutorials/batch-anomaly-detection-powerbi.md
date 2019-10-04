---
title: Visualizar anomalias utilizando a deteção de lote e o Power BI
titleSuffix: Azure Cognitive Services
description: Use a API do detector de anomalias e Power BI para visualizar anomalias em seus dados de série temporal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: aahi
ms.openlocfilehash: fa78e737cd863d19e294c5001dfd27b07760521f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71840867"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Tutorial: Visualizar anomalias utilizando a deteção de lote e o Power BI

Use este tutorial para localizar anomalias dentro de um conjunto de dados de série temporal como um lote. Usando Power BI área de trabalho, você usará um arquivo do Excel, preparará os dados para a API do detector de anomalias e visualizará anomalias estatísticas em todo o uso.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Usar Power BI Desktop para importar e transformar um conjunto de dados de série temporal
> * Integrar Power BI Desktop com a API do detector de anomalias para detecção de anomalias do lote
> * Visualize anomalias encontradas em seus dados, incluindo valores esperados e vistos e limites de detecção de anomalias.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Power bi desktop](https://powerbi.microsoft.com/get-started/), disponível gratuitamente.
* Um arquivo do Excel (. xlsx) que contém pontos de dados de série temporal. Os dados de exemplo deste guia de início rápido podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Carregar e formatar os dados de série temporal

Para começar, abra Power BI Desktop e carregue os dados de série temporal que você baixou dos pré-requisitos. Esse arquivo do Excel contém uma série de pares de carimbo de data/hora UTC (tempo Universal Coordenado).  

> [!NOTE]
> Power BI pode usar dados de uma ampla variedade de fontes, como arquivos. csv, bancos de dados SQL, armazenamento de BLOBs do Azure e muito mais.  

Na janela principal do Power BI Desktop, clique na faixa de faixas **página inicial** . No grupo de **dados externos** da faixa de bits, abra o menu suspenso **obter dados** e clique em **Excel**.

![Uma imagem do botão "obter dados" no Power BI](../media/tutorials/power-bi-get-data-button.png)

Depois que a caixa de diálogo for exibida, navegue até a pasta onde você baixou o arquivo example. xlsx e selecione-o. Após a caixa de diálogo **navegador** ser exibida, clique em **Plan1**e em **Editar**.

![Uma imagem da tela "Navigator" da fonte de dados no Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI converterá os carimbos de data/hora na primeira coluna em um tipo de dados `Date/Time`. Esses carimbos de data/hora devem ser convertidos em texto para serem enviados para a API do detector de anomalias. Se o editor de Power Query não abrir automaticamente, clique em **editar consultas** na guia página inicial. 

Clique na faixa de faixas **transformar** no Editor de Power Query. No grupo **qualquer coluna** , abra o menu suspenso **tipo de dados:** e selecione **texto**.

![Uma imagem da tela "Navigator" da fonte de dados no Power BI](../media/tutorials/data-type-drop-down.png)

Quando você receber um aviso sobre como alterar o tipo de coluna, clique em **substituir atual**. Posteriormente, clique em **fechar & aplicar** ou **aplicar** na faixa de opções **página inicial** . 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Criar uma função para enviar os dados e formatar a resposta

Para formatar e enviar o arquivo de dados para a API do detector de anomalias, você pode invocar uma consulta na tabela criada acima. No editor de Power Query, na faixa de bits **página inicial** , abra o menu suspenso **nova fonte** e clique em **consulta em branco**.

Verifique se a nova consulta está selecionada e clique em **Editor avançado**. 

![Uma imagem do botão "Editor Avançado" no Power BI](../media/tutorials/advanced-editor-screen.png)

No Editor Avançado, use o seguinte trecho de Power Query M para extrair as colunas da tabela e enviá-las para a API. Posteriormente, a consulta criará uma tabela a partir da resposta JSON e a retornará. Substitua a variável `apiKey` pela chave de API do detector de anomalias válida e `endpoint` pelo ponto de extremidade. Depois de inserir a consulta na Editor Avançado, clique em **concluído**.

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),

    respTable = Table.FromColumns({
                    
                     Table.Column(inputTable, "Timestamp")
                     ,Table.Column(inputTable, "Value")
                     , Record.Field(jsonresp, "IsAnomaly") as list
                     , Record.Field(jsonresp, "ExpectedValues") as list
                     , Record.Field(jsonresp, "UpperMargins")as list
                     , Record.Field(jsonresp, "LowerMargins") as list
                     , Record.Field(jsonresp, "IsPositiveAnomaly") as list
                     , Record.Field(jsonresp, "IsNegativeAnomaly") as list

                  }, {"Timestamp", "Value", "IsAnomaly", "ExpectedValues", "UpperMargin", "LowerMargin", "IsPositiveAnomaly", "IsNegativeAnomaly"}
               ),
    
    respTable1 = Table.AddColumn(respTable , "UpperMargins", (row) => row[ExpectedValues] + row[UpperMargin]),
    respTable2 = Table.AddColumn(respTable1 , "LowerMargins", (row) => row[ExpectedValues] -  row[LowerMargin]),
    respTable3 = Table.RemoveColumns(respTable2, "UpperMargin"),
    respTable4 = Table.RemoveColumns(respTable3, "LowerMargin"),

    results = Table.TransformColumnTypes(

                respTable4,
                {{"Timestamp", type datetime}, {"Value", type number}, {"IsAnomaly", type logical}, {"IsPositiveAnomaly", type logical}, {"IsNegativeAnomaly", type logical},
                 {"ExpectedValues", type number}, {"UpperMargins", type number}, {"LowerMargins", type number}}
              )

 in results
```

Invoque a consulta em sua folha de dados selecionando `Sheet1` abaixo de **Inserir parâmetro**e clique em **invocar**. 

![Uma imagem do botão "Editor Avançado"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacidade e autenticação da fonte de dados

> [!NOTE]
> Lembre-se das políticas da sua organização para privacidade e acesso a dados. Consulte [Power bi desktop níveis de privacidade](https://docs.microsoft.com/power-bi/desktop-privacy-levels) para obter mais informações.

Você pode receber uma mensagem de aviso ao tentar executar a consulta, pois ela utiliza uma fonte de dados externa. 

![Uma imagem que mostra um aviso criado por Power BI](../media/tutorials/blocked-function.png)

Para corrigir isso, clique em **arquivo**, **Opções e configurações**. Em seguida, clique em **Opções**. Abaixo do **arquivo atual**, selecione **privacidade**e **ignore os níveis de privacidade e, potencialmente, melhorar o desempenho**. 

Além disso, você pode receber uma mensagem solicitando que você especifique como deseja se conectar à API.

![Uma imagem que mostra uma solicitação para especificar credenciais de acesso](../media/tutorials/edit-credentials-message.png)

Para corrigir isso, clique em **Editar credenciais** na mensagem. Depois que a caixa de diálogo for exibida, selecione **anônimo** para se conectar à API anonimamente. Em seguida, clique em **Connect** (Ligar). 

Posteriormente, clique em **fechar & aplicar** na faixa de opções **página inicial** para aplicar as alterações.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizar a resposta da API do detector de anomalias

Na tela principal do Power BI, comece a usar as consultas criadas acima para visualizar os dados. Primeiro, selecione o **gráfico de linhas** nas **visualizações**. Em seguida, adicione o carimbo de data/hora da função invocada ao **eixo**do gráfico de linhas. Clique com o botão direito do mouse nele e selecione **carimbo de data/hora**. 

![Clicando com o botão direito do mouse no valor timestamp](../media/tutorials/timestamp-right-click.png)

Adicione os campos a seguir da **função invocada** ao campo **valores** do gráfico. Use a captura de tela abaixo para ajudar a criar seu gráfico.

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Uma imagem da nova tela de medida rápida](../media/tutorials/chart-settings.png)

Depois de adicionar os campos, clique no gráfico e redimensione-o para mostrar todos os pontos de dados. Seu gráfico será semelhante à captura de tela abaixo:

![Uma imagem da nova tela de medida rápida](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Exibir pontos de dados de anomalias

No lado direito da janela Power BI, abaixo do painel **campos** , clique com o botão direito do mouse no **valor** na **consulta de função invocada**e clique em **nova medida rápida**.

![Uma imagem da nova tela de medida rápida](../media/tutorials/new-quick-measure.png)

Na tela que é exibida, selecione **valor filtrado** como o cálculo. Defina o **valor de base** como `Sum of Value`. Em seguida, arraste `IsAnomaly` dos campos de **função invocados** para **Filtrar**. Selecione `True` no menu suspenso **filtro** .

![Uma imagem da nova tela de medida rápida](../media/tutorials/new-quick-measure-2.png)

Depois de clicar em **OK**, você terá um campo `Value for True`, na parte inferior da lista de seus campos. Clique com o botão direito do mouse e renomeie-o como **anomalia**. Adicione-o aos **valores**do gráfico. Em seguida, selecione a ferramenta **formato** e defina o tipo de eixo X como **categórico**.

![Uma imagem da nova tela de medida rápida](../media/tutorials/format-x-axis.png)

Aplique cores ao seu gráfico clicando na ferramenta de **formato** e nas **cores de dados**. O gráfico deve ser semelhante ao seguinte:

![Uma imagem da nova tela de medida rápida](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Detecção de anomalias de streaming com Azure Databricks](anomaly-detection-streaming-databricks.md)
