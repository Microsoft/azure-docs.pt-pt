---
title: Agregados definidos pelo utilizador JavaScript no Azure Stream Analytics
description: Este artigo descreve como executar mecânicas de consulta avançadacom agregados definidos pelo utilizador JavaScript no Azure Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: c509d174787a58abeee33e039eb7bbbcbcb43f38
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79531739"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Agregados definidos pelo utilizador do Azure Stream Analytics JavaScript
 
O Azure Stream Analytics suporta agregados definidos pelo utilizador (UDA) escritos no JavaScript, que lhe permite implementar uma lógica de negócio complexa e atemeda. Dentro da UDA tem o controlo total da estrutura de dados do Estado, da acumulação do Estado, da acumulação do Estado e da computação de resultados agregados. O artigo introduz as duas diferentes interfaces JavaScript UDA, passos para criar um UDA, e como usar a UDA com operações baseadas em janelas na consulta Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregados definidos pelo utilizador JavaScript

Um agregado definido pelo utilizador é utilizado em cima de uma especificação da janela do tempo para agregar sobre os eventos naquela janela e produzir um único valor de resultado. Existem dois tipos de interfaces UDA que o Stream Analytics suporta hoje, Acumulando Only e Acumulando. Ambos os tipos de UDA podem ser utilizados por Tumbling, Hopping, Sliding e Session Window. A UDA Acumulada apresenta-se melhor do que a UDA Acumulada quando utilizada juntamente com a Janela hopping, Deslizante e Sessão. Escolhe-se um dos dois tipos com base no algoritmo que utiliza.

### <a name="accumulateonly-aggregates"></a>AcumularAgregados

AcumulandoAaApenas agregados só podem acumular novos eventos ao seu estado, o algoritmo não permite a desacumulação de valores. Escolha este tipo agregado quando desacumule uma informação de evento do valor do Estado é impossível de implementar. Segue-se o modelo JavaScript para agregados acumuladosamente:

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

### <a name="accumulatedeaccumulate-aggregates"></a>Agregados Acumulados Acumulados

Os agregados Acumulados de Acumulação permitem a desacumulação de um valor acumulado anterior do Estado, por exemplo, remover um par de valor-chave de uma lista de valores de eventos, ou subtrair um valor de um estado agregado de soma. Segue-se o modelo JavaScript para agregados acumulados:

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

Cada UDA JavaScript é definido por uma declaração de objeto de função. Seguem-se os principais elementos de uma definição da UDA.

### <a name="function-alias"></a>Pseudónimo de função

O pseudónimo de função é o identificador uDA. Quando chamado na consulta Stream Analytics, use sempre pseudónimo uDA juntamente com um "uda". .

### <a name="function-type"></a>Tipo de função

Para a UDA, o tipo de função deve ser **Javascript UDA**.

### <a name="output-type"></a>Tipo de saída

Um tipo específico que o trabalho do Stream Analytics suportava, ou "Qualquer" se quiser lidar com o tipo na sua consulta.

### <a name="function-name"></a>Nome da função

O nome deste objeto função. O nome da função deve coincidir com o pseudónimo da UDA.

### <a name="method---init"></a>Método - init()

O método init() inicializa o estado do agregado. Este método é chamado quando a janela começa.

### <a name="method--accumulate"></a>Método – acumular()

O método acumulado () calcula o estado da UDA com base no estado anterior e nos valores atuais do evento. Este método é chamado quando um evento entra numa janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Método – desacumular()

O método de desacumulação() recalcula o estado com base no estado anterior e nos valores atuais do evento. Este método é chamado quando um evento deixa uma JANELA DESLIZANTE ou JANELA DE SESSÃO.

### <a name="method--deaccumulatestate"></a>Método – desacumulação Do Estado()

O método desacumulação do Estado recalcula o estado com base no estado anterior e no estado de um lúpulo. Este método é chamado quando um conjunto de eventos deixa uma HOPPINGWINDOW.

### <a name="method--computeresult"></a>Método – computeResult()

O método computeResult() devolve o resultado agregado com base no estado atual. Este método é chamado no final de uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>JavaScript UDA suportava os tipos de dados de entrada e saída
Para os tipos de dados JavaScript UDA, consulte a secção Stream Analytics e a conversão do **tipo JavaScript** de [UDFs Integrados JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Adicionar um JavaScript UDA do portal Azure

Abaixo caminhamos pelo processo de criação de uma UDA a partir do Portal. O exemplo que usamos aqui é a computação de médias ponderadas pelo tempo.

Agora vamos criar um JavaScript UDA sob um trabalho asa existente seguindo passos.

1. Inicie sessão no portal Azure e localize o seu trabalho existente no Stream Analytics.
1. Em seguida, clique no link de funções em **JOB TOPOLOGY**.
1. Clique no ícone **Adicionar** para adicionar uma nova função.
1. Na vista New Function, selecione **JavaScript UDA** como o Tipo de Função, então verá um modelo de UDA padrão aparecer no editor.
1. Preencha "TWA" como pseudónimo da UDA e altere a implementação da função como a seguinte:

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

1. Assim que clicar no botão "Guardar", o seu UDA aparece na lista de funções.

1. Clique na nova função "TWA", pode verificar a definição de função.

## <a name="calling-javascript-uda-in-asa-query"></a>Chamando JavaScript UDA em consulta ASA

No portal Azure e abra o seu trabalho, edite a consulta e ligue para a função TWA com um prefixo de mandato "uda". Por exemplo:

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

## <a name="testing-query-with-uda"></a>Testar consulta com UDA

Crie um ficheiro JSON local com conteúdo abaixo, faça upload do ficheiro para o trabalho do Stream Analytics e teste acima da consulta.

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

Para obter mais ajuda, experimente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência linguística de consulta Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência rest API de gestão do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
