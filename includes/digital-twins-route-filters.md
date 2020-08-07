---
author: baanders
description: incluir ficheiro para opções de filtro de rota Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902211"
---
| Nome do filtro | Descrição | Esquema de texto do filtro | Valores suportados | 
| --- | --- | --- | --- |
| Verdadeiro / Falso | Permite criar uma rota sem filtragem, ou desativar uma rota para que nenhum evento sejam enviados | `<true/false>` | `true`= rota está ativada sem filtragem <br> `false`= rota é desativada |
| Tipo | O [tipo de evento](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) que flui através da sua instância digital gémea | `type = '<eventType>'` | Aqui estão os possíveis valores do tipo de evento: <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Fonte | Nome da instância Azure Digital Twins | `source = '<hostname>'`| Aqui estão os possíveis valores do nome de anfitrião: <br> **Para notificações:**`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Para telemetria:**`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Assunto | Uma descrição do evento no contexto da fonte do evento acima | `subject = '<subject>'` | Aqui estão os valores possíveis: <br>**Para notificações**: O assunto é`<twinid>` <br> ou um formato URI para sujeitos, que são identificados exclusivamente por múltiplas partes ou IDs:<br>`<twinid>/relationships/<relationshipid>`<br> **Para a telemetria**: O sujeito é o caminho do componente (se a telemetria for emitida a partir de um componente gémeo), tais como `comp1.comp2` . Se a telemetria não for emitida a partir de um componente, então o seu campo de objetos está vazio. |
| Esquema de dados | ID modelo DTDL | `dataschema = '<model-dtmi-ID>'` | **Para a telemetria**: O esquema de dados é o ID do modelo do gémeo ou o componente que emite a telemetria. Por exemplo, `dtmi:example:com:floor4;2` <br>**Para notificações**: O esquema de dados não é suportado|
| Tipo do conteúdo | Tipo de conteúdo do valor dos dados | `datacontenttype = '<contentType>'` | O tipo de conteúdo é`application/json` |
| Versão spec | A versão do esquema de evento que está a usar | `specversion = '<version>'` | A versão deve `1.0` ser. Isto indica que o esquema do CloudEvents versão 1.0 |

Também é possível combinar filtros utilizando as seguintes operações:

| Nome do filtro | Esquema de texto do filtro | Exemplo | 
| --- | --- | --- |
| E / OU | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| E / OU | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Operações aninhadas | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Durante a pré-visualização, apenas a igualdade de cordas é suportada (=, !=).

Quando implementar ou atualizar um filtro, a alteração pode demorar alguns minutos a ser refletida no pipeline de dados.
