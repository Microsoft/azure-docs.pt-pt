---
title: Linguagem da consulta
titleSuffix: Azure Digital Twins
description: Compreenda os fundamentos da linguagem Azure Digital Twins Query Store.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: e4952bc3df2b5b164038654f5d77f4c352827463
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/12/2020
ms.locfileid: "84726811"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para Azure Digital Twins

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Recorde-se que o centro das Gémeas Digitais Azure é o [**gráfico gémeo,**](concepts-twins-graph.md)construído a partir de **gémeos digitais** e **relacionamentos.** Este gráfico pode ser questionado para obter informações sobre os gémeos digitais e as relações que contém. Estas consultas são escritas numa linguagem de consulta personalizada semelhante ao SQL chamada **Azure Digital Twins Query Store.**

Para submeter uma consulta ao serviço a partir de uma aplicação de cliente, utilizará a **API de Consulta**de Gémeos Digitais Azure. Isto permite que os desenvolvedores escrevam consultas e apliquem filtros para encontrar conjuntos de gémeos digitais no gráfico gémeo, e outras informações sobre o cenário Azure Digital Twins.

## <a name="query-language-features"></a>Características da linguagem de consulta

A Azure Digital Twins fornece extensas capacidades de consulta contra o gráfico gémeo. As consultas são descritas usando sintaxe semelhante ao SQL, como um superconjunto das capacidades da linguagem de [consulta IoT Hub](../iot-hub/iot-hub-devguide-query-language.md).

Aqui estão as operações disponíveis no Idioma Azure Digital Twins Query Store:
* Arranja gémeos pelas propriedades dos gémeos digitais.
* Arranja gémeos pelas interfaces dos gémeos digitais.
* Arranja gémeos por propriedades de relacionamento.
* Obter gémeos em vários tipos de relacionamento `JOIN` (consultas). Existem limitações no número de `JOIN` s permitidos (um nível para visualização pública).
* Utilize a função `IS_OF_MODEL(twinCollection, twinTypeName)` personalizada, que permite a filtragem com base no [modelo](concepts-models.md)do gémeo . Apoia a herança.
* Utilize qualquer combinação `AND` `OR` `NOT` (,, operador) do acima.
* Utilize funções de escala: `IS_BOOL` , , , , , , , , , `IS_DEFINED` , , `IS_NULL` . `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` `ENDS_WITH` .
* Utilize operadores de comparação de consultas: `AND` / `OR` / `NOT` `IN` / `NOT IN` , `STARTSWITH` / `ENDSWITH` , `=` , , , , , , , , `!=` `<` `>` `<=` `>=` .
* Continuação da utilização: O objeto de consulta é instantâneo com um tamanho de página (até 100). Pode recuperar os gémeos digitais uma página de cada vez, repetindo chamadas para o `nextAsTwin` método.

## <a name="next-steps"></a>Passos seguintes

Saiba como escrever consultas e ver exemplos de código do cliente em [Como-a-fazer: Consultar o gráfico gémeo](how-to-query-graph.md).