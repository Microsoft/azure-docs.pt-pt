---
title: Do Azure Machine Learning API - processo de ciência de dados de equipa de deteção de anomalias
description: API de deteção de anomalias é um exemplo criado com o Microsoft Azure Machine Learning que Deteta anomalias nos dados de séries de tempo com valores numéricos que são espaçados uniformemente no tempo.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: a09094cf0d1bd3c2e299e968d7de8410dcd9c3cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721885"
---
# <a name="machine-learning-anomaly-detection-api"></a>Do Machine Learning API de deteção de anomalias

> [!NOTE]
> Este artigo está em manutenção. Encorajamo-lo a usar o [serviço API](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) do Detetor de Anomalias alimentado por uma galeria de algoritmos de Machine Learning no âmbito dos Serviços Cognitivos Azure para detetar anomalias de métricas de negócios, operacionais e IoT.

## <a name="overview"></a>Visão geral
[API de deteção de anomalias](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) é um exemplo criado com o Azure Machine Learning que Deteta anomalias nos dados de séries de tempo com valores numéricos que são espaçados uniformemente no tempo.

Esta API pode detetar os seguintes tipos de padrões anómalos nos dados de séries de tempo:

* **Tendências positivas e negativas**: por exemplo, quando monitorizar a utilização de memória na computação uma tendência de subida pode ser de interesse como que pode ser indicativo de um vazamento de memória
* **Alterações no intervalo dinâmico de valores**: por exemplo, ao monitorizar exceções acionadas por um serviço em nuvem, quaisquer alterações no intervalo dinâmico de valores podem indicar instabilidade no estado de funcionamento do serviço, e
* **Picos e quedas**: por exemplo, ao monitorizar o número de falhas de início de sessão num serviço ou um número de finalizações de compras num site de comércio eletrônico, picos ou as quedas anormais podem indicar um comportamento anormal.

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
Para utilizar a API, tem de implementá-la à sua subscrição do Azure onde será alojado como um serviço web do Azure Machine Learning.  Pode fazê-lo a partir da [Galeria de IA do Azure](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Isto irá implantar dois Serviços Web Azure Machine Learning Studio (clássicos) para a sua subscrição Azure - um para deteção de anomalias com deteção de sazonalidade, e outro sem deteção de sazonalidade.  Uma vez concluída a implementação, poderá gerir as suas APIs a partir da página de serviços web Do [Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  Nesta página, será possível localizar as localizações de ponto final, chaves de API, bem como código de exemplo para chamar a API.  Estão disponíveis instruções mais detalhadas [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>A API de dimensionamento
Por predefinição, a sua implementação terá um plano gratuito de faturação Dev/Test que inclui 1.000 transações/mês e 2 horas/mês de computação.  Pode atualizar para o outro plano de acordo com suas necessidades.  Detalhes sobre os preços de diferentes planos estão disponíveis [aqui](https://azure.microsoft.com/pricing/details/machine-learning/) em "API Web de produção de preços".

## <a name="managing-aml-plans"></a>Planos de gestão de AML
Pode gerir o seu plano de faturação [aqui](https://services.azureml.net/plans/).  O nome do plano irá basear-se o nome do grupo de recursos que escolheu quando implementar a API, além de uma cadeia de caracteres que seja exclusiva para a sua subscrição.  Estão disponíveis instruções sobre como atualizar o seu plano [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) na secção "Gerir planos de faturação".

## <a name="api-definition"></a>Definição da API
O serviço web fornece um API baseado em REST sobre HTTPS que pode ser consumido de diferentes maneiras, incluindo uma aplicação web ou móvel, R, Python, Excel, etc.  Envia os dados da sua série de tempo para este serviço através de uma chamada REST API, e executa uma combinação dos três tipos de anomalias descritos abaixo.

## <a name="calling-the-api"></a>Chamar a API
Para chamar a API, terá de conhecer a localização do ponto final e a chave de API.  Estes dois requisitos, juntamente com o código de amostra para chamar a API, estão disponíveis na página de serviços web Do [Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  Navegue para a API pretendida e, em seguida, clique no separador de "Consume" para encontrá-los.  Pode chamar a API como uma API swagger (isto é, com o parâmetro URL `format=swagger`) ou como uma API não-Swagger (isto é, sem o parâmetro URL `format`).  O código de exemplo utiliza o formato de Swagger.  Segue-se um exemplo de solicitação e resposta no formato não Swagger.  Esses exemplos são para o ponto final de sazonalidade.  O ponto de extremidade não sazonalidade é semelhante.

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
Para ver o campo `ColumnNames`, deve incluir `details=true` como parâmetro URL a seu pedido.  Consulte a tabela abaixo para o significado por trás de cada um destes campos.

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
A figura abaixo mostra um exemplo de anomalias pode detetar a API de pontuação. Esta série tem duas mudanças de nível distintas, e três picos. Os pontos vermelhos mostram o tempo em que a alteração do nível é detectada, embora os pontos de pretas mostram os picos detetados.
![API de pontuação][1]

### <a name="detectors"></a>Detectores
A API de deteção de anomalias suporta detetores em três grandes categorias. Obter detalhes sobre os parâmetros de entrada específicos e saídas de cada detetor podem ser encontrados na tabela seguinte.

| Detetor de categoria | Detetor de | Descrição | Parâmetros de Entrada | Saídas |
| --- | --- | --- | --- | --- |
| Detetores de pico |Detetor de TSpike |Detetar a picos e descidas nos muito valores são de primeira e terceira quartiles |*tspikedetector.Sensitivity:* assume o valor de número inteiro no intervalo predefinido de 1 a 10: 3; Valores mais altos capturará mais valores extremos, tornando menos confidencial |TSpike: valores binários-1 se for detetado um pico/dip, '0'; caso contrário |
| Detetores de pico | Detetor de ZSpike |Detetar picos e descidas com base na distância datapoints são da sua média |*zspikedetector.Sensitivity:* tirar o valor de número inteiro no intervalo predefinido de 1 a 10: 3; Valores mais altos capturará mais valores extremos, tornando menos confidencial |ZSpike: valores binários-1 se for detetado um pico/dip, '0'; caso contrário |
| Detetor de tendência lenta |Detetor de tendência lenta |Detetar a tendência positiva lenta de acordo com a sensibilidade de conjunto |*trenddetector.sensibilidade:* limiar na pontuação do detetor (predefinido: 3.25, 3.25 - 5 é uma gama razoável para selecionar; Quanto maior, menos sensível) |tscore: número de vírgula flutuante que representa a classificação de anomalia no tendência |
| Detetores de nível de alteração | Detetor de alteração do nível de bidirecional |Detetar alteração nível, ascendente e descendente, de acordo com a sensibilidade de conjunto |*bileveldetector.sensibilidade:* limiar na pontuação do detetor (predefinido: 3.25, 3.25 - 5 é uma gama razoável para selecionar; Quanto maior, menos sensível) |rpscore: número de vírgula flutuante que representa a classificação de anomalia na alteração de nível de ascendente e descendente |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre estes parâmetros de entrada estão listadas na tabela abaixo:

| Parâmetros de Entrada | Descrição | Definição predefinida | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| detectors.historywindow |Histórico (no número de pontos de dados), utilizado para o cálculo de pontuação de anomalias |500 |número inteiro |10-2000 |Dependente de séries temporais |
| detectors.spikesdips | Se pretende detetar apenas picos, apenas dips, ou ambos |Ambos |enumerar |Ambos, picos, Dips |Ambos |
| bileveldetector.sensitivity |Sensibilidade para detetor de alteração de nível de bidirecional. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| trenddetector.sensitivity |Sensibilidade para detetor de tendência positiva. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| tspikedetector.sensitivity |Sensibilidade para detetor de TSpike |3 |número inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| zspikedetector.sensitivity |Sensibilidade para detetor de ZSpike |3 |número inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| postprocess.tailRows |Número de pontos de dados mais recentes de ser mantidos nos resultados da saída |0 |número inteiro |0 (manter todos os pontos de dados), ou especificar o número de pontos a ter em resultados |N/A |

### <a name="output"></a>Saída
A API é executado detectores de todos os seus dados de séries de tempo e devolve as pontuações de anomalias e indicadores de pico de binário para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Tempo |Carimbos de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| Dados |Os valores de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| TSpike |Indicador de binário para indicar se um pico é detetado pelo detetor de TSpike |
| ZSpike |Indicador de binário para indicar se um pico é detetado pelo detetor de ZSpike |
| rpscore |Uma número anomalias de representar flutuante pontuados na alteração de nível de bidirecional |
| rpalert |valor de 1/0, indicando que há um nível de bidirecional alterar anomalias com base na confidencialidade entrada |
| tscore |Uma número anomalias de representar flutuante pontuados na tendência positiva |
| talert |valor de 1/0 que indica que existem é uma anomalia de tendência positiva, com base na confidencialidade entrada |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
A API de ScoreWithSeasonality é utilizada para executar a deteção de anomalias em série de tempo que têm padrões de sazonais. Esta API é útil para detetar os desvios em padrões sazonais.
A figura a seguir mostra um exemplo de anomalias detetadas numa série de tempo sazonais. A série tem um pico (o primeiro ponto preto), dois mergulhos (o segundo ponto preto e um no final) e uma mudança de nível (ponto vermelho). Tanto o mergulho no meio da série de tempo como a mudança de nível só são percetíveis após a remoção dos componentes sazonais da série.
![API de sazonalidade][2]

### <a name="detectors"></a>Detectores
Os detetores o ponto final de sazonalidade são semelhantes do ponto de final não sazonalidade, mas com nomes de parâmetro ligeiramente diferente (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre estes parâmetros de entrada estão listadas na tabela abaixo:

| Parâmetros de Entrada | Descrição | Definição predefinida | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| preprocess.aggregationInterval |Série de tempo de entrada de intervalo de agregação em segundos para agregar |0 (nenhuma agregação é realizada) |número inteiro |0: Ignorar caso contrário, a agregação ', ' > 0 |5 minutos para 1 dia, dependente de séries temporais |
| preprocess.aggregationFunc |Função utilizada para agregar dados para o AggregationInterval especificado |média |enumerar |média, soma, comprimento |N/A |
| preprocess.replaceMissing |Valores utilizados para impute dados em falta |lkv (conhecido pela última vez valor) |enumerar |zero, lkv, média |N/A |
| detectors.historywindow |Histórico (no número de pontos de dados), utilizado para o cálculo de pontuação de anomalias |500 |número inteiro |10-2000 |Dependente de séries temporais |
| detectors.spikesdips | Se pretende detetar apenas picos, apenas dips, ou ambos |Ambos |enumerar |Ambos, picos, Dips |Ambos |
| bileveldetector.sensitivity |Sensibilidade para detetor de alteração de nível de bidirecional. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| postrenddetector.sensitivity |Sensibilidade para detetor de tendência positiva. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| negtrenddetector.sensitivity |Sensibilidade para detetor de tendência negativo. |3.25 |double |Nenhuma |3.25-5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| tspikedetector.sensitivity |Sensibilidade para detetor de TSpike |3 |número inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| zspikedetector.sensitivity |Sensibilidade para detetor de ZSpike |3 |número inteiro |1-10 |3 a 5 (valores menores dizer mais sensíveis a maiúsculas e minúsculas) |
| seasonality.Enable |Se a análise de sazonalidade está a ser executada |true |boolean |VERDADEIRO, FALSO |Dependente de séries temporais |
| seasonality.numSeasonality |Número máximo de ciclos periódicos como detetada |1 |número inteiro |1, 2 |1-2 |
| seasonality.Transform |Se sazonais (e) os componentes de tendência deverá ser removidos antes de aplicar a deteção de anomalias |deseason |enumerar |Nenhum, deseason, deseasontrend |N/A |
| postprocess.tailRows |Número de pontos de dados mais recentes de ser mantidos nos resultados da saída |0 |número inteiro |0 (manter todos os pontos de dados), ou especificar o número de pontos a ter em resultados |N/A |

### <a name="output"></a>Saída
A API é executado detectores de todos os seus dados de séries de tempo e devolve as pontuações de anomalias e indicadores de pico de binário para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Tempo |Carimbos de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| OriginalData |Os valores de dados não processados ou dados agregados (e/ou) imputed se agregação (e/ou) em falta imputation de dados é aplicada |
| ProcessedData |Qualquer uma das seguintes opções: <ul><li>Sazonalmente ajustado séries de tempo se sazonalidade significativa foi detetado e deseason opção selecionada;</li><li>sazonalmente ajustado e detrended séries de tempo se sazonalidade significativa foi detetada e a opção de deseasontrend selecionada</li><li>caso contrário, esta opção é a mesma que originalData</li> |
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

