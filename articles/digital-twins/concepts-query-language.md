---
title: Linguagem da consulta
titleSuffix: Azure Digital Twins
description: Compreenda os fundamentos da linguagem Azure Digital Twins Query Store.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 960fff073353375cd50b31bc7284134ca733f142
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87488028"
---
# <a name="about-the-query-language-for-azure-digital-twins"></a>Sobre a linguagem de consulta para Azure Digital Twins

Recorde-se que o centro das Gémeas Digitais Azure é o [**gráfico gémeo,**](concepts-twins-graph.md)construído a partir de **gémeos digitais** e **relacionamentos.** Este gráfico pode ser questionado para obter informações sobre os gémeos digitais e as relações que contém. Estas consultas são escritas numa linguagem de consulta personalizada semelhante ao SQL chamada **Azure Digital Twins Query Store.**

Para submeter uma consulta ao serviço a partir de uma aplicação de cliente, utilizará a [**API de Consulta**](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.query?view=azure-dotnet-preview)de Gémeos Digitais Azure. Isto permite que os desenvolvedores escrevam consultas e apliquem filtros para encontrar conjuntos de gémeos digitais no gráfico gémeo, e outras informações sobre o cenário Azure Digital Twins.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

## <a name="next-steps"></a>Passos seguintes

Saiba como escrever consultas e ver exemplos de código do cliente em [*Como-a-fazer: Consultar o gráfico gémeo*](how-to-query-graph.md).
