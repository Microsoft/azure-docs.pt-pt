---
title: API de deteção de anomalias de aprendizagem automática Azure - Processo de Ciência de Dados da Equipa
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
ms.openlocfilehash: 269cadc50d55c4b986c55f489cecd7fa17922ba8
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656555"
---
# <a name="machine-learning-anomaly-detection-api"></a>API de deteção de anomalias de aprendizagem automática

> [!NOTE]
> Este artigo está em manutenção. Encorajamo-lo a usar o [serviço API](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) do Detetor de Anomalias alimentado por uma galeria de algoritmos de Machine Learning no âmbito dos Serviços Cognitivos Azure para detetar anomalias de métricas de negócios, operacionais e IoT.

## <a name="overview"></a>Descrição geral
[A API de Deteção de Anomalias](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) é um exemplo construído com o Azure Machine Learning que deteta anomalias em dados de séries temporais com valores numéricos que são uniformemente espaçados no tempo.

Esta API pode detetar os seguintes tipos de padrões anómalos nos dados da série temporal:

* **Tendências positivas e negativas**: Por exemplo, ao monitorizar o uso da memória na computação, uma tendência ascendente pode ser interessante, uma vez que pode ser indicativo de uma fuga de memória,
* **Alterações na gama dinâmica de valores**: Por exemplo, ao monitorizar as exceções lançadas por um serviço na nuvem, quaisquer alterações na gama dinâmica de valores podem indicar instabilidade na saúde do serviço,
* **Picos e Mergulhos**: Por exemplo, ao monitorizar o número de falhas de login num serviço ou no número de check-outs num site de e-commerce, picos ou mergulhos podem indicar comportamentos anormais.

Estes detetores de aprendizagem automática acompanham tais mudanças de valores ao longo do tempo e reportam mudanças contínuas nos seus valores como pontuações de anomalias. Não necessitam de afinação do limiar adhoc e as suas pontuações podem ser usadas para controlar a taxa falsa mente positiva. A API de deteção de anomalias é útil em vários cenários como monitorização de serviços, rastreando KPIIs ao longo do tempo, monitorização de uso através de métricas como número de pesquisas, número de cliques, monitorização de desempenho através de contadores como memória, CPU, leituras de ficheiros, etc. ao longo do tempo.

A oferta de Deteção de Anomalias vem com ferramentas úteis para começar.

* A [aplicação web](https://anomalydetection-aml.azurewebsites.net/) ajuda-o a avaliar e visualizar os resultados de APIs de deteção de anomalias nos seus dados.

> [!NOTE]
> Experimente a **solução IT Anomaly Insights** powered by [this API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Implantação da API
Para utilizar a API, deve implantá-la para a sua subscrição Azure, onde será hospedado como um serviço web Azure Machine Learning.  Pode fazê-lo a partir da [Galeria Azure AI.](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)  Isto irá implantar dois Serviços Web Azure Machine Learning Studio (clássicos) para a sua subscrição Azure - um para deteção de anomalias com deteção de sazonalidade, e outro sem deteção de sazonalidade.  Uma vez concluída a implementação, poderá gerir as suas APIs a partir da página de serviços web Do [Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  A partir desta página, poderá encontrar os seus locais de ponta, chaves API, bem como código de amostra para ligar para a API.  Estão disponíveis instruções mais detalhadas [aqui.](/azure/machine-learning/studio/manage-new-webservice)

## <a name="scaling-the-api"></a>Dimensionamento da API
Por predefinição, a sua implementação terá um plano gratuito de faturação Dev/Test que inclui 1.000 transações/mês e 2 horas/mês de computação.  Pode fazer upgrade para outro plano de acordo com as suas necessidades.  Os detalhes sobre os preços dos diferentes planos estão disponíveis [aqui](https://azure.microsoft.com/pricing/details/machine-learning/) no âmbito do "Preço da Web API de produção".

## <a name="managing-aml-plans"></a>Gestão de Planos AML
Pode gerir o seu plano de faturação [aqui.](https://services.azureml.net/plans/)  O nome do plano será baseado no nome do grupo de recursos que escolheu ao implementar a API, além de uma cadeia que é única na sua subscrição.  Instruções sobre como atualizar o seu plano estão disponíveis [aqui](/azure/machine-learning/studio/manage-new-webservice) no âmbito da secção "Gerir planos de faturação".

## <a name="api-definition"></a>Definição API
O serviço web fornece um API baseado em REST sobre HTTPS que pode ser consumido de diferentes maneiras, incluindo uma aplicação web ou móvel, R, Python, Excel, etc.  Envia os dados da sua série de tempo para este serviço através de uma chamada REST API, e executa uma combinação dos três tipos de anomalias descritos abaixo.

## <a name="calling-the-api"></a>Chamando a API
Para ligar para a API, terá de saber a localização do ponto final e a chave API.  Estes dois requisitos, juntamente com o código de amostra para chamar a API, estão disponíveis na página de serviços web Do [Azure Machine Learning Studio (clássico).](https://services.azureml.net/webservices/)  Navegue para a API desejada e, em seguida, clique no separador "Consumir" para os encontrar.  Pode chamar a API como Uma API swagger (isto é, com o parâmetro `format=swagger` URL) ou como uma API não-Swagger (isto é, sem o `format` parâmetro URL).  O código da amostra usa o formato Swagger.  Abaixo está um pedido de exemplo e resposta em formato não-Swagger.  Estes exemplos são para o ponto final da sazonalidade.  O ponto final da não sazonalidade é semelhante.

### <a name="sample-request-body"></a>Corpo de Pedido de Amostra
O pedido contém dois objetos: `Inputs` e `GlobalParameters` .  No pedido de exemplo abaixo, alguns parâmetros são enviados explicitamente enquanto outros não são (rolar para baixo para uma lista completa de parâmetros para cada ponto final).  Os parâmetros que não são enviados explicitamente no pedido utilizarão os valores predefinidos abaixo indicados.

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

### <a name="sample-response"></a>Resposta de Amostra
Para ver o `ColumnNames` campo, deve incluir `details=true` como parâmetro URL no seu pedido.  Veja as tabelas abaixo para obter o significado por trás de cada um destes campos.

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


## <a name="score-api"></a>Pontuação API
O Score API é utilizado para executar a deteção de anomalias em dados de séries de tempo não sazonais. A API executa uma série de detetores de anomalias nos dados e devolve as suas pontuações de anomalia.
A figura abaixo mostra um exemplo de anomalias que a API de pontuação pode detetar. Esta série tem duas mudanças de nível distintas, e três picos. Os pontos vermelhos mostram o tempo em que a mudança de nível é detetada, enquanto os pontos pretos mostram os picos detetados.
![Pontuação API][1]

### <a name="detectors"></a>Detetores
A API de deteção de anomalias suporta detetores em três grandes categorias. Detalhes sobre parâmetros de entrada específicos e saídas para cada detetor podem ser encontrados na tabela seguinte.

| Categoria Detetor | Detetor | Descrição | Parâmetros de Entrada | Saídas |
| --- | --- | --- | --- | --- |
| Detetores de Espigões |TSpike Detector |Detetar picos e mergulhos com base em longe os valores são de primeiro e terceiro quartiles |*tspikedetector.sensibilidade:* assume valor inteiro na gama 1-10, predefinição: 3; Valores mais elevados vão capturar valores mais extremos, tornando-o menos sensível |TSpike: valores binários – '1' se for detetado um pico/mergulho, '0' de outra forma |
| Detetores de Espigões | ZSpike Detector |Detete picos e mergulhos com base na distância dos pontos de dados da sua média |*zspikedetector.sensibilidade:* tome o valor inteiro no intervalo 1-10, predefinido: 3; Valores mais elevados vão captar valores mais extremos tornando-o menos sensível |ZSpike: valores binários – '1' se for detetado um pico/mergulho, '0' de outra forma |
| Detetor de tendências lentas |Detetor de tendências lentas |Detete uma tendência positiva lenta de acordo com a sensibilidade definida |*trenddetector.sensibilidade:* limiar na pontuação do detetor (predefinido: 3.25, 3.25 - 5 é uma gama razoável para selecionar; Quanto maior, menos sensível) |tscore: número flutuante que representa pontuação de anomalia na tendência |
| Detetores de Mudança de Nível | Detetor de Mudança de Nível Bidirecional |Detete a variação do nível ascendente e descendente de acordo com a sensibilidade definida |*bileveldetector.sensibilidade:* limiar na pontuação do detetor (predefinido: 3.25, 3.25 - 5 é uma gama razoável para selecionar; Quanto maior, menos sensível) |rpscore: número flutuante que representa a pontuação da anomalia na variação do nível ascendente e descendente |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre estes parâmetros de entrada estão listadas no quadro abaixo:

| Parâmetros de Entrada | Descrição | Definição padrão | Tipo | Gama Válida | Alcance sugerido |
| --- | --- | --- | --- | --- | --- |
| detetores.janela de histórico |História (em # de pontos de dados) usado para a computação de pontuação de anomalias |500 |número inteiro |10-2000 |Dependente de séries de tempo |
| detetores.spikesdips | Se para detetar apenas picos, apenas mergulhos, ou ambos |Ambos |enumerado |Ambos, Spikes, Dips |Ambos |
| bileveldetector.sensibilidade |Sensibilidade para o detetor de mudança de nível bidirecional. |3,25 |double |Nenhum |3.25-5 (Valores menores significam mais sensíveis) |
| trenddetector.sensibilidade |Sensibilidade para detetor de tendências positivo. |3,25 |double |Nenhum |3.25-5 (Valores menores significam mais sensíveis) |
| tspikedetector.sensibilidade |Sensibilidade para detetor de TSpike |3 |número inteiro |1-10 |3-5 (Valores menores significam mais sensíveis) |
| zspikedetector.sensibilidade |Sensibilidade para detetor zspike |3 |número inteiro |1-10 |3-5 (Valores menores significam mais sensíveis) |
| pós-processo.tailRows |Número dos últimos pontos de dados a manter nos resultados da produção |0 |número inteiro |0 (manter todos os pontos de dados) ou especificar número de pontos a manter nos resultados |N/D |

### <a name="output"></a>Saída
A API executa todos os detetores nos dados da série de tempo e devolve pontuações de anomalias e indicadores binários de pico para cada ponto do tempo. A tabela abaixo lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de tempo de dados brutos, ou dados agregados (e/ou) imputados se a agregação (e/ou) imputação de dados em falta for aplicada |
| Dados |Valores provenientes de dados brutos ou dados agregados (e/ou) imputados se a agregação (e/ou) imputação de dados em falta for aplicada |
| TSpike |Indicador binário para indicar se um pico é detetado pelo Detetor TSpike |
| ZSpike |Indicador binário para indicar se um pico é detetado pelo Detetor ZSpike |
| rpscore |Um número flutuante que representa a pontuação da anomalia na mudança de nível bidirecional |
| rpalert |1/0 valor indicando que existe uma anomalia de alteração do nível bidirecional com base na sensibilidade à entrada |
| tscore |Um número flutuante que representa a pontuação da anomalia na tendência positiva |
| talert |1/0 valor indicando que há uma anomalia de tendência positiva com base na sensibilidade à entrada |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
O ScoreWithSeasonality API é usado para executar a deteção de anomalias em séries temporais que têm padrões sazonais. Esta API é útil para detetar desvios em padrões sazonais.
A figura que se segue mostra um exemplo de anomalias detetadas numa série de horáriosazonal. A série tem um pico (o primeiro ponto preto), dois mergulhos (o segundo ponto preto e um no final) e uma mudança de nível (ponto vermelho). Tanto o mergulho no meio da série de tempo como a mudança de nível só são percetíveis após a remoção dos componentes sazonais da série.
![API sazonalidade][2]

### <a name="detectors"></a>Detetores
Os detetores no ponto final da sazonalidade são semelhantes aos do ponto final não sazonal, mas com nomes de parâmetros ligeiramente diferentes (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre estes parâmetros de entrada estão listadas no quadro abaixo:

| Parâmetros de Entrada | Descrição | Definição padrão | Tipo | Gama Válida | Alcance sugerido |
| --- | --- | --- | --- | --- | --- |
| pré-processo.agregaçãoInterval |Intervalo de agregação em segundos para agregar séries de tempo de entrada |0 (não é realizada agregação) |número inteiro |0: agregação de salto, > 0 de outra forma |5 minutos para 1 dia, dependente da série de tempo |
| pré-processo.agregaçãoFunc |Função utilizada para agregação de dados no intervalo de agregação especificado |mean |enumerado |média, soma, comprimento |N/D |
| pré-processo.substituirMissing |Valores usados para imputar dados em falta |lkv (último valor conhecido) |enumerado |zero, lkv, média |N/D |
| detetores.janela de histórico |História (em # de pontos de dados) usado para a computação de pontuação de anomalias |500 |número inteiro |10-2000 |Dependente de séries de tempo |
| detetores.spikesdips | Se para detetar apenas picos, apenas mergulhos, ou ambos |Ambos |enumerado |Ambos, Spikes, Dips |Ambos |
| bileveldetector.sensibilidade |Sensibilidade para o detetor de mudança de nível bidirecional. |3,25 |double |Nenhum |3.25-5 (Valores menores significam mais sensíveis) |
| postrenddetector.sensibilidade |Sensibilidade para detetor de tendências positivo. |3,25 |double |Nenhum |3.25-5 (Valores menores significam mais sensíveis) |
| negtrenddetector.sensibilidade |Sensibilidade para detetor de tendências negativo. |3,25 |double |Nenhum |3.25-5 (Valores menores significam mais sensíveis) |
| tspikedetector.sensibilidade |Sensibilidade para detetor de TSpike |3 |número inteiro |1-10 |3-5 (Valores menores significam mais sensíveis) |
| zspikedetector.sensibilidade |Sensibilidade para detetor zspike |3 |número inteiro |1-10 |3-5 (Valores menores significam mais sensíveis) |
| sazonalidade.habilitar |Se a análise da sazonalidade deve ser realizada |true |boolean |TRUE, false |Dependente de séries de tempo |
| sazonalidade.numSazonalidade |Número máximo de ciclos periódicos a detetar |1 |número inteiro |1, 2 |1-2 |
| sazonalidade.transformar |Se os componentes de tendência sazonais (e) devem ser removidos antes de aplicar a deteção de anomalias |detemporada |enumerado |nenhum, destemporada, detemporada |N/D |
| pós-processo.tailRows |Número dos últimos pontos de dados a manter nos resultados da produção |0 |número inteiro |0 (manter todos os pontos de dados) ou especificar número de pontos a manter nos resultados |N/D |

### <a name="output"></a>Saída
A API executa todos os detetores nos dados da série de tempo e devolve pontuações de anomalias e indicadores binários de pico para cada ponto do tempo. A tabela abaixo lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de tempo de dados brutos, ou dados agregados (e/ou) imputados se a agregação (e/ou) imputação de dados em falta for aplicada |
| Data Original |Valores provenientes de dados brutos ou dados agregados (e/ou) imputados se a agregação (e/ou) imputação de dados em falta for aplicada |
| Data processado |Qualquer uma das seguintes opções: <ul><li>Séries temporais ajustadas sazonalmente se tiver sido detetada sazonalidade significativa e a opção de destemporada selecionada;</li><li>sazonalmente ajustado e desonrou séries de tempo se a sazonalidade significativa foi detetada e a opção de destemporada selecionada</li><li>caso contrário, esta opção é a mesma que originalData</li> |
| TSpike |Indicador binário para indicar se um pico é detetado pelo Detetor TSpike |
| ZSpike |Indicador binário para indicar se um pico é detetado pelo Detetor ZSpike |
| BiLevelChangeScore |Um número flutuante que representa a pontuação da anomalia na mudança de nível |
| BiLevelChangeAlert |1/0 valor indicando que existe uma anomalia de alteração de nível com base na sensibilidade da entrada |
| PosTrendScore |Um número flutuante que representa a pontuação da anomalia na tendência positiva |
| PosTrendAlert |1/0 valor indicando que há uma anomalia de tendência positiva com base na sensibilidade à entrada |
| NegTrendScore |Um número flutuante que representa a pontuação da anomalia na tendência negativa |
| NegTrendAlert |1/0 valor indicando que existe uma anomalia de tendência negativa com base na sensibilidade à entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

