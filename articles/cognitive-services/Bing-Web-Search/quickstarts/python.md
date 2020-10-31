---
title: 'Guia de Início Rápido: Efetuar uma pesquisa com o Python – API de Pesquisa na Web do Bing'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para enviar pedidos para a Bing Web Search REST API usando Python, e receba uma resposta JSON
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 9d6de9b29f0e68d48eab0b2c4081470858d3d69c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076440"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Início rápido: Utilizar o Python para chamar a API de Pesquisa na Web do Bing  

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](https://aka.ms/cogsvcs/bingmove)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](https://aka.ms/cogsvcs/bingmigration)

Use este quickstart para fazer a sua primeira chamada para a API de Pesquisa web Bing. Esta aplicação Python envia um pedido de pesquisa à API, e mostra a resposta JSON. Embora esta aplicação esteja escrita em Python, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

Este exemplo é executado como um bloco de notas do Jupyter no [MyBinder](https://mybinder.org). Para executá-lo, selecione o crachá de encaixe de lançamento:

[![Encadernação](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 2.x ou 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definir variáveis

1. Substitua o valor `subscription_key` por uma chave de subscrição válida da sua conta do Azure.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Declarar o ponto final da API de Pesquisa na Web do Bing. Pode utilizar o ponto final global no seguinte código ou utilizar o ponto final [de subdomínio personalizado](../../../cognitive-services/cognitive-services-custom-subdomains.md) apresentado no portal Azure para o seu recurso.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. Opcionalmente, personalize a consulta de pesquisa substituindo o valor de `search_term` .

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Fazer um pedido

Este código utiliza a `requests` biblioteca para ligar para a API de Pesquisa web Bing e devolver os resultados como um objeto JSON. A chave de API é passada no dicionário `headers` e os parâmetros de consulta e o termo de pesquisa são passados no dicionário `params`. 

Para obter uma lista completa de opções e parâmetros, consulte [bing Web Search API v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatar e apresentar a resposta

O `search_results` objeto inclui os resultados da pesquisa, e metadados como consultas e páginas relacionadas. Este código utiliza a biblioteca `IPython.display` para formatar e apresentar a resposta no seu browser.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Código de amostra no GitHub

Para executar este código localmente, consulte a amostra completa [disponível no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial de aplicação de pesquisa web de pesquisa web Bing Web API](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
