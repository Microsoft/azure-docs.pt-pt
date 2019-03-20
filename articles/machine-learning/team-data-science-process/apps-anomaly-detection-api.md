---
title: Do Azure Machine Learning API - processo de ciência de dados de equipa de deteção de anomalias
description: API de deteção de anomalias é um exemplo criado com o Microsoft Azure Machine Learning que Deteta anomalias nos dados de séries de tempo com valores numéricos que são espaçados uniformemente no tempo.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 06/05/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e5f428062155ea732dce785955ac76011f3e4678
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899354"
---
# <a name="machine-learning-anomaly-detection-api"></a>Do Machine Learning API de deteção de anomalias
## <a name="overview"></a>Descrição geral
[API de deteção de anomalias](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) é um exemplo criado com o Azure Machine Learning que Deteta anomalias nos dados de séries de tempo com valores numéricos que são espaçados uniformemente no tempo.

Esta API pode detetar os seguintes tipos de padrões anómalos nos dados de séries de tempo:

* **Tendências positivas e negativas**: Por exemplo, ao monitorizar a utilização de memória numa tendência de subida de computação pode ser de interesse como que pode ser indicativo de um vazamento de memória
* **Alterações no intervalo dinâmico de valores**: Por exemplo, ao monitorizar exceções acionadas por um serviço em nuvem, quaisquer alterações no intervalo dinâmico de valores podem indicar instabilidade no estado de funcionamento do serviço, e
* **Picos e quedas**: Por exemplo, ao monitorizar o número de falhas de início de sessão num serviço ou um número de finalizações de compras num site de comércio eletrônico, picos ou as quedas anormais podem indicar comportamento anormal.

Estes detectores de aprendizado de máquina controlam essas alterações em valores ao longo do tempo e o relatório de alterações em curso em seus valores como pontuações de anomalias. Não necessitam de ajuste de limiar do ad hoc e suas classificações podem ser utilizadas para controlar a taxa de falsos positivos. A deteção de anomalias API é útil em vários cenários, como a monitorização do serviço através da monitorização de KPIs ao longo do tempo, monitoramento de uso por meio de métricas, como o número de pesquisas, números de cliques, monitorização do desempenho através de contadores, como memória, CPU, lê o arquivo, etc. ao longo do tempo.

A oferta de deteção de anomalias vem com ferramentas úteis para começar.

* O [aplicação web](https://anomalydetection-aml.azurewebsites.net/) ajuda-o a avaliar e visualizar os resultados da deteção de anomalias APIs nos seus dados.

> [!NOTE]
> Tente **solução IT Anomaly Insights** com tecnologia [esta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Implementação de API
Para utilizar a API, tem de implementá-la à sua subscrição do Azure onde será alojado como um serviço web do Azure Machine Learning.  Pode fazê-lo a partir da [Galeria de IA do Azure](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Isto irá implementar dois serviços da Web do Azure Machine Learning studio (e os recursos relacionados) à sua subscrição do Azure - um para deteção de anomalias com deteção de sazonalidade e outro sem deteção de sazonalidade.  Depois de concluída a implementação, será capaz de gerir as suas APIs do [serviços da web do Azure Machine Learning studio](https://services.azureml.net/webservices/) página.  Nesta página, será possível localizar as localizações de ponto final, chaves de API, bem como código de exemplo para chamar a API.  Estão disponíveis instruções mais detalhadas [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>A API de dimensionamento
Por predefinição, a sua implementação terá um plano de faturação do programador/teste gratuito que inclui 1000 transações/mês e computação de 2 horas/mês.  Pode atualizar para o outro plano de acordo com suas necessidades.  Detalhes sobre os preços de diferentes planos estão disponíveis [aqui](https://azure.microsoft.com/pricing/details/machine-learning/) em "API Web de produção de preços".

## <a name="managing-aml-plans"></a>Planos de gestão de AML
Pode gerir o seu plano de faturação [aqui](https://services.azureml.net/plans/).  O nome do plano irá basear-se o nome do grupo de recursos que escolheu quando implementar a API, além de uma cadeia de caracteres que seja exclusiva para a sua subscrição.  Estão disponíveis instruções sobre como atualizar o seu plano [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) na secção "Gerir planos de faturação".

## <a name="api-definition"></a>Definição da API
O serviço web fornece uma API baseada em REST através de HTTPS, que podem ser consumidos de formas diferentes, incluindo um aplicativo web ou móvel, R, Python, Excel, etc.  Enviar dados de séries de tempo para este serviço por meio de uma chamada à REST API, e é executado uma combinação de tipos de três anomalias descritos abaixo.

## <a name="calling-the-api"></a>Chamar a API
Para chamar a API, terá de conhecer a localização do ponto final e a chave de API.  Ambos, juntamente com o código de exemplo para chamar a API, estão disponíveis a partir da [serviços da web do Azure Machine Learning studio](https://services.azureml.net/webservices/) página.  Navegue para a API pretendida e, em seguida, clique no separador de "Consume" para encontrá-los.  Tenha em atenção de que pode chamar a API como uma API do Swagger (ou seja, com o parâmetro de URL `format=swagger`) ou como um não - API do Swagger (ou seja, sem o `format` parâmetro de URL).  O código de exemplo utiliza o formato de Swagger.  Segue-se um exemplo de solicitação e resposta no formato não Swagger.  Esses exemplos são para o ponto final de sazonalidade.  O ponto de extremidade não sazonalidade é semelhante.

### <a name="sample-request-body"></a>Corpo do pedido de exemplo
O pedido contém dois objetos: `Inputs` e `GlobalParameters`.  O pedido de exemplo abaixo, alguns parâmetros são enviados explicitamente e outros não (role para baixo para uma lista completa de parâmetros para cada ponto de extremidade).  Parâmetros que não são enviados explicitamente no pedido irão utilizar os valores predefinidos, indicados abaixo.

    {
                "Inputs": {
                        "input1": {
                                "ColumnNames": ["Time", "Data"],
                                "Values": [
                                        ["5/30/2010 18:07:00", "1"],
                                        ["5/30/2010 18:08:00", "1.4"],
                                        ["5/30/2010 18:09:00", "1.1"]
                                ]
                        }
                },
        "GlobalParameters": {
            "tspikedetector.sensitivity": "3",
            "zspikedetector.sensitivity": "3",
            "bileveldetector.sensitivity": "3.25",
            "detectors.spikesdips": "Both"
        }
    }

### <a name="sample-response"></a>Pedido de Resposta
Tenha em atenção que, para ver os `ColumnNames` campo, tem de incluir `details=true` como um parâmetro de URL no seu pedido.  Consulte a tabela abaixo para o significado por trás de cada um destes campos.

    {
        "Results": {
            "output1": {
                "type": "table",
                "value": {
                    "Values": [
                        ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                        ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                        ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                    ],
                    "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                    "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
                }
            }
        }
    }


## <a name="score-api"></a>API de pontuação
A API de pontuação é utilizada para executar a deteção de anomalias em dados de séries de tempo não sazonais. A API é executada um número de detectores de anomalias nos dados e retorna suas pontuações de anomalias.
A figura abaixo mostra um exemplo de anomalias pode detetar a API de pontuação. Esta série de tempo tem alterações no nível de distintas 2 e 3 picos. Os pontos vermelhos mostram o tempo em que a alteração do nível é detectada, embora os pontos de pretas mostram os picos detetados.
![API de pontuação][1]

### <a name="detectors"></a>Detectores
A API de deteção de anomalias suporta detectores 3 amplas categorias. Obter detalhes sobre os parâmetros de entrada específicos e saídas de cada detetor podem ser encontrados na tabela seguinte.

| Detetor de categoria | Detetor de | Descrição | Parâmetros de entrada | Saídas |
| --- | --- | --- | --- | --- |
| Detetores de pico |Detetor de TSpike |Detetar a picos e descidas nos muito valores são de primeira e terceira quartiles |*tspikedetector.Sensitivity:* assume o valor de número inteiro no intervalo predefinido de 1 a 10: 3; Valores mais altos capturará mais valores extremos, tornando menos confidencial |TSpike: valores binários-1 se for detetado um pico/dip, '0'; caso contrário |
| Detetores de pico | Detetor de ZSpike |Detetar picos e descidas com base na distância datapoints são da sua média |*zspikedetector.Sensitivity:* tirar o valor de número inteiro no intervalo predefinido de 1 a 10: 3; Valores mais altos capturará mais valores extremos, tornando menos confidencial |ZSpike: valores binários-1 se for detetado um pico/dip, '0'; caso contrário |
| Detetor de tendência lenta |Detetor de tendência lenta |Detetar a tendência positiva lenta de acordo com a sensibilidade de conjunto |*trenddetector.Sensitivity:* limiar na classificação detetor (predefinição: 3.25, 3.25 – 5 é uma gama razoável para selecionar este componente a partir; Quanto maior for a menos maiúsculas de minúsculas) |tscore: número de vírgula flutuante que representa a classificação de anomalia no tendência |
| Detetores de nível de alteração | Detetor de alteração do nível de bidirecional |Detetar alteração nível, ascendente e descendente, de acordo com a sensibilidade de conjunto |*bileveldetector.Sensitivity:* limiar na classificação detetor (predefinição: 3.25, 3.25 – 5 é uma gama razoável para selecionar este componente a partir; Quanto maior for a menos maiúsculas de minúsculas) |rpscore: número de vírgula flutuante que representa a classificação de anomalia na alteração de nível de ascendente e descendente |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre estes parâmetros de entrada estão listadas na tabela abaixo:

| Parâmetros de entrada | Descrição | Definição predefinida | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Histórico (no número de pontos de dados), utilizado para o cálculo de pontuação de anomalias |500 |inteiro |10-2000 |Dependente de séries temporais |
| detectors.spikesdips | Se pretende detetar apenas picos, apenas dips, ou ambos |Ambos |enumerar |Ambos, picos, Dips |Ambos |
| bileveldetector.sensitivity |Sensibilidade para detetor de alteração de nível de bidirecional. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| trenddetector.sensitivity |Sensibilidade para detetor de tendência positiva. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| tspikedetector.sensitivity |Sensibilidade para detetor de TSpike |3 |inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| zspikedetector.sensitivity |Sensibilidade para detetor de ZSpike |3 |inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| postprocess.tailRows |Número de pontos de dados mais recentes de ser mantidos nos resultados da saída |0 |inteiro |0 (manter todos os pontos de dados), ou especificar o número de pontos a ter em resultados |N/A |

### <a name="output"></a>Saída
A API é executado detectores de todos os seus dados de séries de tempo e devolve as pontuações de anomalias e indicadores de pico de binário para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| Dados |Os valores de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| TSpike |Indicador de binário para indicar se um pico é detetado pelo detetor de TSpike |
| ZSpike |Indicador de binário para indicar se um pico é detetado pelo detetor de ZSpike |
| rpscore |Uma número anomalias de representar flutuante pontuados na alteração de nível de bidirecional |
| rpalert |valor de 1/0, indicando que há um nível de bidirecional alterar anomalias com base na confidencialidade entrada |
| tscore |Uma número anomalias de representar flutuante pontuados na tendência positiva |
| talert |valor de 1/0 que indica que existem é uma anomalia de tendência positiva, com base na confidencialidade entrada |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
A API de ScoreWithSeasonality é utilizada para executar a deteção de anomalias em série de tempo que têm padrões de sazonais. Esta API é útil para detetar os desvios em padrões sazonais.
A figura a seguir mostra um exemplo de anomalias detetadas numa série de tempo sazonais. Série de tempo tem um pico (o ponto preto 1), dois dips (o ponto de preto 2nd e um no final) e uma alteração ao nível (ponto vermelho). Observe que tanto o dip no meio da série de tempo e a alteração do nível apenas são discernable depois sazonais componentes são removidas da série.
![API de sazonalidade][2]

### <a name="detectors"></a>Detectores
Os detetores o ponto final de sazonalidade são semelhantes do ponto de final não sazonalidade, mas com nomes de parâmetro ligeiramente diferente (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre estes parâmetros de entrada estão listadas na tabela abaixo:

| Parâmetros de entrada | Descrição | Definição predefinida | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Série de tempo de entrada de intervalo de agregação em segundos para agregar |0 (nenhuma agregação é realizada) |inteiro |0: Ignorar caso contrário, a agregação ', ' > 0 |5 minutos para 1 dia, dependente de séries temporais |
| preprocess.aggregationFunc |Função utilizada para agregar dados para o AggregationInterval especificado |média |enumerar |média, soma, comprimento |N/A |
| preprocess.replaceMissing |Valores utilizados para impute dados em falta |lkv (conhecido pela última vez valor) |enumerar |zero, lkv, média |N/A |
| detectors.historywindow |Histórico (no número de pontos de dados), utilizado para o cálculo de pontuação de anomalias |500 |inteiro |10-2000 |Dependente de séries temporais |
| detectors.spikesdips | Se pretende detetar apenas picos, apenas dips, ou ambos |Ambos |enumerar |Ambos, picos, Dips |Ambos |
| bileveldetector.sensitivity |Sensibilidade para detetor de alteração de nível de bidirecional. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| postrenddetector.sensitivity |Sensibilidade para detetor de tendência positiva. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| negtrenddetector.sensitivity |Sensibilidade para detetor de tendência negativo. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| tspikedetector.sensitivity |Sensibilidade para detetor de TSpike |3 |inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| zspikedetector.sensitivity |Sensibilidade para detetor de ZSpike |3 |inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| seasonality.Enable |Se a análise de sazonalidade está a ser executada |true |boolean |VERDADEIRO, FALSO |Dependente de séries temporais |
| seasonality.numSeasonality |Número máximo de ciclos periódicos como detetada |1 |inteiro |1, 2 |1-2 |
| seasonality.Transform |Se sazonais (e) os componentes de tendência deverá ser removidos antes de aplicar a deteção de anomalias |deseason |enumerar |Nenhum, deseason, deseasontrend |N/A |
| postprocess.tailRows |Número de pontos de dados mais recentes de ser mantidos nos resultados da saída |0 |inteiro |0 (manter todos os pontos de dados), ou especificar o número de pontos a ter em resultados |N/A |

### <a name="output"></a>Saída
A API é executado detectores de todos os seus dados de séries de tempo e devolve as pontuações de anomalias e indicadores de pico de binário para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| OriginalData |Os valores de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| ProcessedData |Um dos seguintes: <ul><li>Sazonalmente ajustado séries de tempo se sazonalidade significativa foi detetado e deseason opção selecionada;</li><li>sazonalmente ajustado e detrended séries de tempo se sazonalidade significativa foi detetada e a opção de deseasontrend selecionada</li><li>caso contrário, isso é o mesmo que OriginalData</li> |
| TSpike |Indicador de binário para indicar se um pico é detetado pelo detetor de TSpike |
| ZSpike |Indicador de binário para indicar se um pico é detetado pelo detetor de ZSpike |
| BiLevelChangeScore |Uma número anomalias de representar flutuante pontuados na alteração do nível |
| BiLevelChangeAlert |0 de 1/valor que indica que existem é uma anomalia de nível de alteração com base na confidencialidade entrada |
| PosTrendScore |Uma número anomalias de representar flutuante pontuados na tendência positiva |
| PosTrendAlert |valor de 1/0 que indica que existem é uma anomalia de tendência positiva, com base na confidencialidade entrada |
| NegTrendScore |Uma número anomalias de representar flutuante pontuados na tendência negativa |
| NegTrendAlert |valor de 1/0 que indica que existem é uma anomalia de tendência negativo com base na confidencialidade entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

