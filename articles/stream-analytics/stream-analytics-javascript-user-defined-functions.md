---
title: Azure Stream Analytics JavaScript funções definidas pelo utilizador
description: Este artigo é uma introdução às funções definidas pelo utilizador JavaScript no Stream Analytics.
author: rodrigoaatmicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc, devx-track-js
ms.date: 06/16/2020
ms.openlocfilehash: 092e07ed01fb870cdcd9a3fd63d46d30cef96007
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780846"
---
# <a name="javascript-user-defined-functions-in-azure-stream-analytics"></a>Funções definidas pelo utilizador JavaScript no Azure Stream Analytics
 
O Azure Stream Analytics suporta as funções definidas pelo utilizador escritas no JavaScript. Com o rico conjunto de **métodos** de String , **RegExp,** **Math,** **Array** e **Date** que o JavaScript fornece, transformações complexas de dados com trabalhos stream Analytics tornam-se mais fáceis de criar.

## <a name="overview"></a>Descrição geral

As funções definidas pelo utilizador JavaScript suportam funções escalares apátridas e apenas computativas que não requerem conectividade externa. O valor devolvido de uma função só pode ser um valor escalar (único). Depois de adicionar uma função definida pelo utilizador do JavaScript para uma tarefa, pode utilizar a função em qualquer parte da consulta, como uma função escalar incorporada.

Seguem-se alguns cenários onde as funções definidas pelo utilizador do JavaScript poderão ser úteis:
* Analisar e manipular cadeias com funções de expressão regular, por exemplo, **Regexp_Replace()** e **Regexp_Extract()**
* Descodificar e codificar dados, por exemplo, conversão de binário para hexadecimal
* Fazer computações matemáticas com funções de **Matemática** JavaScript
* Fazendo operações de matriz como tipo, juntar, encontrar e preencher

Aqui estão algumas coisas que não pode fazer com uma função definida pelo utilizador JavaScript no Stream Analytics:
* Chame pontos finais externos DO REST, por exemplo, fazendo pesquisa ip inversa ou retirando dados de referência de uma fonte externa
* Executar a serialização de formato de evento personalizado ou a desserialização em entradas/saídas
* Criar agregações personalizadas

Embora funções como **Date.GetDate()** ou **Math.random()** não estejam bloqueadas na definição de funções, deve evitar usá-las. Estas funções **não** devolvem o mesmo resultado sempre que as liga, e o serviço Azure Stream Analytics não mantém um diário de invocações de funções e resultados devolvidos. Se uma função retornar resultados diferentes nos mesmos eventos, a repetibilidade não é garantida quando um trabalho é reiniciado por si ou pelo serviço Stream Analytics.

## <a name="add-a-javascript-user-defined-function-to-your-job"></a>Adicione uma função definida pelo utilizador JavaScript ao seu trabalho

> [!NOTE]
> Estes passos funcionam nos trabalhos do Stream Analytics configurados para correr na nuvem. Se o seu trabalho stream Analytics estiver configurado para funcionar no Azure IoT Edge, em vez disso use o Visual Studio e [escreva a função definida pelo utilizador utilizando C#](stream-analytics-edge-csharp-udf.md).

Para criar uma função definida pelo utilizador JavaScript no seu trabalho stream Analytics, selecione **Funções** em **Job Topology**. Em seguida, selecione **JavaScript UDF** a partir do menu de entrega **+Add.** 

![Adicionar JavaScript UDF](./media/javascript/stream-analytics-jsudf-add.png)

Em seguida, deve fornecer as seguintes propriedades e selecionar **Guardar.**

|Propriedade|Descrição|
|--------|-----------|
|Pseudónimo de função|Insira um nome para invocar a função na sua consulta.|
|Tipo de saída|Digite que será devolvido pela sua função definida pelo utilizador JavaScript à sua consulta Stream Analytics.|
|Definição de função|Implementação da sua função JavaScript que será executada cada vez que o seu UDF é invocado a partir da sua consulta.|

## <a name="test-and-troubleshoot-javascript-udfs"></a>Teste e resolução de problemas JavaScript UDFs 

Pode testar e depurar a sua lógica DeSconseção UDF JavaScript em qualquer browser. Depurar e testar a lógica destas funções definidas pelo utilizador não é atualmente suportado no portal Stream Analytics. Uma vez que a função funcione como esperado, pode adicioná-la ao trabalho stream Analytics como mencionado acima e, em seguida, invocá-la diretamente da sua consulta. Pode testar a sua lógica de consulta com o JavaScript UDF utilizando [ferramentas stream analytics para o Estúdio Visual](./stream-analytics-tools-for-visual-studio-install.md).

Os erros de runtime do JavaScript são considerados fatais e são apresentados no Registo de atividades. Para obter o registo, no portal do Azure, aceda à sua tarefa e selecione **Registo de atividades**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo utilizador do JavaScript numa consulta

Pode facilmente invocar a sua função JavaScript na sua consulta utilizando o pseudónimo de função prefixado com **udf**. Aqui está um exemplo de um UDF JavaScript que converte valores hexamalis para inteiros sendo invocados numa consulta stream Analytics.

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
nvarchar(MAX) | Cadeia
Registo | Objeto
Matriz | Matriz
NULL | Nulo

Seguem-se conversões do JavaScript para o Stream Analytics:

JavaScript | Stream Analytics
--- | ---
Número | Bigint (se o número for redondo e entre long.MinValue e long.MaxValue; caso contrário, é duplo)
Data | DateTime
Cadeia | nvarchar(MAX)
Objeto | Registo
Matriz | Matriz
Null, Undefined | NULL
Qualquer outro tipo (por exemplo, uma função ou erro) | Não suportado (resultados num erro de runtime)

A linguagem JavaScript é sensível a casos e o invólucro dos campos de objetos no código JavaScript deve coincidir com o invólucro dos campos nos dados de entrada. Os postos de trabalho com compatibilidade nível 1.0 converterão os campos da declaração SQL SELECT para serem minúsculos. Em termos de compatibilidade nível 1.1 e superior, os campos da declaração SELECT terão o mesmo invólucro especificado na consulta SQL.

## <a name="other-javascript-user-defined-function-patterns"></a>Outros padrões da função definida pelo utilizador do JavaScript

### <a name="write-nested-json-to-output"></a>Escrever JSON aninhado para a saída

Se tiver um passo de processamento de seguimento que utiliza uma saída da tarefa do Stream Analytics como entrada e requer um formato JSON, pode escrever uma cadeia JSON para a saída. O exemplo seguinte chama a função **JSON.stringify()** para empacotar todos os pares de nome/valor da entrada e, em seguida, escreve-os como um valor de cadeia único na saída.

**Definição da função definida pelo utilizador do JavaScript:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de amostras:**
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

### <a name="cast-string-to-json-object-to-process"></a>Fio de elenco para JSON objeto para processar

Se tiver um campo de cordas que seja JSON e quiser convertê-lo num objeto JSON para processamento num UDF JavaScript, pode utilizar a função **JSON.parse()** para criar um objeto JSON que pode ser utilizado.

**Definição da função definida pelo utilizador do JavaScript:**

```javascript
function main(x) {
var person = JSON.parse(x);  
return person.name;
}
```

**Consulta de amostras:**
```SQL
SELECT
    UDF.getName(input) AS Name
INTO
    output
FROM
    input
```

### <a name="use-trycatch-for-error-handling"></a>Utilize tentativa/captura para manuseamento de erros

Os blocos de tentativa/captura podem ajudá-lo a identificar problemas com dados de entrada mal formados que são transmitidos num UDF JavaScript.

**Definição da função definida pelo utilizador do JavaScript:**

```javascript
function main(input, x) {
    var obj = null;

    try{
        obj = JSON.parse(x);
    }catch(error){
        throw input;
    }
    
    return obj.Value;
}
```

**Consulta de amostra: Passe todo o registo como primeiro parâmetro para que possa ser devolvido em caso de erro.**
```SQL
SELECT
    A.context.company AS Company,
    udf.getValue(A, A.context.value) as Value
INTO
    output
FROM
    input A
```

### <a name="tolocalestring"></a>toLocaleString()
O método **toLocaleString** em JavaScript pode ser usado para devolver uma cadeia sensível à linguagem que representa os dados da hora da data de onde este método é chamado.
Mesmo que a Azure Stream Analtyics apenas aceite a hora de data UTC como o relógio do sistema, este método pode ser usado para cobrir o relógio do sistema para outro local e o timezone.
Este método segue o mesmo comportamento de implementação que o disponível no Internet Explorer.

**Definição da função definida pelo utilizador do JavaScript:**

```javascript
function main(datetime){
    const options = { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' };
    return event.toLocaleDateString('de-DE', options);
}
```

**Consulta de amostra: Passe uma data como valor de entrada**
```SQL
SELECT
    udf.toLocaleString(input.datetime) as localeString
INTO
    output
FROM
    input
```

A saída desta consulta será a data de entrada em **de-DE** com as opções fornecidas.
```
Samstag, 28. Dezember 2019
```

## <a name="next-steps"></a>Passos seguintes

* [UDF de aprendizagem automática](./machine-learning-udf.md)
* [UDF em C#](./stream-analytics-edge-csharp-udf-methods.md)
