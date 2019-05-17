---
title: Visualizar anomalias com deteção de batch e o Power BI
titlesuffix: Azure Cognitive Services
description: Utilize a API de detetor de anomalias e o Power BI para visualizar anomalias ao longo de seus dados de séries de tempo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 04/30/2019
ms.author: aahi
ms.openlocfilehash: de353c946da46b2bbe4e755e21e355f6bd18725b
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827144"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Tutorial: Visualizar anomalias com deteção de batch e o Power BI

Utilize este tutorial para localizar anomalias dentro de um conjunto de dados de séries de tempo como um lote. Utilizar o Power BI desktop, executará um ficheiro do Excel, preparar os dados para a API de detetor de anomalias e visualizar anomalias estatísticas em toda ele.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o Power BI Desktop para importar e transformar um conjunto de dados de séries de tempo
> * Integrar o Power BI Desktop com a API de detetor de anomalias para deteção de anomalias de batch
> * Visualize anomalias encontradas dentro de seus dados, incluindo os valores esperados e vistos e limites de deteção de anomalias.

## <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), disponível gratuitamente.
* Pontos de dados de séries de tempo que contém um excel ficheiro (. xlsx). Os dados de exemplo para este início rápido, podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Carregar e formatar os dados de séries de tempo

Para começar, abra o Power BI Desktop e carregar os dados de séries de tempo que transferiu a partir das pré-requisitos. Este ficheiro do excel contém uma série de pares de timestamp e o valor de hora Universal Coordenada (UTC).  

> [!NOTE]
> Power BI possa utilizar os dados a partir de uma grande variedade de origens, como ficheiros. csv, SQL bases de dados, armazenamento de Blobs do Azure e muito mais.  

Na janela principal do Power BI Desktop, clique nas **home page** faixa de opções. Na **dados externos** grupo da faixa de opções, abra o **obter dados** menu pendente e clique em **Excel**.

![Uma imagem do botão "Get Data" no Power BI](../media/tutorials/power-bi-get-data-button.png)

Depois de aparece a caixa de diálogo, navegue para a pasta onde transferiu o exemplo de ficheiro. xlsx e selecioná-lo. Depois do **Navigator** for apresentada a caixa de diálogo, clique em **Sheet1**e, em seguida **editar**.

![Uma imagem do ecrã de "Navegador" de origem de dados no Power BI](../media/tutorials/navigator-dialog-box.png)

Power BI irá converter os carimbos de data / na primeira coluna para um `Date/Time` tipo de dados. Esses carimbos de data / deve ser convertidos para texto para os enviar para a API de detetor de anomalias. Se o editor do Power Query não abrir automaticamente, clique em **editar consultas** na guia inicial. 

Clique nas **transformar** faixa de opções no Editor do Power Query. Na **qualquer coluna** grupo, abra o **tipo de dados:** menu pendente e selecione **texto**.

![Uma imagem do ecrã de "Navegador" de origem de dados no Power BI](../media/tutorials/data-type-drop-down.png)

Quando receber um aviso sobre como alterar o tipo de coluna, clique em **substitua atual**. Em seguida, clique em **fechar e aplicar** ou **Apply** no **home page** faixa de opções. 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Criar uma função para enviar os dados e formatar a resposta

Para formatar e enviar o ficheiro de dados para a API de detetor de anomalias, é possível invocar uma consulta na tabela criada acima. No Editor do Power Query, do **home page** faixa de opções, abra o **nova origem** menu pendente e clique em **consulta em branco**.

Certifique-se a consulta nova é selecionada, em seguida, clique em **Editor avançado**. 

![Uma imagem do botão "Editor avançado" no Power BI](../media/tutorials/advanced-editor-screen.png)

No Editor avançado, utilize o seguinte fragmento do Power Query M para extrair as colunas da tabela e enviá-lo para a API. Depois disso, a consulta irá criar uma tabela da resposta JSON e retorná-lo. Substitua a `apiKey` variável com a sua chave de API de detetor de anomalias válido, e `endpoint` com o ponto final. Depois de introduzir a consulta no Advanced Editor, clique em **feito**.

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

Invocar a consulta na sua folha de dados, selecionando `Sheet1` abaixo **introduzir parâmetro**e clique em **Invoke**. 

![Uma imagem do botão de "Editor avançado"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Autenticação e de privacidade da origem de dados

> [!NOTE]
> Lembre-se das políticas da sua organização para a privacidade dos dados e acesso. Ver [níveis de privacidade do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) para obter mais informações.

Poderá receber uma mensagem de aviso quando tentar executar a consulta uma vez que ele utiliza uma origem de dados externa. 

![Uma imagem que mostra um aviso criado pelo Power BI](../media/tutorials/blocked-function.png)

Para corrigir este problema, clique em **arquivo**, e **opções e definições**. Em seguida, clique em **opções**. Abaixo **ficheiro atual**, selecione **privacidade**, e **ignorar os níveis de privacidade e melhorar potencialmente o desempenho**. 

Além disso, poderá receber uma mensagem a pedir para especificar como pretende ligar à API.

![Uma imagem que mostra um pedido para especificar as credenciais de acesso](../media/tutorials/edit-credentials-message.png)

Para corrigir este problema, clique em **editar credenciais** na mensagem. Depois de aparece a caixa de diálogo, selecione **anónimo** a ligação anónima para a API. Em seguida, clique em **Connect** (Ligar). 

Em seguida, clique em **fechar e aplicar** no **home page** faixa de opções para aplicar as alterações.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizar a resposta de API de detetor de anomalias

Na tela principal do Power BI, começa a utilizar as consultas que criámos acima para visualizar os dados. Primeiro selecione **gráfico de linhas** na **visualizações**. Em seguida, adicione o carimbo de hora da função invocada para o gráfico de linhas **eixo**. Com o botão direito no mesmo e selecione **Timestamp**. 

![Clicar com o botão direito do valor de Timestamp](../media/tutorials/timestamp-right-click.png)

Adicione os seguintes campos do **função invocada** para o gráfico **valores** campo. Utilize a captura de ecrã para ajudar a criar o gráfico abaixo.

    * Value
    * UpperMargins
    * LowerMargins
    * ExpectedValues

![Uma imagem do ecrã nova medida rápida](../media/tutorials/chart-settings.png)

Depois de adicionar os campos, clique no gráfico e redimensione-o para mostrar todos os pontos de dados. O gráfico irá ter um aspeto semelhante a captura de ecrã abaixo:

![Uma imagem do ecrã nova medida rápida](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Apresentar pontos de dados de anomalias

No lado direito da janela do Power BI, abaixo a **campos** painel, o botão direito do mouse no **valor** sob o **consulta de função invocada**e clique em **novo rápido medida**.

![Uma imagem do ecrã nova medida rápida](../media/tutorials/new-quick-measure.png)

No ecrã que aparece, selecione **filtrados valor** como o cálculo. Definir **valor de Base** para `Sum of Value`. Em seguida, arraste `IsAnomaly` partir a **função invocada** campos para **filtro**. Selecione `True` partir de **filtro** menu pendente.

![Uma imagem do ecrã nova medida rápida](../media/tutorials/new-quick-measure-2.png)

Depois de clicar em **Ok**, terá um `Value for True` campo, na parte inferior da lista de seus campos. Faça duplo clique nele e mude o nome para **anomalias**. Adicione-a do gráfico **valores**. Em seguida, selecione o **formato** a ferramenta e definir o tipo de eixo x como **Categórico**.

![Uma imagem do ecrã nova medida rápida](../media/tutorials/format-x-axis.png)

Aplicar cores ao seu gráfico clicando no **formato** ferramenta e **cores de dados**. O gráfico deve ter um aspeto semelhante ao seguinte:

![Uma imagem do ecrã nova medida rápida](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
>[Deteção de anomalias de transmissão em fluxo com o Azure Databricks](anomaly-detection-streaming-databricks.md)
