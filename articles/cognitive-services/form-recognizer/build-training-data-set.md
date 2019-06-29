---
title: Como criar um conjunto de dados de treinamento para um modelo personalizado - reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que seu conjunto de dados de treinamento está otimizado para preparar um modelo de formulário reconhecedor.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 611d5f7983c61fab12c55a46fedf35a3c420c4c8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454820"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Criar um conjunto de dados de treinamento para um modelo personalizado

Quando utiliza o modelo personalizado do reconhecedor de formulário, fornecer seus próprios dados de treinamento para que o modelo pode preparar para seus formulários específicos da indústria. Pode preparar um modelo com cinco formulários preenchidos ou um formulário vazio (incluem a palavra "vazia" no nome do ficheiro) mais dois formulários preenchidos. Mesmo que tenha suficiente formulários preenchidos para treinar com, adicionar um formulário vazio ao seu conjunto de dados de treinamento pode melhorar a precisão do modelo.

## <a name="training-data-tips"></a>Dicas de dados de treinamento

É importante utilizar um conjunto de dados otimizado para treinamento. Utilize as sugestões seguintes para se certificar de que obtém os melhores resultados do [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) operação:

* Se possível, utilize documentos baseados em texto PDF em vez de documentos baseada em imagem. PDFs digitalizados são processadas como imagens.
* Se os tiver disponíveis, utilize um formulário vazio e dois formulários preenchidos.
* Para formulários preenchidos, utilize os exemplos que tenham todas dos respetivos campos preenchidos.
* Use os formulários com valores diferentes em cada campo.
* Se as imagens de formulário de qualidade mais baixa, utilize um conjunto de dados maior (10 a 15 imagens, por exemplo).

## <a name="general-input-requirements"></a>Requisitos gerais de entrada

Certifique-se de que o seu conjunto de dados de treinamento também segue os requisitos de entrada para todos os conteúdos do reconhecedor de formulário.
[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar um conjunto de dados de treinamento, seguir um guia de introdução para preparar um modelo de formulário reconhecedor personalizado e começar a usá-lo em seus formulários.

* [Quickstart: Preparar um modelo e extrair dados de formulário com o cURL](./quickstarts/curl-train-extract.md)
* [Quickstart: Preparar um modelo e extrair dados de formulário com a API REST com Python](./quickstarts/python-train-extract.md)

