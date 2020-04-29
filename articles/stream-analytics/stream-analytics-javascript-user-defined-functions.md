---
title: Funções definidas pelo utilizador do Azure Stream Analytics JavaScript
description: Este artigo é uma introdução às funções definidas pelo utilizador JavaScript no Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 03/23/2020
ms.openlocfilehash: 58d750b47f3f6a2bcfbf23399ca249131e7876ae
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80235398"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo utilizador JavaScript no Azure Stream Analytics
 
O Azure Stream Analytics suporta as funções definidas pelo utilizador escritas no JavaScript. Com o conjunto rico de **métodos**String , **RegExp**, **Math**, **Array**e **Date** que o JavaScript fornece, transformações complexas de dados com trabalhos stream analytics tornam-se mais fáceis de criar.

## <a name="overview"></a>Descrição geral

As funções definidas pelo utilizador JavaScript suportam funções apátridas e apenas com cálculo que não requerem conectividade externa. O valor devolvido de uma função só pode ser um valor escalar (único). Depois de adicionar uma função definida pelo utilizador do JavaScript para uma tarefa, pode utilizar a função em qualquer parte da consulta, como uma função escalar incorporada.

Seguem-se alguns cenários onde as funções definidas pelo utilizador do JavaScript poderão ser úteis:
* Analisar e manipular cadeias com funções de expressão regular, por exemplo, **Regexp_Replace()** e **Regexp_Extract()**
* Descodificar e codificar dados, por exemplo, conversão de binário para hexadecimal
* Fazer computações matemáticas com funções **de Matemática** JavaScript
* Fazendo operações de matriz como tipo, junte-se, encontre e preencha

Aqui estão algumas coisas que não pode fazer com uma função definida pelo utilizador JavaScript no Stream Analytics:
* Chame pontos finais de REST externos, por exemplo, fazendo retrospetiva IP inversa ou retirando dados de referência de uma fonte externa
* Executar a serialização de formato de evento personalizado ou a desserialização em entradas/saídas
* Criar agregações personalizadas

Embora funções como **Date.GetDate()** ou **Math.random()** não estejam bloqueadas na definição de funções, deve evitar usá-las. Estas funções **não** devolvem o mesmo resultado sempre que as liga, e o serviço Azure Stream Analytics não mantém um diário de invocações de funções e resultados devolvidos. Se uma função retornar resultados diferentes nos mesmos eventos, a repetição não é garantida quando um trabalho é reiniciado por si ou pelo serviço Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Adicione uma função definida pelo utilizador JavaScript ao seu trabalho

> [!NOTE]
> Estes passos funcionam nos trabalhos do Stream Analytics configurados para funcionar na nuvem. Se o seu trabalho de Stream Analytics estiver configurado para funcionar no Azure IoT Edge, em vez disso use o Visual Studio e [escreva a função definida pelo utilizador usando C#](stream-analytics-edge-csharp-udf.md).

Para criar uma função definida pelo utilizador JavaScript no seu trabalho stream analytics, selecione **Funções** sob **Topologia de Trabalho**. Em seguida, selecione **JavaScript UDF** a partir do menu **+Adicionar** dropdown. 

![Adicione JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

Em seguida, deve fornecer as seguintes propriedades e selecionar **Guardar**.

|Propriedade|Descrição|
|--------|-----------|
|Pseudónimo de função|Insira um nome para invocar a função na sua consulta.|
|Tipo de saída|Escreva que será devolvido pela sua função definida pelo utilizador JavaScript à sua consulta Stream Analytics.|
|Definição de função|Implementação da sua função JavaScript que será executada cada vez que a sua UDF é invocada a partir da sua consulta.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Teste e resolução de problemas JavaScript UDFs 

Pode testar e desinserm a lógica javaScript UDF em qualquer navegador. Depuração e teste da lógica destas funções definidas pelo utilizador não é atualmente suportada no portal Stream Analytics. Uma vez que a função funcione como esperado, pode adicioná-la ao trabalho de Stream Analytics como mencionado acima e, em seguida, invocá-la diretamente a partir da sua consulta. Pode testar a sua lógica de consulta com a JavaScript UDF utilizando [ferramentas Stream Analytics para Estúdio Visual](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install).

Os erros de runtime do JavaScript são considerados fatais e são apresentados no Registo de atividades. Para obter o registo, no portal do Azure, aceda à sua tarefa e selecione **Registo de atividades**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo utilizador do JavaScript numa consulta

Pode invocar facilmente a sua função JavaScript na sua consulta utilizando o pseudónimo de função prefixado com **udf**. Aqui está um exemplo de uma UDF JavaScript que converte valores hexadecimais para inteiro sendo invocado numa consulta de Stream Analytics.

```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
```

## <a name="supported-javascript-objects"></a>Objetos de JavaScript suportados

As funções definidas pelo utilizador do JavaScript do Stream Analytics suportam objetos do JavaScript standard incorporados. Para obter uma lista destes objetos, veja [Objetos Globais](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversão de tipos do Stream Analytics e do JavaScript

Existem diferenças nos tipos que a linguagem de consulta do Stream Analytics e do JavaScript suportam. Esta tabela lista os mapeamentos de conversão entre os dois:

Stream Analytics | JavaScript
--- | ---
bigint | Number (o JavaScript só pode representar números inteiros até precisamente 2^53)
DateTime | Date (o JavaScript só suporta milissegundos)
double | Número
nvarchar(MAX) | String
Record | Objeto
Matriz | Matriz
NULL | Null

Seguem-se conversões do JavaScript para o Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Número | Bigint (se o número for redondo e entre long.MinValue e long.MaxValue; caso contrário, é duplo)
Date | DateTime
String | nvarchar(MAX)
Objeto | Record
Matriz | Matriz
Null, Undefined | NULL
Qualquer outro tipo (por exemplo, uma função ou erro) | Não suportado (resultados num erro de runtime)

A linguagem JavaScript é sensível a casos e o invólucro dos campos de objetos no código JavaScript deve corresponder ao invólucro dos campos nos dados que chegam. Os postos de trabalho com o nível de compatibilidade 1.0 converterão os campos da declaração do SQL SELECT para serem minúsculos. Sob o nível de compatibilidade 1.1 e superior, os campos da declaração SELECT terão o mesmo invólucro especificado na consulta SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Outros padrões da função definida pelo utilizador do JavaScript

### <a name="write-nested-json-to-output"></a>Escrever JSON aninhado para a saída

Se tiver um passo de processamento de seguimento que utiliza uma saída da tarefa do Stream Analytics como entrada e requer um formato JSON, pode escrever uma cadeia JSON para a saída. O exemplo seguinte chama a função **JSON.stringify()** para empacotar todos os pares de nome/valor da entrada e, em seguida, escreve-os como um valor de cadeia único na saída.

**Definição da função definida pelo utilizador do JavaScript:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de amostra:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.jsonstringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="next-steps"></a>Passos seguintes

* [UDF de Aprendizagem Automática](https://docs.microsoft.com/azure/stream-analytics/machine-learning-udf)
* [UDF em C#](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf-methods)
