---
title: Glossário - serviço de API de rostos
titleSuffix: Azure Cognitive Services
description: O glossário explica os termos que poderá encontrar ao trabalhar com o Serviço da API Face.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 6cee0f55d1c7d53ad28eb5568b07e83ccab28afe
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588912"
---
# <a name="glossary"></a>Glossário

## <a name="a"></a>A

#### <a name="attributes"></a>Atributos

Os atributos são funcionalidades de face opcional que podem ser detetadas, tal como [idade](#Age-Attribute), [sexo](#Gender-Attribute), [representam principal](#Head-Pose-Attribute), [pelo facial, em](#Facial-Hair-Attribute)e o [slat smajlíka](#Smile-Attribute). Eles podem ser obtidos a partir da API de deteção de, especificando o _returnFaceAttributes_ parâmetro de consulta.

Para obter uma lista completa dos atributos faciais, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="age-attribute"></a>Idade (atributo)

Idade é um da [atributos](#Attributes) que descreve a idade de um determinado rosto. O atributo de idade é opcional nos resultados da deteção e pode ser controlado com um pedido de deteção, especificando o parâmetro returnFaceAttributes.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="c"></a>C

#### <a name="candidate"></a>Release Candidate

Candidatos são essencialmente [identificação](#Identification) resultados (por exemplo, pessoas identificadas e nível de confiança nas detecções). Um candidato é representado pela [PersonID](#Person-ID) e [confiança](#Confidence), que indica que a pessoa é identificada com um elevado nível de confiança.

Para obter mais informações, consulte a documentação de referência: [Se deparam - identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

#### <a name="confidence"></a>Confiança

Confiança é uma medida revelando a semelhança entre [rostos](#Face) ou [pessoa](#Person) em valores numéricos – que é utilizada no [identificação](#Identification), e [ verificação](#Verification) para indicar as semelhanças dos resultados pesquisadas, identificados e verificados.

Para obter mais informações, consulte a documentação de referência: [Se deparam - encontrar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [enfrentam - verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="d"></a>D

#### <a name="detectionface-detection"></a>Deteção de deteção/rostos

Deteção de rostos é a ação de localização de rostos em imagens. Os utilizadores podem carregar uma imagem ou especificar um URL de imagem no pedido. Os rostos detetados são devolvidos com [enfrentam IDs](#Face-ID) indicando uma identidade exclusiva na API de rostos. Os retângulos indicam as localizações de rosto na imagem em pixels, bem como o opcional [atributos](#Attributes) para cada rosto como [idade](#Age-Attribute), [sexo](#Gender-Attribute), [representam principal ](#Head-Pose-Attribute), [pelo facial, em](#Facial-Hair-Attribute) e [sorridentes](#Smile-Attribute).

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="e"></a>E

#### <a name="emotion-attribute"></a>Emoções (atributo)

A emoção é um da [atributos faciais](#Attributes). Quando consultado, ele retorna uma lista de emoções e a respetiva confiança de deteção para o rosto. Pontuações de confiança são normalizadas: as pontuações em todas as emoções adicionará até uma. As emoções devolvidas são felicidade, tristeza, neutral, raiva, desdém, repulsa, surpresa e medo.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="f"></a>F

#### <a name="face"></a>Face

Face é um termo unificado para os resultados derivados de API de rostos relacionados com rostos detetados. Por fim, o rosto é representado por uma identidade unificada ([Face ID](#Face-ID)), uma região especificada em imagens ([retângulo de rostos](#Face-Rectangle)) e atributos adicionais relacionados com o rosto, tais como [idade](#Age-Attribute), [género](#Gender-Attribute), pontos de referência e [representam principal](#Head-Pose-Attribute). Além disso, os rostos podem ser retornados de deteção.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-api"></a>API Face

Face API é uma API com base na cloud que fornece os mais avançados algoritmos de deteção de rostos e reconhecimento. A principal funcionalidade da Face API, pode ser dividida em duas categorias: a deteção com atributos e face [reconhecimento](#Recognition).

Para obter mais informações, consulte a documentação de referência: [Descrição geral da API de rostos](./Overview.md), [enfrentam – detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [enfrentam - encontrar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [enfrentam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [ Se deparam - verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="face-attributesfacial-attributes"></a>Atributos de Facial/atributos faciais

Veja [atributos](#Attributes).

#### <a name="face-id"></a>ID do rosto

Face ID é derivado de resultados da deteção, em que uma cadeia de caracteres representa um [face](#Face) na [Face API](#Face-API).

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="face-landmarksfacial-landmarks"></a>Pontos de referência do rosto marcos/Facial

Pontos de referência são opcionais nos resultados da deteção; que são pontos faciais semânticos, tais como os olhos, mergulho e boca (ilustrado na figura a seguir). Pontos de referência podem ser controlados com um pedido de deteção pelo returnFaceLandmarks número booleano. Se returnFaceLandmarks estiver definido como true, as devolvidas faces terão os atributos de ponto de referência.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![HowToDetectFace](./Images/landmarks.1.jpg)

#### <a name="face-rectangle"></a>Face Rectangle

Retângulo de rostos deriva os resultados de deteção, que é um retângulo vertical (à esquerda, superior, largura, altura) em imagens em pixéis. O canto superior esquerdo de um [face](#Face) (à esquerda, top), além da largura e altura, indica os tamanhos de rostos em x e y dos eixos, respetivamente.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facial-hair-attribute"></a>Pelo facial, em (atributo)

Pelo facial, em é um da [atributos](#Attributes) usada para descrever os rostos disponíveis délku pelo facial, em. O atributo pelo facial, em é opcional nos resultados da deteção e pode ser controlado com um pedido de deteção por returnFaceAttributes. Se returnFaceAttributes contém 'facialHair', as devolvidas faces terão atributos pelo facial, em.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="facelist"></a>FaceList

FaceList é uma coleção de [PersistedFace](#PersistedFace) e é a unidade de [encontrar semelhante](#Find-Similar). Um FaceList vem com um [FaceList ID](#FaceList-ID), assim como outros atributos, tais como nomes e os dados de utilizador.

Para obter mais informações, consulte a documentação de referência: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="facelist-id"></a>ID de FaceList

ID de FaceList é uma cadeia de caracteres do fornecido pelo utilizador utilizada como um identificador de um [FaceList](#FaceList). O ID de FaceList tem de ser exclusivo dentro da subscrição.

Para obter mais informações, consulte a documentação de referência: [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [FaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c).

#### <a name="find-similar"></a>Encontrar semelhantes

Esta API é utilizada para/a consulta de pesquisa rostos semelhantes com base numa coleção de rostos. Os rostos de consultas e coleções de face são representadas como [enfrentam IDs](#Face-ID) ou [FceList ID](#FaceList-ID)/[LargeFaceList ID](#LargeFaceList-ID) no pedido. Devolvidos os resultados são pesquisados rostos semelhantes, representados pelo [enfrentam IDs](#Face-ID) ou IDs de PersistedFace.

Para obter mais informações, consulte a documentação de referência: [Se deparam - encontrar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [LargeFaceList - crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [FaceList - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b).

## <a name="g"></a>G

#### <a name="gender-attribute"></a>Sexo (atributo)

Sexo é um da [atributos](#Attributes) usada para descrever os géneros dos rostos disponíveis. O atributo de sexo é opcional nos resultados da deteção e pode ser controlado com um pedido de deteção por returnFaceAttributes. Se returnfaceAttributes contém 'sexo', as devolvidas faces terão atributos de género.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="grouping"></a>Agrupamento

Agrupamento dos mesmos é o agrupamento de uma coleção de rostos, de acordo com as semelhanças faciais. Coleções de face são indicadas por coleções face ID no pedido. Como resultado de agrupamento, rostos semelhantes são agrupados em conjunto como [grupos](#Groups), e rostos que não são semelhantes a qualquer outro face são mesclados em conjunto como um grupo confuso. Há pelo menos, um [confuso grupo](#Messy-Group) no resultado de agrupamento.

Para obter mais informações, consulte a documentação de referência: [Se deparam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

#### <a name="groups"></a>Grupos

Grupos são derivados do [agrupamento](#Grouping) resultados. Cada grupo contém uma coleção de rostos semelhantes, onde os rostos são indicados por [enfrentam IDs](#Face-ID).

Para obter mais informações, consulte a documentação de referência: [Se deparam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="h"></a>H

#### <a name="head-pose-attribute"></a>HEAD representam (atributo)

Posição de cabeça é um da [atributos](#Attributes) que representa enfrenta orientação no espaço 3D, de acordo com os ângulos de agregação, pitch e yaw, conforme mostrado na figura a seguir. Os intervalos de valores de roll e yaw são [-180, 180] e [-90, 90] em graus. Na versão atual, o valor de argumento de venda retornado da deteção é sempre 0. O atributo principal posição é opcional nos resultados da deteção e pode ser controlado com um pedido de deteção pelo parâmetro returnFaceAttributes. Se o parâmetro de returnFaceAttributes contém 'headPose', as devolvidas faces terão head representam os atributos.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

![GlossaryHeadPose](./Images/headpose.1.jpg)

## <a name="i"></a>I

#### <a name="identification"></a>Identificação

Identificação é identificar rostos de um ou mais de um LargePersonGroup/PersonGroup.
R [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup) é uma coleção de [pessoas](#Person).
Rostos e LargePersonGroup/PersonGroup são representados por [enfrentam IDs](#Face-ID) e [LargePersonGroup IDs](#LargePersonGroup-ID)/[PersonGroup IDs](#PersonGroup-ID) respetivamente no pedido.
São identificados de resultados [candidatos](#Candidate), representado pelo [pessoas](#Person) com confiança.
Vários rostos na entrada são considerados separadamente e cada rosto terá seu próprio result identificado.

> [!NOTE]
> LargePersonGroup/PersonGroup devem receber formação com êxito antes de identificação. Se o LargePersonGroup/PersonGroup não está preparado, ou o treinamento [estado](#Status-Train) não seja apresentado como "com êxito" (ou seja, "em execução", "falhado" ou "timeout"), a resposta do pedido é 400.
> 

Para obter mais informações, consulte a documentação de referência: [Se deparam - identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [LargePersonGroup pessoa - crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup – criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup Pessoa - crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup - crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="isidentical"></a>IsIdentical

IsIdentical é um campo Booleano dos [verificação](#Verification) resultados que indica se os dois rostos pertencem à mesma pessoa.

Para obter mais informações, consulte a documentação de referência: [Se deparam - verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

## <a name="l"></a>L

#### <a name="landmarks"></a>Pontos de referência

Consulte face pontos de referência.

#### <a name="largefacelist"></a>LargeFaceList

LargeFaceList é uma coleção de [PersistedFace](#PersistedFace) e é a unidade de [encontrar semelhante](#Find-Similar). Um LargeFaceList vem com um [LargeFaceList ID](#LargeFaceList-ID), assim como outros atributos, tais como nomes e os dados de utilizador.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce), [LargeFaceList - List Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158db4d2de3616c086f2d6).

#### <a name="largefacelist-id"></a>LargeFaceList ID

ID de LargeFaceList é uma cadeia de caracteres do fornecido pelo utilizador utilizada como um identificador de um [LargeFaceList](#LargeFaceList). O ID de LargeFaceList tem de ser exclusivo dentro da subscrição.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [LargeFaceList - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a15827cd2de3616c086f2ce).

#### <a name="largepersongroup"></a>LargePersonGroup

LargePersonGroup é uma coleção de [pessoas](#Person) e é a unidade de [identificação](#Identification). Um LargePersonGroup vem com um [LargePersonGroup ID](#LargePersonGroup-ID), assim como outros atributos, tais como nomes e os dados de utilizador.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [LargePersonGroup pessoa - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adda06ac60f11b48b5aa1).

#### <a name="largepersongroup-id"></a>ID de LargePersonGroup

ID de LargePersonGroup é uma cadeia de caracteres do fornecido pelo utilizador utilizada como um identificador de um [LargePersonGroup](#LargePersonGroup). O ID de LargePersonGroup tem de ser exclusivo dentro da subscrição.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e).

## <a name="m"></a>M

#### <a name="messy-group"></a>Grupo confuso

Grupo confuso é derivado do [agrupamento](#Grouping) resultados; que contém os rostos não semelhantes a qualquer outro face. Cada rosto num grupo confuso é indicado pelos [enfrentam ID](#Face-ID).

Para obter mais informações, consulte a documentação de referência: [Se deparam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="n"></a>N

#### <a name="name-person"></a>Nome (pessoa)

O nome é uma cadeia de caracteres descritiva amigável para [pessoa](#Person). Ao contrário do [ID da pessoa](#Person-ID), o nome das pessoas pode ser duplicado dentro de um grupo.

Para obter mais informações, consulte a documentação de referência: [Pessoa LargePersonGroup – crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup pessoa - obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup pessoa - obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="name-largepersongrouppersongroup"></a>Nome (LargePersonGroup/PersonGroup)

Nome também é uma cadeia de caracteres descritiva amigável para [LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup). Ao contrário do [LargePersonGroup ID](#LargePersonGroup-ID)/[PersonGroup ID](#PersonGroup-ID), o nome de LargePersonGroups/PersonGroups pode estar duplicado numa subscrição.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acebb6ac60f11b48b5a9e), [PersonGroup - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

## <a name="p"></a>P

#### <a name="persistedface"></a>PersistedFace

PersistedFace é uma estrutura de dados na API de rostos. PersistedFace vem com um ID de PersistedFace, bem como outros atributos como o nome e os dados de utilizador.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person-id"></a>ID da pessoa

ID da pessoa é gerado quando um [PersistedFace](#PersistedFace) é criado com êxito. Uma cadeia de caracteres é criada para representar essa rosto na [a API Face](#Face-API).

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [FaceList - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargePersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42), [PersonGroup pessoa - adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

#### <a name="person"></a>Pessoa

Pessoa é uma estrutura de dados gerida na API de rostos. Pessoa vem com um [ID da pessoa](#Person-ID), assim como outros atributos como o nome, uma coleção de [PersistedFace](#PersistedFace)e os dados de utilizador.

Para obter mais informações, consulte a documentação de referência: [Pessoa LargePersonGroup – crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup pessoa - obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup pessoa - obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="person-id"></a>ID da pessoa

ID da pessoa é gerado quando um [pessoa](#Person) é criado com êxito. Uma cadeia de caracteres é criada para representar essa pessoa na [a API Face](#Face-API).

Para obter mais informações, consulte a documentação de referência: [Pessoa LargePersonGroup – crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup pessoa - obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599add376ac60f11b48b5aa0), [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup pessoa - obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523f).

#### <a name="persongroup"></a>PersonGroup

PersonGroup é uma coleção de [pessoas](#Person) e é a unidade de [identificação](#Identification). Um PersonGroup vem com um [PersonGroup ID](#PersonGroup-ID), assim como outros atributos, tais como nomes e os dados de utilizador.

Para obter mais informações, consulte a documentação de referência: [PersonGroup - crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246), [PersonGroup pessoa - lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241).

#### <a name="persongroup-id"></a>ID de PersonGroup

ID de PersonGroup é uma cadeia de caracteres do fornecido pelo utilizador utilizada como um identificador de um [PersonGroup](#PersonGroup). O ID de grupo tem de ser exclusivo dentro da subscrição.

Para obter mais informações, consulte a documentação de referência: [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Get](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246).

#### <a name="pose-attribute"></a>Representar (atributo)

Veja [Head representam](#Head-Pose-Attribute).

## <a name="r"></a>R

#### <a name="recognition"></a>Reconhecimento

Reconhecimento é uma área de aplicações populares para tecnologias de rostos, tal como [encontrar semelhante](#Find-Similar), [agrupamento](#Grouping), [identificar](#Identification),[verificar dois enfrenta iguais ou não ](#Verification).

Para obter mais informações, consulte a documentação de referência: [Se deparam - encontrar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), [enfrentam - grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238), [enfrentam – identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [enfrentam - verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

#### <a name="rectangle-face"></a>Retângulo (Face)

Veja [retângulo de rostos](#Face-Rectangle).

## <a name="s"></a>S

#### <a name="similar-face-searching"></a>Pesquisa de Faces Semelhantes

Veja [encontrar semelhante](#Find-Similar).

#### <a name="smile-attribute"></a>Sorria (atributo)

Sorriso é um da [atributos](#Attributes) usada para descrever a expressão de sorriso dos rostos disponíveis. O atributo de sorriso nos resultados de deteção é opcional e pode ser controlado com um pedido de deteção por returnFaceAttributes. Se returnFaceAttributes contém 'sorria', as devolvidas faces terão sorriso atributos.

Para obter mais informações, consulte a documentação de referência: [Se deparam - detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

#### <a name="snapshot"></a>Instantâneo

Um instantâneo é um armazenamento remoto temporário para determinados tipos de dados de rostos. Ele funciona como um tipo de área de transferência para copiar dados de uma subscrição para outro. Pela primeira vez o utilizador "usa" um instantâneo dos dados na subscrição de origem e, em seguida, eles "aplicam"-lo a um novo objeto de dados na subscrição de destino. 

Para obter mais detalhes, consulte [guia de migração de rosto](./face-api-how-to-topics/how-to-migrate-face-data.md) , bem como a [tirar instantâneo -](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/take) e [do instantâneo - aplicar](https://docs.microsoft.com/rest/api/cognitiveservices/face/snapshot/apply) (REST) de documentação de referência.

#### <a name="status-train"></a>Estado (Train)

O estado é uma cadeia de caracteres usada para descrever o procedimento para treinamento LargeFaceList/LargePersonGroups/PersonGroups, incluindo "notstarted", "em execução", "com êxito", "Falha".

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249).

#### <a name="subscription-key"></a>Chave de subscrição

Chave de subscrição é uma cadeia de caracteres que tem de especificar como um parâmetro de cadeia de caracteres de consulta para invocar a API Face. A chave de subscrição pode ser encontrada na página minhas inscrições, depois de iniciar sessão portal de serviços cognitivos da Microsoft. Haverá duas chaves associadas com cada subscrição: uma chave primária e uma chave secundária. Podem ser utilizados para invocar a API de forma idêntica. Precisa de proteger as chaves de subscrição e pode voltar a gerar chaves de subscrição em qualquer altura a partir também a página minhas inscrições.

## <a name="t"></a>T

#### <a name="train-largefacelistlargepersongrouppersongroup"></a>Treinar (LargeFaceList/LargePersonGroup/PersonGroup)

Esta API é utilizada para processar previamente os [LargeFaceList](#LargeFaceList)/[LargePersonGroup](#LargePersonGroup)/[PersonGroup](#PersonGroup) para garantir que o [encontrar Semelhante](#Find-Similar)/[identificação](#Identification) desempenho. Se o treinamento não é operado, ou o [estado de treinamento](#Status-Train) não é mostrado como foi concluída com êxito, a identificação para este PersonGroup irá resultar na falha.

Para obter mais informações, consulte a documentação de referência: [LargeFaceList - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158422d2de3616c086f2d1), [LargePersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae2d16ac60f11b48b5aa4), [PersonGroup - Train](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

## <a name="u"></a>U

#### <a name="userdatauser-data"></a>Dados de UserData/utilizador

Os dados de utilizador são informações extras associadas [pessoa](#Person) e [PersonGroup](#PersonGroup)/[LargePersonGroup](#LargePersonGroup). Dados de utilizador são definidos pelos utilizadores para tornar mais fácil de utilizar, compreender e lembre-se os dados.

Para obter mais informações, consulte a documentação de referência: [LargePersonGroup – crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d), [LargePersonGroup – atualização](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acfc83a7b9412a4d53f3f), [LargePersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40), [LargePersonGroup pessoa - atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ade043a7b9412a4d53f41), [PersonGroup - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244), [PersonGroup - Update](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524a), [PersonGroup pessoa - criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), [PersonGroup pessoa - atualizar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395242).

## <a name="v"></a>V

#### <a name="verification"></a>Verificação

Esta API é utilizada para verificar se dois rostos são os mesmos ou não. Os dois rostos são representados como IDs de rostos no pedido. Verificar os resultados contêm um campo Booleano indicando o mesmo se for VERDADEIRO (isIdentical) e um campo de número ([confiança](#Confidence)) que indica o nível de confiança.

Para obter mais informações, consulte a documentação de referência: [Se deparam - verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
