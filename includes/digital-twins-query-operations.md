---
author: baanders
description: incluir arquivo para operações de consulta Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 333a7ec4ae0e5c8cbc94a603e2ccf81ee92e7d48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078464"
---
## <a name="query-language-features"></a>Características da linguagem de consulta

A Azure Digital Twins fornece extensas capacidades de consulta contra o gráfico gémeo. As consultas são descritas usando sintaxe semelhante ao SQL, numa linguagem de consulta semelhante à [linguagem de consulta IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md) com muitas características comparáveis.

> [!NOTE]
> Todas as operações de consulta da Azure Digital Twins são sensíveis a casos.

Aqui estão as operações disponíveis em linguagem de consulta Azure Digital Twins.

Obter gémeos digitais pelos seus...
* modelo (utilizando `IS_OF_MODEL` o operador)
* propriedades (incluindo [propriedades de tags)](../articles/digital-twins/how-to-use-tags.md)
* interfaces
* relationships
  - propriedades das relações

Pode ainda melhorar as suas consultas com as seguintes operações:
* Obter gémeos em vários tipos de relacionamento `JOIN` (consultas). 
  - Durante a pré-visualização, são permitidos até cinco níveis de `JOIN` níveis.
* Selecione apenas os resultados de consulta superior `Select TOP` (operador)
* Conte o número de itens num conjunto de resultados usando `Select COUNT`
* Use projeções para escolher quais colunas uma consulta vai devolver
* Utilize funções de escala: `IS_BOOL` , , , , , , , , , `IS_DEFINED` , , `IS_NULL` . `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` `ENDSWITH` .
* Utilize operadores de comparação de consultas: `IN` / `NIN` , , , `=` , , , , `!=` `<` `>` `<=` `>=` .
* Utilize qualquer combinação `AND` (, `OR` `NOT` operador) de `IS_OF_MODEL` funções escalar e operadores de comparação.
* Continuação da utilização: O objeto de consulta é instantâneo com um tamanho de página (até 100). Você pode recuperar os gémeos digitais uma página de cada vez, fornecendo o token de continuação em chamadas subsequentes para a API.