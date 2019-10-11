---
title: Referência do API de Tradução de Texto V 3.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o API de Tradução de Texto V 3.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: swmachan
ms.openlocfilehash: a441ca83230a1c715aadda79683964aaab6d6213
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72252966"
---
# <a name="translator-text-api-v30"></a>API de Tradução de Texto v 3.0

## <a name="whats-new"></a>Novidades

A versão 3 do API de Tradução de Texto fornece uma API da Web moderna baseada em JSON. Ele melhora a usabilidade e o desempenho consolidando recursos existentes em menos operações e fornece novos recursos.

 * Transliteração para converter texto em um idioma de um script para outro.
 * Tradução para vários idiomas em uma solicitação.
 * Detecção de idioma, tradução e transliteração em uma solicitação.
 * Dicionário para pesquisar traduções alternativas de um termo, para encontrar traduções traseiras e exemplos que mostram os termos usados no contexto.
 * Mais resultados de detecção de idioma informativo.

## <a name="base-urls"></a>URLs de base

O Microsoft Translator é servido por vários locais de datacenter. Atualmente, eles estão localizados em 10 [geografias do Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Américas:** Leste dos EUA, Sul EUA Central, Oeste EUA Central e oeste dos EUA 2 
* **Pacífico Asiático:** Sul da Coreia, leste do Japão, Sudeste Asiático e leste da Austrália
* **Europa:** Europa Setentrional e Europa Ocidental

As solicitações para os API de Tradução de Texto da Microsoft estão na maioria dos casos manipulados pelo datacenter mais próximo de onde a solicitação foi originada. No caso de uma falha de datacenter, a solicitação pode ser roteada para fora da geografia do Azure.

Para forçar a manipulação da solicitação por uma geografia do Azure específica, altere o ponto de extremidade global na solicitação de API para o ponto de extremidade regional desejado:

|Descrição|Geografia do Azure|URL base|
|:--|:--|:--|
|Azure|Global (não regional)|   api.cognitive.microsofttranslator.com|
|Azure|Estados Unidos|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Ásia-Pacífico|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Autenticação

Assine API de Tradução de Texto ou [Serviços cognitivas multiatendimento](https://azure.microsoft.com/pricing/details/cognitive-services/) nos serviços cognitivas do Azure e use sua chave de assinatura (disponível no portal do Azure) para autenticar. 

Há três cabeçalhos que você pode usar para autenticar sua assinatura. Esta tabela descreve como cada um é usado:

|Cabeçalhos|Descrição|
|:----|:----|
|OCP-APIM-Subscription-Key|*Use com a assinatura de serviços cognitivas se você estiver passando sua chave secreta*.<br/>O valor é a chave secreta do Azure para sua assinatura para API de Tradução de Texto.|
|Autorização|*Use com a assinatura de serviços cognitivas se você estiver passando um token de autenticação.*<br/>O valor é o token de portador: `Bearer <token>`.|
|OCP-APIM-Subscription – região|*Use com a assinatura de vários serviços cognitivas se você estiver passando uma chave secreta de vários serviços.*<br/>O valor é a região da assinatura de vários serviços. Esse valor é opcional quando não estiver usando uma assinatura de vários serviços.|

###  <a name="secret-key"></a>Chave secreta
A primeira opção é autenticar usando o cabeçalho `Ocp-Apim-Subscription-Key`. Adicione o cabeçalho `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` à sua solicitação.

### <a name="authorization-token"></a>Token de autorização
Como alternativa, você pode trocar sua chave secreta para um token de acesso. Esse token é incluído em cada solicitação como o cabeçalho `Authorization`. Para obter um token de autorização, faça uma solicitação `POST` para a seguinte URL:

| Ambiente     | URL do serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Aqui estão exemplos de solicitações para obter um token fornecido por uma chave secreta:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Uma solicitação bem-sucedida retorna o token de acesso codificado como texto sem formatação no corpo da resposta. O token válido é passado para o serviço do tradutor como um token de portador na autorização.

```http
Authorization: Bearer <Base64-access_token>
```

Um token de autenticação é válido por 10 minutos. O token deve ser reutilizado ao fazer várias chamadas para as APIs do tradutor. No entanto, se o seu programa fizer solicitações para a API do tradutor durante um longo período de tempo, o programa deverá solicitar um novo token de acesso em intervalos regulares (por exemplo, a cada 8 minutos).

### <a name="multi-service-subscription"></a>Assinatura de vários serviços

A última opção de autenticação é usar a assinatura de vários serviços de um serviço cognitiva. Isso permite que você use uma única chave secreta para autenticar solicitações para vários serviços. 

Ao usar uma chave secreta de vários serviços, você deve incluir dois cabeçalhos de autenticação com sua solicitação. A primeira passa a chave secreta, a segunda especifica a região associada à sua assinatura. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

A região é necessária para a assinatura de API de texto de vários serviços. A região selecionada é a única região que você pode usar para a tradução de texto ao usar a chave de assinatura de vários serviços e deve ser a mesma região que você selecionou quando se inscreveu para sua assinatura de vários serviços por meio do portal do Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 0, @no__ t-21 e 2.

Se você passar a chave secreta na cadeia de caracteres de consulta com o parâmetro `Subscription-Key`, deverá especificar a região com o parâmetro de consulta `Subscription-Region`.

Se você usar um token de portador, deverá obter o token do ponto de extremidade da região: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Erros

Uma resposta de erro padrão é um objeto JSON com o par nome/valor denominado `error`. O valor também é um objeto JSON com propriedades:

  * `code`: um código de erro definido pelo servidor.
  * `message`: uma cadeia de caracteres fornecendo uma representação legível do erro.

Por exemplo, um cliente com uma assinatura de avaliação gratuita receberia o seguinte erro depois que a cota livre for esgotada:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Os códigos de erro comuns são:

| Código | Descrição |
|:----|:-----|
| 400000| Uma das entradas de solicitação não é válida.|
| 400001| O parâmetro "Scope" é inválido.|
| 400002| O parâmetro "category" é inválido.|
| 400003| Um especificador de idioma está ausente ou é inválido.|
| 400004| Um especificador de script de destino ("para script") está ausente ou é inválido.|
| 400005| Um texto de entrada está ausente ou é inválido.|
| 400006| A combinação de linguagem e script não é válida.|
| 400018| Um especificador de script de origem ("do script") está ausente ou é inválido.|
| 400019| Não há suporte para um dos idiomas especificados.|
| 400020| Um dos elementos na matriz de texto de entrada não é válido.|
| 400021| O parâmetro de versão da API está ausente ou é inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| O idioma de origem (campo "de") não é válido.|
| 400036| O idioma de destino (campo "para") está ausente ou é inválido.|
| 400042| Uma das opções especificadas (campo "opções") não é válida.|
| 400043| A ID de rastreamento do cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está ausente ou é inválida.|
| 400050| O texto de entrada é muito longo. Exibir [limites de solicitação](../request-limits.md).|
| 400064| O parâmetro "translation" está ausente ou é inválido.|
| 400070| O número de scripts de destino (parâmetro toscript) não corresponde ao número de idiomas de destino (para o parâmetro).|
| 400071| O valor não é válido para TextType.|
| 400072| A matriz de texto de entrada tem muitos elementos.|
| 400073| O parâmetro de script não é válido.|
| 400074| O corpo da solicitação não é um JSON válido.|
| 400075| A combinação de pares de idiomas e categoria não é válida.|
| 400077| O tamanho máximo da solicitação foi excedido. Exibir [limites de solicitação](../request-limits.md).|
| 400079| O sistema personalizado solicitado para tradução entre de e para o idioma não existe.|
| 400080| A transliteração não tem suporte para o idioma ou o script.|
| 401000| A solicitação não está autorizada porque as credenciais estão ausentes ou são inválidas.|
| 401015| "As credenciais fornecidas são para o Speech API. Essa solicitação requer credenciais para a API de texto. Use uma assinatura para API de Tradução de Texto ".|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a assinatura excedeu sua cota livre.|
| 405000| O método de solicitação não tem suporte para o recurso solicitado.|
| 408001| O sistema de tradução solicitado está sendo preparado. Tente novamente em alguns minutos.|
| 408002| A solicitação atingiu o tempo limite aguardando o fluxo de entrada. O cliente não produziu uma solicitação no momento em que o servidor foi preparado para aguardar. O cliente pode repetir a solicitação sem modificações em nenhum momento posterior.|
| 415000| O cabeçalho Content-Type está ausente ou é inválido.|
| 429000, 429001, 429002| O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.|
| 500000| Ocorreu um erro inesperado. Se o erro persistir, relate-o com data/hora de erro, identificador de solicitação do cabeçalho de resposta X-RequestId e identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|
| 503000| O serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, relate-o com data/hora de erro, identificador de solicitação do cabeçalho de resposta X-RequestId e identificador de cliente do cabeçalho de solicitação X-ClientTraceId.|

