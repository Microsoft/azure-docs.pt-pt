---
title: Agregados definidos pelo utilizador JavaScript no Azure Stream Analytics
description: Este artigo descreve como executar mecânicas de consulta avançadas com agregados definidos pelo utilizador JavaScript em Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.custom: devx-track-js
ms.openlocfilehash: e9695a268d1c6ec43a737afd36536e4ec2a41a41
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98012483"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics JavaScript agregados definidos pelo utilizador
 
O Azure Stream Analytics suporta agregados definidos pelo utilizador (UDA) escritos no JavaScript, que lhe permite implementar uma lógica de negócio complexa e imponente. Dentro da UDA você tem o controlo total da estrutura de dados do estado, acumulação do estado, descumulação do estado e cálculo de resultados agregado. O artigo introduz as duas diferentes interfaces JavaScript UDA, passos para criar um UDA, e como usar a UDA com operações baseadas em janelas na consulta Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregados definidos pelo utilizador JavaScript

Um agregado definido pelo utilizador é usado em cima de uma especificação de janela de tempo para agregar sobre os eventos naquela janela e produzir um único valor de resultado. Existem dois tipos de interfaces UDA que o Stream Analytics suporta hoje, AcumuladoOnly e AcumuladoDeacumulato. Ambos os tipos de UDA podem ser utilizados pela Tumbling, Hopping, Sliding e Session Window. Acumular O UDA desaculado apresenta um desempenho melhor do que o UDA acumuladonte quando utilizado juntamente com a Janela Hopping, Deslizante e Sessão. Escolhe um dos dois tipos com base no algoritmo que utiliza.

### <a name="accumulateonly-aggregates"></a>Acumular Agregações Apenas

Acumulação Só os agregados podem acumular novos eventos no seu estado, o algoritmo não permite a desacumulação de valores. Escolha este tipo agregado quando desacumular uma informação do evento a partir do valor do Estado é impossível de implementar. Segue-se o modelo JavaScript para agregados Acumulados:

```JavaScript
// Sample UDA which state can only be accumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

### <a name="accumulatedeaccumulate-aggregates"></a>Acumular Agregados desacumulados

Acumular Agregados desacumulados permitem a desacumulação de um valor acumulado anterior do Estado, por exemplo, remover um par de valores-chave de uma lista de valores de evento, ou subtrair um valor de um estado de soma agregada. Segue-se o modelo JavaScript para agregados acumulados de acumulados:

```JavaScript
// Sample UDA which state can be accumulated and deaccumulated.
function main() {
    this.init = function () {
        this.state = 0;
    }

    this.accumulate = function (value, timestamp) {
        this.state += value;
    }

    this.deaccumulate = function (value, timestamp) {
        this.state -= value;
    }

    this.deaccumulateState = function (otherState){
        this.state -= otherState.state;
    }

    this.computeResult = function () {
        return this.state;
    }
}
```

## <a name="uda---javascript-function-declaration"></a>UDA - Declaração de função JavaScript

Cada UDA JavaScript é definido por uma declaração de objeto de função. Seguem-se os principais elementos numa definição UDA.

### <a name="function-alias"></a>Pseudónimo de função

O pseudónimo da função é o identificador UDA. Quando chamados em consulta Stream Analytics, use sempre o pseudónimo UDA juntamente com um "uda". .

### <a name="function-type"></a>Tipo de função

Para a UDA, o tipo de função deve ser **Javascript UDA**.

### <a name="output-type"></a>Tipo de saída

Um tipo específico que o trabalho do Stream Analytics apoiou, ou "Qualquer" se quiser lidar com o tipo na sua consulta.

### <a name="function-name"></a>Nome da função

O nome deste objeto função. O nome da função deve coincidir com o pseudónimo da UDA.

### <a name="method---init"></a>Método - init()

O método init() inicializa o estado do agregado. Este método chama-se quando a janela começa.

### <a name="method--accumulate"></a>Método – acumular()

O método acumulado() calcula o estado UDA com base no estado anterior e nos valores atuais do evento. Este método é chamado quando um evento entra numa janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Método – desacumular()

O método desacumulado() o método recalcula o estado com base no estado anterior e nos valores atuais do evento. Este método é chamado quando um evento deixa um SLIDINGWINDOW ou SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Método – DesacumulateState()

O método desacumulate() o método recalcula o estado com base no estado anterior e no estado de um lúpulo. Este método é chamado quando um conjunto de eventos deixa um HOPPINGWINDOW.

### <a name="method--computeresult"></a>Método – computeResult()

O método computeResult() devolve o resultado agregado com base no estado atual. Este método é chamado no final de uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA suportado por tipos de dados de entrada e saída
Para os tipos de dados JavaScript UDA, consulte a secção Stream Analytics e a conversão do **tipo JavaScript** de [UDFs Integrados JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Adicionar um UDA JavaScript do portal Azure

Abaixo caminhamos através do processo de criação de uma UDA a partir do Portal. O exemplo que usamos aqui é a computação de médias ponderadas pelo tempo.

Agora vamos criar um JavaScript UDA sob um trabalho asa existente seguindo os passos.

1. Inicie sessão no portal Azure e localize o seu trabalho de Stream Analytics existente.
1. Em seguida, clique no link de funções em **JOB TOPOLOGY**.
1. Clique no ícone **Adicionar** para adicionar uma nova função.
1. Na vista Nova Função, selecione **JavaScript UDA** como o Tipo de Função e, em seguida, vê um modelo UDA padrão aparecer no editor.
1. Preencha o "TWA" como pseudónimo da UDA e altere a implementação da função como o seguinte:

    ```JavaScript
    // Sample UDA which calculate Time-Weighted Average of incoming values.
    function main() {
        this.init = function () {
            this.totalValue = 0.0;
            this.totalWeight = 0.0;
        }

        this.accumulate = function (value, timestamp) {
            this.totalValue += value.level * value.weight;
            this.totalWeight += value.weight;

        }

        // Uncomment below for AccumulateDeaccumulate implementation
        /*
        this.deaccumulate = function (value, timestamp) {
            this.totalValue -= value.level * value.weight;
            this.totalWeight -= value.weight;
        }

        this.deaccumulateState = function (otherState){
            this.state -= otherState.state;
            this.totalValue -= otherState.totalValue;
            this.totalWeight -= otherState.totalWeight;
        }
        */

        this.computeResult = function () {
            if(this.totalValue == 0) {
                result = 0;
            }
            else {
                result = this.totalValue/this.totalWeight;
            }
            return result;
        }
    }
    ```

1. Uma vez que clique no botão "Guardar", o seu UDA aparece na lista de funções.

1. Clique na nova função "TWA", pode verificar a definição de função.

## <a name="calling-javascript-uda-in-asa-query"></a>Chamando JavaScript UDA em consulta ASA

No portal Azure e abra o seu trabalho, edite a função de consulta e ligue para a função TWA com um prefixo de mandato "uda". Por exemplo:

```SQL
WITH value AS
(
    SELECT
    NoiseLevelDB as level,
    DurationSecond as weight
FROM
    [YourInputAlias] TIMESTAMP BY EntryTime
)
SELECT
    System.Timestamp as ts,
    uda.TWA(value) as NoseDoseTWA
FROM value
GROUP BY TumblingWindow(minute, 5)
```

## <a name="testing-query-with-uda"></a>Consulta de teste com a UDA

Crie um ficheiro JSON local com conteúdo abaixo, faça o upload do ficheiro para o trabalho stream Analytics e teste acima da consulta.

```JSON
[
  {"EntryTime": "2017-06-10T05:01:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 22.0},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 81, "DurationSecond": 37.8},
  {"EntryTime": "2017-06-10T05:02:00-07:00", "NoiseLevelDB": 85, "DurationSecond": 26.3},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 95, "DurationSecond": 13.7},
  {"EntryTime": "2017-06-10T05:03:00-07:00", "NoiseLevelDB": 88, "DurationSecond": 10.3},
  {"EntryTime": "2017-06-10T05:05:00-07:00", "NoiseLevelDB": 103, "DurationSecond": 5.5},
  {"EntryTime": "2017-06-10T05:06:00-07:00", "NoiseLevelDB": 99, "DurationSecond": 23.0},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 1.76},
  {"EntryTime": "2017-06-10T05:07:00-07:00", "NoiseLevelDB": 79, "DurationSecond": 17.9},
  {"EntryTime": "2017-06-10T05:08:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 27.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 91, "DurationSecond": 17.1},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 115, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:09:00-07:00", "NoiseLevelDB": 80, "DurationSecond": 28.3},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 55, "DurationSecond": 18.2},
  {"EntryTime": "2017-06-10T05:10:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 25.8},
  {"EntryTime": "2017-06-10T05:11:00-07:00", "NoiseLevelDB": 83, "DurationSecond": 11.4},
  {"EntryTime": "2017-06-10T05:12:00-07:00", "NoiseLevelDB": 89, "DurationSecond": 7.9},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 112, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:15:00-07:00", "NoiseLevelDB": 93, "DurationSecond": 9.7},
  {"EntryTime": "2017-06-10T05:18:00-07:00", "NoiseLevelDB": 96, "DurationSecond": 3.7},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 108, "DurationSecond": 0.99},
  {"EntryTime": "2017-06-10T05:20:00-07:00", "NoiseLevelDB": 113, "DurationSecond": 25.1},
  {"EntryTime": "2017-06-10T05:22:00-07:00", "NoiseLevelDB": 110, "DurationSecond": 5.3}
]
```

## <a name="get-help"></a>Obter ajuda

Para obter ajuda adicional, experimente o nosso [Microsoft Q&Uma página de perguntas para Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Próximos passos

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência linguística de consulta Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência de Azure Stream Analytics REST API](/rest/api/streamanalytics/)