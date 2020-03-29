---
title: Notas de lançamento - Serviço de rosto
titleSuffix: Azure Cognitive Services
description: As notas de lançamento para o serviço Face incluem um histórico de alterações de lançamento para várias versões.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165875"
---
# <a name="face-release-notes"></a>Notas de lançamento do rosto

Este artigo diz respeito à versão 1.0 do serviço Face.

### <a name="release-changes-in-june-2019"></a>Mudanças de lançamento em junho de 2019

* Adicione um novo modelo de deteção facial com uma precisão melhorada em rostos pequenos, laterais, obstruídos e borrados. Use-o através [do Rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), Person Group Person - Add [Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) especificando o novo nome `detection_02` do modelo de deteção facial no `detectionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de deteção](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Mudanças de lançamento em abril de 2019

* Melhorprecisão geral dos `age` `headPose` atributos e atributos. O `headPose` atributo também é `pitch` atualizado com o valor ativado agora. Utilize estes atributos especificando-os no parâmetro face - `returnFaceAttributes` [Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` o parâmetro. 

* Velocidade melhorada do [rosto - Detetar,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) [FaceList - Adicionar face,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [BigFaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), Person Group - Adicionar [face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e pessoa do Grupo [De PessoaS Grandes - Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Mudanças de lançamento em março de 2019

* Adicioneum novo modelo de reconhecimento facial com maior precisão. Use-o através do [Face - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) and [LargePersonGroup - Crie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) especificando o novo nome `recognition_02` do modelo de reconhecimento facial no `recognitionModel` parâmetro. Mais detalhes sobre [como especificar um modelo de reconhecimento](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Mudanças de lançamento em janeiro de 2019

* Recurso Snapshot adicionado para apoiar a migração de dados através de subscrições: [Snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mais detalhes sobre [Como Migrar os seus dados faciais para uma subscrição face diferente](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Mudanças de lançamento em outubro de 2018

* Descrição refinada `lastActionDateTime`para `lastSuccessfulTrainingDateTime` `status`, `createdDateTime`, , e no [PersonGroup - Obter Estatuto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)de Formação , [LargePersonGroup - Obter Estado](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)de Formação , e [BigFaceList - Obter Estatuto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)de Formação .

### <a name="release-changes-in-may-2018"></a>Mudanças de lançamento em maio de 2018

* Atributo `gender` melhorado significativamente e `age`também `glasses` `facialHair`melhorado, `makeup` , , atributos. `hair` Use-os através [do Face - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` o parâmetro. 

* Limite de tamanho de ficheiro de imagem de entrada aumentado de 4 MB para 6 MB no [rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Adicionar Face,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) [LargeFaceList - Adicionar Face,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3) [Person Do Grupo de Pessoas - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e Pessoa do Grupo De [PessoaS Maiores - Adicionar Rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Mudanças de lançamento em março de 2018

* Recipiente de escala acrescida: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mais detalhes sobre [como utilizar a funcionalidade em larga escala](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Face Aumentada [- Identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` o parâmetro de [1, 5] para [1,100] e padrão para 10.

### <a name="release-changes-in-may-2017"></a>Mudanças de lançamento em maio de 2017

* `hair`Adicionado, `makeup` `accessory`, `occlusion` `blur`, `exposure`, `noise` e atributos no [Rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` o parâmetro.

* Pessoas suportadas em 10K num PersonGroup e [Face - Identifiquem](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Paginação suportada em [PessoaGrupo Pessoa -](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) `start` Lista `top`com parâmetros opcionais: e .

* Conmoeda suportada na adição/abater rostos contra diferentes FaceLists e diferentes pessoas no PersonGroup.

### <a name="release-changes-in-march-2017"></a>Mudanças de lançamento em março de 2017
* Atributo adicionado `emotion` no [Rosto - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` o parâmetro.

* Fixo o rosto não poderia ser redetectado com retângulo devolvido do [Face - Detete](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como `targetFace` em [FaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e Pessoa do Grupo - Adicionar [Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Fixou o tamanho do rosto detetável para se certificar de que se encontra estritamente entre 36x36 e 4096x4096 pixels.

### <a name="release-changes-in-november-2016"></a>Mudanças de lançamento em novembro de 2016
* Acréscimo de subscrição face storage Standard para armazenar rostos persuinos adicionais ao usar [persongroup person - Adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [faceList - Adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) para identificação ou semelhança correspondente. As imagens armazenadas são cobradas a 0,5 $ por 1000 rostos e esta tarifa é calculada diariamente. As subscrições de nível livre continuam limitadas a 1.000 pessoas totais.

### <a name="release-changes-in-october-2016"></a>Mudanças de lançamento em outubro de 2016
* Alterou a mensagem de erro de mais de um rosto no alvoFace de 'Há mais de um rosto na imagem' para 'Há mais de um rosto na imagem' no [FaceList - Adicionar Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Mudanças de lançamento em julho de 2016
* Autenticação de objetos suportados face a pessoa no [rosto - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Parâmetro `mode` opcional adicionado que permite a seleção de dois modos de `matchPerson` trabalho: e `matchFace` no [Face - Encontre Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e padrão é `matchPerson`.

* Adicione `confidenceThreshold` um parâmetro opcional adicionado para o utilizador definir o limiar de se um rosto pertence a um objeto pessoa no [rosto - Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Adicionado `start` opcional `top` e parâmetros no [PersonGroup - Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) para permitir ao utilizador especificar o ponto de partida e o número total de PersonGroups para listar.

### <a name="v10-changes-from-v0"></a>V1.0 muda a partir de V0
* Ponto final de ```https://westus.api.cognitive.microsoft.com/face/v0/``` raiz ```https://westus.api.cognitive.microsoft.com/face/v1.0/```de serviço atualizado de . Alterações aplicadas a: [Face - Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), Rosto - [Identificar,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) [Rosto - Encontrar Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e Rosto - [Grupo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Atualizou o tamanho mínimo detetável do rosto para 36x36 pixels. Não serão detetados rostos inferiores a 36x36 pixels.

* Deprecatou os dados do PersonGroup e da Pessoa no Rosto V0. Estes dados não podem ser acedidos com o serviço Face V1.0.

* Deprecado o ponto final V0 da API face em 30 de junho de 2016.
