---
title: Obter estado de tradução
titleSuffix: Azure Cognitive Services
description: O método de estado de tradução devolve uma lista dos pedidos de lote apresentados e o estado de cada pedido.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 04/21/2021
ms.author: v-jansk
ms.openlocfilehash: c3301283f0a7334a7c207ff7c80b4f71a13de465
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864834"
---
# <a name="get-translations-status"></a>Obter estado de tradução

O método de estado de tradução Get devolve uma lista dos pedidos de lote apresentados e o estado de cada pedido. Esta lista contém apenas pedidos de lote enviados pelo utilizador (com base na subscrição). O estado de cada pedido é classificado por id.

Se o número de pedidos exceder o nosso limite de paging, é utilizado um paging do lado do servidor. Respostas paginadas indicam um resultado parcial e incluem um sinal de continuação na resposta. A ausência de um símbolo de continuação significa que não há páginas adicionais disponíveis.

$top e $skip podem ser usados parâmetros de consulta para especificar uma série de resultados para devolver e uma compensação para a coleção.

O servidor honra os valores especificados pelo cliente. No entanto, os clientes devem estar preparados para lidar com respostas que contenham um tamanho de página diferente ou contenham um token de continuação.

Quando $top e $skip estiverem incluídos, o servidor deve primeiro aplicar $skip e, em seguida, $top na coleção. 

> [!NOTE]
> Se o servidor não puder honrar $top e/ou $skip, o servidor deve devolver um erro ao cliente informando-o em vez de apenas ignorar as opções de consulta. Isto reduz o risco de o cliente fazer suposições sobre os dados devolvidos.

## <a name="request-url"></a>URL do Pedido

Envie um `GET` pedido para:
```HTTP
GET https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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
|200|OK. Pedido bem sucedido e devolve o estado de todas as operações. HeadersRetry-After: inteiroETag: string|
|400|Mau pedido. Pedido inválido. Verifique os parâmetros de entrada.|
|401|Não autorizado. Verifique as suas credenciais.|
|500|Erro interno do servidor.|
|Outros Códigos de Estado|<ul><li>Muitos pedidos</li><li>Servidor temporário indisponível</li></ul>|

## <a name="get-translations-status-response"></a>Obtenha resposta do estado das traduções

### <a name="successful-get-translations-status-response"></a>Obter resposta de estado de tradução bem sucedida

As seguintes informações são devolvidas numa resposta bem sucedida.

|Nome|Tipo|Description|
|--- |--- |--- |
|ID|string|Identificação da operação.|
|createdDateTimeUtc|string|Operação criou a hora da data.|
|últimaActionDateTimeUtc|string|Data em que o estado da operação foi atualizado.|
|status|String|Lista de possíveis estatutos para trabalho ou documento: <ul><li>Cancelado</li><li>Cancelamento</li><li>Com falhas</li><li>Não Começou</li><li>Em Execução</li><li>Com êxito</li><li>ValidaçãoFailed</li></ul>|
|resumo|StatusSummary[]|Resumo contendo os detalhes listados abaixo.|
|resumo.total|número inteiro|Contagem de documentos totais.|
|resumo.falhou|número inteiro|A contagem de documentos falhou.|
|resumo.sucesso|número inteiro|Contagem de documentos traduzidos com sucesso.|
|resumo.inProgress|número inteiro|Contagem de documentos em curso.|
|resumo.notYetStarted|número inteiro|A contagem de documentos ainda não começou a ser processada.|
|resumo.cancelado|número inteiro|Contagem de documentos cancelados.|
|resumo.totalCharacterCharged|número inteiro|Contagem total de caracteres carregados.|

### <a name="error-response"></a>Resposta de erro

|Nome|Tipo|Description|
|--- |--- |--- |
|code|string|Números contendo códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>Argumento invalido</li><li>InáduloRequest</li><li>RequestRateTooHigh</li><li>ResourceNotFound</li><li>ServiceUnavailable</li><li>Não autorizado</li></ul>|
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
      "id": "727bf148-f327-47a0-9481-abae6362f11e",
      "createdDateTimeUtc": "2020-03-26T00:00:00Z",
      "lastActionDateTimeUtc": "2020-03-26T01:00:00Z",
      "status": "Succeeded",
      "summary": {
        "total": 10,
        "failed": 1,
        "success": 9,
        "inProgress": 0,
        "notYetStarted": 0,
        "cancelled": 0,
        "totalCharacterCharged": 0
      }
    }
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
