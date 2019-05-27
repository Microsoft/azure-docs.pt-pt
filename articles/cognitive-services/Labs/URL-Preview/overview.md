---
title: O que é a Pré-visualização do URL do Projeto?
titlesuffix: Azure Cognitive Services
description: Introdução à Pré-visualização do URL do Projeto.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: overview
ms.date: 03/16/2018
ms.author: rosh
ms.openlocfilehash: 7022c3b2d2f3618d55b0a70d2690abf1497ec6a6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "61473184"
---
# <a name="what-is-project-url-preview"></a>O que é a Pré-visualização do URL do Projeto?
O ponto final de Pré-visualização do URL assume um parâmetro de consulta de URL e devolve uma resposta JSON com o nome do recurso de destino, uma breve descrição e uma ligação para uma imagem a apresentar numa pré-visualização. A resposta também inclui o sinalizador [isFamilyFriendly](url-preview-reference.md#query-parameters), que indica se o URL contém conteúdo para adultos, pirateado ou outro conteúdo ilícito. 

Para obter os resultados de pré-visualização do URL, submeta um pedido GET e inclua o cabeçalho *Ocp-Apim-Subscription-Key* com um token válido:  
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```
A resposta: 
```
HTTP Headers:
BingAPIs-TraceId: 3CC74C94769440C0851D9DF0869FCE7F
BingAPIs-SessionId: 52219085A6364692958C9C83983A0DBA
X-MSEdge-ClientID: 13D44DC2DE946B4B0F25460CDF036AD6
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 3CC74C94769440C0851D9DF0869FCE7F Ref B: CO1EDGE0315 Ref C: 2018-04-11T18:47:40Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile typing",
  "url": "https:\/\/swiftkey.com\/en",
  "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https:\/\/swiftkey.com\/images\/og\/default.jpg"
  }
}

```
## <a name="scenarios"></a>Cenários 

A API de Pré-visualização do URL suporta descrições breves de recursos da Web. Os programadores utilizam-na para criar experiências de pré-visualização avançadas.  Os utilizadores podem partilhar ou marcar páginas Web, notícias, blogues, fóruns, etc. Esta API também pode ser utilizada para moderação de conteúdos.    

As aplicações utilizam a Pré-visualização do URL para enviar pedidos Web ao ponto final com uma consulta atribuída ao URL para visualização.  A resposta JSON contém as informações de pré-visualização: nome, descrição do recurso, sinalizador *familyFriendly* e ligações que concedem acesso a uma imagem representativa e ao recurso completo online. 

## <a name="terms-of-use"></a>Termos de utilização
O Utilizador só deve utilizar os dados da Pré-visualização do URL do Projeto para apresentar fragmentos de pré-visualização e imagens em miniatura hiperligados aos sites de origem, na partilha de URL iniciada pelo utilizador final nas redes sociais, chatboxs ou ofertas semelhantes. O Utilizador não pode copiar, armazenar nem colocar em cache quaisquer dados que receber da Pré-visualização do URL do Projeto. O Utilizador deve respeitar todos os pedidos para desativar as pré-visualizações que possa receber dos proprietários de sites ou de conteúdos.

O Utilizador ou terceiros em seu nome não poderão utilizar, reter, armazenar, colocar em cache, partilhar nem distribuir quaisquer dados da API de Pré-visualização de URL para testes, desenvolvimento, formação, distribuição ou disponibilização de qualquer serviço ou funcionalidade que não seja da Microsoft. 

## <a name="throttling-requests"></a>Limitar pedidos

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passos Seguintes
- [Início rápido do C#](csharp.md)
- [Início rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
