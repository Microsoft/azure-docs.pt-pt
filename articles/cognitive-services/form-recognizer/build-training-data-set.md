---
title: Como criar um conjunto de dados de treinamento para um reconhecedor de formulário de modelo personalizado
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que o conjunto de dados de treinamento seja otimizado para treinar um modelo de reconhecimento de formulário.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75380631"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Criar um conjunto de dados de treinamento para um modelo personalizado

Ao usar o modelo personalizado do reconhecedor de formulário, você fornece seus próprios dados de treinamento para que o modelo possa treinar seus formulários específicos do setor. Você pode treinar um modelo com cinco formulários preenchidos ou um formulário vazio (você deve incluir a palavra "Empty" no nome do arquivo) mais dois formulários preenchidos. Mesmo que você tenha formulários preenchidos suficientes para treinar, adicionar um formulário vazio ao seu conjunto de dados de treinamento pode melhorar a precisão do modelo.

Se você quiser usar dados de treinamento rotulados manualmente, deverá começar com pelo menos cinco formulários do mesmo tipo. Você ainda pode usar formulários sem rótulo e um formulário vazio no mesmo conjunto de dados.

## <a name="training-data-tips"></a>Dicas de dados de treinamento

É importante usar um conjunto de dados otimizado para treinamento. Use as dicas a seguir para garantir que você obtenha os melhores resultados da operação [treinar modelo personalizado](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) :

* Se possível, use documentos PDF baseados em texto em vez de documentos baseados em imagem. Os PDFs digitalizados são tratados como imagens.
* Para formulários preenchidos, use exemplos que tenham todos os seus campos preenchidos.
* Utilize formulários com diferentes valores em cada campo.
* Se suas imagens de formulário forem de qualidade inferior, use um conjunto de dados maior (10-15 imagens, por exemplo).
* O tamanho total do conjunto de dados de treinamento pode ser de até 500 páginas.

## <a name="general-input-requirements"></a>Requisitos de entrada gerais

Verifique se o conjunto de dados de treinamento também segue os requisitos de entrada para todo o conteúdo do reconhecedor de formulário. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Carregue seus dados de treinamento

Depois de reunir o conjunto de documentos de formulário que você usará para treinamento, você precisa carregá-lo em um contêiner de armazenamento de BLOBs do Azure. Se você não souber como criar uma conta de armazenamento do Azure com um contêiner, seguindo o guia de [início rápido do armazenamento do Azure para Portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizar seus dados em subpastas (opcional)

Por padrão, a API de [modelo personalizado Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) só usará documentos de formulário localizados na raiz do seu contêiner de armazenamento. No entanto, você pode treinar com dados em subpastas se especificá-los na chamada à API. Normalmente, o corpo da chamada de [modelo personalizado Train](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) tem o seguinte formulário, em que `<SAS URL>` é a URL de assinatura de acesso compartilhado do seu contêiner:

```json
{
  "source":"<SAS URL>"
}
```

Se você adicionar o conteúdo a seguir ao corpo da solicitação, a API será treinada com documentos localizados em subpastas. O campo `"prefix"` é opcional e limitará o conjunto de dados de treinamento a arquivos cujos caminhos começam com a cadeia de caracteres especificada. Portanto, um valor de `"Test"`, por exemplo, fará com que a API examine apenas os arquivos ou pastas que começam com a palavra "Test".

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

Agora que você aprendeu como criar um conjunto de dados de treinamento, siga um guia de início rápido para treinar um modelo de reconhecedor de formulário personalizado e comece a usá-lo em seus formulários.

* [Início rápido: treinar um modelo e extrair dados de formulário usando a ondulação](./quickstarts/curl-train-extract.md)
* [Início rápido: treinar um modelo e extrair dados de formulário usando a API REST com Python](./quickstarts/python-train-extract.md)
* [Treine com rótulos usando a API REST e o Python](./quickstarts/python-labeled-data.md)