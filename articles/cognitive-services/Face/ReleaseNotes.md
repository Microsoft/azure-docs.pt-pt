---
title: Notas de versão-serviço de face
titleSuffix: Azure Cognitive Services
description: As notas de versão do serviço de face incluem um histórico das alterações de versão para várias versões.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76165875"
---
# <a name="face-release-notes"></a>Notas de versão facial

Este artigo pertence à versão do serviço de face 1,0.

### <a name="release-changes-in-june-2019"></a>Alterações de versão em junho de 2019

* Foi adicionado um novo modelo de detecção facial com precisão aprimorada em faces pequenas, obstruído e borradas. Use-a por meio [de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facelist-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)adicionar face, [LargeFaceList-Add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), pessoa do usuário [-Adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) especificando o novo nome do modelo de detecção facial `detection_02` no parâmetro `detectionModel`. Mais detalhes sobre [como especificar um modelo de detecção](Face-API-How-to-Topics/specify-detection-model.md).

### <a name="release-changes-in-april-2019"></a>Alterações de versão em abril de 2019

* Maior precisão geral dos atributos `age` e `headPose`. O atributo `headPose` também é atualizado com o valor de `pitch` habilitado agora. Use esses atributos especificando-os no parâmetro `returnFaceAttributes` do parâmetro [face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Melhoria na velocidade de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Facelist-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [pessoa de](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) pessoa física-adicionar face e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Alterações de versão em março de 2019

* Adicionou um novo modelo de reconhecimento facial com precisão aprimorada. Use-a por meio [de detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [facelist-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-criar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [Person-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) Create e [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) especificando o novo nome do modelo de reconhecimento facial `recognition_02` no parâmetro `recognitionModel`. Mais detalhes sobre [como especificar um modelo de reconhecimento](Face-API-How-to-Topics/specify-recognition-model.md).

### <a name="release-changes-in-january-2019"></a>Alterações de versão em janeiro de 2019

* Adicionado recurso de instantâneo para dar suporte à migração de dados entre assinaturas: [instantâneo](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Mais detalhes [sobre como migrar os dados de face para uma assinatura facial diferente](Face-API-How-to-Topics/how-to-migrate-face-data.md).

### <a name="release-changes-in-october-2018"></a>Alterações de versão em outubro de 2018

* Descrição refinada para `status`, `createdDateTime`, `lastActionDateTime`e `lastSuccessfulTrainingDateTime` em [pessoa-obter](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247)status de treinamento, [LargePersonGroup-obter status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)de treinamento e [LargeFaceList-obter status de treinamento](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Alterações de versão em maio de 2018

* Melhorou o atributo `gender` significativamente e também melhorou os atributos `age`, `glasses`, `facialHair`, `hair``makeup`. Use-os por meio de `returnFaceAttributes` parâmetro [de detecção de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . 

* Maior limite de tamanho de arquivo de imagem de entrada de 4 MB a 6 MB em [face para detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Facelist-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)face, [LargeFaceList-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [pessoa-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) adicionar face e [LargePersonGroup pessoa-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Alterações de versão em março de 2018

* Contêiner de milhão de escala adicionado: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Mais detalhes sobre [como usar o recurso de grande escala](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Maior [face-identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) o parâmetro `maxNumOfCandidatesReturned` de [1, 5] para [1, 100] e o padrão como 10.

### <a name="release-changes-in-may-2017"></a>Alterações de versão em maio de 2017

* Adicionados os atributos `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure`e `noise` no parâmetro de `returnFaceAttributes` de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

* As pessoas de 10K com suporte em uma pessoa e uma [identificação facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Paginação com suporte na [lista de pessoas pessoais](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) com parâmetros opcionais: `start` e `top`.

* Simultaneidade com suporte na adição/exclusão de rostos em diferentes FaceLists e pessoas diferentes em Person.

### <a name="release-changes-in-march-2017"></a>Alterações de versão em março de 2017
* Adicionado `emotion` atributo no parâmetro de `returnFaceAttributes` de [detecção de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

* A face corrigida não pôde ser redetectada com o retângulo retornado da [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como `targetFace` na [Facelist-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) face e pessoa [-Adicionar rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* Corrigido o tamanho de face detectável para garantir que ele seja estritamente entre 36x36 e 4096x4096 pixels.

### <a name="release-changes-in-november-2016"></a>Alterações de versão em novembro de 2016
* Adição de Armazenamento Facial assinatura padrão para armazenar faces persistentes adicionais ao usar o usuário da pessoa [-Adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ou [Facelist-adicionar face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) para identificação ou correspondência de similaridade. As imagens armazenadas são cobradas a 0,5 $ por 1000 rostos e esta tarifa é calculada diariamente. As assinaturas de camada gratuita continuam limitadas a 1.000 total de pessoas.

### <a name="release-changes-in-october-2016"></a>Alterações de versão em outubro de 2016
* Foi alterada a mensagem de erro de mais de uma face no targetFace de ' há mais de uma face na imagem ' para ' há mais de uma face na imagem ' na [facelist-adicionar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) face e [pessoa-adicionar rosto](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Alterações de versão em julho de 2016
* Autenticação de objeto de face para pessoa com suporte na [verificação de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* Parâmetro `mode` opcional adicionado habilitando a seleção de dois modos de trabalho: `matchPerson` e `matchFace` em [face-localizar semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e padrão é `matchPerson`.

* Parâmetro opcional `confidenceThreshold` adicionado para o usuário definir o limite de se uma face pertence a um objeto Person em [face de identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Foram adicionados `start` opcionais e parâmetros de `top` na [lista de pessoas](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) para permitir que o usuário especifique o ponto de partida e o número total de PersonGroups a serem listados.

### <a name="v10-changes-from-v0"></a>V 1.0 alterações de V0
* Ponto de extremidade raiz de serviço atualizado de ```https://westus.api.cognitive.microsoft.com/face/v0/``` para ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Alterações aplicadas a: [face para detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [face para identificação](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [face-localização semelhante](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [grupo de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Atualizado o tamanho de face mínimo detectável para 36x36 pixels. Faces menores que 36x36 pixels não serão detectadas.

* Preterimos os dados do The Person e Person no V0 facial. Esses dados não podem ser acessados com o serviço de face V 1.0.

* Preterido o ponto de extremidade V0 de API de Detecção Facial em 30 de junho de 2016.
