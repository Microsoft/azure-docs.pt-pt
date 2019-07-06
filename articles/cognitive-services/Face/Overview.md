---
title: O que é o API Face?
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o serviço Face para detetar e analisar rostos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 4be33f781dec93fd9fe1b1846322672266cd7350
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606916"
---
# <a name="what-is-the-azure-face-api"></a>O que é o API Face do Azure?

A API de Face de serviços cognitivos do Azure fornece algoritmos que são utilizados para detetar, reconhecer e analise rostos humanos em imagens. A capacidade de processar informações de face humana é importante em muitos cenários de software diferentes. Cenários de exemplo são segurança, interface do usuário natural, análise de conteúdo de imagem e gestão, aplicações móveis e robótica.

A API Face fornece várias funções diferentes. Cada função é descrita nas seções a seguir. Continue a ler para saber mais sobre eles.

## <a name="face-detection"></a>Deteção de rostos

A API Face Deteta rostos humanos numa imagem e devolve as coordenadas do retângulo de seus locais. Opcionalmente, deteção de rostos pode extrair uma série de atributos de rostos. Os exemplos são representam principal, sexo, idade, emoção, pelo facial, em e óculos.

> [!NOTE]
> A funcionalidade de deteção de rostos também está disponível através da [API de imagem digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Se quiser mais operações com dados de rosto, utilize a API Face, que é o serviço discutido neste artigo.

![Uma imagem de uma mulher e um homem, com retângulos desenhada em torno de seus rostos e idade e género apresentado](./Images/Face.detection.jpg)

Para obter mais informações sobre a deteção de rostos, consulte a [deteção facial](concepts/face-detection.md) artigo conceitos. Consulte também os [detetar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) documentação de referência.

## <a name="face-verification"></a>Verificação de rostos

A Verify API faz a autenticação contra dois rostos detetados ou entre um rosto detetado e um objeto de pessoa. Na prática, avalia se dois rostos pertencem à mesma pessoa. Esta capacidade é potencialmente útil em cenários de segurança. Para obter mais informações, consulte a [reconhecimento facial](concepts/face-recognition.md) guia de conceitos ou o [verificar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) documentação de referência.

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API semelhante encontrar compara um rosto de destino com um conjunto de faces Release candidate para localizar um conjunto menor de faces com aspeto semelhante a face de destino. Dois modos de trabalho, matchPerson e matchFace, são suportados. O modo de matchPerson devolve rostos semelhantes, depois de este filtra para a mesma pessoa, utilizando o [Certifique-se de API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo de matchFace ignora o filtro de mesma pessoa. Devolve uma lista de rostos semelhantes do candidato que pode ou não poderá pertencer à mesma pessoa.

O exemplo seguinte mostra a aparência de destino:

![Mulher a sorrir](./Images/FaceFindSimilar.QueryFace.jpg)

E estes são os rostos candidatos:

![Cinco imagens de pessoas a sorrir. Imagens de um e b mostra a mesma pessoa.](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar rostos semelhantes quatro, devolve o modo de matchPerson um e b, que mostram a mesma pessoa que a face de destino. O retorna de modo matchFace a, b, c e d, exatamente quatro candidatos, mesmo que alguns não são a mesma pessoa que o destino ou tem semelhança baixa. Para obter mais informações, consulte a [reconhecimento facial](concepts/face-recognition.md) guia de conceitos ou o [encontrar API semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) documentação de referência.

## <a name="face-grouping"></a>Agrupamento de rostos

A API Agrupamento divide um conjunto de rostos desconhecidos em vários grupos com base na semelhança. Cada grupo é um subconjunto adequado e desassociado do conjunto original de rostos. Todos os rostos de um grupo são provável que pertencem à mesma pessoa. Pode haver vários grupos diferentes para uma única pessoa. Os grupos são diferenciados por outro fator, por exemplo, a expressão, por exemplo. Para obter mais informações, consulte a [reconhecimento facial](concepts/face-recognition.md) guia de conceitos ou o [grupo API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) documentação de referência.

## <a name="person-identification"></a>Identificação de pessoas

A API de identificar é utilizada para identificar um rosto detetado em relação a uma base de dados de pessoas. Esta funcionalidade pode ser útil para identificação de imagem automática no software de gerenciamento de fotos. Criar a base de dados com antecedência e pode editá-lo ao longo do tempo.

A imagem seguinte mostra um exemplo de uma base de dados com o nome `"myfriends"`. Cada grupo pode conter objetos de outra pessoa de até 1 milhão. Cada objeto de pessoa pode ter 248 rostos registados.

![Um grid com três colunas para pessoas diferentes, cada um com três linhas de imagens de rostos](./Images/person.group.clare.jpg)

Depois de criar e formar um banco de dados, é possível realizar a identificação em relação ao grupo com um novo rosto detetado. Se o rosto for identificado como uma pessoa no grupo, esse objeto será devolvido.

Para obter mais informações sobre a identificação de pessoas, consulte a [reconhecimento facial](concepts/face-recognition.md) guia de conceitos ou o [identificar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) documentação de referência.

## <a name="use-containers"></a>Utilizar contentores

[Usar o contêiner de Face](face-how-to-install-containers.md) para detectar, reconhecer e identifique rostos através da instalação de um contentor Docker padronizado mais perto aos seus dados.

## <a name="sample-apps"></a>Exemplos de aplicações

As aplicações de exemplo seguintes mostram algumas formas de utilizar a API Face:

- [Microsoft Face API: Exemplo e biblioteca de cliente do Windows](https://github.com/Microsoft/Cognitive-Face-Windows) é um aplicativo do WPF que demonstra vários cenários de deteção de rostos, análise e identificação.
- [Aplicação FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes) é uma aplicação plataforma Universal do Windows (UWP) que utiliza enfrenta identificação, juntamente com a conversão de voz, Cortana, tinta e a câmara num cenário de compartilhamento de anotações família.

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Tal como acontece com todos os recursos de serviços cognitivos, os desenvolvedores que utilizam o serviço de rosto tem de ser cientes das políticas da Microsoft em dados do cliente. Para obter mais informações, consulte a [página dos serviços cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center.

## <a name="next-steps"></a>Passos Seguintes

Seguir um guia de introdução de implementar um cenário de deteção de rostos em código:

- [Quickstart: Detetar rostos numa imagem com o SDK do .NET com o C# ](quickstarts/csharp.md). Outros idiomas estão disponíveis.
