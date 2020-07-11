---
title: Linguagem da consulta
titleSuffix: Azure Digital Twins
description: Compreenda os fundamentos da linguagem Azure Digital Twins Query Store.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6da539ccd8ad293aed402a4a6d130b6701e7b9c2
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187120"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para Azure Digital Twins

Recorde-se que o centro das Gémeas Digitais Azure é o [**gráfico gémeo,**](concepts-twins-graph.md)construído a partir de **gémeos digitais** e **relacionamentos.** Este gráfico pode ser questionado para obter informações sobre os gémeos digitais e as relações que contém. Estas consultas são escritas numa linguagem de consulta personalizada semelhante ao SQL chamada **Azure Digital Twins Query Store.**

Para submeter uma consulta ao serviço a partir de uma aplicação de cliente, utilizará a **API de Consulta**de Gémeos Digitais Azure. Isto permite que os desenvolvedores escrevam consultas e apliquem filtros para encontrar conjuntos de gémeos digitais no gráfico gémeo, e outras informações sobre o cenário Azure Digital Twins.

## <a name="query-language-features"></a>Características da linguagem de consulta

A Azure Digital Twins fornece extensas capacidades de consulta contra o gráfico gémeo. As consultas são descritas usando sintaxe semelhante ao SQL, numa linguagem de consulta semelhante à [linguagem de consulta IoT Hub](../iot-hub/iot-hub-devguide-query-language.md) com muitas características comparáveis.

Aqui estão as operações disponíveis no Idioma Azure Digital Twins Query Store:
* Arranja gémeos pelas propriedades dos gémeos digitais.
* Arranja gémeos pelas interfaces dos gémeos digitais.
* Arranja gémeos por propriedades de relacionamento.
* Obter gémeos em vários tipos de relacionamento `JOIN` (consultas). Existem limitações no número de `JOIN` s permitidos (um nível para visualização pública).
* Utilize a função `IS_OF_MODEL(twinCollection, twinTypeName)` personalizada, que permite a filtragem com base no [modelo](concepts-models.md)do gémeo . Apoia a herança.
* Utilize funções de escala: `IS_BOOL` , , , , , , , , , `IS_DEFINED` , , `IS_NULL` . `IS_NUMBER` `IS_OBJECT` `IS_PRIMITIVE` `IS_STRING` `STARTS_WITH` `ENDS_WITH` .
* Utilize operadores de comparação de consultas: `IN` / `NIN` , , , `=` , , , , `!=` `<` `>` `<=` `>=` .
* Utilize qualquer combinação `AND` `OR` `NOT` (,, operador) do acima.
* Continuação da utilização: O objeto de consulta é instantâneo com um tamanho de página (até 100). Você pode recuperar os gémeos digitais uma página de cada vez, fornecendo o token de continuação em chamadas subsequentes para a API.

## <a name="next-steps"></a>Passos seguintes

Saiba como escrever consultas e ver exemplos de código do cliente em [Como-a-fazer: Consultar o gráfico gémeo](how-to-query-graph.md).
