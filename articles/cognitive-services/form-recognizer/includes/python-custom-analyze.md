---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446417"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analisar formulários para pares de chave-valor e tabelas

Em seguida, você usará seu modelo treinado recentemente para analisar um documento e extrair pares de chave-valor e tabelas dele. Chame a API de **[análise de formulário](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** executando o código a seguir em um novo script Python. Antes de executar o script, faça estas alterações:

1. Substitua `<file path>` pelo caminho do arquivo do formulário (por exemplo, C:\temp\File.pdf). Essa também pode ser a URL de um arquivo remoto. Para este guia de início rápido, você pode usar os arquivos na pasta **Test** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Substitua `<model_id>` pela ID do modelo que você recebeu na seção anterior.
1. Substitua `<endpoint>` pelo ponto de extremidade obtido com a chave de assinatura do reconhecedor de formulário. Você pode encontrá-lo na guia **visão geral** de recursos do reconhecedor de formulário.
1. Substitua `<file type>` pelo tipo de arquivo. Tipos com suporte: `application/pdf`, `image/jpeg`, `image/png``image/tiff`.
1. Substitua `<subscription key>` pela sua chave de subscrição.

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<endpoint>"
    apim_key = "<subsription key>"
    model_id = "<model_id>"
    post_url = endpoint + "/formrecognizer/v2.0-preview/custom/models/%s/analyze" % model_id
    source = r"<file path>"
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Salve o código em um arquivo com uma extensão. py. Por exemplo, *Form-Recognizer-Analyze.py*.
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-analyze.py`.

Ao chamar a API de **análise de formulário** , você receberá uma resposta de `201 (Success)` com um cabeçalho **de local de operação** . O valor desse cabeçalho é uma ID que você usará para acompanhar os resultados da operação de análise. O script acima imprime o valor desse cabeçalho no console.

## <a name="get-the-analyze-results"></a>Obter os resultados da análise

Adicione o código a seguir à parte inferior do seu script Python. Isso usa o valor de ID da chamada anterior em uma nova chamada à API para recuperar os resultados da análise. A operação **analisar formulário** é assíncrona, portanto, esse script chama a API em intervalos regulares até que os resultados estejam disponíveis. É recomendável um intervalo de um segundo ou mais.

```python 
n_tries = 15
n_try = 0
wait_sec = 5
max_wait_sec = 60
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = resp.json()
        if resp.status_code != 200:
            print("GET analyze results failed:\n%s" % json.dumps(resp_json))
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Analysis succeeded:\n%s" % json.dumps(resp_json))
            quit()
        if status == "failed":
            print("Analysis failed:\n%s" % json.dumps(resp_json))
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1
        wait_sec = min(2*wait_sec, max_wait_sec)     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
print("Analyze operation did not complete within the allocated time.")
```
