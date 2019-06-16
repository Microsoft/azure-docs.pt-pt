---
title: Agregados definidos pelo utilizador do JavaScript no Azure Stream Analytics
description: Este artigo descreve como realizar mecânicas de consulta avançada com agregados definidos pelo utilizador do JavaScript no Azure Stream Analytics.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6663e3fc48408de83e92f39e8c8070005818852d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479563"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates-preview"></a>Azure Stream Analytics definidas pelo utilizador agregados do JavaScript (pré-visualização)
 
O Azure Stream Analytics suporta definidas pelo utilizador agregados (UDA) escritos em JavaScript, permite-lhe implementar a lógica de negócio complexa de monitorização de estado. Dentro de UDA tem controlo total sobre a estrutura de dados de estado, a acumulação de estado, o decumulation de estado e a computação de resultado agregado. O artigo apresenta os dois diferentes que interfaces UDA de JavaScript, passos para criar uma UDA e como utilizar o UDA com operações baseadas em janela na consulta do Stream Analytics.

## <a name="javascript-user-defined-aggregates"></a>Agregações definidas pelo utilizador do JavaScript

Um agregado definido pelo utilizador é utilizado por cima de uma especificação de janela de tempo para agregar sobre os eventos nessa janela e produzir um valor de resultado único. Existem dois tipos de interfaces UDA Stream Analytics suporta hoje em dia, AccumulateOnly e AccumulateDeaccumulate. Ambos os tipos de UDA podem ser utilizados por janela em cascata, janela de saltos e janela deslizante. AccumulateDeaccumulate UDA tem um desempenho melhor AccumulateOnly UDA quando utilizado em conjunto com a janela de saltos e janela deslizante. Escolha um dos dois tipos com base no algoritmo que utilizar.

### <a name="accumulateonly-aggregates"></a>Agregações de AccumulateOnly

Agregações de AccumulateOnly apenas podem acumular novos eventos para o seu estado, o algoritmo não permite deaccumulation de valores. Selecione este tipo de agregação deaccumulate quando um evento informações do valor de estado são impossíveis implementar. Segue-se o modelo de JavaScript para as agregações de AccumulatOnly:

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

### <a name="accumulatedeaccumulate-aggregates"></a>Agregações de AccumulateDeaccumulate

Agregações de AccumulateDeaccumulate permitem deaccumulation de um valor acumulado anterior do Estado, por exemplo, remover um par chave-valor de uma lista de valores de eventos ou subtrair um valor de agregação de soma um Estado. Segue-se o modelo de JavaScript para as agregações de AccumulateDeaccumulate:

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

## <a name="uda---javascript-function-declaration"></a>UDA - declaração da função de JavaScript

Cada UDA de JavaScript é definido por uma declaração de objeto de função. Seguem-se os elementos principais numa definição de UDA.

### <a name="function-alias"></a>Alias de função

Alias de função é o identificador UDA. Quando chamado na consulta do Stream Analytics, utilize sempre o alias UDA, juntamente com uma "uda". prefixo.

### <a name="function-type"></a>Tipo de função

Para UDA, o tipo de função deve ser **UDA de Javascript**.

### <a name="output-type"></a>Tipo de saída

Uma específicas escreva essa tarefa do Stream Analytics suportada ou "Qualquer" se deseja lidar com o tipo na sua consulta.

### <a name="function-name"></a>Nome da função

O nome deste objeto de função. O nome da função, literalmente, deve corresponder ao UDA alias (pré-visualização do comportamento, estamos Considerando função anônima de suporte quando DG).

### <a name="method---init"></a>Método - Init)

O método Init () inicializa o estado da agregação. Esse método é chamado quando a janela é iniciado.

### <a name="method--accumulate"></a>Método – accumulate()

O método accumulate() calcula o estado UDA, com base no estado anterior e os valores atuais do evento. Este método é chamado quando um evento aciona uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW ou SLIDINGWINDOW).

### <a name="method--deaccumulate"></a>Método – deaccumulate()

O método deaccumulate() recalcula Estado consoante o estado anterior e os valores de evento atual. Este método é chamado quando um evento deixa um SLIDINGWINDOW.

### <a name="method--deaccumulatestate"></a>Método – deaccumulateState()

O método deaccumulateState() recalcula Estado consoante o estado anterior e o estado de um salto. Este método é chamado quando um conjunto de eventos deixe um HOPPINGWINDOW.

### <a name="method--computeresult"></a>Método – computeResult()

O método de computeResult() retorna o resultado agregado com base no estado atual. Este método é chamado no final de uma janela de tempo (TUMBLINGWINDOW HOPPINGWINDOW e SLIDINGWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>UDA de JavaScript suportados tipos de dados de entrada e saída
Para tipos de dados UDA de JavaScript, consulte a secção **conversão de tipos do Stream Analytics e o JavaScript** dos [integrar UDFs de JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Adicionar uma UDA do JavaScript a partir do portal do Azure

Veja a seguir vamos analisar o processo de criação de uma UDA do Portal. O exemplo que usamos aqui é a computação médias de tempo ponderada.

Agora vamos criar uma UDA do JavaScript numa tarefa ASA existente ao seguir os passos.

1. Inicie sessão no portal do Azure e localize a tarefa de Stream Analytics existente.
1. Em seguida, clique no link de funções sob **TOPOLOGIA da tarefa**.
1. Clique nas **adicionar** ícone para adicionar uma nova função.
1. Na vista de nova função, selecione **UDA de JavaScript** como o tipo de função, em seguida, verá um modelo UDA padrão aparecem no editor.
1. Preencha "TWA" como o alias UDA e alterar a implementação de função como o seguinte:

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

1. Depois de clicar no botão "Guardar", o UDA aparece na lista de função.

1. Clique na função nova "TWA", pode verificar a definição da função.

## <a name="calling-javascript-uda-in-asa-query"></a>Chamar o UDA de JavaScript na consulta do ASA

No portal do Azure e abra a sua tarefa, editar a consulta e chamar a função TWA() com um prefixo de mandado "uda.". Por exemplo:

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

## <a name="testing-query-with-uda"></a>Teste de consulta com UDA

Crie um ficheiro JSON local com abaixo conteúdo, carregar o ficheiro para a tarefa do Stream Analytics e testar a consulta acima.

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

## <a name="next-steps"></a>Passos Seguintes

* [Introdução ao Azure Stream Analytics](stream-analytics-introduction.md)
* [Começar a utilizar o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Tarefas de escala do Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referência de linguagem de consulta do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referência de REST API de gestão de Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
