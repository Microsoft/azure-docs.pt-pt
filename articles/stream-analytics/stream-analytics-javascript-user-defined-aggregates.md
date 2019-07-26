---
title: Agregados definidos pelo utilizador do JavaScript no Azure Stream Analytics
description: Este artigo descreve como executar uma mecânica de consulta avançada com agregações definidas pelo usuário do JavaScript em Azure Stream Analytics.
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2017
ms.openlocfilehash: 6c590ae62e080a6681e49c87264089f9a5f4ce2f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489526"
---
# <a name="azure-stream-analytics-javascript-user-defined-aggregates"></a>Azure Stream Analytics agregações definidas pelo usuário do JavaScript
 
Azure Stream Analytics dá suporte a UDA (agregações definidas pelo usuário) escritas em JavaScript, ela permite que você implemente uma lógica de negócios com estado complexa. Em UDA, você tem controle total da estrutura de dados de estado, acumulação de estado, desacumulação de estado e cálculo de resultado agregado. O artigo apresenta as duas diferentes interfaces UDA do JavaScript, as etapas para criar um UDA e como usar o UDA com operações baseadas em janelas em Stream Analytics consulta.

## <a name="javascript-user-defined-aggregates"></a>Agregações definidas pelo usuário do JavaScript

Uma agregação definida pelo usuário é usada na parte superior de uma especificação de janela de tempo para agregar sobre os eventos nessa janela e produzir um único valor de resultado. Há dois tipos de interfaces UDA que Stream Analytics oferece suporte a hoje, AccumulateOnly e AccumulateDeaccumulate. Os dois tipos de UDA podem ser usados por em cascata, salto, deslizante e janela de sessão. AccumulateDeaccumulate UDA tem um desempenho melhor do que o AccumulateOnly UDA quando usado junto com a janela de salto, deslizante e sessão. Você escolhe um dos dois tipos com base no algoritmo usado.

### <a name="accumulateonly-aggregates"></a>Agregações AccumulateOnly

As agregações AccumulateOnly só podem acumular novos eventos para seu estado, o algoritmo não permite a desacumulação de valores. Escolha esse tipo de agregação ao desacumular uma informação de evento do valor de estado é impossível de implementar. Veja a seguir o modelo de JavaScript para agregações AccumulatOnly:

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

### <a name="accumulatedeaccumulate-aggregates"></a>Agregações AccumulateDeaccumulate

As agregações AccumulateDeaccumulate permitem a desacumulação de um valor acumulado anterior do estado, por exemplo, remover um par chave-valor de uma lista de valores de evento ou subtrair um valor de um estado de agregação Sum. Veja a seguir o modelo de JavaScript para agregações AccumulateDeaccumulate:

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

## <a name="uda---javascript-function-declaration"></a>UDA-declaração de função de JavaScript

Cada UDA de JavaScript é definido por uma declaração de objeto de função. A seguir estão os principais elementos em uma definição de UDA.

### <a name="function-alias"></a>Alias de função

O alias da função é o identificador UDA. Quando chamado na consulta Stream Analytics, sempre use o alias UDA junto com um "UDA". prefixo.

### <a name="function-type"></a>Tipo de função

Para UDA, o tipo de função deve ser **JavaScript UDA**.

### <a name="output-type"></a>Tipo de saída

Um tipo específico que Stream Analytics trabalho com suporte, ou "any" se você quiser manipular o tipo em sua consulta.

### <a name="function-name"></a>Nome da função

O nome deste objeto de função. O nome da função deve corresponder ao alias UDA.

### <a name="method---init"></a>Método-init ()

O método init () inicializa o estado da agregação. Esse método é chamado quando a janela é iniciada.

### <a name="method--accumulate"></a>Método – acumular ()

O método Accumulate () calcula o estado UDA com base no estado anterior e nos valores de evento atuais. Esse método é chamado quando um evento entra em uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

### <a name="method--deaccumulate"></a>Método – desacumular ()

O método decumulate () recalcula o estado com base no estado anterior e nos valores do evento atual. Esse método é chamado quando um evento deixa um SLIDINGWINDOW ou um SESSIONWINDOW.

### <a name="method--deaccumulatestate"></a>Método – deaccumulateState ()

O método deaccumulateState () recalcula o estado com base no estado anterior e no estado de um salto. Esse método é chamado quando um conjunto de eventos deixa um HOPPINGWINDOW.

### <a name="method--computeresult"></a>Método – computeResult ()

O método computeResult () retorna o resultado agregado com base no estado atual. Esse método é chamado no final de uma janela de tempo (TUMBLINGWINDOW, HOPPINGWINDOW, SLIDINGWINDOW ou SESSIONWINDOW).

## <a name="javascript-uda-supported-input-and-output-data-types"></a>Tipos de dados de entrada e saída com suporte do JavaScript UDA
Para tipos de dados JavaScript UDA, consulte a seção **Stream Analytics e conversão de tipo JavaScript** de [integrar UDFs JavaScript](stream-analytics-javascript-user-defined-functions.md).

## <a name="adding-a-javascript-uda-from-the-azure-portal"></a>Adicionando um UDA de JavaScript do portal do Azure

Abaixo, percorremos o processo de criação de um UDA do Portal. O exemplo que usamos aqui é a computação de médias de tempo ponderado.

Agora, vamos criar um UDA JavaScript em um trabalho ASA existente seguindo as etapas.

1. Faça logon no portal do Azure e localize o trabalho de Stream Analytics existente.
1. Em seguida, clique no link funções em **topologia do trabalho**.
1. Clique no ícone **Adicionar** para adicionar uma nova função.
1. Na nova exibição de função, selecione **JavaScript UDA** como o tipo de função e, em seguida, você verá um modelo de UDA padrão aparecendo no editor.
1. Preencha "TWA" como o alias UDA e altere a implementação da função da seguinte maneira:

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

1. Depois de clicar no botão "salvar", seu UDA aparecerá na lista de funções.

1. Clique na nova função "TWA", você pode verificar a definição da função.

## <a name="calling-javascript-uda-in-asa-query"></a>Chamando o JavaScript UDA na consulta ASA

Em portal do Azure e abra seu trabalho, edite a consulta e chame a função TWA () com um prefixo obrigatório "UDA.". Por exemplo:

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

## <a name="testing-query-with-uda"></a>Testando consulta com UDA

Crie um arquivo JSON local com o conteúdo abaixo, carregue o arquivo para Stream Analytics trabalho e teste a consulta acima.

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
* [Referência de linguagem de consulta Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referência da API REST de gerenciamento de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
