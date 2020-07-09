---
title: Parâmetros de cadeia de consulta V3 API
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 47727f6df772669fa323a10cd099764a6d35cb6a
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610795"
---
Os parâmetros de cadeia de consulta V3 API incluem:

|Parâmetro de consulta|Nome do portal LUIS|Tipo|Versão|Predefinição|Objetivo|
|--|--|--|--|--|--|
|`log`|Guardar registos|boolean|V2 & V3|false|Faça a consulta no ficheiro de registo. O valor predefinido é falso.|
|`query`|-|string|Apenas V3|Sem predefinição - é necessário no pedido GET|**Em V2,** a expressão a prever está no `q` parâmetro. <br><br>**Em V3,** a funcionalidade é passada no `query` parâmetro.|
|`show-all-intents`|Incluir pontuações para todos os efeitos|boolean|Apenas V3|false|Devolva todas as intenções com a pontuação correspondente no objeto **de previsão.intenções.** As intenções são devolvidas como objetos num `intents` objeto principal. Isto permite o acesso programático sem necessidade de encontrar a intenção numa matriz: `prediction.intents.give` . Em V2, estes foram devolvidos numa matriz. |
|`verbose`|Incluir mais detalhes de entidades|boolean|V2 & V3|false|**Em V2,** quando definido como verdadeiro, todas as intenções previstas foram devolvidas. Se precisar de todas as intenções previstas, use o param V3 de `show-all-intents` .<br><br>**Na V3,** este parâmetro apenas fornece detalhes de metadados de entidades da previsão da entidade.  |
|`timezoneOffset`|-|string|V2|-|Timezone aplicado às entidades datav2.|
|`datetimeReference`|-|string|V3|-|[Timezone](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) aplicado às entidades datav2. Substitui `timezoneOffset` a V2.|