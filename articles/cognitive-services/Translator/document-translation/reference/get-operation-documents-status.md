---
title: Tradução documental obtenha estado dos documentos de operação
titleSuffix: Azure Cognitive Services
description: O método de estado dos documentos de operação obtém o estado de todos os documentos num pedido de tradução de documentos de lote.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: c6cf33f32abb473badbb11bf58bd52ffba53d75c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613102"
---
# <a name="document-translation-get-operation-documents-status"></a>Tradução documental: obtenha o estado dos documentos de operação

O método 'Obter Documentos de Funcionamento' devolve o estado de todos os documentos num pedido de tradução de documentos de lote.

Os documentos incluídos na resposta são classificados por documento ID em ordem descendente. Se o número de documentos na resposta exceder o nosso limite de paging, é utilizado um paging do lado do servidor. Respostas paginadas indicam um resultado parcial e incluem um sinal de continuação na resposta. A ausência de um símbolo de continuação significa que não há páginas adicionais disponíveis.

$top e $skip podem ser usados parâmetros de consulta para especificar uma série de resultados para devolver e uma compensação para a coleção. O servidor honra os valores especificados pelo cliente. No entanto, os clientes devem estar preparados para lidar com respostas que contenham um tamanho de página diferente ou contenham um token de continuação.

Quando $top e $skip estiverem incluídos, o servidor deve primeiro aplicar $skip e, em seguida, $top na coleção.

> [!NOTE]
> Se o servidor não puder honrar $top e/ou $skip, o servidor deve devolver um erro ao cliente informando-o em vez de apenas ignorar as opções de consulta. Isto reduz o risco de o cliente fazer suposições sobre os dados devolvidos.

## <a name="request-url"></a>URL do Pedido

Envie um `GET` pedido para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches/{id}/documents
```

Saiba como encontrar o [nome de domínio personalizado.](../get-started-with-document-translation.md#find-your-custom-domain-name)

> [!IMPORTANT]
>
> * **Todos os pedidos da API ao serviço de Tradução documental requerem um ponto final de domínio personalizado.**
> * Não é possível utilizar o ponto final encontrado na página de recursos do portal Azure _Keys e Endpoint,_ nem o ponto final do tradutor `api.cognitive.microsofttranslator.com` global, para fazer pedidos HTTP para Tradução documental.

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

|Parâmetro de consulta|Obrigatório|Descrição|
|--- |--- |--- |
|ID|Verdadeiro|A operação de identificação.|
|$skip|Falso|Ignore as entradas $skip na coleção. Quando os $top e $skip são fornecidos, $skip é aplicado primeiro.|
|$top|Falso|Pegue as entradas $top na coleção. Quando os $top e $skip são fornecidos, $skip é aplicado primeiro.|

## <a name="request-headers"></a>Cabeçalhos do pedido

Os cabeçalhos de pedido são:

|Cabeçalhos|Description|
|--- |--- |
|Ocp-Apim-Subscription-Key|Cabeçalho de pedido exigido|

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve.

|Código de Estado|Description|
|--- |--- |
|200|OK. Pedido bem sucedido e devolve o estado dos documentos. HeadersRetry-After: inteiroETag: string|
|400|Pedido inválido. Verifique os parâmetros de entrada.|
|401|Não autorizado. Verifique as suas credenciais.|
|404|O recurso não é encontrado.|
|500|Erro interno do servidor.|
|Outros Códigos de Estado|<ul><li>Muitos pedidos</li><li>Servidor temporário indisponível</li></ul>|


## <a name="get-operation-documents-status-response"></a>Obtenha resposta do estado dos documentos de operação

### <a name="successful-get-operation-documents-status-response"></a>Resposta de estado de documentos de operação com sucesso

As seguintes informações são devolvidas numa resposta bem sucedida.

|Nome|Tipo|Description|
|--- |--- |--- |
|@nextLink|cadeia (de carateres)|Url para a próxima página. Nulo se não houver mais páginas disponíveis.|
|valor|DocumentStatusDetail []|O estado de pormenor dos documentos individuais listados abaixo.|
|valor.caminho|string|Localização do documento ou pasta.|
|value.createdDateTimeUtc|string|Operação criou a hora da data.|
|value.lastActionDateTimeUt|string|Data em que o estado da operação foi atualizado.|
|value.status|status|Lista de possíveis estatutos para trabalho ou documento.<ul><li>Cancelado</li><li>Cancelamento</li><li>Com falhas</li><li>Não Começou</li><li>Em Execução</li><li>Com êxito</li><li>ValidaçãoFailed</li></ul>|
|value.to|string|À linguagem.|
|valor.progresso|string|Progresso da tradução, se disponível.|
|value.id|string|Identificação de documento.|
|value.characterCharged|número inteiro|Caracteres cobrados pela API.|

### <a name="error-response"></a>Resposta de erro

|Nome|Tipo|Description|
|--- |--- |--- |
|code|string|Números contendo códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>Argumento invalido</li><li>InáduloRequest</li><li>RequestRateTooHigh</li><li>RecursosNotFound</li><li>ServiceUnavailable</li><li>Não autorizado</li></ul>|
|message|string|Recebe uma mensagem de erro de alto nível.|
|alvo|string|Obtém a origem do erro. Por exemplo, seria "documentos" ou "documento de identificação" no caso de um documento inválido.|
|interiorError|InteriorErrorV2|Novo formato de Erro Interior, que está em conformidade com as Diretrizes da API dos Serviços Cognitivos. Contém propriedades necessárias ErrorCode, mensagem e alvo de propriedades opcionais, detalhes (par de valor chave), erro interno (este pode ser aninhado).|
|innerError.code|string|Obtém a cadeia de erro de código.|
|innerError.message|string|Recebe uma mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem sucedida

Segue-se um exemplo de uma resposta bem sucedida.

```JSON
{
  "value": [
    {
      "path": "https://myblob.blob.core.windows.net/destinationContainer/fr/mydoc.txt",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Running",
      "to": "fr",
      "progress": 0.1,
      "id": "273622bd-835c-4946-9798-fd8f19f6bbf2",
      "characterCharged": 0
    }
  ],
  "@nextLink": "https://westus.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55/documents?$top=5&$skip=15"
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
    "target": "Operation",
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
