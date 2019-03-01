---
title: 'Tutorial: Funções definidas pelo utilizador para JavaScript do Stream Analytics do Azure | Documentos da Microsoft '
description: Neste tutorial, vai realizar mecânicas de consulta avançada com as funções definidas pelo utilizador do JavaScript
services: stream-analytics
author: rodrigoamicrosoft
ms.author: rodrigoa
ms.service: stream-analytics
ms.topic: tutorial
ms.reviewer: mamccrea
ms.custom: mvc
ms.date: 04/01/2018
ms.openlocfilehash: ff8e61c53774429087ffe1a9137d40b155eb3f68
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192280"
---
# <a name="tutorial-azure-stream-analytics-javascript-user-defined-functions"></a>Tutorial: Funções definidas pelo utilizador para JavaScript do Stream Analytics do Azure
 
O Azure Stream Analytics suporta as funções definidas pelo utilizador escritas no JavaScript. Com o conjunto avançado de métodos de **Cadeia**, **RegExp**, **Matemática**, **Matriz** e **Data** que o JavaScript dispõe, as transformações de dados complexas com tarefas do Stream Analytics tornam-se mais fáceis de criar.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Definir uma função definida pelo utilizador do JavaScript
> * Adicionar a função ao portal
> * Definir uma consulta que executa a função

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="javascript-user-defined-functions"></a>Funções definidas pelo utilizador do JavaScript
As funções definidas pelo utilizador do JavaScript suportam funções escalares apenas de computação e sem estado que não necessitam de conectividade externa. O valor devolvido de uma função só pode ser um valor escalar (único). Depois de adicionar uma função definida pelo utilizador do JavaScript para uma tarefa, pode utilizar a função em qualquer parte da consulta, como uma função escalar incorporada.

Seguem-se alguns cenários onde as funções definidas pelo utilizador do JavaScript poderão ser úteis:
* Analisar e manipular cadeias com funções de expressão regular, por exemplo, **Regexp_Replace()** e **Regexp_Extract()**
* Descodificar e codificar dados, por exemplo, conversão de binário para hexadecimal
* Realizar cálculos matemáticos com funções **Matemáticas** do JavaScript
* Realizar operações de matriz, como ordenar, associar, encontrar e preencher

Eis algumas coisas que não pode fazer com uma função definida pelo utilizador do JavaScript no Stream Analytics:
* Chamar pontos finais REST externos, por exemplo, realizar pesquisa de IP inversa ou solicitar dados de referência de uma origem externa
* Executar a serialização de formato de evento personalizado ou a desserialização em entradas/saídas
* Criar agregações personalizadas

Embora as funções como **Date.GetDate()** ou **Math.random()** não estejam bloqueadas na definição das funções, deve evitar utilizá-las. Estas funções **não** devolvem o mesmo resultado sempre que as chamar e o serviço Azure Stream Analytics não mantém um diário das invocações de função e dos resultados devolvidos. Se uma função devolve diferentes resultados nos mesmos eventos, não é garantida repetibilidade quando uma tarefa for reiniciada por si ou pelo serviço do Stream Analytics.

## <a name="add-a-javascript-user-defined-function-in-the-azure-portal"></a>Adicionar uma função definida pelo utilizador do JavaScript no portal do Azure
Para criar uma função de definidas pelo utilizador de JavaScript simples numa tarefa do Stream Analytics existente, siga estes passos:

> [!NOTE]
> Estes passos funcionem nas tarefas do Stream Analytics configuradas para ser executada na cloud. Se a tarefa de Stream Analytics é configurada para executar no Azure IoT Edge, em vez disso, utilizar o Visual Studio e [escrever usando a função definida pelo utilizador C# ](stream-analytics-edge-csharp-udf.md).

1.  No portal do Azure, encontre a tarefa do Stream Analytics.

2. Sob o **topologia da tarefa** cabeçalho, selecione **funções**. É apresentada uma lista vazia das funções.

3.  Para criar uma nova função definida pelo utilizador, selecione **+ adicionar**.

4.  No painel **Nova Função**, para **Tipo de Função**, selecione **JavaScript**. É apresentado um modelo de função predefinido no editor.

5.  Para o **alias do UDF**, introduza **hex2Int** e altere a implementação da função da seguinte forma:

    ```javascript
    // Convert Hex value to integer.
    function hex2Int(hexValue) {
        return parseInt(hexValue, 16);
    }
    ```

6.  Selecione **Guardar**. A função é apresentada na lista das funções.
7.  Selecione a nova função **hex2Int** e marque a definição da função. Todas as funções têm um prefixo **UDF** adicionado ao alias da função. Tem de *incluir o prefixo* quando chama a função na sua consulta do Stream Analytics. Neste caso, tem de chamar **UDF.hex2Int**.

## <a name="call-a-javascript-user-defined-function-in-a-query"></a>Chamar uma função definida pelo utilizador do JavaScript numa consulta

1. No editor de consulta, sob o **topologia da tarefa** cabeçalho, selecione **consulta**.
2.  Edite a consulta e, em seguida, chame a função definida pelo utilizador, da seguinte forma:

    ```SQL
    SELECT
        time,
        UDF.hex2Int(offset) AS IntOffset
    INTO
        output
    FROM
        InputStream
    ```

3.  Para carregar o ficheiro de dados de exemplo, clique com botão direito do rato na entrada da tarefa.
4.  Para testar a sua consulta, selecione **Testar**.


## <a name="supported-javascript-objects"></a>Objetos de JavaScript suportados
As funções definidas pelo utilizador do JavaScript do Stream Analytics suportam objetos do JavaScript standard incorporados. Para obter uma lista destes objetos, veja [Objetos Globais](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

### <a name="stream-analytics-and-javascript-type-conversion"></a>Conversão de tipos do Stream Analytics e do JavaScript

Existem diferenças nos tipos que a linguagem de consulta do Stream Analytics e do JavaScript suportam. Esta tabela lista os mapeamentos de conversão entre os dois:

Stream Analytics | JavaScript
--- | ---
bigint | Number (o JavaScript só pode representar números inteiros até precisamente 2^53)
DateTime | Date (o JavaScript só suporta milissegundos)
double | Number
nvarchar(MAX) | String
Record | Object
Array | Array
NULL | Null


Seguem-se conversões do JavaScript para o Stream Analytics:


JavaScript | Stream Analytics
--- | ---
Number | Bigint (se o número for redondo e entre long.MinValue e long.MaxValue; caso contrário, é duplo)
Date | DateTime
String | nvarchar(MAX)
Object | Record
Array | Array
Null, Undefined | NULL
Qualquer outro tipo (por exemplo, uma função ou erro) | Não suportado (resultados num erro de runtime)

## <a name="troubleshooting"></a>Resolução de problemas
Os erros de runtime do JavaScript são considerados fatais e são apresentados no Registo de atividades. Para obter o registo, no portal do Azure, aceda à sua tarefa e selecione **Registo de atividades**.


## <a name="other-javascript-user-defined-function-patterns"></a>Outros padrões da função definida pelo utilizador do JavaScript

### <a name="write-nested-json-to-output"></a>Escrever JSON aninhado para a saída
Se tiver um passo de processamento de seguimento que utiliza uma saída da tarefa do Stream Analytics como entrada e requer um formato JSON, pode escrever uma cadeia JSON para a saída. O exemplo seguinte chama a função **JSON.stringify()** para empacotar todos os pares de nome/valor da entrada e, em seguida, escreve-os como um valor de cadeia único na saída.

**Definição da função definida pelo utilizador do JavaScript:**

```javascript
function main(x) {
return JSON.stringify(x);
}
```

**Consulta de exemplo:**
```SQL
SELECT
    DataString,
    DataValue,
    HexValue,
    UDF.json_stringify(input) As InputEvent
INTO
    output
FROM
    input PARTITION BY PARTITIONID
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo do portal do Azure, clique em **Grupos de recursos** e, em seguida, clique no nome de recurso que criou.  
2. Na página do grupo de recursos, clique em **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, clique em **Eliminar**.

## <a name="get-help"></a>Obter ajuda
Para obter mais ajuda, experimente ler o nosso [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma tarefa do Stream Analytics que executa uma função simples definida pelo utilizador do JavaScript. Para saber mais sobre o Stream Analytics, avance para os artigos de cenário em tempo real:

> [!div class="nextstepaction"]
> [Análise de sentimento do Twitter em tempo real no Azure Stream Analytics](stream-analytics-twitter-sentiment-analysis-trends.md)
