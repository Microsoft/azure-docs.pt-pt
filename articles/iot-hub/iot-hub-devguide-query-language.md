---
title: Compreenda a linguagem de consulta do Azure IoT Hub ! Microsoft Docs
description: Developer guide - descrição da linguagem de consulta IoT Hub semelhante ao SQL usada para obter informações sobre gémeos de dispositivo/módulo e empregos do seu hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: dbdc1c079f7ef2a06ece553e9fec542cbc05ea54
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147669"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Linguagem de consulta do Hub IoT do dispositivo e módulos duplos, tarefas e encaminhamento de mensagens

O IoT Hub fornece uma poderosa linguagem semelhante ao SQL para recuperar informações sobre gémeos do [dispositivo, gémeos](iot-hub-devguide-device-twins.md) [módulos,](iot-hub-devguide-module-twins.md) [empregos](iot-hub-devguide-jobs.md)e [encaminhamento de mensagens.](iot-hub-devguide-messages-d2c.md) Este artigo apresenta:

* Uma introdução às principais características da linguagem de consulta IoT Hub, e
* A descrição detalhada da língua. Para obter detalhes sobre o idioma de consulta para encaminhamento de mensagens, consulte [consultas no encaminhamento de mensagens](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Consultas de módulo e módulo

[Gémeos do dispositivo](iot-hub-devguide-device-twins.md) e [gémeos módulos](iot-hub-devguide-module-twins.md) podem conter objetos JSON arbitrários como etiquetas e propriedades. O IoT Hub permite-lhe consultar gémeos de dispositivos e gémeos módulos como um único documento JSON contendo todas as informações gémeas.

Suponha, por exemplo, que os gémeos do seu dispositivo de hub IoT têm a seguinte estrutura (módulo twin seria semelhante apenas com um módulo AdicionalId):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Consultas de gémeos do dispositivo

O IoT Hub expõe os gémeos do dispositivo como uma coleção de documentos chamada **dispositivos**. Por exemplo, a seguinte consulta recupera todo o conjunto de gémeos do dispositivo:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Os SDKs Azure IoT](iot-hub-devguide-sdks.md) suportam a paging de grandes resultados.

O IoT Hub permite-lhe recuperar o dispositivo gémeos filtrando com condições arbitrárias. Por exemplo, para receber gémeos do dispositivo onde a etiqueta **local.região** está definida para **OS EUA,** utilize a seguinte consulta:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Os operadores booleanos e as comparações aritméticas também são apoiadas. Por exemplo, para recuperar os gémeos do dispositivo localizados nos EUA e configurados para enviar telemetria menos do que cada minuto, utilize a seguinte consulta:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Como conveniência, também é possível utilizar constantes de matriz com os operadores **IN** e **NIN** (não dentro). Por exemplo, para recuperar gémeos de dispositivos que reportem Wi-Fi ou conectividade com fios utilize a seguinte consulta:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

É frequentemente necessário identificar todos os gémeos do dispositivo que contêm uma propriedade específica. O IoT Hub suporta a `is_defined()` função para este fim. Por exemplo, para recuperar os gémeos do dispositivo que definem a `connectivity` propriedade usam a seguinte consulta:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Consulte a secção [de cláusulaS WHERE](iot-hub-devguide-query-language.md#where-clause) para obter a referência completa das capacidades de filtragem.

São também apoiados agrupamentos e agregações. Por exemplo, para encontrar a contagem de dispositivos em cada estado de configuração de telemetria, utilize a seguinte consulta:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Esta consulta de agrupamento devolveria um resultado semelhante ao seguinte exemplo:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

Neste exemplo, três dispositivos relataram uma configuração bem sucedida, dois ainda estão a aplicar a configuração e um relatou um erro.

As consultas de projeção permitem que os desenvolvedores devolvam apenas as propriedades que lhes interessam. Por exemplo, para recuperar o último tempo de atividade de todos os dispositivos desligados, utilize a seguinte consulta:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Consultas gémeas do módulo

A consulta em gémeos módulos é semelhante à consulta em gémeos do dispositivo, mas usando um espaço de recolha/nome diferente; em vez de **dispositivos,** consultar a partir de **dispositivos.módulos:**

```sql
SELECT * FROM devices.modules
```

Não permitimos juntar-nos entre os dispositivos e as coleções de dispositivos.módulos. Se quiser consultar gémeos módulos através de dispositivos, fá-lo com base em etiquetas. Esta consulta irá devolver todos os gémeos módulos em todos os dispositivos com o estado de digitalização:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Esta consulta devolverá todos os gémeos módulos com o estado de digitalização, mas apenas no subconjunto especificado de dispositivos:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Exemplo C#

A funcionalidade de consulta é exposta pelo [serviço C# SDK](iot-hub-devguide-sdks.md) na classe **RegistryManager.**

Aqui está um exemplo de uma simples consulta:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

O objeto **de consulta** é instantâneo com um tamanho de página (até 100). Em seguida, várias páginas são recuperadas chamando os métodos **GetNextAsTwinAsync** várias vezes.

O objeto de consulta expõe vários valores **Seguintes,** dependendo da opção de deserialização exigida pela consulta. Por exemplo, dispositivos duplos ou objetos de trabalho, ou JSON simples quando utilizar projeções.

### <a name="nodejs-example"></a>Node.js exemplo

A funcionalidade de consulta é exposta pelo [serviço Azure IoT SDK para Node.js](iot-hub-devguide-sdks.md) no objeto **registro.**

Aqui está um exemplo de uma simples consulta:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

O objeto **de consulta** é instantâneo com um tamanho de página (até 100). Em seguida, várias páginas são recuperadas ligando para o **método nextAsTwin** várias vezes.

O objeto de consulta expõe vários valores **Seguintes,** dependendo da opção de deserialização exigida pela consulta. Por exemplo, dispositivos duplos ou objetos de trabalho, ou JSON simples quando utilizar projeções.

### <a name="limitations"></a>Limitações

> [!IMPORTANT]
> Os resultados da consulta podem ter alguns minutos de atraso no que diz respeito aos valores mais recentes em gémeos do dispositivo. Se consultar os gémeos individuais do dispositivo por ID, utilize a [API REST twin get twin](/java/api/com.microsoft.azure.sdk.iot.device.devicetwin?view=azure-java-stable). Esta API devolve sempre os valores mais recentes e tem limites de estrangulamento mais elevados. Pode emitir diretamente a API REST ou utilizar a funcionalidade equivalente num dos [SDKs de serviço Azure IoT Hub](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

Atualmente, as comparações são suportadas apenas entre tipos primitivos (sem objetos), por `... WHERE properties.desired.config = properties.reported.config` exemplo, só são suportadas se essas propriedades tiverem valores primitivos.

## <a name="get-started-with-jobs-queries"></a>Começar com consultas de emprego

[Os empregos](iot-hub-devguide-jobs.md) fornecem uma forma de executar operações em conjuntos de dispositivos. Cada dispositivo gémeo contém a informação dos trabalhos dos quais faz parte numa coleção chamada **jobs**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Atualmente, esta coleção é questionável como **devices.jobs** na linguagem de consulta IoT Hub.

> [!IMPORTANT]
> Atualmente, a propriedade do emprego nunca é devolvida quando consulta gémeos dispositivos. Ou seja, consultas que contêm "dispositivos FROM". A propriedade jobs só pode ser acedida diretamente com consultas usando `FROM devices.jobs` .
>
>

Por exemplo, para obter todos os empregos (passados e programados) que afetem um único dispositivo, pode utilizar a seguinte consulta:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Note como esta consulta fornece o estado específico do dispositivo (e possivelmente a resposta do método direto) de cada trabalho devolvido.

Também é possível filtrar com condições arbitrárias booleanas em todas as propriedades do objeto na coleção **devices.jobs.**

Por exemplo, para recuperar todos os trabalhos de atualização de dois dispositivos concluídos que foram criados após setembro de 2016 para um dispositivo específico, utilize a seguinte consulta:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Também pode recuperar os resultados por dispositivo de um único trabalho.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limitações

Atualmente, as consultas sobre **devices.jobs** não suportam:

* As projeções, portanto, só `SELECT *` são possíveis.
* Condições que se referem ao dispositivo twin para além das propriedades do trabalho (ver secção anterior).
* Realizando agregações, como contagem, avg, grupo por.

## <a name="basics-of-an-iot-hub-query"></a>Fundamentos de uma consulta do IoT Hub

Cada consulta IoT Hub é composta por cláusulas SELECT e FROM, com cláusulas OPCIONAIS WHERE e GROUP BY. Cada consulta é executada numa coleção de documentos JSON, por exemplo gémeos de dispositivos. A cláusula FROM indica que a recolha do documento deve ser iterada **(dispositivos,** **dispositivos,módulos,** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. Com agregações, os resultados deste passo são agrupados conforme especificado na cláusula GROUP BY. Para cada grupo, uma linha é gerada conforme especificado na cláusula SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Cláusula FROM

A cláusula **from <from_specification>** pode assumir apenas três **valores: desde dispositivos** a gémeos de dispositivos de consulta, **desde dispositivos.módulos a gémeos módulos** de consulta, ou de **devices.jobs** a detalhes de trabalho por dispositivo.

## <a name="where-clause"></a>Cláusula WHERE

A cláusula **where <filter_condition>** é opcional. Especifica uma ou mais condições que os documentos JSON na coleção FROM devem satisfazer para serem incluídos como parte do resultado. Qualquer documento JSON deve avaliar as condições especificadas para ser "verdadeiro" a incluir no resultado.

As condições permitidas são descritas na secção [Expressões e condições](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Cláusula SELECT

O **SELECT <select_list>** é obrigatório e especifica quais os valores obtidos a partir da consulta. Especifica os valores JSON a utilizar para gerar novos objetos JSON.
Para cada elemento do subconjunto filtrado (e opcionalmente agrupado) da coleção FROM, a fase de projeção gera um novo objeto JSON. Este objeto é construído com os valores especificados na cláusula SELECT.

Segue-se a gramática da cláusula SELECT:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM. Alguns exemplos de cláusulas SELECT podem ser encontrados na Secção de Iniciar com consultas de dois dispositivos.

Atualmente, cláusulas de seleção diferentes **do SELECT*** são suportadas apenas em consultas agregadas em gémeos de dispositivos.

## <a name="group-by-clause"></a>Cláusula GROUP BY

A cláusula **GROUP BY <group_specification>** é um passo opcional que executa após o filtro especificado na cláusula WHERE, e antes da projeção especificada no SELECT. Agru cria documentos com base no valor de um atributo. Estes grupos são utilizados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta utilizando o GROUP BY é:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A sintaxe formal para o GROUP BY é:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM.

Atualmente, a cláusula DO GRUPO BY só é suportada quando se consulta os gémeos do dispositivo.

> [!IMPORTANT]
> O termo `group` é atualmente tratado como uma palavra-chave especial em consultas. No caso de utilizar `group` como nome da sua propriedade, considere rodeá-lo com suportes duplos para evitar erros, por exemplo, `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'` .
>

## <a name="expressions-and-conditions"></a>Expressões e condições

A um nível elevado, uma *expressão:*

* Avalia uma instância do tipo JSON (como Boolean, número, corda, matriz ou objeto).
* É definido através da manipulação de dados provenientes do documento JSON do dispositivo e das constantes utilizando operadores e funções incorporados.

*As condições* são expressões que avaliam a um Boolean. Qualquer constante diferente da **realidade** booleana é considerada **falsa.** Esta regra inclui **nulo,** **indefinido,** qualquer objeto ou instância de matriz, qualquer cadeia e o **falso**Boolean .

A sintaxe para expressões é:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Para entender o que significa cada símbolo na sintaxe de expressões, consulte a seguinte tabela:

| Símbolo | Definição |
| --- | --- |
| attribute_name | Qualquer propriedade do documento JSON na coleção **FROM.** |
| binary_operator | Qualquer operador binário listado na secção [operators.](#operators) |
| function_name| Qualquer função listada na secção [Funções.](#functions) |
| decimal_literal |Uma boia expressa em notação decimal. |
| hexadecimal_literal |Um número expresso pela cadeia '0x' seguido por uma série de dígitos hexadémicos. |
| string_literal |As cordas literais são cordas Unicode representadas por uma sequência de caracteres unicode zero ou mais ou sequências de fuga. As literais de cordas são fechadas em cotações individuais ou em ações duplas. Fugas permitidas: `\'` , , para caracteres `\"` `\\` `\uXXXX` Unicode definidos por 4 dígitos hexadémicos. |

### <a name="operators"></a>Operadores

São apoiados os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Aritmético |+, -, *, /, % |
| Lógico |E, OU, NÃO |
| Comparação |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funções

Ao consultar gémeos e empregos, a única função suportada é:

| Função | Descrição |
| -------- | ----------- |
| IS_DEFINED (propriedade) | Devolve um Boolean indicando se a propriedade foi atribuída um valor `null` (incluindo). |

Nas condições de percurso, suportam-se as seguintes funções matemáticas:

| Função | Descrição |
| -------- | ----------- |
| ABS(x) | Devolve o valor absoluto (positivo) da expressão numérica especificada. |
| EXP(x) | Devolve o valor exponencial da expressão numérica especificada (e^x). |
| PODER(x,y) | Devolve o valor da expressão especificada à potência especificada (x^y).|
| SQUARE(x)    | Devolve o quadrado do valor numérico especificado. |
| TETO(x) | Devolve o menor valor inteiro maior do que, ou igual a, a expressão numérica especificada. |
| PISO(x) | Devolve o maior número inteiro menos ou igual à expressão numérica especificada. |
| SIGN(x) | Devolve o sinal positivo (+1), zero (0) ou negativo (-1) da expressão numérica especificada.|
| SQRT(x) | Devolve a raiz quadrada do valor numérico especificado. |

Nas condições de rotas, suportam-se as seguintes funções de verificação e fundição:

| Função | Descrição |
| -------- | ----------- |
| AS_NUMBER | Converte a cadeia de entrada num número. `noop` se a entrada for um número; `Undefined` se a corda não representar um número.|
| IS_ARRAY | Devolve um valor Boolean que indica se o tipo de expressão especificada é uma matriz. |
| IS_BOOL | Devolve um valor Boolean que indica se o tipo de expressão especificada é um Boolean. |
| IS_DEFINED | Devolve um Boolean indicando se a propriedade foi atribuída um valor. Isto só é suportado quando o valor é um tipo primitivo. Os tipos primitivos incluem cordas, booleanos, numéricos, ou `null` . DataTime, os tipos de objetos e as matrizes não são suportados. |
| IS_NULL | Devolve um valor Boolean que indica se o tipo de expressão especificada é nulo. |
| IS_NUMBER | Devolve um valor Boolean que indica se o tipo de expressão especificada é um número. |
| IS_OBJECT | Devolve um valor Boolean que indica se o tipo de expressão especificada é um objeto JSON. |
| IS_PRIMITIVE | Devolve um valor Boolean que indica se o tipo de expressão especificada é primitivo (corda, booleana, numérico ou `null` . |
| IS_STRING | Devolve um valor Boolean que indica se o tipo de expressão especificada é uma corda. |

Nas condições de rotas, suportam-se as seguintes funções de corda:

| Função | Descrição |
| -------- | ----------- |
| CONCAT(x, y, ...) | Devolve uma corda que é o resultado da concatenação de dois ou mais valores de corda. |
| COMPRIMENTO(x) | Devolve o número de caracteres da expressão de corda especificada.|
| LOWER(x) | Devolve uma expressão de corda após converter dados de caracteres maiúsculas para minúsculas. |
| UPPER(x) | Devolve uma expressão de corda após converter dados de caracteres minúsculos para maiúsculas. |
| SUBSTRING (corda, início [, comprimento]) | Devolve parte de uma expressão de corda a partir da posição de base de caracteres especificado e continua até ao comprimento especificado, ou até à extremidade da cadeia. |
| INDEX_OF (corda, fragmento) | Retorna a posição inicial da primeira ocorrência da segunda expressão de corda dentro da primeira expressão de corda especificada, ou -1 se a corda não for encontrada.|
| STARTS_WITH(x, y) | Devolve um Boolean indicando se a primeira expressão de corda começa com a segunda. |
| ENDS_WITH(x, y) | Devolve um Boolean indicando se a primeira expressão de corda termina com a segunda. |
| CONTÉM (x,y) | Devolve um Boolean indicando se a primeira expressão de corda contém a segunda. |

## <a name="next-steps"></a>Passos seguintes

Saiba como executar consultas nas suas apps utilizando [SDKs Azure IoT](iot-hub-devguide-sdks.md).