---
title: O que é o serviço Azure Face?
titleSuffix: Azure Cognitive Services
description: O serviço Azure Face fornece algoritmos de IA que utiliza para detetar, reconhecer e analisar rostos humanos em imagens.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconhecimento facial, software de reconhecimento facial, análise facial, correspondência facial, app de reconhecimento facial, pesquisa facial por imagem, pesquisa de reconhecimento facial
ms.openlocfilehash: 600ca48cc19ee8723b423e484ec96736a55ae7fc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95532261"
---
# <a name="what-is-the-azure-face-service"></a>O que é o serviço Azure Face?

> [!WARNING]
> A 11 de junho de 2020, a Microsoft anunciou que não irá vender tecnologia de reconhecimento facial a esquadras da polícia nos Estados Unidos até à implementação de uma regulamentação rígida assente nos direitos humanos. Como tal, os clientes não podem utilizar funcionalidades ou funcionalidades de reconhecimento facial incluídas nos Serviços Azure, como o Face ou o Video Indexer, se um cliente estiver, ou estiver a permitir o uso de tais serviços por ou para, um departamento de polícia nos Estados Unidos.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço Azure Face fornece algoritmos de IA que detetam, reconhecem e analisam rostos humanos em imagens. O software de reconhecimento facial é importante em muitos cenários diferentes, tais como segurança, interface natural do utilizador, análise e gestão de conteúdos de imagem, aplicações móveis e robótica.

O serviço Face fornece várias funções de análise facial diferentes que são descritas nas seguintes secções.

## <a name="face-detection"></a>Deteção facial

O serviço Face deteta rostos humanos numa imagem e devolve as coordenadas do retângulo das suas localizações. Opcionalmente, a deteção facial pode extrair uma série de atributos relacionados com o rosto, tais como pose de cabeça, sexo, idade, emoção, pelos faciais e óculos.

> [!NOTE]
> A funcionalidade de deteção facial também está disponível através do [serviço de Visão de Computador.](../computer-vision/overview.md) No entanto, se pretender fazer mais operações com dados faciais, deverá utilizar este serviço.

![Uma imagem de uma mulher e de um homem, com retângulos desenhados em torno dos seus rostos e idade e género exibidos](./Images/Face.detection.jpg)

Para obter mais informações sobre a deteção facial, consulte o artigo [de conceitos de deteção](concepts/face-detection.md) facial. Consulte também a documentação de referência da [API deteta.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Verificação de rostos

A API verificar faz uma autenticação contra duas faces detetadas ou de um rosto detetado para um objeto de uma pessoa. Na prática, avalia se dois rostos pertencem à mesma pessoa. Esta capacidade é potencialmente útil em cenários de segurança. Para obter mais informações, consulte o guia de conceitos [de reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="find-similar-faces"></a>Encontrar rostos semelhantes

A API Find Similar enfrenta a correspondência entre a face do alvo e um conjunto de rostos de candidatos, encontrando um conjunto menor de rostos que se parecem com a face do alvo. Isto é útil para fazer uma pesquisa facial por imagem. 

Dois modos de trabalho, **matchPerson** e **matchFace,** são suportados. O modo **matchPerson** retorna faces semelhantes após a filtragem para a mesma pessoa utilizando a [API verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). O modo **MatchFace** ignora o filtro da mesma pessoa. Devolve uma lista de rostos de candidatos semelhantes que podem ou não pertencer à mesma pessoa.

O exemplo a seguir mostra a face do alvo:

![Mulher a sorrir](./Images/FaceFindSimilar.QueryFace.jpg)

E estas imagens são os rostos dos candidatos:

![Cinco imagens de pessoas a sorrir. As imagens a e b mostram a mesma pessoa.](./Images/FaceFindSimilar.Candidates.jpg)

Para encontrar quatro faces semelhantes, o modo **matchPerson** retorna a e b, que mostram a mesma pessoa que a face do alvo. O modo **matchFace** devolve a, b, c, e d &mdash; exatamente quatro candidatos, mesmo que alguns não sejam a mesma pessoa que o alvo ou tenham baixa semelhança. Para obter mais informações, consulte o guia de conceitos [de reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API Find Similar.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)

## <a name="face-grouping"></a>Agrupamento de rostos

A API Agrupamento divide um conjunto de rostos desconhecidos em vários grupos com base na semelhança. Cada grupo é um subconjunto adequado e desassociado do conjunto original de rostos. Todos os rostos de um grupo são suscetíveis de pertencer à mesma pessoa. Pode haver vários grupos diferentes para uma única pessoa. Os grupos são diferenciados por outro fator, como a expressão, por exemplo. Para mais informações, consulte o guia de conceitos [de reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API do Grupo.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)

## <a name="person-identification"></a>Identificação de pessoas

A API de identificação é usada para identificar um rosto detetado contra uma base de dados de pessoas (pesquisa de reconhecimento facial). Esta funcionalidade pode ser útil para a marcação automática de imagens em software de gestão de fotografias. Cria-se a base de dados com antecedência e pode editá-la com o tempo.

A imagem a seguir mostra um exemplo de uma base de dados chamada `"myfriends"` . Cada grupo pode conter até 1 milhão de objetos pessoais diferentes. Cada objeto de pessoa pode ter 248 rostos registados.

![Uma grelha com três colunas para pessoas diferentes, cada uma com três linhas de imagens faciais](./Images/person.group.clare.jpg)

Depois de criar e treinar uma base de dados, pode fazer a identificação contra o grupo com uma nova face detetada. Se o rosto for identificado como uma pessoa no grupo, esse objeto será devolvido.

Para obter mais informações sobre a identificação da pessoa, consulte o guia de conceitos [de reconhecimento facial](concepts/face-recognition.md) ou a documentação de referência da [API identificar.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="deploy-on-premises-using-docker-containers"></a>Implantar nas instalações utilizando contentores Docker

[Utilize o recipiente Face (pré-visualização)](face-how-to-install-containers.md) para implantar as funcionalidades API no local. Este recipiente Docker permite-lhe aproximar o serviço dos seus dados por razões de conformidade, segurança ou outras razões operacionais.

## <a name="sample-apps"></a>Exemplos de aplicações

As seguintes aplicações de amostra mostram algumas formas de utilizar o serviço Face:

- [Face API: Windows Client Library e sample](https://github.com/Microsoft/Cognitive-Face-Windows) é uma aplicação WPF que demonstra vários cenários de deteção, análise e identificação do Rosto.
- [FamilyNotes UWP app](https://github.com/Microsoft/Windows-appsample-familynotes) é uma aplicação Universal Windows Platform (UWP) que usa a identificação facial juntamente com a fala, Cortana, tinta e câmera em um cenário de partilha de notas familiares.

## <a name="data-privacy-and-security"></a>Privacidade e segurança dos dados

Tal como acontece com todos os recursos dos Serviços Cognitivos, os desenvolvedores que utilizam o serviço Face devem estar cientes das políticas da Microsoft sobre os dados dos clientes. Para mais informações, consulte a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) no Microsoft Trust Center.

## <a name="next-steps"></a>Passos seguintes

Siga um quickstart para codificar os componentes básicos de uma aplicação de reconhecimento facial no idioma à sua escolha.

- [Início rápido da biblioteca do cliente.](quickstarts/client-libraries.md)