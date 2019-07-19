---
title: 'Início rápido: Analisar o conteúdo de texto em Python-Content Moderator'
titlesuffix: Azure Cognitive Services
description: Como analisar o conteúdo de texto para vários material objetáveis utilizando o SDK do moderador de conteúdo para Python
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: bb0e44f83e2101a7b21e7b7ec6fdc75974c6d6d8
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68333605"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Início rápido: Analisar o conteúdo de texto para materiais censuráveis em Python

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a utilizar o SDK de moderador de conteúdo para o Python. Vai aprender a executar a filtragem baseada em termos e a classificação de conteúdos de texto com o objetivo de moderar materiais potencialmente censuráveis.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
- A chave de uma subscrição do Content Moderator. Siga as instruções em [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Criar uma conta dos Serviços Cognitivos) para subscrever o Content Moderator e obter a sua chave.
- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-content-moderator-sdk"></a>Obtenha o SDK do Content Moderator

Instale o SDK de Python de moderador conteúdo ao abrir um prompt de comando e executar o seguinte comando:

```bash
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importar módulos

Criar um novo script de Python com o nome _ContentModeratorQS.py_ e adicione o código seguinte para importar as partes necessárias do SDK. O módulo de impressão bonita está incluído para uma resposta JSON mais fácil de ler.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=imports)]


## <a name="initialize-variables"></a>Inicializar variáveis

Em seguida, adicione as variáveis para o URL de ponto final e a chave de subscrição do Content Moderator. Adicione o nome `CONTENT_MODERATOR_SUBSCRIPTION_KEY` às suas variáveis de ambiente com sua chave de assinatura como seu valor. Para a URL do ponto de extremidade `CONTENT_MODERATOR_ENDPOINT` base, adicione às suas variáveis de ambiente com a URL específica da região como seu `https://westus.api.cognitive.microsoft.com`valor, por exemplo. Chaves de subscrição de avaliação gratuita são geradas na **westus** região.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=authentication)]

Uma cadeia de caracteres de texto com várias linhas de um arquivo será moderada. Inclua o arquivo [content_moderator_text_moderation. txt](https://github.com/Azure-Samples/cognitive-services-content-moderator-samples/blob/master/documentation-samples/python/content_moderator_text_moderation.txt) em sua pasta raiz local e adicione seu nome de arquivo às variáveis:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModerationFile)]

## <a name="query-the-moderator-service"></a>Consultar o serviço do moderador

Criar uma **ContentModeratorClient** instância com o seu URL de ponto final e a chave de subscrição. 

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=client)]

Em seguida, use seu cliente com sua instância **TextModerationOperations** do membro para chamar a API de moderação com a função `screen_text`. Consulte a **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python)** documentação para obter mais informações sobre como chamá-lo de referência.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/content_moderator_quickstart.py?name=textModeration)]

## <a name="check-the-printed-response"></a>Verificar a resposta impressa

Execute o exemplo e confirme a resposta. Após a conclusão bem-sucedida, retorna uma instância de **tela** . Um resultado bem-sucedido é mostrado abaixo:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, que desenvolveu um script de Python simples que utiliza o serviço do Content Moderator para devolver informações relevantes sobre um exemplo de texto especificado. Em seguida, saiba o que querem dizer os diferentes sinalizadores e as diferentes classificações, para determinar de que dados precisa e como é que a sua aplicação os deve processar.

> [!div class="nextstepaction"]
> [Text moderation guide](text-moderation-api.md) (Guia de moderação de textos)
