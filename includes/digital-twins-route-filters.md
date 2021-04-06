---
author: baanders
description: incluir ficheiro para opções de filtro de rota Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98859576"
---
| Nome do filtro | Description | Esquema de texto do filtro | Valores suportados | 
| --- | --- | --- | --- |
| Verdadeiro / Falso | Permite criar uma rota sem filtragem, ou desativar uma rota para que nenhum evento sejam enviados | `<true/false>` | `true` = rota está ativada sem filtragem <br> `false` = rota é desativada |
| Tipo | O [tipo de evento](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) que flui através da sua instância digital gémea | `type = '<eventType>'` | Aqui estão os possíveis valores do tipo de evento: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Origem | Nome da instância Azure Digital Twins | `source = '<hostname>'`| Aqui estão os possíveis valores do nome de anfitrião: <br> **Para notificações:**`<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Para telemetria:**`<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Assunto | Uma descrição do evento no contexto da fonte do evento acima | `subject = '<subject>'` | Aqui estão os valores possíveis: <br>**Para notificações**: O assunto é `<twinid>` <br> ou um formato URI para sujeitos, que são identificados exclusivamente por múltiplas partes ou IDs:<br>`<twinid>/relationships/<relationshipid>`<br> **Para a telemetria**: O sujeito é o caminho do componente (se a telemetria for emitida a partir de um componente gémeo), tais como `comp1.comp2` . Se a telemetria não for emitida a partir de um componente, então o seu campo de objetos está vazio. |
| Esquema de dados | ID modelo DTDL | `dataschema = '<model-dtmi-ID>'` | **Para a telemetria**: O esquema de dados é o ID do modelo do gémeo ou o componente que emite a telemetria. Por exemplo, `dtmi:example:com:floor4;2` <br>**Para notificações (criar/excluir)**: O esquema de dados pode ser acedido no organismo de notificação em `$body.$metadata.$model` . <br>**Para notificações (atualização)**: O esquema de dados pode ser acedido no organismo de notificação `$body.modelId`|
| Tipo do conteúdo | Tipo de conteúdo do valor dos dados | `datacontenttype = '<contentType>'` | O tipo de conteúdo é `application/json` |
| Versão spec | A versão do esquema de evento que está a usar | `specversion = '<version>'` | A versão deve `1.0` ser. Isto indica que o esquema do CloudEvents versão 1.0 |
| Organismo de notificação | Referenciar qualquer propriedade no `data` campo de uma notificação | `$body.<property>` | Ver [*Como fazer: Compreender os dados do evento*](../articles/digital-twins/how-to-interpret-event-data.md) por exemplo de notificações. Qualquer propriedade no `data` campo pode ser referenciada usando `$body`

Os seguintes tipos de dados são suportados como valores devolvidos por referências aos dados acima:

| Tipo de dados | Exemplo |
|-|-|-|
|**String**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Número inteiro**|`$body.errorCode > 200`|
|**Duplo**|`$body.temperature <= 5.5`|
|**Rio Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Os seguintes operadores são suportados na definição de filtros de rota:

|Família|Operadores|Exemplo|
|-|-|-|
|Lógico|E, OU, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Comparação|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

As seguintes funções são suportadas ao definir filtros de rota:

|Função|Descrição|Exemplo|
|--|--|--|
|STARTS_WITH(x,y)|Retorna verdadeira se o valor `x` começar com a cadeia `y` .|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | Retorna verdadeira se o valor `x` termina com a corda `y` .|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTÉM (x,y)| Retorna verdadeira se o valor `x` contiver a `y` cadeia.|`CONTAINS(subject, '<twinID>')`|

Quando implementar ou atualizar um filtro, a alteração pode demorar alguns minutos a ser refletida no pipeline de dados.