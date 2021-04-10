---
title: 'Tutorial: Visualizar anomalias usando deteção de lotes e Power BI'
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o Detetor de Anomalias API e o Power BI para visualizar anomalias ao longo dos dados da série de tempo.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: fc2667dfddf4442acaf39f6783190e8640b22a89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561734"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Tutorial: Visualizar anomalias usando deteção de lotes e Power BI

Utilize este tutorial para encontrar anomalias dentro de um conjunto de dados da série de tempo como um lote. Utilizando o ambiente de trabalho Power BI, irá pegar num ficheiro Excel, preparar os dados para a API do Detetor de Anomalias e visualizar anomalias estatísticas ao longo do mesmo.

Neste tutorial, irá aprender a:

> [!div class="checklist"]
> * Use Power BI Desktop para importar e transformar um conjunto de dados de séries de tempo
> * Integrar o Power BI Desktop com o Detetor de Anomalia API para deteção de anomalias em lotes
> * Visualize anomalias encontradas dentro dos seus dados, incluindo valores esperados e vistos, e limites de deteção de anomalias.

## <a name="prerequisites"></a>Pré-requisitos
* Uma [subscrição do Azure](https://azure.microsoft.com/free/cognitive-services)
* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/get-started/), disponível gratuitamente.
* Um ficheiro excel (.xlsx) contendo pontos de dados da série de tempo. Os dados de exemplo para este quickstart podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* Assim que tiver a subscrição do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" crie um recurso de Detetor de "  target="_blank"> Anomalias crie um recurso de Detetor de </a> Anomalias no portal Azure para obter a sua chave e ponto final.
    * Necessitará da chave e ponto final do recurso que criar para ligar a sua aplicação à API do Detetor de Anomalias. Vais fazer isto mais tarde, no início rápido.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Carregar e formatar os dados da série de tempo

Para começar, abra o Power BI Desktop e carregue os dados da série de tempo que descarregou a partir dos pré-requisitos. Este ficheiro excel contém uma série de relógios e pares de valor do tempo universal coordenado (UTC).  

> [!NOTE]
> O Power BI pode usar dados de uma grande variedade de fontes, tais como ficheiros .csv, bases de dados SQL, armazenamento de blob Azure e muito mais.  

Na janela principal do Ambiente de Trabalho Power BI, clique na fita **Home.** No grupo de **dados externos** da fita, abra o menu **Get Data** drop-down e clique no **Excel**.

![Uma imagem do botão "Obter Dados" no Power BI](../media/tutorials/power-bi-get-data-button.png)

Depois do diálogo aparecer, navegue para a pasta onde descarregou o exemplo .xlsx ficheiro e selecione-o. Depois do diálogo **Do Navegador** aparecer, clique em **Sheet1** e, em seguida, **Edite**.

![Uma imagem do ecrã "Navigator" da fonte de dados no Power BI](../media/tutorials/navigator-dialog-box.png)

O Power BI converterá os tempos na primeira coluna para um tipo de `Date/Time` dado. Estes cartões de tempo devem ser convertidos em texto para serem enviados para a API do Detetor de Anomalias. Se o editor de consulta de energia não abrir automaticamente, clique em **Editar Consultas** no separador casa.

Clique na fita **Transform** no Editor de Consulta de Energia. No grupo **Any Column,** abra o tipo de **dados:** menu suspenso e selecione **Texto**.

![Uma imagem do tipo de dados desce](../media/tutorials/data-type-drop-down.png)

Quando receber um aviso sobre a alteração do tipo de coluna, clique em **Substituir Corrente**. Em seguida, clique **em Fechar & Aplicar** ou **Aplicar** na fita **Home.**

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Criar uma função para enviar os dados e formatar a resposta

Para formatar e enviar o ficheiro de dados para a API do Detetor de Anomalias, pode invocar uma consulta na tabela criada acima. No Power Query Editor, a partir da fita **Home,** abra o menu drop-down **New Source** e clique em Consulta **em Branco**.

Certifique-se de que a sua nova consulta está selecionada e, em seguida, clique em **Editor Avançado**.

![Uma imagem do ecrã "Editor Avançado"](../media/tutorials/advanced-editor-screen.png)

Dentro do Editor Avançado, utilize o seguinte corte de power query M para extrair as colunas da tabela e enviá-la para a API. Em seguida, a consulta criará uma tabela a partir da resposta JSON, e devolvê-la-á. Substitua a `apiKey` variável pela sua chave API do Detetor de Anomalias válida e `endpoint` pelo seu ponto final. Depois de inserir a consulta no Editor Avançado, clique em **'Fazer'.**

```M
(table as table) => let

    apikey      = "[Placeholder: Your Anomaly Detector resource access key]",
    endpoint    = "[Placeholder: Your Anomaly Detector resource endpoint]/anomalydetector/v1.0/timeseries/entire/detect",
    inputTable = Table.TransformColumnTypes(table,{{"Timestamp", type text},{"Value", type number}}),
    jsontext    = Text.FromBinary(Json.FromValue(inputTable)),
    jsonbody    = "{ ""Granularity"": ""daily"", ""Sensitivity"": 95, ""Series"": "& jsontext &" }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Content-Type" = "application/json", #"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody, ManualStatusHandling={400}]),
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

Invoque a consulta na sua ficha de dados selecionando `Sheet1` abaixo **Enter Parâmetro**, e clique em **Invocar**.

![Uma imagem da função invocada](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacidade e autenticação de fontes de dados

> [!NOTE]
> Esteja atento às políticas da sua organização para a privacidade e acesso dos dados. Consulte [os níveis de privacidade do Power BI Desktop](/power-bi/desktop-privacy-levels) para obter mais informações.

Pode receber uma mensagem de aviso quando tentar executar a consulta, uma vez que utiliza uma fonte de dados externa.

![Uma imagem mostrando um aviso criado por Power BI](../media/tutorials/blocked-function.png)

Para corrigir isto, clique em **'Ficheiro'** e **Opções e definições**. Em seguida, clique em **Opções**. Abaixo **do Ficheiro Atual**, selecione **Privacidade** e ignore **os Níveis de Privacidade e potencialmente melhore o desempenho**.

Além disso, pode receber uma mensagem a pedir-lhe que especifique como pretende ligar-se à API.

![Uma imagem mostrando um pedido para especificar credenciais de acesso](../media/tutorials/edit-credentials-message.png)

Para corrigir isto, clique em **Editar Credenciais** na mensagem. Depois da caixa de diálogo aparecer, selecione **Anonymous** para ligar à API de forma anónima. Em seguida, clique em **Connect** (Ligar).

Em seguida, clique em **Fechar & Aplicar** na fita **Home** para aplicar as alterações.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizar a resposta da API do Detetor de Anomalias

No ecrã principal do Power BI, comece a utilizar as consultas criadas acima para visualizar os dados. Primeira tabela de **linha** selecionada em **visualizações.** Em seguida, adicione o tempotamp da função invocada ao **eixo** da linha . Clique com o botão direito e selecione **Timetamp**.

![Clicando à direita no valor do timestamp](../media/tutorials/timestamp-right-click.png)

Adicione os seguintes campos da **Função Invocada** ao campo **Valores** do gráfico. Use a imagem abaixo para ajudar a construir o seu gráfico.

* Valor
* UpperMargins
* LowerMargins
* Valores Esperados

![Uma imagem das definições do gráfico](../media/tutorials/chart-settings.png)

Depois de adicionar os campos, clique no gráfico e redimensione-o para mostrar todos os pontos de dados. A sua tabela será semelhante à imagem abaixo:

![Uma imagem da visualização do gráfico](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Mostrar pontos de dados de anomalia

No lado direito da janela Power BI, abaixo do painel **FIELDS,** clique com o botão direito no **Valor** sob a **consulta Função Invocada,** e clique em **Nova medida rápida**.

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/new-quick-measure.png)

No ecrã que aparece, selecione **o valor filtrado** como o cálculo. Desa estava **o valor base** para `Sum of Value` . Em seguida, arraste `IsAnomaly` dos campos **de função invocada** para **filtrar**. Selecione `True` no menu de entrega do **filtro.**

![Uma segunda imagem do novo ecrã de medida rápida](../media/tutorials/new-quick-measure-2.png)

Depois de clicar **em Ok,** terá um `Value for True` campo na parte inferior da lista dos seus campos. Clique com o botão direito e rebatize-o para **Anomalia.** Adicione-o aos **Valores** do gráfico. Em seguida, selecione a ferramenta **Formato** e coloque o tipo de eixo X **em Categorical**.

![Uma imagem do eixo formato x](../media/tutorials/format-x-axis.png)

Aplique cores no seu gráfico clicando na ferramenta **Formato** e nas **cores dos Dados.** A sua ficha deve parecer-se com o seguinte:

![Uma imagem do gráfico final](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Deteção de anomalias em streaming com Azure Databricks](../overview.md)