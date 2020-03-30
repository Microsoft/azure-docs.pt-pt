---
title: Compreenda a linguagem de consulta do Azure IoT Hub [ Microsoft Docs
description: Guia de desenvolvedores - descrição da linguagem de consulta IoT Hub semelhante ao SQL usada para obter informações sobre gémeos dispositivo/módulo e trabalhos do seu hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.openlocfilehash: ad8b4b39e582d10c2a3b6003bfa07138f4697b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499183"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Linguagem de consulta do Hub IoT do dispositivo e módulos duplos, tarefas e encaminhamento de mensagens

O IoT Hub fornece uma poderosa linguagem semelhante ao SQL para recuperar informações sobre [gémeos dispositivos,](iot-hub-devguide-device-twins.md) [gémeos módulos,](iot-hub-devguide-module-twins.md) [empregos](iot-hub-devguide-jobs.md)e [encaminhamento de mensagens.](iot-hub-devguide-messages-d2c.md) Este artigo apresenta:

* Uma introdução às principais características da linguagem de consulta IoT Hub, e
* A descrição detalhada da língua. Para mais detalhes sobre a linguagem de consulta para o encaminhamento de mensagens, consulte [consultas no encaminhamento de mensagens](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Consultas gémeas de dispositivo e módulo

[Gémeos dispositivos](iot-hub-devguide-device-twins.md) e gémeos de [módulos](iot-hub-devguide-module-twins.md) podem conter objetos JSON arbitrários como etiquetas e propriedades. O IoT Hub permite-lhe consultar gémeos e módulos gémeos como um único documento JSON contendo todas as informações gémeas.

Assuma, por exemplo, que os gémeos do seu dispositivo hub IoT têm a seguinte estrutura (o módulo twin seria semelhante apenas com um módulo adicionalId):

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

### <a name="device-twin-queries"></a>Consultas gémeas do dispositivo

O IoT Hub expõe os gémeos do dispositivo como uma coleção de documentos chamada **dispositivos.** Por exemplo, a seguinte consulta recupera todo o conjunto de gémeos dispositivo:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Os SDKs Azure IoT](iot-hub-devguide-sdks.md) suportam a paging de grandes resultados.

O IoT Hub permite-lhe recuperar gémeos dispositivos filtrando com condições arbitrárias. Por exemplo, para receber gémeos dispositivos onde a etiqueta **local.região** é definida para **os EUA** use a seguinte consulta:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Os operadores booleanos e as comparações aritméticas também são apoiadas. Por exemplo, para recuperar gémeos dispositivos localizados nos EUA e configurados para enviar telemetria menos de cada minuto, use a seguinte consulta:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Como conveniência, também é possível utilizar constantes de matriz com os operadores **IN** e **NIN** (não in). Por exemplo, para recuperar gémeos dispositivos que reportam WiFi ou conectividade com fios usam a seguinte consulta:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

É muitas vezes necessário identificar todos os gémeos dispositivoque contêm uma propriedade específica. O IoT Hub `is_defined()` suporta a função para este fim. Por exemplo, para recuperar gémeos dispositivo que definem a `connectivity` propriedade use a seguinte consulta:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Consulte a secção [da cláusula WHERE](iot-hub-devguide-query-language.md#where-clause) para obter a referência completa das capacidades de filtragem.

O agrupamento e as agregações também são apoiados. Por exemplo, para encontrar a contagem de dispositivos em cada estado de configuração da telemetria, utilize a seguinte consulta:

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

Neste exemplo, três dispositivos relataram uma configuração bem sucedida, dois ainda estão a aplicar a configuração, e um relatou um erro.

As consultas de projeção permitem que os desenvolvedores devolvam apenas as propriedades com que se preocupam. Por exemplo, para recuperar o último tempo de atividade de todos os dispositivos desligados, utilize a seguinte consulta:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Consultas gémeas do módulo

A consulta em gémeos módulos é semelhante à consulta em gémeos dispositivo, mas usando um espaço de recolha/nome diferente; em vez de **dispositivos,** consulta-se a partir de **dispositivos.módulos:**

```sql
SELECT * FROM devices.modules
```

Não permitimos a adesão entre os dispositivos e dispositivos.as coleções de módulos. Se quiser consultar gémeos de módulos através de dispositivos, faça-o com base em etiquetas. Esta consulta devolverá todos os gémeos do módulo em todos os dispositivos com o estado de digitalização:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Esta consulta devolverá todos os gémeos do módulo com o estado de digitalização, mas apenas no subconjunto especificado de dispositivos:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# exemplo

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

O objeto **de consulta** é instantâneo com um tamanho de página (até 100). Em seguida, várias páginas são recuperadas ligando para os métodos **GetNextAsTwinAsync** várias vezes.

O objeto de consulta expõe vários valores **Seguintes,** dependendo da opção de desserialização exigida pela consulta. Por exemplo, dispositivo twin ou job objects, ou JSON simples ao utilizar projeções.

### <a name="nodejs-example"></a>Exemplo de Nó.js

A funcionalidade de consulta é exposta pelo [serviço Azure IoT SDK para Node.js](iot-hub-devguide-sdks.md) no objeto **de registo.**

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

O objeto **de consulta** é instantâneo com um tamanho de página (até 100). Em seguida, várias páginas são recuperadas chamando o **método asTwin seguinte** várias vezes.

O objeto de consulta expõe vários valores **Seguintes,** dependendo da opção de desserialização exigida pela consulta. Por exemplo, dispositivo twin ou job objects, ou JSON simples ao utilizar projeções.

### <a name="limitations"></a>Limitações

> [!IMPORTANT]
> Os resultados da consulta podem ter alguns minutos de atraso em relação aos valores mais recentes em gémeos dispositivos. Se consultar gémeos de dispositivo individual por ID, utilize o [get twin REST API](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin). Esta API devolve sempre os valores mais recentes e tem limites de estrangulamento mais elevados. Pode emitir a API REST diretamente ou utilizar a funcionalidade equivalente num dos [SDKs de Serviço Hub Azure IoT](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

Atualmente, as comparações são suportadas apenas `... WHERE properties.desired.config = properties.reported.config` entre tipos primitivos (sem objetos), por exemplo, só é suportado se essas propriedades tiverem valores primitivos.

## <a name="get-started-with-jobs-queries"></a>Começar com consultas de emprego

[Os postos](iot-hub-devguide-jobs.md) de trabalho fornecem uma forma de executar operações em conjuntos de dispositivos. Cada gémeo dispositivo contém a informação dos trabalhos de que faz parte de uma coleção chamada **jobs**.

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

Atualmente, esta coleção é consultada como **devices.jobs** na linguagem de consulta IoT Hub.

> [!IMPORTANT]
> Atualmente, a propriedade de empregos nunca é devolvida quando se consultam gémeos dispositivos. Ou seja, consultas que contêm "dispositivos from". A propriedade de empregos só pode ser `FROM devices.jobs`acedida diretamente com consultas usando .
>
>

Por exemplo, para obter todos os empregos (passadoe programado) que afetam um único dispositivo, pode utilizar a seguinte consulta:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Note como esta consulta fornece o estado específico do dispositivo (e possivelmente a resposta do método direto) de cada trabalho devolvido.

Também é possível filtrar com condições booleanar arbitrárias em todas as propriedades do objeto na coleção **devices.jobs.**

Por exemplo, para recuperar todos os trabalhos de atualização de twin update do dispositivo concluídos que foram criados após setembro de 2016 para um dispositivo específico, use a seguinte consulta:

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

* As projeções, `SELECT *` portanto, só são possíveis.
* Condições que se referem ao dispositivo twin para além das propriedades de trabalho (ver a secção anterior).
* Realizando agregações, tais como contagem, avg, grupo por.

## <a name="basics-of-an-iot-hub-query"></a>Fundamentos de uma consulta do IoT Hub

Todas as consultas ioT Hub consistem em cláusulas SELECT e FROM, com cláusulas opcionais de ONDE e GROUP BY. Todas as consultas são executadas numa coleção de documentos JSON, por exemplo, gémeos dispositivos. A cláusula FROM indica que a recolha de documentos será iterada em **(dispositivos,** **dispositivos.módulos,** ou **devices.jobs).** Em seguida, o filtro na cláusula WHERE é aplicado. Com agregações, os resultados deste passo são agrupados conforme especificado na cláusula GROUP BY. Para cada grupo, gera-se uma linha conforme especificado na cláusula SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Cláusula FROM

A **cláusula de <from_specification>** pode assumir apenas três **valores: Desde dispositivos** a gémeos de dispositivos de consulta, **dispositivos.módulos** a gémeos de módulos de consulta, ou de **devices.jobs** a consultar detalhes sobre o trabalho por dispositivo.

## <a name="where-clause"></a>Cláusula WHERE

A cláusula **de>de filter_condition DE WHERE <é** opcional. Especifica uma ou mais condições que os documentos JSON na coleção FROM devem satisfazer para serem incluídos como parte do resultado. Qualquer documento JSON deve avaliar as condições especificadas para "verdadeiro" a incluir no resultado.

As condições permitidas são descritas na secção [Expressões e condições](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Cláusula SELECT

O **SELECT <select_list>** é obrigatório e especifica quais os valores recuperados da consulta. Especifica os valores JSON a utilizar para gerar novos objetos JSON.
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

**Attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM. Alguns exemplos de cláusulas SELECT podem ser encontrados no Início com a secção de consultas gémeas do dispositivo.

Atualmente, as cláusulas de seleção diferentes do **SELECT*** são suportadas apenas em consultas agregadas em gémeos dispositivos.

## <a name="group-by-clause"></a>Cláusula GROUP BY

O **grupo por <group_specification cláusula>** é um passo opcional que executa após o filtro especificado na cláusula WHERE, e antes da projeção especificada no SELECT. Agrupa documentos com base no valor de um atributo. Estes grupos são utilizados para gerar valores agregados, conforme especificado na cláusula SELECT.

Um exemplo de uma consulta usando GROUP BY é:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A sintaxe formal para group BY é:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM.

Atualmente, a cláusula GROUP BY só é suportada na consulta de gémeos dispositivos.

> [!IMPORTANT]
> O `group` termo é atualmente tratado como uma palavra-chave especial em consultas. No caso de `group` usar como nome de propriedade, considere rodeá-lo com `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`parênteses duplos para evitar erros, por exemplo, .
>

## <a name="expressions-and-conditions"></a>Expressões e condições

A um nível elevado, uma *expressão:*

* Avalia uma instância do tipo JSON (como Boolean, número, corda, matriz ou objeto).
* É definido pela manipulação de dados provenientes do documento JSON do dispositivo e constantes utilizando operadores e funções incorporados.

*As condições* são expressões que avaliam um Boolean. Qualquer constante diferente do **booleano verdadeiro** é considerado **falso.** Esta regra inclui **nulo,** **indefinido,** qualquer objeto ou instância de matriz, qualquer corda, e o **falso**Boolean .

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

Para entender o que cada símbolo nas expressões sintaxe significa, consulte a seguinte tabela:

| Símbolo | Definição |
| --- | --- |
| attribute_name | Qualquer propriedade do documento JSON na coleção **FROM.** |
| binary_operator | Qualquer operador binário listado na secção [Operadores.](#operators) |
| function_name| Qualquer função listada na secção [Funções.](#functions) |
| decimal_literal |Um carro alegórico expresso em notação decimal. |
| hexadecimal_literal |Um número expresso pela corda '0x' seguido de uma série de dígitos hexadecimais. |
| string_literal |Os literais de cordas são cordas Unicode representadas por uma sequência de caracteres Zero ou mais Unicode ou sequências de fuga. Os literais de cordas são incluídos em citações únicas ou citações duplas. Fugas `\'`permitidas: `\"` `\\`, `\uXXXX` para caracteres Unicode definidos por 4 dígitos hexadecimais. |

### <a name="operators"></a>Operadores

São apoiados os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Aritmética |+, -, *, /, % |
| Lógico |E, OU, NÃO |
| Comparação |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funções

Ao consultar gémeos e empregos, a única função apoiada é:

| Função | Descrição |
| -------- | ----------- |
| IS_DEFINED (imóvel) | Devolve uma Boolean indicando se a propriedade `null`foi atribuída um valor (incluindo). |

Nas condições das rotas, são suportadas as seguintes funções matemáticas:

| Função | Descrição |
| -------- | ----------- |
| ABS(x) | Devolve o valor absoluto (positivo) da expressão numérica especificada. |
| EXP(x) | Devolve o valor exponencial da expressão numérica especificada (e^x). |
| POWER (x,y) | Devolve o valor da expressão especificada à potência especificada (x^y).|
| QUADRADO (x)    | Devolve o quadrado do valor numérico especificado. |
| TETO(x) | Devolve o menor valor inteiro maior ou igual à expressão numérica especificada. |
| PISO (x) | Devolve o maior inteiro inferior ou igual à expressão numérica especificada. |
| SINAL(x) | Devolve o sinal positivo (+1), zero (0) ou negativo (-1) da expressão numérica especificada.|
| SQRT(x) | Devolve a raiz quadrada do valor numérico especificado. |

Nas condições das rotas, são suportadas as seguintes funções de verificação e fundição de tipo:

| Função | Descrição |
| -------- | ----------- |
| AS_NUMBER | Converte a cadeia de entrada para um número. `noop`se a entrada for um número; `Undefined` se a corda não representar um número.|
| IS_ARRAY | Devolve um valor booleano indicando se o tipo da expressão especificada é uma matriz. |
| IS_BOOL | Devolve um valor booleano indicando se o tipo da expressão especificada é um Boolean. |
| IS_DEFINED | Devolve uma Boolean indicando se a propriedade foi atribuída um valor. |
| IS_NULL | Devolve um valor booleano indicando se o tipo da expressão especificada é nulo. |
| IS_NUMBER | Devolve um valor booleano indicando se o tipo da expressão especificada é um número. |
| IS_OBJECT | Devolve um valor booleano indicando se o tipo da expressão especificada é um objeto JSON. |
| IS_PRIMITIVE | Devolve um valor booleano indicando se o tipo da expressão especificada é `null`primitivo (corda, booleano, numérico ou ). |
| IS_STRING | Devolve um valor booleano indicando se o tipo da expressão especificada é uma corda. |

Nas condições das rotas, são suportadas as seguintes funções de cadeia:

| Função | Descrição |
| -------- | ----------- |
| CONCAT (x, y, ...) | Devolve uma corda que é o resultado da concatenação de dois ou mais valores de cadeia. |
| COMPRIMENTO (x) | Devolve o número de caracteres da expressão de corda especificada.|
| INFERIOR(x) | Devolve uma expressão de cadeia após converter dados de caracteres maiúsculos em minúsculas. |
| UPPER (x) | Devolve uma expressão de cadeia após converter dados de caracteres minúsculos em maiúsculas. |
| SUBSTRING (corda, início [,comprimento]) | Devolve parte de uma expressão de corda a partir da posição de base zero do caracteres especificado e continua até ao comprimento especificado, ou até ao fim da corda. |
| INDEX_OF (corda, fragmento) | Devolve a posição inicial da primeira ocorrência da segunda expressão de corda dentro da primeira expressão especificada da corda, ou -1 se a corda não for encontrada.|
| STARTS_WITH(x, y) | Devolve uma Boolean indicando se a primeira expressão de corda começa com a segunda. |
| ENDS_WITH(x, y) | Devolve uma Boolean indicando se a primeira expressão de corda termina com a segunda. |
| CONTÉM(x,y) | Devolve uma Boolean indicando se a primeira expressão de corda contém a segunda. |

## <a name="next-steps"></a>Passos seguintes

Saiba como executar consultas nas suas apps utilizando [SDKs Azure IoT](iot-hub-devguide-sdks.md).