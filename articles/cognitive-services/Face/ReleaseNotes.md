---
title: O que há de novo no serviço Face?
titleSuffix: Azure Cognitive Services
description: As notas de lançamento para o serviço Face incluem um histórico de alterações de lançamento para várias versões.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: pafarley
ms.openlocfilehash: f280e1a70ab595a87789c7514d8cce4fccbe8e84
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046900"
---
# <a name="whats-new-in-face-service"></a>O que há de novo no serviço Face?

O serviço Azure Face é atualizado de forma contínua. Utilize este artigo para se manter atualizado com melhorias de funcionalidades, correções e atualizações de documentação.

## <a name="february-2021"></a>Fevereiro de 2021

* Novo modelo de deteção de API face: O novo modelo de deteção 03 é o modelo de deteção mais preciso atualmente disponível. Se for um cliente novo, recomendamos a utilização deste modelo. A deteção 03 melhora tanto a recolha como a precisão em rostos mais pequenos encontrados dentro de imagens (64x64 pixels). As melhorias adicionais incluem uma redução global dos falsos positivos e uma melhor deteção das orientações faciais rotativas. Combinar a deteção 03 com o novo reconhecimento 04 proporcionará uma melhor precisão de reconhecimento também. Consulte [Especificar um modelo de deteção facial](./face-api-how-to-topics/specify-detection-model.md) para obter mais detalhes.
* Atributo da máscara facial: O atributo da máscara facial está disponível com o modelo de deteção 03 mais recente, juntamente com o atributo adicional `"noseAndMouthCovered"` que deteta se a máscara facial está usada como pretendido, cobrindo o nariz e a boca. Para utilizar a mais recente capacidade de deteção de máscaras, os utilizadores precisam de especificar o modelo de deteção no pedido da API: atribua a versão do modelo com o parâmetro _de detecçãoModel_ para `detection_03` . Consulte [Especificar um modelo de deteção facial](./face-api-how-to-topics/specify-detection-model.md) para obter mais detalhes.
* Novo Modelo de Reconhecimento da API Face: O novo modelo de reconhecimento 04 é o modelo de reconhecimento mais preciso atualmente disponível. Se for um novo cliente, recomendamos a utilização deste modelo para verificação e identificação. Melhora a precisão do reconhecimento 03, incluindo um reconhecimento melhorado para utilizadores inscritos que usam capas faciais (máscaras cirúrgicas, máscaras N95, máscaras de pano). Agora os clientes podem construir experiências de utilizador seguras e sem emenda que detetem se um utilizador inscrito está a usar uma capa facial com o mais recente modelo de deteção 03, e reconhecer quem são com o mais recente modelo de reconhecimento 04. Consulte [Especificar um modelo de reconhecimento facial](./face-api-how-to-topics/specify-recognition-model.md) para obter mais detalhes.


## <a name="january-2021"></a>Janeiro de 2021
* Mitigar a latência ao utilizar a API Face: A equipa Face publicou um novo artigo que detalha potenciais causas de latência ao utilizar o serviço e possíveis estratégias de mitigação. Consulte [a latência de Mitigate ao utilizar o serviço Face](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>Dezembro de 2020
* Configuração do cliente para armazenamento face ID: Enquanto o Serviço Facial não armazenar imagens do cliente, a(s) função(s) facial extraída será armazenada no servidor. O Face ID é um identificador da função facial e será usado no [Face - Identificar,](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) [Enfrentar - Verificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)e Face - Encontrar [Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). As características do rosto armazenadas expirarão e serão apagadas 24 horas após a chamada de deteção original. Os clientes podem agora determinar o tempo que estes IDs faciais estão em cache. O valor máximo ainda é de até 24 horas, mas um valor mínimo de 60 segundos pode agora ser definido. Os novos intervalos de tempo para os Face IDs estarem em cache é qualquer valor entre 60 segundos e 24 horas. Mais detalhes podem ser encontrados na referência [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) API (o parâmetro *faceIdTimeToLive).*

## <a name="november-2020"></a>Novembro de 2020
* Publicou uma aplicação de inscrição facial de amostra para demonstrar as melhores práticas para estabelecer um consentimento significativo e criar sistemas de reconhecimento facial de alta precisão através de matrículas de alta qualidade. A amostra de código aberto pode ser encontrada no Guia de [aplicações build a e](build-enrollment-app.md) no [GitHub,](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample)pronto para os desenvolvedores implementarem ou personalizarem. 

## <a name="august-2020"></a>Agosto de 2020
* Encriptação gerida pelo cliente de dados em repouso: O serviço Face encripta automaticamente os seus dados ao persistência na nuvem. A encriptação do serviço Face protege os seus dados para ajudá-lo a cumprir os seus compromissos de segurança organizacional e conformidade. Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também uma nova opção para gerir a sua subscrição com as suas próprias chaves chamadas chaves geridas pelo cliente (CMK). Mais detalhes podem ser encontrados nas [teclas geridas pelo Cliente](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>Abril de 2020
* Novo Modelo de Reconhecimento de API: O novo modelo de reconhecimento 03 é o modelo mais preciso atualmente disponível. Se for um novo cliente, recomendamos a utilização deste modelo. O reconhecimento 03 proporcionará uma maior precisão tanto para comparações de semelhança como para comparações de pessoas. Mais detalhes podem ser encontrados em [Especificar um modelo de reconhecimento facial.](./face-api-how-to-topics/specify-recognition-model.md)

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

* Adicionado `hair` , , , , , , e `makeup` `accessory` `occlusion` `blur` `exposure` `noise` atributos em Face - [Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parâmetro.

* Suportado 10K pessoas num PersonGroup e [Face - Identifique](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Paginação suportada em [PersonGroup Person - Lista](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) com parâmetros opcionais: `start` e `top` .

* Apoio de concordância na adição/eliminação de rostos contra diferentes FaceLists e diferentes pessoas no PersonGroup.

## <a name="march-2017"></a>Março de 2017
* `emotion`Atributo adicionado no face - [Detetar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` parâmetro.

* Fixar o rosto não pôde ser redetecido com retângulo devolvido do [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) como em `targetFace` [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) and [PersonGroup Person Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

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