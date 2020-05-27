---
title: Como construir um conjunto de dados de treino para um modelo personalizado - Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Saiba como garantir que o seu conjunto de dados de treino está otimizado para treinar um modelo de Reconhecimento de Formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 9342d87318eb6a5248c75d2333fb5e2a4cbef8f4
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873300"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Construa um conjunto de dados de treino para um modelo personalizado

Quando utiliza o modelo personalizado Form Recogniser, fornece os seus próprios dados de formação para que o modelo possa treinar para as suas formas específicas da indústria. 

Se estiver a treinar sem etiquetas manuais, pode utilizar cinco formulários preenchidos ou um formulário vazio (deve incluir a palavra "vazio" no nome do ficheiro) mais dois formulários preenchidos. Mesmo que tenha formulários preenchidos suficientes, adicionar um formulário vazio ao seu conjunto de dados de treino pode melhorar a precisão do modelo.

Se pretender utilizar dados de treino manualmente rotulados, deve começar com pelo menos cinco formas preenchidas do mesmo tipo. Ainda pode utilizar formulários não rotulados e um formulário vazio para além do conjunto de dados necessário.

## <a name="training-data-tips"></a>Dicas de dados de formação

É importante usar um conjunto de dados otimizado para o treino. Utilize as seguintes dicas para garantir que obtém os melhores resultados da operação Modelo Personalizado do [Comboio:](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)

* Se possível, utilize documentos PDF baseados em texto em vez de documentos baseados em imagem. Os PDFs digitalizados são tratados como imagens.
* Para formulários preenchidos, use exemplos que tenham todos os seus campos preenchidos.
* Utilize formulários com diferentes valores em cada campo.
* Se as imagens do formulário forem de menor qualidade, utilize um conjunto de dados maior (10-15 imagens, por exemplo).
* O tamanho total do conjunto de dados de treino pode ser de até 500 páginas.

## <a name="general-input-requirements"></a>Requisitos gerais de entrada

Certifique-se de que o seu conjunto de dados de formação também segue os requisitos de entrada para todos os conteúdos do Reconhecimento de Formulários. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Faça upload dos seus dados de treino

Quando tiver reunido o conjunto de documentos de formulário que irá usar para treinar, precisa de o enviar para um recipiente de armazenamento de blob Azure. Se não sabe como criar uma conta de armazenamento Azure com um recipiente, seguindo o [quickstart de Armazenamento Azure para](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)o portal Azure .

Se pretender utilizar dados com etiqueta manual, terá também de carregar os *ficheiros .labels.json* e *.ocr.json* que correspondam aos documentos de treino. Pode utilizar a ferramenta de [rotulagem sample](./quickstarts/label-tool.md) (ou a sua própria UI) para gerar estes ficheiros.

### <a name="organize-your-data-in-subfolders-optional"></a>Organize os seus dados em subpastas (opcional)

Por padrão, a API do [Modelo Personalizado do Comboio](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) só utilizará documentos de formulário que estejam localizados na raiz do seu recipiente de armazenamento. No entanto, pode treinar com dados em subpastas se os especificar na chamada DaPI. Normalmente, o corpo da chamada modelo personalizada do [comboio](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) tem o seguinte formato, onde `<SAS URL>` está o URL de assinatura de acesso partilhado do seu recipiente:

```json
{
  "source":"<SAS URL>"
}
```

Se adicionar o seguinte conteúdo ao organismo de pedido, a API treinará com documentos localizados em subpastas. O campo é opcional e limitará o conjunto de `"prefix"` dados de treino a ficheiros cujos caminhos começam com a corda dada. Assim, um valor `"Test"` de, por exemplo, fará com que a API olhe apenas para os ficheiros ou pastas que começam com a palavra "Teste".

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

Agora que aprendeu a construir um conjunto de dados de treino, siga um início rápido para treinar um modelo personalizado de Reconhecimento de Formulários e comece a usá-lo nos seus formulários.

* [Treine um modelo e extrai dados de formulário usando cURL](./quickstarts/curl-train-extract.md)
* [Treine um modelo e extrate dados usando a REST API e Python](./quickstarts/python-train-extract.md)
* [Treine com etiquetas utilizando a ferramenta de rotulagem da amostra](./quickstarts/label-tool.md)
* [Preparar com etiquetas através da API REST e do Python](./quickstarts/python-labeled-data.md)
