---
title: O que é o API Face?
titleSuffix: Azure Cognitive Services
description: Saiba como utilizar o serviço Face para detetar e analisar rostos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: dcfb50c58f1205a5ab31c3fc6b9b22fdb503e4ec
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588963"
---
# <a name="what-is-the-azure-face-api"></a>O que é o API Face do Azure?

A API Face do Azure é um serviço cognitivo que fornece algoritmos para detetar, reconhecer e analisar rostos humanos em imagens. A capacidade de processar informações de rostos humanos é importante em muitos cenários de software, como segurança, interface de utilizador natural, análise e gestão de conteúdo de imagens, aplicações móveis e robótica.

A API Face proporciona várias funções diferentes que estão descritas nas secções seguintes. Continue a ler para saber mais sobre cada.

## <a name="face-detection"></a>Deteção de rostos

A API Face consegue detetar rostos humanos em imagens e devolver as coordenadas de retângulo das localizações dos rostos. Opcionalmente, deteção de rostos pode extrair uma série de atributos de face como representam, representam principal, sexo, idade, emoção, pelo facial, em e óculos.

> [!NOTE] 
> A funcionalidade de deteção facial também está disponível através da [API de Imagem Digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Contudo, se quiser realizar outras operações com os dados faciais, deve utilizar a API Face (este serviço).

![Imagem de um homem e uma mulher, com retângulos desenhados à volta dos rostos, e idade e sexto apresentados.](./Images/Face.detection.jpg)

Para obter mais informações sobre a deteção de rostos, consulte a [guia de procedimentos de deteção de rostos](face-api-how-to-topics/howtodetectfacesinimage.md), ou procure o [API detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) documentação de referência.

## <a name="face-verification"></a>Verificação de rostos

A Verify API faz a autenticação contra dois rostos detetados ou entre um rosto detetado e um objeto de pessoa. Na prática, avalia se dois rostos pertencem à mesma pessoa. Esta característica é potencialmente útil para cenários de segurança. Para obter mais informações, veja [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) (API Verificação).

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

Find Similar API utiliza um rosto de destino e um conjunto de rostos candidatos e encontra um conjunto mais pequeno dos rostos que são mais parecidos com o rosto de destino. São suportados dois modos de trabalho, **matchPerson** e **matchFace**. O modo **matchPerson** devolve rostos semelhantes depois de filtrar a mesma pessoa (com [API Verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)). O modo **matchFace** ignora o filtro de mesma pessoa e devolve uma lista de rostos candidatos semelhantes que podem ou não pertencer à mesma pessoa.

No exemplo seguinte, este é o rosto de destino:

![Mulher a sorrir](./Images/FaceFindSimilar.QueryFace.jpg)

E estes são os rostos candidatos:

![Cinco imagens de pessoas a sorrir. As imagens a) e b) pertencem à mesma pessoa](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro rostos semelhantes, o modo **matchPerson** devolveria (a) e (b), que representam a mesma pessoa que o rosto de destino. O modo **matchFace** devolve (a), (b), (c) e (d) &mdash; exatamente quatro candidatos, mesmo que alguns não pertençam à mesma pessoa de destino ou sejam pouco semelhantes entre si. Para obter mais informações, veja [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) (API Encontrar Semelhante).

## <a name="face-grouping"></a>Agrupamento de rostos

A API Agrupamento divide um conjunto de rostos desconhecidos em vários grupos com base na semelhança. Cada grupo é um subconjunto adequado e desassociado do conjunto original de rostos. É provável que todos os rostos num grupo pertençam à mesma pessoa, mas cada pessoa pode estar em vários grupos diferentes (diferenciados por outros fatores, como uma expressão). Para obter mais informações, veja [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) (API Agrupamento).

## <a name="person-identification"></a>Identificação de pessoas

A API Identificação pode ser utilizada para identificar um rosto detetado numa base de dados de pessoas. Esta identificação pode ser útil na etiquetagem automática de imagens em software de gestão de fotografias. A base de dados é criada previamente e pode ser editada ao longo do tempo.

A imagem abaixo mostra um exemplo de uma base de dados com o nome "myfriends." Cada grupo pode conter até 1 000 000 de objetos de pessoa diferentes e cada objeto de pessoa pode ter registados 248 rostos.

![Grelha com 3 colunas para pessoas diferentes, cada qual com 3 linhas de imagens de rostos](./Images/person.group.clare.jpg)

Depois de ter sido criada e preparada uma base de dados, pode fazer a identificação em relação ao grupo com a deteção de um rosto novo. Se o rosto for identificado como uma pessoa no grupo, esse objeto será devolvido.

Para obter mais informações sobre a identificação de pessoas, veja [Identify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) (API Identificação).

## <a name="use-containers"></a>Utilizar contentores

[Usar o contêiner de Face](face-how-to-install-containers.md) para detectar, reconhecer e identifique rostos, ao instalar um contentor Docker padronizado mais perto aos seus dados.

## <a name="sample-apps"></a>Exemplos de aplicações

Os exemplos de aplicações seguintes mostram algumas das formas como a API Face pode ser utilizada.

- [Microsoft Face API: Biblioteca de cliente do Windows e & xemplo](https://github.com/Microsoft/Cognitive-Face-Windows) -um aplicativo do WPF que demonstra vários cenários de deteção de rostos, análise e identificação.
- [Aplicação FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes) - aplicação da Plataforma Universal do Windows (UWP) que utiliza a identificação facial juntamente com voz, Cortana, tinta e câmera num cenário de partilha de notas familiar.

## <a name="data-privacy-and-security"></a>Segurança e privacidade dos dados

Tal como acontece com todos os serviços cognitivos, os desenvolvedores que usam o serviço de Face devem de estar ciente das políticas da Microsoft em dados do cliente. Consulte a [página dos serviços cognitivos](https://www.microsoft.com/en-us/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center para obter mais informações.

## <a name="next-steps"></a>Passos Seguintes

Siga um início rápido para implementar um cenário simples de deteção facial em código.
- [Quickstart: Detetar rostos numa imagem usando o SDK do .NET com o C# ](quickstarts/csharp.md) (outras linguagens disponíveis)
