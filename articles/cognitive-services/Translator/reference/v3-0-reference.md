---
title: Referência tradutor V3.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o Tradutor V3.0. A versão 3 do Tradutor fornece uma API web moderna baseada em JSON.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: lajanuar
ms.openlocfilehash: 021a65ba69b9d1909df4a86d92b660c0c1033a3b
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895463"
---
# <a name="translator-v30"></a>Tradutor v3.0

## <a name="whats-new"></a>Novidades?

A versão 3 do Tradutor fornece uma API web moderna baseada em JSON. Melhora a usabilidade e o desempenho, consolidando as funcionalidades existentes em menos operações e fornece novas funcionalidades.

 * Transliteração para converter texto em uma língua de um script para outro script.
 * Tradução para várias línguas num único pedido.
 * Deteção de linguagem, tradução e transliteração num único pedido.
 * Dicionário para procurar traduções alternativas de um termo, para encontrar back-translations e exemplos mostrando termos usados em contexto.
 * Resultados de deteção de linguagem mais informativos.

## <a name="base-urls"></a>Base URLs

O Microsoft Tradutor é servido a partir de vários locais do datacenter. Atualmente estão localizados em 10 [geografias Azure:](https://azure.microsoft.com/global-infrastructure/regions)

* **Américas:** Leste dos EUA, Centro Sul dos EUA, Centro-Oeste dos EUA e Oeste dos EUA 2 
* **Ásia-Pacífico:** Coreia do Sul, Japão Leste, Sudeste Asiático e Austrália Oriental
* **Europa:** Europa do Norte e Europa Ocidental

Os pedidos ao Microsoft Tradutor são, na maioria dos casos, tratados pelo datacenter que está mais próximo do local de origem do pedido. Em caso de falha no datacenter, o pedido pode ser encaminhado para fora da geografia Azure.

Para forçar o pedido a ser tratado por uma geografia específica do Azure, altere o ponto final Global no pedido da API para o ponto final geográfico pretendido:

|Descrição|Geografia azul|URL de base (ponto final geográfico)|
|:--|:--|:--|
|Azure|Global (não regional)|   api.cognitive.microsofttranslator.com|
|Azure|Estados Unidos da América|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Ásia-Pacífico|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Autenticação

Subscreva o Multi-serviço de Tradutor ou [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) nos Serviços Cognitivos Azure e use a sua chave de subscrição (disponível no portal Azure) para autenticar. 

Existem três cabeçalhos que pode utilizar para autenticar a sua subscrição. Esta tabela descreve como cada um é usado:

|Cabeçalhos|Descrição|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Use com a subscrição de Serviços Cognitivos se estiver a passar a sua chave secreta.*<br/>O valor é a chave secreta Azure para a sua subscrição ao Tradutor.|
|Autorização|*Use com subscrição de Serviços Cognitivos se estiver a passar um token de autenticação.*<br/>O valor é o símbolo do Portador: `Bearer <token>` .|
|Ocp-Apim-Região de Subscrição|*Utilizar com serviços cognitivos multi-serviço e recurso de tradutor regional.*<br/>O valor é a região do recurso multi-serviço ou tradutor regional. Este valor é opcional quando se utiliza um recurso de tradutor global.|

###  <a name="secret-key"></a>Chave secreta
A primeira opção é autenticar usando o `Ocp-Apim-Subscription-Key` cabeçalho. Adicione o `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` cabeçalho ao seu pedido.

#### <a name="authenticating-with-a-global-resource"></a>Autenticação com recurso global

Quando utilizar um [recurso de tradutor global,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)tem de incluir um cabeçalho para ligar para o Tradutor.

|Cabeçalhos|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta Azure para a sua subscrição ao Tradutor.|

Aqui está um pedido de exemplo para ligar para o Tradutor usando o recurso tradutor global

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autenticação com recurso regional

Quando se utiliza um [recurso de tradutor regional.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)
Há dois cabeçalhos que precisa chamar ao Tradutor.

|Cabeçalhos|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta Azure para a sua subscrição ao Tradutor.|
|Ocp-Apim-Região de Subscrição| O valor é a região do recurso tradutor. |

Aqui está um pedido de exemplo para ligar para o Tradutor usando o recurso tradutor regional

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autenticação com recurso multi-serviço

Quando se utiliza o recurso multi-serviço de um Serviço Cognitivo. Isto permite-lhe utilizar uma única chave secreta para autenticar pedidos de múltiplos serviços. 

Quando utilizar uma chave secreta multi-serviço, deve incluir dois cabeçalhos de autenticação com o seu pedido. Há dois cabeçalhos que precisa chamar ao Tradutor.

|Cabeçalhos|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta Azure para o seu recurso multi-serviço.|
|Ocp-Apim-Região de Subscrição| O valor é a região do recurso multi-serviço. |

A região é necessária para a subscrição de API de texto multi-serviço. A região que seleciona é a única região que pode utilizar para tradução de texto ao utilizar a chave de subscrição multi-serviço, e deve ser a mesma região que selecionou quando se inscreveu para a sua subscrição multi-serviço através do portal Azure.

As regiões disponíveis `australiaeast` são, `brazilsouth` , , , `canadacentral` , `centralindia` `centralus` `centraluseuap` `eastasia` , `eastus` `eastus2` , `francecentral` `japaneast` `japanwest` `koreacentral` e `northcentralus` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` `southafricanorth` .

Se passar a chave secreta na cadeia de consulta com o `Subscription-Key` parâmetro, então deve especificar a região com o parâmetro de consulta `Subscription-Region` .

### <a name="authenticating-with-an-access-token"></a>Autenticação com ficha de acesso
Em alternativa, pode trocar a sua chave secreta por um token de acesso. Este token está incluído a cada pedido como `Authorization` cabeçalho. Para obter um sinal de autorização, faça um `POST` pedido ao seguinte URL:

| Tipo de recurso     | URL de serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regional ou Multi-Serviço | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Aqui estão os pedidos de exemplo para obter um símbolo dado uma chave secreta:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Um pedido bem sucedido devolve o token de acesso codificado como texto simples no corpo de resposta. O token válido é passado para o serviço tradutor como símbolo portador na Autorização.

```http
Authorization: Bearer <Base64-access_token>
```

Um sinal de autenticação é válido por 10 minutos. O token deve ser reutilizado ao fazer várias chamadas para o Tradutor. No entanto, se o seu programa fizer pedidos ao Tradutor durante um longo período de tempo, o seu programa deve solicitar um novo token de acesso a intervalos regulares (por exemplo, a cada 8 minutos).

## <a name="virtual-network-support"></a>Suporte de Rede Virtual

O serviço Tradutor está agora disponível com capacidades de Rede Virtual (VNET) em todas as regiões da nuvem pública Azure. Para ativar a Rede Virtual, consulte [as Redes Virtuais de Serviços Cognitivos Azure.](../../cognitive-services-virtual-networks.md?tabs=portal) 

Assim que ligar esta capacidade, deve utilizar o ponto final personalizado para ligar para o Tradutor. Não é possível utilizar o ponto final do tradutor global ("api.cognitive.microsofttranslator.com") e não pode autenticar com um token de acesso.

Pode encontrar o ponto final personalizado depois de criar um [recurso de tradutor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) e permitir o acesso a partir de redes selecionadas e pontos finais privados.

|Cabeçalhos|Descrição|
|:-----|:----|
|Ocp-Apim-Subscription-Key| O valor é a chave secreta Azure para a sua subscrição ao Tradutor.|
|Ocp-Apim-Região de Subscrição| O valor é a região do recurso tradutor. Este valor é opcional se o recurso for `global`|

Aqui está um pedido de exemplo para ligar para o Tradutor usando o ponto final personalizado

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Erros

Uma resposta padrão de erro é um objeto JSON com nome/par de valor chamado `error` . O valor é também um objeto JSON com propriedades:

  * `code`: Um código de erro definido pelo servidor.
  * `message`: Uma corda que dá uma representação legível pelo homem do erro.

Por exemplo, um cliente com uma subscrição de teste gratuito receberia o seguinte erro assim que a quota gratuita esteja esgotada:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Os códigos de erro comuns são:

| Código | Descrição |
|:----|:-----|
| 400000| Uma das entradas de pedido não é válida.|
| 400001| O parâmetro "âmbito" é inválido.|
| 400002| O parâmetro "categoria" é inválido.|
| 400003| Um especificador de idiomas está em falta ou inválido.|
| 400004| Um especificador de scripts-alvo ("Para script") está em falta ou inválido.|
| 400005| Falta um texto de entrada ou inválido.|
| 400006| A combinação de linguagem e script não é válida.|
| 400018| Um especificador de script de origem ("A partir do script") está em falta ou inválido.|
| 400019| Uma das línguas especificadas não é suportada.|
| 400020| Um dos elementos da matriz do texto de entrada não é válido.|
| 400021| O parâmetro da versão API está em falta ou inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| A língua de origem (campo "From") não é válida.|
| 400036| A língua-alvo (campo "Para") está em falta ou inválida.|
| 400042| Uma das opções especificadas (campo "Opções") não é válida.|
| 400043| O iD de rastreio de cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está em falta ou inválido.|
| 400050| O texto de entrada é demasiado longo. Ver [limites de pedido](../request-limits.md).|
| 400064| O parâmetro de "tradução" está em falta ou inválido.|
| 400070| O número de scripts-alvo (parâmetro ToScript) não corresponde ao número de idiomas-alvo (Para o parâmetro).|
| 400071| O valor não é válido para TextType.|
| 400072| A matriz do texto de entrada tem demasiados elementos.|
| 400073| O parâmetro do script não é válido.|
| 400074| O corpo do pedido não é válido JSON.|
| 400075| O par de idiomas e a combinação de categorias não são válidos.|
| 400077| O tamanho máximo do pedido foi ultrapassado. Ver [limites de pedido](../request-limits.md).|
| 400079| O sistema personalizado solicitado para tradução entre e para a língua não existe.|
| 400080| A transliteração não é suportada para a língua ou o script.|
| 401000| O pedido não é autorizado porque as credenciais estão em falta ou inválidas.|
| 401015| "As credenciais fornecidas são para a API do Discurso. Este pedido requer credenciais para a API de texto. Use uma assinatura para Tradutor."|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a subscrição excedeu a sua quota livre.|
| 405000| O método de pedido não é suportado para o recurso solicitado.|
| 408001| O sistema de tradução solicitado está a ser preparado. Por favor, reda o redaça em alguns minutos.|
| 408002| Pedido cronometrado à espera no fluxo de entrada. O cliente não fez um pedido no momento em que o servidor estava preparado para esperar. O cliente pode repetir o pedido sem modificações em qualquer momento posterior.|
| 415000| O cabeçalho do tipo de conteúdo está em falta ou inválido.|
| 429000, 429001, 429002| O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.|
| 500000| Ocorreu um erro inesperado. Se o erro persistir, reporte-o com data/hora de erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de pedido X-ClientTraceId.|
| 503000| O serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, reporte-o com data/hora de erro, solicite o identificador do cabeçalho de resposta X-RequestId e o identificador de cliente do cabeçalho de pedido X-ClientTraceId.|

## <a name="metrics"></a>Métricas 
As métricas permitem visualizar a utilização do tradutor e a informação de disponibilidade no portal Azure, na secção métrica, como mostrado na imagem abaixo. Para obter mais informações, consulte [datas e métricas da plataforma.](../../../azure-monitor/platform/data-platform-metrics.md)

![Métricas do Tradutor](../media/translatormetrics.png)

Esta tabela lista as métricas disponíveis com descrição de como são usadas para monitorizar as chamadas de API de tradução.

| Métricas | Descrição |
|:----|:-----|
| Total de Chamadas| Número total de chamadas da API.|
| TotalTokenCalls| Número total de chamadas API através do serviço token usando token de autenticação.|
| Chamadas de sucesso| Número de chamadas bem sucedidas.|
| TotalErrors| Número de chamadas com resposta a erros.|
| BlockedCalls| Número de chamadas que excederam a taxa ou o limite de quota.|
| ServerErrors| Número de chamadas com erro interno do servidor (5XX).|
| ClientErrors| Número de chamadas com erro lateral do cliente (4XX).|
| Latência| Duração para completar pedido em milissegundos.|
| CaracteresTranslatados| Número total de caracteres no pedido de texto de entrada.|
