---
title: API de detecção de anomalias Azure Machine Learning-processo de ciência de dados de equipe
description: A API de detecção de anomalias é um exemplo criado com Microsoft Azure Machine Learning que detecta anomalias em dados de série temporal com valores numéricos com espaçamento uniforme no tempo.
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
ms.openlocfilehash: 25b08bf78de61e556bab790869b45131a01ce6b8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495111"
---
# <a name="machine-learning-anomaly-detection-api"></a>API de detecção de anomalias Machine Learning

> [!NOTE]
> Este item está em manutenção. Incentivamos você a usar o [serviço de API do detector de anomalias](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) fornecido por uma galeria de algoritmos de Machine Learning em serviços cognitivas do Azure para detectar anomalias de métricas de negócios, operacionais e IOT.

## <a name="overview"></a>Descrição geral
A [API de detecção de anomalias](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2) é um exemplo criado com Azure Machine Learning que detecta anomalias em dados de série temporal com valores numéricos com espaçamento uniforme no tempo.

Essa API pode detectar os seguintes tipos de padrões anormais nos dados de série temporal:

* **Tendências positivas e negativas**: por exemplo, ao monitorar o uso de memória na computação, uma tendência ascendente pode ser de interesse, pois pode ser um indício de um vazamento de memória,
* **Alterações no intervalo dinâmico de valores**: por exemplo, ao monitorar as exceções geradas por um serviço de nuvem, quaisquer alterações no intervalo dinâmico de valores podem indicar instabilidade na integridade do serviço e
* **Picos e DIPs**: por exemplo, ao monitorar o número de falhas de logon em um serviço ou número de check-outs em um site de comércio eletrônico, picos ou DIPs podem indicar um comportamento anormal.

Esses detectores de aprendizado de máquina controlam essas alterações em valores ao longo do tempo e relatam alterações contínuas em seus valores como pontuações de anomalias. Eles não exigem ajuste de limite adhoc e suas pontuações podem ser usadas para controlar a taxa de falsos positivos. A API de detecção de anomalias é útil em vários cenários como o monitoramento de serviço rastreando KPIs ao longo do tempo, monitoramento de uso por meio de métricas, como o número de pesquisas, números de cliques, monitoramento de desempenho por meio de contadores como memória, CPU, leituras de arquivo, etc. ao longo do tempo.

A oferta de detecção de anomalias vem com ferramentas úteis para você começar.

* O [aplicativo Web](https://anomalydetection-aml.azurewebsites.net/) ajuda a avaliar e visualizar os resultados de APIs de detecção de anomalias em seus dados.

> [!NOTE]
> Experimente a solução de análise de **anomalias** da plataforma [desta API](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2)
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>Implantação de API
Para usar a API, você deve implantá-la em sua assinatura do Azure, na qual ela será hospedada como um serviço Web Azure Machine Learning.  Você pode fazer isso na [Galeria de ia do Azure](https://gallery.cortanaintelligence.com/MachineLearningAPI/Anomaly-Detection-2).  Isso implantará dois serviços da Web Azure Machine Learning Studio (clássico) (e seus recursos relacionados) para sua assinatura do Azure-um para detecção de anomalias com detecção de sazonalidade e outro sem detecção de sazonalidade.  Depois que a implantação for concluída, você poderá gerenciar suas APIs na página de [serviços web Azure Machine Learning Studio (clássico)](https://services.azureml.net/webservices/) .  Nessa página, você poderá encontrar seus locais de ponto de extremidade, chaves de API, bem como um código de exemplo para chamar a API.  Instruções mais detalhadas estão disponíveis [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice).

## <a name="scaling-the-api"></a>Dimensionando a API
Por padrão, sua implantação terá um plano de cobrança de desenvolvimento/teste gratuito que inclui 1.000 transações/mês e 2 horas de computação/mês.  Você pode atualizar para outro plano de acordo com suas necessidades.  Detalhes sobre os preços de planos diferentes estão disponíveis [aqui](https://azure.microsoft.com/pricing/details/machine-learning/) em "preços da API da Web de produção".

## <a name="managing-aml-plans"></a>Gerenciando planos AML
Você pode gerenciar seu plano de cobrança [aqui](https://services.azureml.net/plans/).  O nome do plano será baseado no nome do grupo de recursos que você escolheu ao implantar a API, além de uma cadeia de caracteres exclusiva à sua assinatura.  As instruções sobre como atualizar seu plano estão disponíveis [aqui](https://docs.microsoft.com/azure/machine-learning/machine-learning-manage-new-webservice) na seção "Gerenciando planos de cobrança".

## <a name="api-definition"></a>Definição de API
O serviço Web fornece uma API baseada em REST sobre HTTPS que pode ser consumida de diferentes maneiras, incluindo um aplicativo Web ou móvel, R, Python, Excel, etc.  Você envia os dados de série temporal para esse serviço por meio de uma chamada à API REST e executa uma combinação dos três tipos de anomalias descritos abaixo.

## <a name="calling-the-api"></a>Chamando a API
Para chamar a API, você precisará saber a localização do ponto de extremidade e a chave de API.  Ambos, juntamente com o código de exemplo para chamar a API, estão disponíveis na página de [serviços web Azure Machine Learning Studio (clássico)](https://services.azureml.net/webservices/) .  Navegue até a API desejada e clique na guia "consumir" para encontrá-las.  Observe que você pode chamar a API como uma API do Swagger (ou seja, com o parâmetro de URL `format=swagger`) ou como uma API não Swagger (ou seja, sem o parâmetro de URL `format`).  O código de exemplo usa o formato Swagger.  Veja abaixo um exemplo de solicitação e resposta no formato não Swagger.  Esses exemplos são para o ponto de extremidade sazonalidade.  O ponto de extremidade não sazonalidade é semelhante.

### <a name="sample-request-body"></a>Corpo da solicitação de amostra
A solicitação contém dois objetos: `Inputs` e `GlobalParameters`.  Na solicitação de exemplo abaixo, alguns parâmetros são enviados explicitamente enquanto outros não estão (role para baixo para obter uma lista completa de parâmetros para cada ponto de extremidade).  Os parâmetros que não são enviados explicitamente na solicitação usarão os valores padrão fornecidos abaixo.

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
Observe que, para ver o campo `ColumnNames`, você deve incluir `details=true` como um parâmetro de URL em sua solicitação.  Consulte as tabelas abaixo para saber o significado por trás de cada um desses campos.

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


## <a name="score-api"></a>API de Pontuação
A API de pontuação é usada para executar a detecção de anomalias em dados de série temporal não sazonais. A API executa vários detectores de anomalias nos dados e retorna suas pontuações de anomalias.
A figura a seguir mostra um exemplo de anomalias que a API de Pontuação pode detectar. Essa série temporal tem duas alterações de nível distintos e 3 picos. Os pontos vermelhos mostram a hora em que a alteração de nível é detectada, enquanto os pontos pretos mostram os picos detectados.
API de Pontuação de ![][1]

### <a name="detectors"></a>Detectores
A API de detecção de anomalias dá suporte a detectores em três categorias amplas. Os detalhes sobre os parâmetros de entrada e as saídas específicos de cada detector podem ser encontrados na tabela a seguir.

| Categoria do detector | Exclusões | Descrição | Parâmetros de Entrada | Saídas |
| --- | --- | --- | --- | --- |
| Detectores de pico |Detector de TSpike |Detectar picos e quedas com base nos valores distantes do primeiro e do terceiro quartil |*tspikedetector. sensibilidade:* Obtém o valor inteiro no intervalo de 1-10, padrão: 3; Valores mais altos obterão valores mais extremos, tornando-os menos sensíveis |TSpike: valores binários – ' 1 ' se um pico/DIP for detectado, ' 0 ' caso contrário |
| Detectores de pico | Detector de ZSpike |Detecte picos e quedas com base na distância dos pontos de extremidade de sua média |*zspikedetector. sensibilidade:* pegue o valor inteiro no intervalo de 1-10, padrão: 3; Valores mais altos capturarão valores mais extremos, tornando-os menos sensíveis |ZSpike: valores binários – ' 1 ' se um pico/DIP for detectado, ' 0 ' caso contrário |
| Detector de tendência lenta |Detector de tendência lenta |Detectar uma tendência positiva lenta de acordo com a sensibilidade do conjunto |*trenddetector. sensibilidade:* limite na pontuação do detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar isso; Quanto maior o menos sensível) |tscore: número flutuante que representa a pontuação de anomalias na tendência |
| Detectores de alteração de nível | Detector de alteração de nível bidirecional |Detectar as alterações de nível ascendente e descendente de acordo com a sensibilidade do conjunto |*bileveldetector. sensibilidade:* limite na pontuação do detector (padrão: 3,25, 3,25 – 5 é um intervalo razoável para selecionar isso; Quanto maior o menos sensível) |rpscore: número flutuante que representa a pontuação de anomalias na alteração de nível ascendente e descendente |

### <a name="parameters"></a>Parâmetros
Informações mais detalhadas sobre esses parâmetros de entrada estão listadas na tabela a seguir:

| Parâmetros de Entrada | Descrição | Configuração padrão | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| detectores. historywindow |Histórico (em n º de pontos de dados) usado para computação de Pontuação de anomalias |500 |número inteiro |10-2000 |Dependente da série temporal |
| detectores. spikesdips | Se é para detectar apenas picos, somente DIPs ou ambos |Ambos |enumerados |Ambos, picos, Dips |Ambos |
| bileveldetector. sensibilidade |Sensibilidade para o detector de alteração de nível bidirecional. |3,25 |double |Nenhum |3,25-5 (valores menores significam mais confidencial) |
| trenddetector. sensibilidade |Sensibilidade para o detector de tendência positivo. |3,25 |double |Nenhum |3,25-5 (valores menores significam mais confidencial) |
| tspikedetector. sensibilidade |Sensibilidade para o detector TSpike |3 |número inteiro |1-10 |3-5 (valores menores significam mais confidencial) |
| zspikedetector. sensibilidade |Sensibilidade para o detector ZSpike |3 |número inteiro |1-10 |3-5 (valores menores significam mais confidencial) |
| postprocess.tailRows |Número dos pontos de dados mais recentes a serem mantidos nos resultados da saída |0 |número inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos para manter os resultados |N/A |

### <a name="output"></a>Saída
A API executa todos os detectores em seus dados de série temporal e retorna pontuações de anomalias e indicadores de pico binário para cada ponto no tempo. A tabela a seguir lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de data/hora de dados brutos ou dados agregados (and/or) imputados se a agregação (e/ou) imputação de dados ausentes for aplicada |
| Dados |Valores de dados brutos ou dados agregados (and/or) imputados se a agregação (and/or) imputação de dados ausentes for aplicada |
| TSpike |Indicador binário para indicar se um pico é detectado pelo detector TSpike |
| ZSpike |Indicador binário para indicar se um pico é detectado pelo detector ZSpike |
| rpscore |Um número flutuante que representa A pontuação de anomalias na alteração de nível bidirecional |
| rpalert |valor 1/0 indicando que há uma anomalia de alteração de nível bidirecional com base na sensibilidade de entrada |
| tscore |Um número flutuante que representa A pontuação de anomalias na tendência positiva |
| talert |1/0 valor indicando que há uma anomalia de tendência positiva com base na sensibilidade de entrada |

## <a name="scorewithseasonality-api"></a>API ScoreWithSeasonality
A API ScoreWithSeasonality é usada para executar a detecção de anomalias na série temporal que têm padrões sazonais. Essa API é útil para detectar desvios em padrões sazonais.
A figura a seguir mostra um exemplo de anomalias detectadas em uma série temporal sazonal. A série temporal tem um pico (o primeiro ponto preto), dois DIPs (o 2º ponto preto e outro no final) e uma alteração de nível (ponto vermelho). Observe que o DIP no meio da série temporal e a alteração de nível só são discernidos depois que os componentes sazonais são removidos da série.
![][2] de API do sazonalidade

### <a name="detectors"></a>Detectores
Os detectores no ponto de extremidade sazonalidade são semelhantes àqueles no ponto de extremidade não sazonalidade, mas com nomes de parâmetros ligeiramente diferentes (listados abaixo).

### <a name="parameters"></a>Parâmetros

Informações mais detalhadas sobre esses parâmetros de entrada estão listadas na tabela a seguir:

| Parâmetros de Entrada | Descrição | Configuração padrão | Tipo | Intervalo válido | Intervalo sugerido |
| --- | --- | --- | --- | --- | --- |
| pré-processar. aggregationInterval |Intervalo de agregação em segundos para agregar a série temporal de entrada |0 (nenhuma agregação é executada) |número inteiro |0: ignorar agregação, > 0 caso contrário |5 minutos a 1 dia, dependente da série temporal |
| pré-processar. aggregationFunc |Função usada para agregar dados no AggregationInterval especificado |Significa |enumerados |média, soma, comprimento |N/A |
| pré-processar. replaceMissing |Valores usados para imputar dados ausentes |LKV (último valor conhecido) |enumerados |zero, LKV, média |N/A |
| detectores. historywindow |Histórico (em n º de pontos de dados) usado para computação de Pontuação de anomalias |500 |número inteiro |10-2000 |Dependente da série temporal |
| detectores. spikesdips | Se é para detectar apenas picos, somente DIPs ou ambos |Ambos |enumerados |Ambos, picos, Dips |Ambos |
| bileveldetector. sensibilidade |Sensibilidade para o detector de alteração de nível bidirecional. |3,25 |double |Nenhum |3,25-5 (valores menores significam mais confidencial) |
| postrenddetector. sensibilidade |Sensibilidade para o detector de tendência positivo. |3,25 |double |Nenhum |3,25-5 (valores menores significam mais confidencial) |
| negtrenddetector. sensibilidade |Sensibilidade para o detector de tendência negativa. |3,25 |double |Nenhum |3,25-5 (valores menores significam mais confidencial) |
| tspikedetector. sensibilidade |Sensibilidade para o detector TSpike |3 |número inteiro |1-10 |3-5 (valores menores significam mais confidencial) |
| zspikedetector. sensibilidade |Sensibilidade para o detector ZSpike |3 |número inteiro |1-10 |3-5 (valores menores significam mais confidencial) |
| sazonalidade. Enable |Se a análise de sazonalidade deve ser executada |true |boolean |verdadeiro, falso |Dependente da série temporal |
| sazonalidade. numSeasonality |Número máximo de ciclos periódicos a serem detectados |1 |número inteiro |1, 2 |1-2 |
| sazonalidade. Transform |Se os componentes de tendência (e) sazonais devem ser removidos antes da aplicação da detecção de anomalias |sem sazonalidade |enumerados |nenhum, detemporada, deseasontrend |N/A |
| postprocess.tailRows |Número dos pontos de dados mais recentes a serem mantidos nos resultados da saída |0 |número inteiro |0 (manter todos os pontos de dados) ou especificar o número de pontos para manter os resultados |N/A |

### <a name="output"></a>Saída
A API executa todos os detectores em seus dados de série temporal e retorna pontuações de anomalias e indicadores de pico binário para cada ponto no tempo. A tabela a seguir lista as saídas da API.

| Saídas | Descrição |
| --- | --- |
| Hora |Carimbos de data/hora de dados brutos ou dados agregados (and/or) imputados se a agregação (e/ou) imputação de dados ausentes for aplicada |
| OriginalData |Valores de dados brutos ou dados agregados (and/or) imputados se a agregação (and/or) imputação de dados ausentes for aplicada |
| ProcessedData |Um dos seguintes: <ul><li>Ajustada oportunamente a série temporal, se o sazonalidade significativo tiver sido detectado e a opção de destemporada selecionada;</li><li>série de tempo ajustada e detendência por hora, se sazonalidade significativa tiver sido detectado e a opção deseasontrend selecionada</li><li>caso contrário, isso é o mesmo que OriginalData</li> |
| TSpike |Indicador binário para indicar se um pico é detectado pelo detector TSpike |
| ZSpike |Indicador binário para indicar se um pico é detectado pelo detector ZSpike |
| BiLevelChangeScore |Um número flutuante que representa A pontuação de anomalias na alteração de nível |
| BiLevelChangeAlert |1/0 valor indicando que há uma anomalia na alteração de nível com base na sensibilidade de entrada |
| PosTrendScore |Um número flutuante que representa A pontuação de anomalias na tendência positiva |
| PosTrendAlert |1/0 valor indicando que há uma anomalia de tendência positiva com base na sensibilidade de entrada |
| NegTrendScore |Um número flutuante que representa A pontuação de anomalias na tendência negativa |
| NegTrendAlert |1/0 valor indicando que há uma anomalia de tendência negativa com base na sensibilidade de entrada |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png

