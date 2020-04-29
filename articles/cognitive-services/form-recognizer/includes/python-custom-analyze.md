---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/14/2019
ms.author: pafarley
ms.openlocfilehash: 3c6059e131eadf1144fd189c47691b2352176745
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446417"
---
## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analisar formulários para pares e tabelas de valor-chave

Em seguida, você usará o seu modelo recém-treinado para analisar um documento e extrair pares e tabelas de valor-chave do mesmo. Ligue para a **[API do Formulário de Análise](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** executando o seguinte código num novo script Python. Antes de executar o script, faça estas alterações:

1. Substitua-a `<file path>` pelo caminho de ficheiro do seu formulário (por exemplo, C:\temp\file.pdf). Isto também pode ser o URL de um ficheiro remoto. Para este arranque rápido, pode utilizar os ficheiros sob a pasta **de teste** do conjunto de dados da [amostra](https://go.microsoft.com/fwlink/?linkid=2090451).
1. Substitua `<model_id>` pelo ID modelo que recebeu na secção anterior.
1. Substitua-o `<endpoint>` pelo ponto final que obteve com a chave de subscrição do Reconhecimento de Formulários. Pode encontrá-lo no separador de **visão geral** do recurso 'Reconhecimento de Formulários'.
1. Substitua `<file type>` pelo tipo de ficheiro. Tipos `application/pdf`suportados: `image/jpeg` `image/png`, `image/tiff`, .
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

1. Guarde o código num ficheiro com uma extensão .py. Por exemplo, *form-recognizer-analyze.py.*
1. Abra uma janela da linha de comandos.
1. Na linha de comandos, utilize o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-analyze.py`.

Quando ligar para a API do Formulário `201 (Success)` de **Análise,** receberá uma resposta com um cabeçalho de Localização de **Operação.** O valor deste cabeçalho é um ID que utilizará para acompanhar os resultados da operação Analyze. O script acima imprime o valor deste cabeçalho para a consola.

## <a name="get-the-analyze-results"></a>Obtenha os resultados da Análise

Adicione o seguinte código na parte inferior do seu script Python. Isto utiliza o valor de ID da chamada anterior numa nova chamada da API para recuperar os resultados da análise. A operação **Analyze Form** é assíncrona, pelo que este script chama a API em intervalos regulares até que os resultados estejam disponíveis. Recomendamos um intervalo de um segundo ou mais.

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
