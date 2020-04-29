---
title: 'Tutorial: Visualizar anomalias utilizando a deteção de lotes e o Power BI'
titleSuffix: Azure Cognitive Services
description: Aprenda a utilizar o API e o Power BI do Detetor de Anomalias para visualizar anomalias ao longo dos dados da série de tempo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 1b486aaf0ce33e31433c2c3d0f7a1ff2c7089132
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78402658"
---
# <a name="tutorial-visualize-anomalies-using-batch-detection-and-power-bi"></a>Tutorial: Visualizar anomalias utilizando a deteção de lotes e o Power BI

Utilize este tutorial para encontrar anomalias dentro de um conjunto de dados da série de tempo como um lote. Utilizando o ambiente de trabalho Power BI, você pegará num ficheiro Excel, preparará os dados para a API do Detetor de Anomalias e visualizará anomalias estatísticas em todo o mesmo.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Use power BI Desktop para importar e transformar um conjunto de dados de séries de tempo
> * Integrar o Power BI Desktop com a API do Detetor de Anomalias para deteção de anomalias em lote
> * Visualize anomalias encontradas dentro dos seus dados, incluindo valores esperados e vistos, e limites de deteção de anomalias.

## <a name="prerequisites"></a>Pré-requisitos
* Uma [subscrição azure](https://azure.microsoft.com/free/)
* [Microsoft Power BI Desktop,](https://powerbi.microsoft.com/get-started/)disponível gratuitamente.
* Um ficheiro excel (.xlsx) contendo pontos de dados da série de tempo. Os dados de exemplo para este quickstart podem ser encontrados no [GitHub](https://go.microsoft.com/fwlink/?linkid=2090962)
* Assim que tiver a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="sua subscrição"  target="_blank">Azure, crie um recurso Text Analytics criar um recurso <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Text Analytics no portal Azure para obter a sua chave e ponto final. 
    * Necessitará da chave e do ponto final do recurso que cria para ligar a sua aplicação à API textanalytics. Vais fazer isto mais tarde, no início.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

## <a name="load-and-format-the-time-series-data"></a>Carregar e formatar os dados da série de tempo

Para começar, abra o Power BI Desktop e carregue os dados da série de tempo que descarregou a partir dos pré-requisitos. Este ficheiro excel contém uma série de pares de tempo e valores coordenados do tempo universal (UTC).  

> [!NOTE]
> O Power BI pode utilizar dados de uma grande variedade de fontes, tais como ficheiros .csv, bases de dados SQL, armazenamento de blob Azure, e muito mais.  

Na janela principal do Power BI Desktop, clique na fita **Home.** No grupo de **dados externos** da fita, abra o menu **Get Data** drop-down e clique em **Excel**.

![Uma imagem do botão "Obter Dados" no Power BI](../media/tutorials/power-bi-get-data-button.png)

Depois de aparecer o diálogo, navegue para a pasta onde descarregou o ficheiro .xlsx e selecione-o. Depois do diálogo **Navigator** aparecer, clique na **Folha1**, e depois **editar**.

![Uma imagem do ecrã "Navigator" de origem de dados no Power BI](../media/tutorials/navigator-dialog-box.png)

O Power BI converterá os selos `Date/Time` temporais na primeira coluna para um tipo de dados. Estes carimbos temporais devem ser convertidos em texto para serem enviados para a API do Detetor de Anomalias. Se o editor da Power Query não abrir automaticamente, clique em **Editar Consultas** no separador inicial. 

Clique na fita **Transform** no Editor de Consulta de Poder. No grupo **Qualquer Coluna,** abra o Tipo de **Dados:** menu suspenso e selecione **Texto**.

![Uma imagem do ecrã "Navigator" de origem de dados no Power BI](../media/tutorials/data-type-drop-down.png)

Quando tiver um aviso sobre a alteração do tipo de coluna, clique em **Substituir**Current . Depois, clique em **Fechar & Aplicar** ou **Aplicar** na fita **Home.** 

## <a name="create-a-function-to-send-the-data-and-format-the-response"></a>Criar uma função para enviar os dados e formato da resposta

Para formatar e enviar o ficheiro de dados para a API do Detetor de Anomalias, pode invocar uma consulta na tabela acima criada. No Power Query Editor, a partir da fita **Home,** abra o menu **new source** drop-down e clique em **Blank Query**.

Certifique-se de que a sua nova consulta é selecionada e, em seguida, clique em **Editor Avançado**. 

![Uma imagem do botão "Editor Avançado" no Power BI](../media/tutorials/advanced-editor-screen.png)

Dentro do Editor Avançado, utilize o seguinte corte de Power Query M para extrair as colunas da mesa e enviá-la para a API. Em seguida, a consulta criará uma tabela a partir da resposta JSON, e devolvê-la-á. Substitua `apiKey` a variável pela sua chave `endpoint` API do Detetor de Anomalias válida e pelo seu ponto final. Depois de ter entrado na consulta no Editor Avançado, clique em **Done**.

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

Invoque a consulta na sua `Sheet1` ficha de dados selecionando abaixo **o Parâmetro de Entrada,** clique em **Invocar**. 

![Uma imagem do botão "Editor Avançado"](../media/tutorials/invoke-function-screenshot.png)

## <a name="data-source-privacy-and-authentication"></a>Privacidade e autenticação de fontes de dados

> [!NOTE]
> Esteja atento às políticas da sua organização para a privacidade e acesso de dados. Consulte os níveis de [privacidade do Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-privacy-levels) para obter mais informações.

Pode receber uma mensagem de aviso quando tentar executar a consulta, uma vez que utiliza uma fonte de dados externa. 

![Uma imagem mostrando um aviso criado por Power BI](../media/tutorials/blocked-function.png)

Para corrigir isto, clique em **Ficheiros**, e **Opções e definições**. Em seguida, clique em **Opções**. Abaixo **do Ficheiro Atual**, selecione **Privacidade**e ignore os níveis de privacidade **e potencialmente melhore o desempenho.** 

Além disso, pode receber uma mensagem a pedir-lhe para especificar como pretende ligar-se à API.

![Uma imagem que mostra um pedido para especificar credenciais de acesso](../media/tutorials/edit-credentials-message.png)

Para corrigir isto, clique em **editar credenciais** na mensagem. Depois da caixa de diálogo aparecer, selecione **Anonymous** para ligar à API anonimamente. Em seguida, clique em **Connect** (Ligar). 

Depois, clique em **Fechar & Aplicar** na fita **Home** para aplicar as alterações.

## <a name="visualize-the-anomaly-detector-api-response"></a>Visualizar a resposta aAPI do Detetor de Anomalias

No ecrã principal do Power BI, comece a utilizar as consultas acima criadas para visualizar os dados. Primeiro selecione **Line Chart** em **Visualizações**. Em seguida, adicione a marca de tempo da função invocada ao **eixo**do gráfico de linha . Clique à direita e selecione **Timestamp**. 

![Clicando à direita no valor da marca de tempo](../media/tutorials/timestamp-right-click.png)

Adicione os seguintes campos da **Função Invocada** ao campo **Valores** da Tabela. Use a imagem abaixo para ajudar a construir o seu gráfico.

    * Valor
    * Margens Superiores
    * Margens Mais Baixas
    * Valores Esperados

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/chart-settings.png)

Depois de adicionar os campos, clique na tabela e redimensione-o para mostrar todos os pontos de dados. O seu gráfico será semelhante ao screenshot abaixo:

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/chart-visualization.png)

### <a name="display-anomaly-data-points"></a>Mostrar pontos de dados de anomalias

No lado direito da janela Power BI, abaixo do painel **FIELDS,** clique à direita no **Valor** sob a **consulta da Função Invocada,** e clique em **Nova medida rápida**.

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/new-quick-measure.png)

No ecrã que aparece, selecione **o valor filtrado** como cálculo. Definir **valor** `Sum of Value`base para . Em `IsAnomaly` seguida, arraste dos campos **de função invocada** para **filtrar**. Selecione `True` no menu de entrega do **Filtro.**

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/new-quick-measure-2.png)

Depois de clicar em **Ok,** terá um `Value for True` campo, na parte inferior da lista dos seus campos. Clique na direita e mude o nome para **Anomaly**. Adicione-o aos **Valores**do gráfico . Em seguida, selecione a ferramenta **Formato** e desloque o tipo de eixo X para **Categorical**.

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/format-x-axis.png)

Aplique as cores no seu gráfico clicando na ferramenta **Formato** e nas **cores data**. A sua ficha deve parecer algo como o seguinte:

![Uma imagem do novo ecrã de medida rápida](../media/tutorials/final-chart.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Deteção de anomalias de streaming com tijolos de dados Azure](anomaly-detection-streaming-databricks.md)
