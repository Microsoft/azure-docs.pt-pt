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
ms.openlocfilehash: ad9bba53390e3c4262f999ebcc57ab354f1e3d69
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537625"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Criar um conjunto de dados de treinamento para um modelo personalizado

Quando utiliza o modelo personalizado do reconhecedor de formulário, fornecer seus próprios dados de treinamento para que o modelo pode preparar para seus formulários específicos da indústria. Pode preparar um modelo com um formulário vazio (tem de incluir o palavra "vazio" no nome do ficheiro) ou com cinco formulários preenchidos mais dois formulários preenchidos. Mesmo que tenha suficiente formulários preenchidos para treinar com, adicionar um formulário vazio ao seu conjunto de dados de treinamento pode melhorar a precisão do modelo.

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

## <a name="upload-your-training-data"></a>Carregar os dados de treinamento

Quando Reunimos o conjunto de documentos de forma que pretende utilizar para formação, terá de carregá-lo para um contentor de armazenamento de Blobs do Azure. Se não sabe como criar uma conta de armazenamento do Azure com um contentor, a seguir a [início rápido de armazenamento do Azure para o portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizar os dados em subpastas (opcionais)

Por predefinição, o [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API irá utilizar apenas os documentos de forma que estão localizados na raiz do seu contentor de armazenamento. No entanto, pode treinar com dados em subpastas caso o especifique na chamada de API. Normalmente, o corpo da [Train Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) chamada faz com que o formulário seguinte, onde `<SAS URL>` é o URL de assinatura de acesso partilhado do seu contentor:

```json
{
  "source":"<SAS URL>"
}
```

Se adicionar o seguinte conteúdo para o corpo do pedido, a API irá preparar com documentos localizados em subpastas. O `"prefix"` campo é opcional e limitará o conjunto de dados de treinamento para arquivos cujos caminhos para começar, a determinada seqüência de caracteres. Portanto, um valor de `"Test"`, por exemplo, fará com que a API para ver apenas os ficheiros ou pastas que começam com a palavra "Teste".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar um conjunto de dados de treinamento, seguir um guia de introdução para preparar um modelo de formulário reconhecedor personalizado e começar a usá-lo em seus formulários.

* [Quickstart: Preparar um modelo e extrair dados de formulário com o cURL](./quickstarts/curl-train-extract.md)
* [Quickstart: Preparar um modelo e extrair dados de formulário com a API REST com Python](./quickstarts/python-train-extract.md)

