---
title: Azure Machine Learning Anomalia de Deteção de Anomalias API - Processo de Ciência de Dados de Equipa
description: A API de Deteção de Anomalias é um exemplo construído com o Microsoft Azure Machine Learning que deteta anomalias em dados de séries temporais com valores numéricos que são uniformemente espaçados no tempo.
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
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305693"
---
# <a name="machine-learning-anomaly-detection-api"></a>API de deteção de anomalias de aprendizagem automática

> [!NOTE]
> Este artigo está sob manutenção. Encorajamo-lo a usar o [serviço API do Detetor](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) de Anomalias alimentado por uma galeria de algoritmos de Machine Learning ao abrigo dos Serviços Cognitivos Azure para detetar anomalias de métricas de negócios, operacionais e IoT.

## <a name="overview"></a>Descrição Geral
[A API de Deteção de Anomalias](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) é um exemplo construído com Azure Machine Learning que deteta anomalias em dados de séries temporais com valores numéricos que são uniformemente espaçados no tempo.

Esta API pode detetar os seguintes tipos de padrões anómalos nos dados da série de tempo:

* **Tendências positivas e negativas**: Por exemplo, ao monitorizar a utilização da memória na computação, pode ser interessante, uma vez que pode ser indicativa de uma fuga de memória,
* **Alterações na gama dinâmica de valores**: Por exemplo, ao monitorizar as exceções lançadas por um serviço de nuvem, quaisquer alterações na gama dinâmica de valores podem indicar instabilidade na saúde do serviço, e
* **Picos e Dips**: Por exemplo, ao monitorizar o número de falhas de login num serviço ou num número de check-outs num site de e-commerce, picos ou mergulhos podem indicar um comportamento anormal.

Estes detetores de aprendizagem automática acompanham tais mudanças de valores ao longo do tempo e relatam mudanças contínuas nos seus valores como pontuações de anomalias. Não necessitam de afinação do limiar de adhoc e as suas pontuações podem ser usadas para controlar uma taxa falsamente positiva. A API de deteção de anomalias é útil em vários cenários, como monitorização de serviços, rastreando KPI's ao longo do tempo, monitorização de utilização através de métricas como número de pesquisas, números de cliques, monitorização de desempenho através de contadores como memória, CPU, leituras de ficheiros, etc. ao longo do tempo.

A oferta de Deteção de Anomalias vem com ferramentas úteis para começar.

* A [aplicação web](https://anomalydetection-aml.azurewebsites.net/) ajuda-o a avaliar e visualizar os resultados das APIs de deteção de anomalias nos seus dados.

> [!NOTE]
> Experimente **a solução IT Anomaly Insights** alimentada por esta [API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Implantação da API
Para utilizar a API, deve implantá-la na sua subscrição Azure, onde será hospedada como um serviço web Azure Machine Learning.  Pode fazê-lo a partir da [Galeria Azure AI.](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)  Isto irá implementar dois Azure Machine Learning Studio (clássico) Web Services (e seus recursos relacionados) para a sua subscrição Azure - um para deteção de anomalias com deteção de sazonalidade, e um sem deteção de sazonalidade.  Uma vez concluída a implementação, poderá gerir as suas APIs a partir da página de [serviços web do Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  A partir desta página, poderá encontrar as suas localizações de ponto final, chaves API, bem como o código de amostra para ligar para a API.  Instruções mais detalhadas estão disponíveis [aqui.](../classic/manage-new-webservice.md)

## <a name="scaling-the-api"></a>Dimensionamento da API
Por padrão, a sua implementação terá um plano de faturação gratuito de Dev/Test que inclui 1.000 transações/mês e 2 horas de cálculo/mês.  Pode atualizar para outro plano de acordo com as suas necessidades.  Detalhes sobre os preços de diferentes planos estão disponíveis [aqui](https://azure.microsoft.com/pricing/details/machine-learning/) no âmbito do "Preço da API web de produção".

## <a name="managing-aml-plans"></a>Gestão de Planos AML
Pode gerir o seu plano de faturação [aqui.](https://services.azureml.net/plans/)  O nome do plano será baseado no nome do grupo de recursos que escolheu ao implementar a API, além de uma cadeia única na sua subscrição.  As instruções sobre como atualizar o seu plano estão disponíveis [aqui](../classic/manage-new-webservice.md) na secção "Gerir planos de faturação".

## <a name="api-definition"></a>Definição API
O serviço web fornece uma API baseada em REST sobre HTTPS que pode ser consumida de diferentes maneiras, incluindo uma aplicação web ou móvel, R, Python, Excel, etc.  Envia os dados da série de tempo para este serviço através de uma chamada de API REST, e executa uma combinação dos três tipos de anomalias descritos abaixo.

## <a name="calling-the-api"></a>Chamando a API
Para ligar para a API, terá de saber a localização do ponto final e a chave API.  Estes dois requisitos, juntamente com o código de amostra para chamar a API, estão disponíveis na página de serviços web do [Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  Navegue para a API desejada e, em seguida, clique no separador "Consumir" para encontrá-los.  Pode chamar a API como uma API swagger (isto é, com o parâmetro `format=swagger` URL) ou como uma API não-Swagger (isto é, sem o `format` parâmetro URL).  O código de amostra utiliza o formato Swagger.  Abaixo está um exemplo de pedido e resposta em formato não-Swagger.  Estes exemplos são para o ponto final da sazonalidade.  O ponto final da não-sazonalidade é semelhante.

### <a name="sample-request-body"></a>Corpo de Pedido de Amostra
O pedido contém dois objetos: `Inputs` e `GlobalParameters` .  No pedido de exemplo abaixo, alguns parâmetros são enviados explicitamente enquanto outros não são (rolar para baixo para uma lista completa de parâmetros para cada ponto final).  Os parâmetros que não são enviados explicitamente no pedido utilizarão os valores predefinidos indicados abaixo.

```json
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
```

### <a name="sample-response"></a>Resposta de Amostra
Para ver o `ColumnNames` campo, deve incluir `details=true` como parâmetro URL no seu pedido.  Veja as tabelas abaixo para ver o significado por trás de cada um destes campos.

```json
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
```


## <a name="score-api"></a>Pontuação API
A API de pontuação é utilizada para a deteção de anomalias em dados de séries de tempo não sazonais. A API executa uma série de detetores de anomalias nos dados e devolve as suas pontuações de anomalia.
A figura abaixo mostra um exemplo de anomalias que a API de pontuação pode detetar. Esta série de tempo tem duas mudanças de nível distintas, e três picos. Os pontos vermelhos mostram a hora em que a mudança de nível é detetada, enquanto os pontos pretos mostram os picos detetados.
![Pontuação API][1]

### <a name="detectors"></a>Detetores
A API de deteção de anomalias suporta detetores em três grandes categorias. Os detalhes sobre parâmetros e saídas de entrada específicos para cada detetor podem ser encontrados na tabela seguinte.

| Categoria detetor | Detetor | Description | Parâmetros de Entrada | Saídas |
| --- | --- | --- | --- | --- |
| Detetores de Espigões |TSpike Detector |Detetar picos e mergulhos com base em longe os valores são do primeiro e terceiro quartil |*tspikedetector.sensibilidade:* tem valor inteiro na gama 1-10, padrão: 3; Valores mais elevados irão captar valores mais extremos, tornando-o menos sensível |TSpike: valores binários – '1' se for detetado um pico/mergulho, '0' de outra forma |
| Detetores de Espigões | ZSpike Detector |Detetar picos e mergulhos com base na distância dos pontos de dados da sua média |*zspikedetector.sensibilidade:* ter valor inteiro na gama 1-10, padrão: 3; Valores mais elevados vão captar valores mais extremos tornando-o menos sensível |ZSpike: valores binários – '1' se for detetado um pico/mergulho, '0' de outra forma |
| Detetor de Tendências Lentas |Detetor de Tendências Lentas |Detetar tendência positiva lenta de acordo com a sensibilidade definida |*trenddetector.sensibilidade:* limiar na pontuação do detetor (padrão: 3.25, 3.25 - 5 é um intervalo razoável para selecionar a partir de; Quanto maior, menos sensível) |tscore: número flutuante que representa a pontuação da anomalia na tendência |
| Detetores de mudança de nível | Detetor de alteração de nível bidirecional |Detetar a variação de nível ascendente e descendente de acordo com a sensibilidade definida |*bileveldetector.sensibilidade:* limiar na pontuação do detetor (padrão: 3.25, 3.25 - 5 é um intervalo razoável para selecionar a partir de; Quanto maior, menos sensível) |rpscore: número flutuante que representa a pontuação da anomalia na variação de nível ascendente e descendente |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre estes parâmetros de entrada estão listadas no quadro seguinte:

| Parâmetros de Entrada | Description | Definição predefinida | Tipo | Gama Válida | Gama Sugerida |
| --- | --- | --- | --- | --- | --- |
| detetores.historywindow |História (em # de pontos de dados) usada para cálculo de pontuação de anomalia |500 |número inteiro |10-2000 |Série temporal dependente |
| detetores.spikesdips | Se detetar apenas picos, apenas mergulhos, ou ambos |Ambos |enumerado |Ambos, Spikes, Dips |Ambos |
| bileveldetector.sensibilidade |Sensibilidade para o detetor de alteração de nível bidirecional. |3,25 |double |Nenhum |3.25-5 (Valores inferiores significam mais sensíveis) |
| trenddetector.sensibilidade |Sensibilidade para detetor de tendências positivo. |3,25 |double |Nenhum |3.25-5 (Valores inferiores significam mais sensíveis) |
| tspikedetector.sensibilidade |Sensibilidade para detetor de TSpike |3 |número inteiro |1-10 |3-5 (Valores inferiores significam mais sensíveis) |
| zspikedetector.sensibilidade |Sensibilidade para Detetor ZSpike |3 |número inteiro |1-10 |3-5 (Valores inferiores significam mais sensíveis) |
| postprocess.tailRows |Número dos últimos pontos de dados a manter nos resultados da saída |0 |número inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos para manter nos resultados |N/D |

### <a name="output"></a>Saída
A API executa todos os detetores nos dados da série de tempo e devolve pontuações de anomalias e indicadores binários de pico para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| Saídas | Description |
| --- | --- |
| Hora |Os tempos de data dos dados brutos ou dados agregados (e/ou) imputados se forem aplicados a imputação de dados em falta de agregação (e/ou) em falta |
| Dados |Valores a partir de dados brutos ou dados agregados (e/ou) imputados se a imputação de dados em falta de agregação (e/ou) em falta |
| TSpike |Indicador binário para indicar se um pico é detetado pelo Detetor de TSpike |
| ZSpike |Indicador binário para indicar se um pico é detetado pelo Detetor ZSpike |
| rpscore |Um número flutuante que representa a pontuação da anomalia na alteração do nível bidirecional |
| rpalert |Valor de 1/0 indicando que existe uma anomalia de alteração de nível bidirecional com base na sensibilidade à entrada |
| tscore |Um número flutuante que representa a pontuação da anomalia na tendência positiva |
| talert |Valor de 1/0 indicando que há uma anomalia de tendência positiva baseada na sensibilidade à entrada |

## <a name="scorewithseasonality-api"></a>Pontuação ComSeasonality API
A API scoreWithSeasonality é usada para executar a deteção de anomalias em séries de tempo que têm padrões sazonais. Esta API é útil para detetar desvios em padrões sazonais.
A figura a seguir mostra um exemplo de anomalias detetadas numa série de tempo sazonal. A série de tempo tem um pico (o primeiro ponto preto), dois mergulhos (o segundo ponto preto e um no final) e uma mudança de nível (ponto vermelho). Tanto o mergulho no meio da série de tempo como a mudança de nível só são percetíveis após os componentes sazonais serem removidos da série.
![API de sazonalidade][2]

### <a name="detectors"></a>Detetores
Os detetores no ponto final da sazonalidade são semelhantes aos do ponto final da não sazonalidade, mas com nomes de parâmetros ligeiramente diferentes (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre estes parâmetros de entrada estão listadas no quadro seguinte:

| Parâmetros de Entrada | Description | Definição predefinida | Tipo | Gama Válida | Gama Sugerida |
| --- | --- | --- | --- | --- | --- |
| pré-processamento.agregaçãoInterente |Intervalo de agregação em segundos para agregação de séries de tempo de entrada |0 (não é feita agregação) |número inteiro |0: saltar agregação, > 0 de outra forma |5 minutos a 1 dia, série de tempo dependente |
| pré-processamento.agregaçãoFunc |Função utilizada para agregar dados na agregação especificadaInterente |mean |enumerado |média, soma, comprimento |N/D |
| pré-processamento.substituindo Aissing |Valores utilizados para imputar dados em falta |lkv (último valor conhecido) |enumerado |zero, lkv, média |N/D |
| detetores.historywindow |História (em # de pontos de dados) usada para cálculo de pontuação de anomalia |500 |número inteiro |10-2000 |Série temporal dependente |
| detetores.spikesdips | Se detetar apenas picos, apenas mergulhos, ou ambos |Ambos |enumerado |Ambos, Spikes, Dips |Ambos |
| bileveldetector.sensibilidade |Sensibilidade para o detetor de alteração de nível bidirecional. |3,25 |double |Nenhum |3.25-5 (Valores inferiores significam mais sensíveis) |
| pós-doutoramento.sensibilidade |Sensibilidade para detetor de tendências positivo. |3,25 |double |Nenhum |3.25-5 (Valores inferiores significam mais sensíveis) |
| negtrenddetector.sensibilidade |Sensibilidade para detetor de tendências negativas. |3,25 |double |Nenhum |3.25-5 (Valores inferiores significam mais sensíveis) |
| tspikedetector.sensibilidade |Sensibilidade para detetor de TSpike |3 |número inteiro |1-10 |3-5 (Valores inferiores significam mais sensíveis) |
| zspikedetector.sensibilidade |Sensibilidade para Detetor ZSpike |3 |número inteiro |1-10 |3-5 (Valores inferiores significam mais sensíveis) |
| sazonalidade.permitir |Se a análise da sazonalidade deve ser realizada |true |boolean |TRUE, false |Série temporal dependente |
| sazonalidade.numSeasonality |Número máximo de ciclos periódicos a detetar |1 |número inteiro |1, 2 |1-2 |
| sazonalidade.transformar |Se os componentes de tendência sazonais (e) devem ser removidos antes de aplicar a deteção de anomalias |desesta |enumerado |nenhuma, desesta, desazonaltrend |N/D |
| postprocess.tailRows |Número dos últimos pontos de dados a manter nos resultados da saída |0 |número inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos para manter nos resultados |N/D |

### <a name="output"></a>Saída
A API executa todos os detetores nos dados da série de tempo e devolve pontuações de anomalias e indicadores binários de pico para cada ponto no tempo. A tabela abaixo lista as saídas da API.

| Saídas | Description |
| --- | --- |
| Hora |Os tempos de data dos dados brutos ou dados agregados (e/ou) imputados se forem aplicados a imputação de dados em falta de agregação (e/ou) em falta |
| OriginalData |Valores a partir de dados brutos ou dados agregados (e/ou) imputados se a imputação de dados em falta de agregação (e/ou) em falta |
| Data processado |Qualquer uma das seguintes opções: <ul><li>Séries hortôns ajustadas sazonalmente se tiver sido detetada sazonalidade significativa e selecionada opção de desestação;</li><li>séries de tempo ajustadas sazonalmente e destrendadas se tiver sido detetada sazonalidade significativa e selecionada opção de desazonar</li><li>caso contrário, esta opção é a mesma que o OriginalData</li> |
| TSpike |Indicador binário para indicar se um pico é detetado pelo Detetor de TSpike |
| ZSpike |Indicador binário para indicar se um pico é detetado pelo Detetor ZSpike |
| BiLevelChangeScore |Um número flutuante que representa a pontuação da anomalia na mudança de nível |
| BiLevelChangeAlert |Valor de 1/0 indicando que há uma anomalia de mudança de nível baseada na sensibilidade à entrada |
| PosTrendScore |Um número flutuante que representa a pontuação da anomalia na tendência positiva |
| PosTrendAlert |Valor de 1/0 indicando que há uma anomalia de tendência positiva baseada na sensibilidade à entrada |
| NegTrendScore |Um número flutuante que representa a pontuação da anomalia na tendência negativa |
| NegTrendAlert |Valor de 1/0 indicando que há uma anomalia de tendência negativa baseada na sensibilidade à entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png