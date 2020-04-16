---
title: O que é o serviço Face?
titleSuffix: Azure Cognitive Services
description: O serviço Azure Cognitive Services Face fornece algoritmos que são usados para detetar, reconhecer e analisar rostos humanos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 6714a0c4b967d80ad683ef023b5811423bdcb022
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403458"
---
# <a name="what-is-the-azure-face-service"></a>O que é o serviço Azure Face?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço Azure Cognitive Services Face fornece algoritmos que detetam, reconhecem e analisam rostos humanos em imagens. A capacidade de processar informação facial humana é importante em muitos cenários de software diferentes. Os cenários exemploincluem segurança, interface natural do utilizador, análise e gestão de conteúdos de imagem, aplicações móveis e robótica.

O serviço Face fornece várias funções diferentes que são delineadas nas seguintes secções.

## <a name="face-detection"></a>Deteção de rostos

O serviço Face deteta rostos humanos numa imagem e devolve as coordenadas de retângulo das suas localizações. Opcionalmente, a deteção facial pode extrair uma série de atributos relacionados com o rosto. Exemplos são pose de cabeça, sexo, idade, emoção, pelos faciais e óculos.

> [!NOTE]
> A funcionalidade de deteção facial também está disponível através da [API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)computer Vision . Se quiser fazer mais operações com dados faciais, utilize o serviço Face, que é o serviço discutido neste artigo.

![Uma imagem de uma mulher e de um homem, com retângulos desenhados em torno do rosto e idade e sexo exibidos](./Images/Face.detection.jpg)

Para obter mais informações sobre a deteção facial, consulte o artigo conceitos de deteção de [face.](concepts/face-detection.md) Consulte também a documentação de referência [da API de deteção.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Verificação de rostos

A API verificar faz uma autenticação contra dois rostos detetados ou de um objeto de rosto para uma pessoa detetado. Na prática, avalia se dois rostos pertencem à mesma pessoa. Esta capacidade é potencialmente útil em cenários de segurança. Para mais informações, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API Find Similar compara um rosto alvo com um conjunto de rostos de candidatos para encontrar um conjunto menor de rostos que se parecem com a face alvo. São suportados dois modos de trabalho, matchPerson e matchFace. O modo MatchPerson devolve rostos semelhantes depois de filtrar para a mesma pessoa utilizando a [API de verificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo matchFace ignora o filtro da mesma pessoa. Devolve uma lista de rostos semelhantes de candidatos que podem ou não pertencer à mesma pessoa.

O exemplo que se segue mostra o rosto-alvo:

![Mulher a sorrir](./Images/FaceFindSimilar.QueryFace.jpg)

E estas imagens são as caras dos candidatos:

![Cinco imagens de pessoas a sorrir. As imagens a e b mostram a mesma pessoa.](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro faces semelhantes, o modo matchPerson devolve a e b, que mostram a mesma pessoa que a face alvo. O modo matchFace devolve a, b, c, e d&mdash;exatamente quatro candidatos, mesmo que alguns não sejam a mesma pessoa que o alvo ou tenham baixa semelhança. Para mais informações, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API similar.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Agrupamento de rostos

A API Agrupamento divide um conjunto de rostos desconhecidos em vários grupos com base na semelhança. Cada grupo é um subconjunto adequado e desassociado do conjunto original de rostos. Todos os rostos de um grupo são suscetíveis de pertencer à mesma pessoa. Pode haver vários grupos diferentes para uma única pessoa. Os grupos são diferenciados por outro fator, como a expressão, por exemplo. Para mais informações, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência do [Grupo API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)

## <a name="person-identification"></a>Identificação de pessoas

A API identificar é usada para identificar um rosto detetado contra uma base de dados de pessoas. Esta funcionalidade pode ser útil para a marcação automática de imagem em software de gestão de fotos. Cria a base de dados com antecedência e pode editá-la ao longo do tempo.

A imagem que se segue `"myfriends"`mostra um exemplo de uma base de dados chamada . Cada grupo pode conter até 1 milhão de objetos pessoais diferentes. Cada objeto de pessoa pode ter 248 rostos registados.

![Uma grelha com três colunas para pessoas diferentes, cada uma com três linhas de imagens faciais](./Images/person.group.clare.jpg)

Depois de criar e treinar uma base de dados, pode fazer a identificação contra o grupo com um novo rosto detetado. Se o rosto for identificado como uma pessoa no grupo, esse objeto será devolvido.

Para obter mais informações sobre a identificação de pessoas, consulte o guia de conceitos de [reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API identificar.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="containers"></a>Contentores

[Utilize o recipiente Face](face-how-to-install-containers.md) para detetar, reconhecer e identificar rostos instalando um recipiente Docker padronizado mais próximo dos seus dados.

## <a name="sample-apps"></a>Exemplos de aplicações

As seguintes aplicações de amostra mostram algumas formas de utilizar o serviço Face:

- [Face API: Windows Client Library and sample](https://github.com/Microsoft/Cognitive-Face-Windows) is a WPF app that demonstra vários cenários de deteção, análise e identificação face.
- [FamilyNotes UWP app](https://github.com/Microsoft/Windows-appsample-familynotes) é uma aplicação Universal Windows Platform (UWP) que usa a identificação facial juntamente com a fala, Cortana, tinta e câmera em um cenário de partilha de notas familiar.

## <a name="data-privacy-and-security"></a>Privacidade e segurança de dados

Tal como acontece com todos os recursos dos Serviços Cognitivos, os desenvolvedores que utilizam o serviço Face devem estar cientes das políticas da Microsoft em dados de clientes. Para mais informações, consulte a página de [Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center.

## <a name="next-steps"></a>Passos seguintes

Siga um início rápido para implementar um cenário de deteção facial em código:

- [Quickstart: Detete os rostos numa imagem utilizando o .NET SDK com C#](quickstarts/csharp.md). Outras línguas estão disponíveis.
