---
author: baanders
description: incluir arquivo para operações de consulta Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 7/28/2020
ms.author: baanders
ms.openlocfilehash: 82639a19ef728c22a74381d24754992e0f647976
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87562480"
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
  - Existem limitações no número de `JOIN` s permitidos (um nível para visualização pública).
* Selecione apenas os resultados de consulta superior `Select TOP` (operador)
* Utilize funções de escala: `IS_BOOL` , , , , , , , , , `IS_DEFINED` , , `IS_NULL` . `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTSWITH` `ENDSWITH` .
* Utilize operadores de comparação de consultas: `IN` / `NIN` , , , `=` , , , , `!=` `<` `>` `<=` `>=` .
* Utilize qualquer combinação `AND` (, `OR` `NOT` operador) de `IS_OF_MODEL` funções escalar e operadores de comparação.
* Continuação da utilização: O objeto de consulta é instantâneo com um tamanho de página (até 100). Você pode recuperar os gémeos digitais uma página de cada vez, fornecendo o token de continuação em chamadas subsequentes para a API.