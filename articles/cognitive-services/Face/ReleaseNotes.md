---
title: Quais as novidades no serviço Face?
titleSuffix: Azure Cognitive Services
description: As notas de lançamento para o serviço Face incluem um histórico de alterações de lançamento para várias versões.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: pafarley
ms.openlocfilehash: 8a409d66065a7ccd2b34365bd565202f207ac0e8
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97560751"
---
# <a name="whats-new-in-face-service"></a>Quais as novidades no serviço Face?

O serviço Azure Face é atualizado de forma contínua. Utilize este artigo para se manter atualizado com melhorias de funcionalidades, correções e atualizações de documentação.

## <a name="november-2020"></a>Novembro de 2020
* Publicou uma aplicação de inscrição facial de amostra para demonstrar as melhores práticas para estabelecer um consentimento significativo e criar sistemas de reconhecimento facial de alta precisão através de inscrições de alta qualidade. A amostra de código aberto pode ser encontrada no Guia de [aplicações build a e](build-enrollment-app.md) no [GitHub,](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample)pronto para os desenvolvedores implementarem ou personalizarem.  

## <a name="june-2019"></a>Junho de 2019

* Adicionou um novo modelo de deteção facial com uma precisão melhorada em faces pequenas, laterais, obstruídas e desfocadas. Utilize-o através do [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) especificando o novo nome do modelo de deteção facial no `detection_02` `detectionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de deteção.](Face-API-How-to-Topics/specify-detection-model.md)

## <a name="april-2019"></a>Abril de 2019

* Melhor precisão geral dos `age` `headPose` atributos e atributos. O `headPose` atributo também é atualizado com o valor `pitch` ativado agora. Utilize estes atributos especificando-os no `returnFaceAttributes` parâmetro do Face - [Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` o parâmetro. 

* Melhor velocidade do [rosto - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2019"></a>Março de 2019

* Adicionou um novo modelo de reconhecimento facial com uma precisão melhorada. Utilize-o através do [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) and [LargePersonGroup - Criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) especificando o novo nome do modelo de reconhecimento facial no `recognition_02` `recognitionModel` parâmetro. Mais detalhes em [Como especificar um modelo de reconhecimento.](Face-API-How-to-Topics/specify-recognition-model.md)

## <a name="january-2019"></a>Janeiro de 2019

* Funcionalidade Snapshot adicionada para apoiar a migração de dados através de subscrições: [Snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mais detalhes em [Como Migrar os dados do seu rosto para uma subscrição face diferente.](Face-API-How-to-Topics/how-to-migrate-face-data.md)

## <a name="october-2018"></a>Outubro de 2018

* Descrição refinada para `status` , , e em `createdDateTime` `lastActionDateTime` `lastSuccessfulTrainingDateTime` [PersonGroup - Obter Estado de Formação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Obter Estado de Formação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5), e [LargeFaceList - Obter Estado de Formação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>Maio de 2018

* Melhorou `gender` significativamente o atributo e também melhorou, `age` `glasses` `facialHair` `hair` `makeup` atributos. Use-os através [do face - Detete o](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parâmetro. 

* Aumento do limite de tamanho do ficheiro de imagem de entrada de 4 MB a 6 MB no [rosto - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Março de 2018

* Recipiente Million-Scale adicionado: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mais detalhes sobre [como utilizar a funcionalidade de grande escala.](Face-API-How-to-Topics/how-to-use-large-scale.md)

* Rosto Aumentado [- Identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` o parâmetro de [1, 5] para [1,100] e o padrão para 10.

## <a name="may-2017"></a>Maio de 2017

* Adicionado `hair` , `makeup` e `accessory` `occlusion` `blur` `exposure` `noise` atributos em Face - [Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parâmetro.

* Suportado 10K pessoas num PersonGroup e [Face - Identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Paginação suportada em [PersonGroup Person - Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) com parâmetros opcionais: `start` e `top` .

* Apoio de concordância na adição/eliminação de rostos contra diferentes FaceLists e diferentes pessoas no PersonGroup.

## <a name="march-2017"></a>Março de 2017
* `emotion`Atributo adicionado no face - [Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parâmetro.

* Fixar o rosto não pôde ser redetecido com retângulo devolvido do [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como em `targetFace` [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) and [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fixo o tamanho do rosto detetável para se certificar de que está estritamente entre 36x36 a 4096x4096 pixels.

## <a name="november-2016"></a>Novembro de 2016
* Acedida subscrição Padrão de Armazenamento facial para armazenar rostos adicionais persistidos ao utilizar [persongroup person person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [FaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) para identificação ou semelhança. As imagens armazenadas são cobradas a 0,5 $ por 1000 rostos e esta tarifa é calculada diariamente. As subscrições de nível livre continuam a ser limitadas a 1.000 pessoas no total.

## <a name="october-2016"></a>Outubro de 2016
* Alterou a mensagem de erro de mais de uma face no targetFace de 'Existem mais de um rosto na imagem' para 'Há mais de uma face na imagem' na [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [PersonGroup Person Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

## <a name="july-2016"></a>Julho de 2016
* Autenticação de objetos suportados Face to Person no [Rosto - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Parâmetro opcional adicionado `mode` que permite a seleção de dois modos de trabalho: `matchPerson` e no Face - Encontrar `matchFace` [Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e padrão é `matchPerson` .

* Parâmetro opcional adicionado `confidenceThreshold` para o utilizador definir o limiar de se uma face pertence a um objeto de Pessoa no Rosto [- Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Adicionado opcional `start` e `top` parâmetros no [PersonGroup - Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) para permitir ao utilizador especificar o ponto de partida e o número total de Grupos de Pessoas para listar.

## <a name="v10-changes-from-v0"></a>V1.0 muda de V0

* Ponto final raiz de serviço atualizado ```https://westus.api.cognitive.microsoft.com/face/v0/``` de . ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` Alterações aplicadas a: [Face - Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), Rosto - [Identificar,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) [Rosto - Encontrar Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e Cara - [Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Atualize o tamanho mínimo do rosto detetável para 36x36 pixels. Não serão detetados rostos com menos de 36x36 pixels.

* Precava os dados do PersonGroup e da Pessoa no Face V0. Estes dados não podem ser acedidos com o serviço Face V1.0.

* Depreifou o ponto final V0 da Face API em 30 de junho de 2016.
