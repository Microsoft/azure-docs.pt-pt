---
title: 'Quickstart: Extrair dados do cartão de visita usando Python - Form Recogniser'
titleSuffix: Azure Cognitive Services
description: Neste arranque rápido, você usará o Formulário Reconhecendo API rest com Python para extrair dados de imagens de cartões de visita em inglês.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: aa16952d2b2dff6f69abfc37090a9e00b7d48a27
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751126"
---
# <a name="quickstart-extract-business-card-data-using-the-form-recognizer-rest-api-with-python"></a>Quickstart: Extrair dados do cartão de visita utilizando a API do Reconhecimento de Formulários REST com Python

Neste arranque rápido, você usará o API API do Reconhecimento de FormulárioS Azure com Python para extrair e identificar informações relevantes em cartões de visita.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este arranque rápido, você deve ter:
- [Python](https://www.python.org/downloads/) instalado (se quiser executar a amostra localmente).
- Um URL para uma imagem de um cartão de visita. Pode utilizar uma [imagem de amostra](../media/business-card-english.jpg) para este arranque rápido.

> [!NOTE]
> Este quickstart utiliza uma imagem de cartão de visita remota acedida por URL. Para utilizar ficheiros locais, consulte a [documentação de referência.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)

## <a name="create-a-form-recognizer-resource"></a>Criar um recurso de reconhecimento de formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-a-business-card"></a>Analisar um cartão de visita

Para começar a analisar um cartão de visita, ligue para a API do **[Cartão De Negócios analisando](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)** o script Python abaixo. Antes de executar o script, faça estas alterações:

1. `<Endpoint>`Substitua-o pelo ponto final que obteve com a subscrição do Form Recogniser.
1. `<your business card URL>`Substitua-o pelo endereço URL de uma imagem do cartão de visita.
1. `<subscription key>`Substitua-a pela chave de subscrição que copiou do passo anterior.

    ```python
    ########### Python Form Recognizer Async Business cards #############

    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<subscription key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults"
    source = r"<path to your business card>"
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    
    params = {
        "includeTextDetails": True
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```

1. Guarde o código num ficheiro com uma extensão .py. Por exemplo, *form-recognizer-businesscards.py.*
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-businesscards.py`.

Receberá uma `202 (Success)` resposta que inclui um **cabeçalho operação-localização,** que o script irá imprimir na consola. Este cabeçalho contém um ID de funcionamento que pode utilizar para consultar o estado da operação assíncronea e obter os resultados. No seguinte valor de exemplo, a cadeia seguinte `operations/` é o ID de funcionamento.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.1/prebuilt/businessCard/analyzeresults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

## <a name="get-the-business-card-results"></a>Obtenha os resultados do cartão de visita

Depois de ter chamado a API do **Cartão de Visita,** ligue para a API do **[Resultado do Cartão de Visita da Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult)** para obter o estado da operação e os dados extraídos. Adicione o seguinte código na parte inferior do seu script Python. Isto utiliza o valor de ID de operação numa nova chamada da API. Este script chama a API em intervalos regulares até que os resultados estejam disponíveis. Recomendamos um intervalo de um segundo ou mais.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Business card results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Business card Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Guarde o guião.
1. Volte a usar o `python` comando para executar a amostra. Por exemplo, `python form-recognizer-businesscards.py`.

### <a name="examine-the-response"></a>Examinar a resposta
![Um cartão de visita da empresa Contoso](../media/business-card-english.jpg)

Esta amostra ilustra a saída JSON devolvida pelo Form Recogniser. Estas amostras foram truncadas para a legibilidade do exemplo.

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

O script imprimirá respostas à consola até que a operação **do Cartão De Visita de Análise** esteja concluída. O `"readResults"` nó contém todo o texto reconhecido. O texto é organizado por página, depois por linha, depois por palavras individuais. O `"documentResults"` nó contém os valores específicos do cartão de visita que o modelo descobriu. É aqui que encontrará pares de chaves/valor úteis como o nome da empresa, nome próprio, apelido, telefone e assim por diante.


## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, você usou o Formulário Reconhecendo API rest com Python para extrair o conteúdo de um cartão de visita. Em seguida, consulte a documentação de referência para explorar mais aprofundadamente a API do Reconhecimento de Formulários.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeReceiptAsync)
