---
title: Exemplos do SDK de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Utilize o SDK de Pesquisa na Web do Bing para adicionar funcionalidades de pesquisa à sua aplicação Python, Node.js, C# ou Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350437"
---
# <a name="bing-web-search-sdk-samples"></a>Exemplos do SDK de Pesquisa na Web do Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

O SDK de Pesquisa na Web do Bing está disponível no Python, Node.js, C# e Java. Os exemplos de código, os pré-requisitos e as instruções de compilação são fornecidos no GitHub. São abordados os seguintes cenários:

* Consultar uma única palavra e imprimir o nome e o URL do primeiro resultado para páginas Web, imagens, artigos de notícias e vídeos.
* Consultar uma expressão, verificar o número de resultados e imprimir o nome e URL do primeiro resultado.
* Consultar um termo de pesquisa com filtros de resposta definidos como `news` e imprimir os detalhes dos resultados de notícias.
* Consultar um termo de pesquisa com os parâmetros `answerCount` e `promote` e, em seguida, imprimir os detalhes dos resultados.

## <a name="sdks-and-libraries"></a>SDKs e bibliotecas

Utilize estas ligações para aceder ao SDK no seu idioma preferencial.

* Introdução aos [exemplos de Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Veja também [bibliotecas do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) para obter definições e dependências.
* Começar com [Node.js amostras](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Consulte também [Node.js Pesquisa na Web.](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch)
* Introdução aos [exemplos de .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Consulte também [bibliotecas .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch) para definições e dependências.
* Introdução aos [exemplos de Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Veja também [bibliotecas Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) para obter as definições e as dependências.