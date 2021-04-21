---
title: Obtenha o método de fontes de armazenamento suportados
titleSuffix: Azure Cognitive Services
description: O método de fontes de armazenamento suportados devolve uma lista de fontes de armazenamento suportadas.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 97212a0da47a25330def26a5bff2b39ff840a23d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107836254"
---
# <a name="get-supported-storage-sources"></a>Obtenha fontes de armazenamento suportadas

O método de fontes de armazenamento suportados pela Get devolve uma lista de fontes/opções de armazenamento suportadas pelo serviço de Tradução documental.

## <a name="request-url"></a>URL do Pedido

Envie um `GET` pedido para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/storagesources
```

Saiba como encontrar o [nome de domínio personalizado.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Todos os pedidos da API ao serviço de Tradução documental requerem um ponto final de domínio personalizado.**
> * Não é possível utilizar o ponto final encontrado na página de recursos do portal Azure _Keys e Endpoint,_ nem o ponto final do tradutor `api.cognitive.microsofttranslator.com` global, para fazer pedidos HTTP para Tradução documental.

## <a name="request-headers"></a>Cabeçalhos do pedido

Os cabeçalhos de pedido são:

|Cabeçalhos|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de pedido exigido|

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve.

|Código de Estado|Description|
|--- |--- |
|200|OK. Pedido bem sucedido e devolve a lista de fontes de armazenamento.|
|500|Erro interno do servidor.|
|Outros Códigos de Estado|<ul><li>Muitos pedidos</li><li>Servidor temporário indisponível</li></ul>|

## <a name="get-supported-storage-sources-response"></a>Obtenha resposta de fontes de armazenamento suportadas

### <a name="successful-get-supported-storage-sources-response"></a>Obter resposta de fontes de armazenamento suportadas com sucesso
Tipo base para retorno da lista na API de fontes de armazenamento suportadas.

|Nome|Tipo|Description|
|--- |--- |--- |
|valor|corda []|Lista de objetos.|


### <a name="error-response"></a>Resposta de erro

|Nome|Tipo|Description|
|--- |--- |--- |
|code|string|Números contendo códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>Argumento invalido</li><li>InáduloRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não autorizado</li></ul>|
|message|string|Recebe uma mensagem de erro de alto nível.|
|interiorError|InteriorErrorV2|Novo formato de Erro Interior, que está em conformidade com as Diretrizes da API dos Serviços Cognitivos. Contém propriedades necessárias ErrorCode, mensagem e alvo de propriedades opcionais, detalhes (par de valor chave), erro interno (este pode ser aninhado).|
|innerError.code|string|Obtém a cadeia de erro de código.|
|innerError.message|string|Recebe uma mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem sucedida

Segue-se um exemplo de uma resposta bem sucedida.

```JSON
{
  "value": [
    "AzureBlob"
  ]
}
```

### <a name="example-error-response"></a>Resposta de erro de exemplo
Segue-se um exemplo de uma resposta de erro. O esquema de outros códigos de erro é o mesmo.

Código de estado: 500

```JSON
{
  "error": {
    "code": "InternalServerError",
    "message": "Internal Server Error",
    "innerError": {
      "code": "InternalServerError",
      "message": "Unexpected internal server error has occurred"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Siga o nosso quickstart para saber mais sobre a utilização da Tradução documental e da biblioteca do cliente.

> [!div class="nextstepaction"]
> [Começar com tradução de documentos](../get-started-with-document-translation.md)