---
title: Compreender a linguagem de consulta do IoT Hub do Azure | Documentos da Microsoft
description: Guia do desenvolvedor – descrição do IoT Hub semelhante a SQL utilizada para obter informações sobre o dispositivo/módulo duplos e tarefas do seu hub IoT de linguagem de consulta.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: rezas
ms.openlocfilehash: e5387f1e44a55b0a30f8620b49d237ac1e1ec2b6
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730590"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>Linguagem de consulta do IoT Hub para twins de dispositivo e o módulo, tarefas e encaminhamento de mensagens

O IoT Hub fornece uma poderosa linguagem de tipo SQL para obter informações sobre [dispositivos duplos](iot-hub-devguide-device-twins.md) e [trabalhos](iot-hub-devguide-jobs.md), e [roteamento de mensagens](iot-hub-devguide-messages-d2c.md). Este artigo apresenta:

* Uma introdução para os principais recursos da linguagem de consulta do IoT Hub, e
* A descrição detalhada da linguagem. Para obter detalhes sobre a linguagem de consulta para o encaminhamento de mensagens, consulte [consultas de encaminhamento de mensagens](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Consultas de gémeos de dispositivo e módulo

[Dispositivos duplos](iot-hub-devguide-device-twins.md) e duplos de módulo podem conter objetos JSON arbitrários como etiquetas e propriedades. IoT Hub permite-lhe para consulta dispositivos duplos e duplos de módulo como um único documento JSON que contém todas as informações de duplo.

Suponha, por exemplo, que o gémeos de dispositivo do hub IoT tem a seguinte estrutura (módulo duplo seria semelhante apenas com um moduleId adicional):

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

### <a name="device-twin-queries"></a>Consultas de gémeos de dispositivo

IoT Hub expõe os twins do dispositivo como uma coleção de documentos chamada **dispositivos**. Por exemplo, a consulta seguinte obtém o conjunto completo de dispositivos duplos:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Os SDKs IoT do Azure](iot-hub-devguide-sdks.md) suportar a paginação de resultados grandes.

IoT Hub permite-lhe obter dispositivos duplos com condições arbitrárias de filtragem. Por exemplo receber o dispositivo twins onde o **location.region** etiqueta está definida como **E.U.A.** utilize a seguinte consulta:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Operadores booleanos e comparações aritméticas são também suportadas. Por exemplo, para obter dispositivos duplos localizados nos Estados Unidos e configurado para enviar telemetria inferior a cada minuto, utilize a seguinte consulta:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Como uma conveniência, também é possível utilizar as constantes de matriz com o **IN** e **NIN** operadores (não em). Por exemplo, para obter os dispositivos duplos que reportam Wi-Fi ou de conectividade com fio utilizam a seguinte consulta:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Muitas vezes, é necessário identificar todos os dispositivos duplos, que contêm uma propriedade específica. IoT Hub suporta a função `is_defined()` para esta finalidade. Por exemplo, para obter dispositivos duplos, que definem o `connectivity` propriedade utilize a seguinte consulta:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Consulte a [cláusula WHERE](iot-hub-devguide-query-language.md#where-clause) secção para obter a referência completa das capacidades de filtragem.

Agrupamento e agregações também são suportadas. Por exemplo, para localizar a contagem de dispositivos em cada Estado de configuração de telemetria, utilize a seguinte consulta:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Esta consulta do agrupamento irá devolver um resultado semelhante ao seguinte exemplo:

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

Neste exemplo, três dispositivos comunicados configuração com êxito, dois ainda estiverem sendo aplicadas a configuração e um comunicou um erro.

Consultas de projeção permitem aos programadores devolver apenas as propriedades que ele está preocupado. Por exemplo obter a hora da última atividade de todos os desconectados dispositivos utilize a seguinte consulta:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Consultas do módulo duplo

Consulta de duplos de módulo é semelhante à consulta de dispositivos duplos, mas usando diferente coleção/espaço de nomes, ou seja, em vez de "de dispositivos", pode consultar device.modules:

```sql
SELECT * FROM devices.modules
```

Não é permitido junção entre os dispositivos e coleções de devices.modules. Se quiser duplos de módulo de consulta em todos os dispositivos, fazê-lo com base em etiquetas. Esta consulta devolverá todos os duplos de módulo em todos os dispositivos com o estado de verificação:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Esta consulta devolverá todos os duplos de módulo com o estado de verificação, mas apenas em determinado subconjunto de dispositivos:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>Exemplo do c#

A funcionalidade de consulta está exposta pelos [c# SDK do serviço](iot-hub-devguide-sdks.md) no **RegistryManager** classe.

Eis um exemplo de uma consulta simples:

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

O **consulta** objeto é instanciado com um tamanho de página (até 100). Em seguida, várias páginas são obtidas ao chamar o **GetNextAsTwinAsync** métodos várias vezes.

O objeto de consulta expõe várias **seguinte** valores, consoante a opção de desserialização necessárias para a consulta. Por exemplo, objetos de dispositivo duplo ou tarefa sem formatação JSON ou quando o uso de projeções.

### <a name="nodejs-example"></a>Exemplo de node. js

A funcionalidade de consulta está exposta pela [serviço do Azure IoT SDK para node. js](iot-hub-devguide-sdks.md) no **Registro** objeto.

Eis um exemplo de uma consulta simples:

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

O **consulta** objeto é instanciado com um tamanho de página (até 100). Em seguida, várias páginas são obtidas ao chamar o **nextAsTwin** método várias vezes.

O objeto de consulta expõe várias **seguinte** valores, consoante a opção de desserialização necessárias para a consulta. Por exemplo, objetos de dispositivo duplo ou tarefa sem formatação JSON ou quando o uso de projeções.

### <a name="limitations"></a>Limitações

> [!IMPORTANT]
> Os resultados da consulta podem ter alguns minutos de atraso em relação aos valores mais recentes em dispositivos duplos. Se as consultas a gémeos de dispositivos individuais por ID, utilize a API de gémeos de dispositivo de obter. Esta API sempre contém os valores mais recentes e tem limites de conjunto superior.

Comparações são atualmente suportadas apenas entre tipos primitivos (não existem objetos), por exemplo `... WHERE properties.desired.config = properties.reported.config` só é suportada se essas propriedades têm valores primitivos.

## <a name="get-started-with-jobs-queries"></a>Introdução às consultas de tarefas

[Tarefas](iot-hub-devguide-jobs.md) proporcionam uma forma de realizar operações em conjuntos de dispositivos. Cada dispositivo duplo contém as informações de tarefas do qual é a parte de uma coleção denominada **tarefas**.

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

Atualmente, esta coleção é consultável como **devices.jobs** no IoT Hub a linguagem de consulta.

> [!IMPORTANT]
> Atualmente, a propriedade de tarefas nunca é devolvida ao consultar dispositivos duplos. Ou seja, as consultas que contêm "a partir de dispositivos". A propriedade de tarefas só pode ser acessada diretamente com consultas com `FROM devices.jobs`.
>
>

Por exemplo, para obter todas as tarefas (últimos e agendadas) que afetam um único dispositivo, pode usar a seguinte consulta:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Observe como esta consulta fornece o estado de específicos de dispositivos (e, possivelmente, a resposta do método direto) de cada tarefa retornada.

Também é possível filtrar com condições booleanas arbitrárias em todas as propriedades de objeto na **devices.jobs** coleção.

Por exemplo, para obter todos os dispositivos concluído de tarefas de atualização do duplo que foram criadas depois de Setembro de 2016 para um dispositivo específico, use a seguinte consulta:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Também pode obter os resultados por dispositivo de uma única tarefa.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Limitações

Atualmente, a consulta no **devices.jobs** não suportam:

* Projeções, por conseguinte, apenas `SELECT *` é possível.
* Condições que se referem para o dispositivo duplo, além de propriedades da tarefa (consulte a secção anterior).
* Efetuar agregações, como contagem, média, agrupar por.

## <a name="basics-of-an-iot-hub-query"></a>Noções básicas de uma consulta do IoT Hub

Todas as consultas do IoT Hub é composta por de SELECT e cláusulas, com WHERE opcional e as cláusulas GROUP BY. Cada consulta é executada numa coleção de documentos JSON, por exemplo, dispositivos duplos. A cláusula FROM indica a coleção de documentos a ser iterado em (**dispositivos** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. Com as agregações, os resultados deste passo são agrupados conforme especificado na cláusula GROUP BY. Para cada grupo, é gerada uma linha conforme especificado na cláusula SELECT.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Cláusula FROM

O **partir < from_specification >** cláusula pode assumir apenas dois valores: **A partir de dispositivos** para dispositivos duplos da consulta, ou **partir devices.jobs** para detalhes da consulta tarefa por dispositivo.


## <a name="where-clause"></a>Cláusula WHERE
O **em que < filter_condition >** cláusula é opcional. Especifica uma ou mais condições que o JSON documentos na coleção FROM tem de cumprir para ser incluído como parte do resultado. Qualquer documento JSON tem de avaliar as condições especificadas como "true" a serem incluídos no resultado.

As condições permitidas são descritas na seção [expressões e condições](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="select-clause"></a>Cláusula SELECT

O **SELECT < select_list >** é obrigatória e especifica quais os valores são obtidos a partir da consulta. Especifica os valores JSON para ser usado para gerar novos objetos JSON.
Para cada elemento do subconjunto filtrado (e, opcionalmente, agrupado) da coleção FROM, a fase de projeção gera um novo objeto JSON. Este objeto é construído com os valores especificados na cláusula SELECT.

Segue-se a gramática de cláusula SELECT:

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

**Attribute_name** refere-se a qualquer propriedade do documento JSON da coleção de FROM. Alguns exemplos das cláusulas SELECT podem ser encontrados na introdução com a secção de consultas de gémeos de dispositivo.

Atualmente, seleção cláusulas diferentes **SELECIONE*** só são suportadas nas consultas de agregação em dispositivos duplos.

## <a name="group-by-clause"></a>Cláusula GROUP BY
O **GROUP BY < group_specification >** cláusula é uma etapa opcional que é executado depois do filtro especificado na cláusula WHERE e antes da projecção especificada em SELECT. Grupos de documentos com base no valor de um atributo. Estes grupos são utilizados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta usando GROUP BY é:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A sintaxe formal para GROUP BY é:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** refere-se a qualquer propriedade do documento JSON da coleção de FROM.

Atualmente, a cláusula GROUP BY só é suportada quando as consultas a gémeos de dispositivo.

> [!IMPORTANT]
> O termo `group` atualmente é tratado como uma palavra-chave especial nas consultas. No caso, utilize `group` como o nome de propriedade, considere envolvente, Retos duplo para evitar erros, por exemplo, `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`.
>
>

## <a name="expressions-and-conditions"></a>Expressões e condições
Num alto nível, uma *expressão*:

* Avalia a uma instância de um tipo JSON (por exemplo, booleano, número, cadeia de caracteres, matriz ou objeto).
* É definido por dados provenientes do documento JSON de dispositivo e constantes com operadores internos e as funções de manipulação.

*Condições* são expressões avaliadas como um booleano. Qualquer constante diferente booleano **true** é considerado **falso**. Esta regra inclui **nulo**, **indefinido**, qualquer instância do objeto ou matriz, qualquer cadeia de caracteres e o valor de booleano **false**.

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

Para compreender o que quer dizer cada símbolo a sintaxe de expressões, consulte a tabela seguinte:

| Símbolo | Definição |
| --- | --- |
| attribute_name | Qualquer propriedade do documento JSON no **FROM** coleção. |
| binary_operator | Qualquer operador binário listado na [operadores](#operators) secção. |
| function_name| Qualquer função listada na [funções](#functions) secção. |
| decimal_literal |Um número de vírgula flutuante expressado na notação decimal. |
| hexadecimal_literal |Um número expressos pela seqüência de caracteres "0x" seguido de uma cadeia de caracteres de dígitos hexadecimais. |
| string_literal |Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma seqüência de zero ou mais carateres Unicode ou seqüências de escape. Literais de cadeia de caracteres encontram-se entre aspas simples ou aspas duplas. Permitido escapa: `\'`, `\"`, `\\`, `\uXXXX` para caracteres Unicode definidos por 4 dígitos hexadecimais. |

### <a name="operators"></a>Operadores
São suportados os seguintes operadores:

| Família | Operadores |
| --- | --- |
| Operações aritméticas |+, -, *, /, % |
| Lógica |E, EM ALTERNATIVA, NÃO |
| Comparação |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Funções
Ao consultar twins e tarefas, que o único suportado é a função:

| Função | Descrição |
| -------- | ----------- |
| IS_DEFINED(Property) | Devolve um valor booleano que indica se a propriedade foi atribuída um valor (incluindo `null`). |

Em condições de rotas, são suportadas as seguintes funções de matemática:

| Função | Descrição |
| -------- | ----------- |
| ABS(x) | Devolve o valor absoluto (positivo) da expressão especificada numérico. |
| EXP(x) | Devolve o valor exponencial da expressão especificada numérico (e ^ x). |
| Power(x,y) | Devolve o valor da expressão especificada à potência especificada (x ^ y).|
| SQUARE(x) | Devolve o quadrado do valor numérico especificado. |
| CEILING(x) | Devolve o menor valor de número inteiro maior que ou igual a, a expressão numérica especificada. |
| FLOOR(x) | Devolve o maior número inteiro menor ou igual a expressão numérica especificada. |
| SIGN(x) | Devolve o positivo (+ 1), zero (0) ou sinal negativo de (-1) da expressão especificada numérico.|
| SQRT(x) | Devolve a raiz quadrada do valor numérico especificado. |

Em condições de rotas, são suportadas as funções de conversão e verificação de tipo seguintes:

| Função | Descrição |
| -------- | ----------- |
| AS_NUMBER | Converte a cadeia de entrada para um número. `noop` Se a entrada é um número; `Undefined` se a cadeia de caracteres não representa um número.|
| IS_ARRAY | Devolve um valor Booleano indicando se o tipo da expressão especificada é uma matriz. |
| IS_BOOL | Devolve um valor Booleano indicando se o tipo da expressão especificada é um booleano. |
| IS_DEFINED | Devolve um valor booleano que indica se a propriedade foi atribuída um valor. |
| IS_NULL | Devolve um valor Booleano indicando se o tipo da expressão especificada é nulo. |
| IS_NUMBER | Devolve um valor Booleano indicando se o tipo da expressão especificada é um número. |
| IS_OBJECT | Devolve um valor Booleano indicando se o tipo da expressão especificada é um objeto JSON. |
| IS_PRIMITIVE | Devolve um valor Booleano indicando se o tipo da expressão especificada é um primitivo (string, booleano, numéricos, ou `null`). |
| IS_STRING | Devolve um valor Booleano indicando se o tipo da expressão especificada é uma cadeia de caracteres. |

Em condições de rotas, são suportadas as seguintes funções de cadeia de caracteres:

| Função | Descrição |
| -------- | ----------- |
| CONCAT(x, y, …) | Devolve uma cadeia que é o resultado da concatenação de dois ou mais valores de cadeia de caracteres. |
| LENGTH(x) | Devolve o número de carateres da expressão de cadeia especificada.|
| LOWER(x) | Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em maiúsculas em minúsculas. |
| UPPER(x) | Devolve uma expressão de cadeia de caracteres após a conversão de dados de caráter em minúsculas em maiúsculas. |
| SUBCADEIA (cadeia de caracteres, início [, comprimento]) | Devolve a parte de uma expressão de cadeia de caracteres a partir da posição caractere especificado baseado em zero e continua ao comprimento especificado, ou ao fim da cadeia de caracteres. |
| INDEX_OF (string, fragmento) | Devolve a posição inicial da primeira ocorrência da segunda cadeia de expressão dentro da primeira expressão de cadeia especificada ou -1 se a cadeia de caracteres não foi encontrada.|
| STARTS_WITH(x, y) | Retorna um Booleano indicando se a primeira expressão de cadeia começa com a segunda. |
| ENDS_WITH(x, y) | Retorna um Booleano indicando se a primeira expressão de cadeia termina com a segunda. |
| CONTAINS(x,y) | Retorna um Booleano indicando se a primeira cadeia de expressão contém o segundo. |

## <a name="next-steps"></a>Passos Seguintes

Saiba como executar consultas nas suas aplicações através de [do Azure IoT SDKs](iot-hub-devguide-sdks.md).
