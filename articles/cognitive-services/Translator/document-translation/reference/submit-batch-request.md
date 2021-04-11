---
title: Documento Tradução submeter pedido de lote
titleSuffix: Azure Cognitive Services
description: Envie um pedido de tradução documental para o serviço de Tradução documental.
services: cognitive-services
author: jann-skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/25/2021
ms.author: v-jansk
ms.openlocfilehash: 803a0b9f4496a3d785aa6f22833fee4ae6eca6e0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613095"
---
# <a name="document-translation-submit-batch-request"></a>Tradução documental: submeter pedido de lote

Utilize esta API para submeter um pedido de tradução a granel (lote) ao serviço de Tradução documental. Cada pedido pode conter vários documentos e deve conter um recipiente de origem e destino para cada documento.

O prefixo e o filtro de sufixo (se fornecidos) são utilizados para filtrar pastas. O prefixo é aplicado ao subpata após o nome do recipiente.

Glossários / Memória de tradução podem ser incluídos no pedido e são aplicados pelo serviço quando o documento é traduzido.

Se o glossário for inválido ou inacessível durante a tradução, é indicado um erro no estado do documento. Se um ficheiro com o mesmo nome já existir no destino, será substituído. O targetUrl para cada língua-alvo deve ser único.

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:
```HTTP
POST https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1/batches
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

## <a name="request-body-batch-submission-request"></a>Corpo de Pedido: Pedido de Submissão de Lote

|Nome|Tipo|Description|
|--- |--- |--- |
|entradas|BatchRequest[]|BatchRequest listado abaixo. A lista de entradas de documentos ou pastas que contenham documentos. Tipos de Mídia: "aplicação/json", "texto/json", "aplicação/*+json".|

### <a name="inputs"></a>Entradas

Definição para o pedido de tradução do lote de entrada.

|Nome|Tipo|Necessário|Descrição|
|--- |--- |--- |--- |
|source|FonteInput[]|Verdadeiro|inputs.source listados abaixo. Fonte dos documentos de entrada.|
|dispositivo de armazenamento|StorageInputType[]|Verdadeiro|inputs.storageType listado abaixo. Tipo de armazenamento da cadeia de origem dos documentos de entrada.|
|alvos|TargetInput[]|Verdadeiro|inputs.target listado abaixo. Localização do destino para a saída.|

**inputs.source**

Fonte dos documentos de entrada.

|Nome|Tipo|Necessário|Descrição|
|--- |--- |--- |--- |
|filter|DocumentFilter[]|Falso|DocumentFilter[] listado abaixo.|
|filtro.prefixo|string|Falso|Um prefixo sensível a maiíssaturas para filtrar documentos no caminho de origem para tradução. Por exemplo, quando utilizar uma bolha de armazenamento Azure Uri, utilize o prefixo para restringir as sub-pastas para tradução.|
|filtro.sufixo|string|Falso|Um sufixo sensível a casos para filtrar documentos no caminho de origem para tradução. Isto é mais frequentemente usado para extensões de ficheiros.|
|language|string|Falso|Código linguístico Se nenhum for especificado, efetuaremos a deteção automática no documento.|
|fonteUrl|string|Verdadeiro|Localização da pasta/recipiente ou ficheiro único com os seus documentos.|
|storageSource|Fonte de armazenamento|Falso|StorageSource listado abaixo.|
|storageSource.AzureBlob|string|Falso||

**inputs.storageType**

Tipo de armazenamento da cadeia de origem dos documentos de entrada.

|Nome|Tipo|
|--- |--- |
|file|string|
|pasta|string|

**inputs.target**

Destino para os documentos traduzidos acabados.

|Nome|Tipo|Necessário|Descrição|
|--- |--- |--- |--- |
|categoria|string|Falso|Sistema de categoria/personalizado para pedido de tradução.|
|glossários|Glossário[]|Falso|Glossário listado abaixo. Lista de Glossário.|
|glossários.formato|string|Falso|O formato.|
|glossários.glossaryUrl|string|Verdade (se usar glossários)|Localização do glossário. Utilizaremos a extensão do ficheiro para extrair a formatação se o parâmetro do formato não for fornecido. Se o par de linguagem de tradução não estiver presente no glossário, não será aplicado.|
|glossários.storageSource|Fonte de armazenamento|Falso|StorageSource listado acima.|
|targetUrl|string|Verdadeiro|Localização da pasta/recipiente com os seus documentos.|
|language|string|Verdadeiro|Código de linguagem alvo de duas letras. Consulte [a lista de códigos linguísticos.](../../language-support.md)|
|storageSource|StorageSource []|Falso|StorageSource [] listado acima.|
|versão|string|Falso|Versão.|

## <a name="example-request"></a>Pedido de exemplo

Seguem-se exemplos de pedidos de lote.

**Tradução de todos os documentos num recipiente**

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Tradução de todos os documentos num recipiente que aplica glossários**

Certifique-se de que criou o URL glossário & ficha SAS para o blob/documento específico (não para o recipiente)

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
     "glossaries": [
                        {
                            "glossaryUrl": https://my.blob.core.windows.net/glossaries/en-fr.xlf?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=BsciG3NWoOoRjOYesTaUmxlXzyjsX4AgVkt2AsxJ9to%3D,
                            "format": "xliff",
                            "version": "1.2"
                        }
                    ]

                }
            ]
        }
    ]
}
```

**Traduzindo pasta específica num recipiente**

Certifique-se de que especificou o nome da pasta (sensível à caixa) como prefixo no filtro – embora o token SAS ainda esteja para o recipiente.

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D,
                "filter": {
                    "prefix": "MyFolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D,
                    "language": "fr"
                }
            ]
        }
    ]
}
```

**Tradução de documento específico num recipiente**

* Certifique-se de que especificou "StorageType": "File"
* Certifique-se de que criou o URL de origem & ficha SAS para o blob/documento específico (não para o recipiente)
* Certifique-se de que especificou o nome de ficheiro alvo como parte do URL-alvo – embora o token SAS ainda esteja para o recipiente.
* O pedido de amostra abaixo mostra um único documento a ser traduzido em duas línguas-alvo

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D
            },
            "targets": [
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "es"
                },
                {
                    "targetUrl": https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D,
                    "language": "de"
                }
            ]
        }
    ]
}
```

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve.

|Código de Estado|Description|
|--- |--- |
|202|Aceite. O pedido de sucesso e o pedido de lote são criados pelo serviço. O cabeçalho Operation-Location indicará um url de estado com a operação ID.HeadersOperation-Localização: cadeia|
|400|Mau pedido. Pedido inválido. Verifique os parâmetros de entrada.|
|401|Não autorizado. Por favor, verifique as suas credenciais.|
|429|A taxa de pedido é muito alta.|
|500|Erro interno do servidor.|
|503|O serviço encontra-se atualmente indisponível.  Tente novamente mais tarde.|
|Outros Códigos de Estado|<ul><li>Muitos pedidos</li><li>Servidor temporário indisponível</li></ul>|

## <a name="error-response"></a>Resposta de erro

|Nome|Tipo|Description|
|--- |--- |--- |
|code|string|Números contendo códigos de erro de alto nível. Valores possíveis:<br/><ul><li>InternalServerError</li><li>Argumento invalido</li><li>InáduloRequest</li><li>RequestRateTooHigh</li><li>RecursosNotFound</li><li>ServiceUnavailable</li><li>Não autorizado</li></ul>|
|message|string|Recebe uma mensagem de erro de alto nível.|
|interiorError|InteriorErrorV2|Novo formato de Erro Interior, que está em conformidade com as Diretrizes da API dos Serviços Cognitivos. Contém propriedades necessárias ErrorCode, mensagem e alvo de propriedades opcionais, detalhes (par de valor chave), erro interno (este pode ser aninhado).|
|interior. Código de erro|string|Obtém a cadeia de erro de código.|
|innerError.message|string|Recebe uma mensagem de erro de alto nível.|

## <a name="examples"></a>Exemplos

### <a name="example-successful-response"></a>Exemplo de resposta bem sucedida

As seguintes informações são devolvidas numa resposta bem sucedida.

Pode encontrar o ID de trabalho na resposta do método POST Cabeçalho Operation-Location valor URL. O último parâmetro do URL é o ID de trabalho da operação (a cadeia seguinte "/operation/").

```HTTP
Operation-Location: https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0.preview.1/operation/0FA2822F-4C2A-4317-9C20-658C801E0E55
```

### <a name="example-error-response"></a>Resposta de erro de exemplo

```JSON
{
  "error": {
    "code": "ServiceUnavailable",
    "message": "Service is temporary unavailable",
    "innerError": {
      "code": "ServiceTemporaryUnavailable",
      "message": "Service is currently unavailable.  Please try again later"
    }
  }
}
```

## <a name="next-steps"></a>Passos seguintes

Siga o nosso quickstart para saber mais sobre a utilização da Tradução documental e da biblioteca do cliente.

> [!div class="nextstepaction"]
> [Começar com tradução de documentos](../get-started-with-document-translation.md)
