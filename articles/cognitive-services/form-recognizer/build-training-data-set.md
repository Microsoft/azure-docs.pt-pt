---
title: Como construir um conjunto de dados de formação para um modelo personalizado - Form Recogniser
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que o seu conjunto de dados de treino está otimizado para a formação de um modelo de Reconhecimento de Formulários.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: lajanuar
ms.openlocfilehash: b33ac3cb710a2d2a9d92efadf14dc829cb5da6e8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467498"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Construa um conjunto de dados de formação para um modelo personalizado

Ao utilizar o modelo personalizado Form Recogniser, fornece os seus próprios dados de formação para a operação [Do Modelo Personalizado de Comboio,](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) para que o modelo possa treinar para os seus formulários específicos da indústria. Siga este guia para aprender a recolher e preparar dados para treinar o modelo de forma eficaz.

Precisa de pelo menos cinco formas preenchidas do mesmo tipo.

Se quiser utilizar dados de treino etiquetados manualmente, deve começar com pelo menos cinco formas preenchidas do mesmo tipo. Ainda pode utilizar formulários não rotulados para além do conjunto de dados necessário.

## <a name="custom-model-input-requirements"></a>Requisitos de entrada de modelo personalizado

Em primeiro lugar, certifique-se de que o seu conjunto de dados de treino segue os requisitos de entrada para o Reconhecimento de Formulários.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Dicas de dados de formação

Siga estas dicas adicionais para otimizar ainda mais o seu conjunto de dados para o treino.

* Se possível, utilize documentos PDF baseados em texto em vez de documentos baseados em imagem. Os PDFs digitalizados são tratados como imagens.
* Para formulários preenchidos, use exemplos que tenham todos os seus campos preenchidos.
* Utilize formulários com diferentes valores em cada campo.
* Se as imagens do seu formulário forem de menor qualidade, utilize um conjunto de dados maior (10-15 imagens, por exemplo).

## <a name="upload-your-training-data"></a>Faça upload dos seus dados de formação

Quando tiver reunido o conjunto de documentos de formulário que vai usar para o treino, tem de o enviar para um recipiente de armazenamento de bolhas Azure. Se não souber como criar uma conta de armazenamento Azure com um recipiente, seguindo o [quickstart de Armazenamento Azure para o portal Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Utilize o nível de desempenho padrão.

Se quiser utilizar dados etiquetados manualmente, também terá de carregar o *.labels.js* e *.ocr.jsem* ficheiros que correspondam aos seus documentos de treino. Pode utilizar a [ferramenta de rotulagem sample](./quickstarts/label-tool.md) (ou a sua própria UI) para gerar estes ficheiros.

### <a name="organize-your-data-in-subfolders-optional"></a>Organize os seus dados em sub-dobradeiras (opcional)

Por predefinição, a [API do Modelo Personalizado do Comboio](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) apenas utilizará documentos de formulário que estejam localizados na raiz do seu recipiente de armazenamento. No entanto, pode treinar com dados em sub-dobradeiras se o especificar na chamada API. Normalmente, o corpo da chamada [Train Custom Model](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync) tem o seguinte formato, onde está o URL de assinatura de acesso partilhado do seu `<SAS URL>` recipiente:

```json
{
  "source":"<SAS URL>"
}
```

Se adicionar o seguinte conteúdo ao organismo de pedido, a API irá treinar com documentos localizados em subversões. O `"prefix"` campo é opcional e irá limitar o conjunto de dados de treino a ficheiros cujos caminhos começam com a cadeia dada. Assim, um valor `"Test"` de, por exemplo, fará com que a API olhe apenas para os ficheiros ou pastas que começam com a palavra "Teste".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a construir um conjunto de dados de treino, siga um quickstart para treinar um modelo personalizado de Reconhecimento de Formulários e comece a usá-lo nos seus formulários.

* [Treine um modelo e extraia dados de formulário usando a biblioteca do cliente ou REST API](./quickstarts/client-library.md)
* [Treine com etiquetas usando a ferramenta de rotulagem da amostra](./quickstarts/label-tool.md)

## <a name="see-also"></a>Ver também

* [O que é o Reconhecedor de Formato?](./overview.md)