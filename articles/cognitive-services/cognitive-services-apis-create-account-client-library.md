---
title: Criar recursos de Serviços Cognitivos utilizando a biblioteca de clientes da Azure Management
titleSuffix: Azure Cognitive Services
description: Criar e gerir os recursos dos Serviços Cognitivos da Azure utilizando a biblioteca de clientes da Azure Management.
services: cognitive-services
keywords: serviços cognitivos, inteligência cognitiva, soluções cognitivas, serviços ai
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91262455"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Quickstart: Criar um recurso de Serviços Cognitivos utilizando a biblioteca de clientes da Azure Management

Utilize este quickstart para criar e gerir os recursos dos Serviços Cognitivos Azure utilizando a biblioteca de clientes da Azure Management.

Os Azure Cognitive Services são serviços de base na nuvem com APIs rest, e SDKs da biblioteca de clientes disponíveis para ajudar os desenvolvedores a construir inteligência cognitiva em aplicações sem ter inteligência artificial direta (IA) ou habilidades ou conhecimentos de ciência de dados. O Azure Cognitive Services permite que os desenvolvedores adicionem facilmente funcionalidades cognitivas nas suas aplicações com soluções cognitivas que podem ver, ouvir, falar, entender e até começar a raciocinar.

Os serviços individuais de IA são representados pelos [recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure que cria sob a sua subscrição Azure. Depois de criar um recurso, pode utilizar as teclas e o ponto final gerados para autenticar as suas aplicações.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
