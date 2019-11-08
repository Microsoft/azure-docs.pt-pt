---
title: O que é o API Face?
titleSuffix: Azure Cognitive Services
description: Os serviços cognitivas do Azure API de Detecção Facial fornecem algoritmos que são usados para detectar, reconhecer e analisar faces humanas em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 95ea1718682340967d5d39fe5f550f2638273796
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73743697"
---
# <a name="what-is-the-azure-face-api"></a>O que é o API Face do Azure?

Os serviços cognitivas do Azure API de Detecção Facial fornecem algoritmos que são usados para detectar, reconhecer e analisar faces humanas em imagens. A capacidade de processar informações de face humana é importante em vários cenários de software diferentes. Os cenários de exemplo são segurança, interface do usuário natural, análise e gerenciamento de conteúdo de imagem, aplicativos móveis e robótica.

O API de Detecção Facial fornece várias funções diferentes. Cada função é descrita nas seções a seguir. Continue lendo para saber mais sobre eles.

## <a name="face-detection"></a>Deteção de rostos

O API de Detecção Facial detecta faces humanas em uma imagem e retorna as coordenadas de retângulo de seus locais. Opcionalmente, a detecção facial pode extrair uma série de atributos relacionados à face. Os exemplos são pose de cabeça, gênero, idade, emoções, cabelo facial e óculos.

> [!NOTE]
> O recurso de detecção facial também está disponível por meio do [API da pesquisa Visual computacional](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home). Se você quiser realizar outras operações com dados de face, use o API de Detecção Facial, que é o serviço discutido neste artigo.

![Uma imagem de uma mulher e um homem, com retângulos desenhados ao redor de suas faces e idade e gênero exibidos](./Images/Face.detection.jpg)

Para obter mais informações sobre a detecção facial, consulte o artigo conceitos de [detecção facial](concepts/face-detection.md) . Consulte também a documentação detectar referência de [API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-verification"></a>Verificação de rostos

A Verify API faz a autenticação contra dois rostos detetados ou entre um rosto detetado e um objeto de pessoa. Na prática, avalia se dois rostos pertencem à mesma pessoa. Esse recurso é potencialmente útil em cenários de segurança. Para obter mais informações, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência de [API de verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) .

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API find similar compara uma face de destino com um conjunto de rostos candidatas para encontrar um conjunto menor de faces que se assemelham à face de destino. Há suporte para dois modos de trabalho, matchPerson e matchFace. O modo matchPerson retorna faces semelhantes depois que ele filtra para a mesma pessoa usando a [API Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo matchFace ignora o filtro de mesma pessoa. Ele retorna uma lista de faces candidatas semelhantes que podem ou não pertencer à mesma pessoa.

O exemplo a seguir mostra a face de destino:

![Mulher a sorrir](./Images/FaceFindSimilar.QueryFace.jpg)

E estes são os rostos candidatos:

![Cinco imagens de pessoas a sorrir. As imagens a e b mostram a mesma pessoa.](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro faces semelhantes, o modo matchPerson retorna a e b, que mostra a mesma pessoa que a face de destino. O modo matchFace retorna a, b, c e d, exatamente quatro candidatos, mesmo que alguns não sejam a mesma pessoa que o destino ou tenham pouca semelhança. Para obter mais informações, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência de [API semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) .

## <a name="face-grouping"></a>Agrupamento de rostos

A API Agrupamento divide um conjunto de rostos desconhecidos em vários grupos com base na semelhança. Cada grupo é um subconjunto adequado e desassociado do conjunto original de rostos. É provável que todas as faces em um grupo pertençam à mesma pessoa. Pode haver vários grupos diferentes para uma única pessoa. Os grupos são diferenciados por outro fator, como expressão, por exemplo. Para obter mais informações, consulte o guia conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API de grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) .

## <a name="person-identification"></a>Identificação de pessoas

A API de identificação é usada para identificar uma face detectada em um banco de dados de pessoas. Esse recurso pode ser útil para marcação automática de imagem no software de gerenciamento de fotos. Você cria o banco de dados antecipadamente e pode editá-lo ao longo do tempo.

A imagem a seguir mostra um exemplo de um banco de dados chamado `"myfriends"`. Cada grupo pode conter até 1 milhão objetos de pessoa diferentes. Cada objeto de pessoa pode ter 248 rostos registados.

![Uma grade com três colunas para pessoas diferentes, cada uma com três linhas de imagens de face](./Images/person.group.clare.jpg)

Depois de criar e treinar um banco de dados, você pode executar a identificação em relação ao grupo com uma nova face detectada. Se o rosto for identificado como uma pessoa no grupo, esse objeto será devolvido.

Para obter mais informações sobre a identificação de pessoa, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência de [API de identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) .

## <a name="use-containers"></a>Utilizar contentores

[Use o contêiner facial](face-how-to-install-containers.md) para detectar, reconhecer e identificar faces instalando um contêiner do Docker padronizado mais perto de seus dados.

## <a name="sample-apps"></a>Exemplos de aplicações

Os aplicativos de exemplo a seguir mostram algumas maneiras de usar o API de Detecção Facial:

- [Microsoft API de detecção facial: a biblioteca de cliente do Windows e o exemplo](https://github.com/Microsoft/Cognitive-Face-Windows) são um aplicativo WPF que demonstra vários cenários de detecção de face, análise e identificação.
- O [aplicativo FAMILYNOTES UWP](https://github.com/Microsoft/Windows-appsample-familynotes) é um aplicativo de plataforma universal do Windows (UWP) que usa a identificação facial junto com fala, Cortana, tinta e câmera em um cenário de compartilhamento de nota da família.

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Assim como acontece com todos os recursos de serviços cognitivas, os desenvolvedores que usam o serviço de face devem estar cientes das políticas da Microsoft nos dados do cliente. Para obter mais informações, consulte a [página de serviços cognitivas](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na central de confiabilidade da Microsoft.

## <a name="next-steps"></a>Passos seguintes

Siga um guia de início rápido para implementar um cenário de detecção facial no código:

- [Início rápido: detectar faces em uma imagem usando o SDK do .NET C#com ](quickstarts/csharp.md). Outros idiomas estão disponíveis.
