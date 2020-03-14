---
title: Referência do texto tradutor API V3.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o Texto tradutor API V3.0. A versão 3 do Tradutor Text API fornece uma Moderna API baseada em JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 3/13/2020
ms.author: swmachan
ms.openlocfilehash: 4180dc6127fb2d31465400b1b25fb7e2d68f4754
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79369170"
---
# <a name="translator-text-api-v30"></a>Texto tradutor API v3.0

## <a name="whats-new"></a>Novidades

A versão 3 do Tradutor Text API fornece uma Moderna API baseada em JSON. Melhora a usabilidade e o desempenho, consolidando as funcionalidades existentes em menos operações e fornece novas funcionalidades.

 * Transliteração para converter texto numa língua de um guião para outro script.
 * Tradução para várias línguas num pedido.
 * Deteção de linguagens, tradução e transliteração num só pedido.
 * Dicionário para procurar traduções alternativas de um termo, para encontrar traduções e exemplos mostrando termos usados no contexto.
 * Resultados mais informativos de deteção de linguagem.

## <a name="base-urls"></a>Base URLs

O Microsoft Tradutor é servido em vários locais do datacenter. Atualmente estão localizados em 10 [geografias Azure:](https://azure.microsoft.com/global-infrastructure/regions)

* **Américas:** Leste dos EUA, Centro-Sul dos EUA, Centro-Oeste dos EUA e Us 2 Ocidental 
* **Ásia-Pacífico:** Coreia do Sul, Japão Leste, Sudeste Asiático e Austrália Leste
* **Europa:** Norte da Europa e Europa Ocidental

Os pedidos à API de Texto do Tradutor da Microsoft são, na maioria dos casos, tratados pelo datacenter mais próximo do local de origem do pedido. Em caso de falha no datacenter, o pedido pode ser encaminhado para fora da geografia Azure.

Para forçar o pedido a ser tratado por uma geografia específica do Azure, altere o ponto final global no pedido da API para o ponto final regional desejado:

|Descrição|Geografia azul|Base URL|
|:--|:--|:--|
|Azure|Global (não regional)|   api.cognitive.microsofttranslator.com|
|Azure|Estados Unidos|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Ásia-Pacífico|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Autenticação

Subscreva o Multi-serviço de Serviços Cognitivos Tradutor API ou [Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) em Serviços Cognitivos Azure e utilize a sua chave de subscrição (disponível no portal Azure) para autenticar. 

Existem três cabeçalhos que pode usar para autenticar a sua subscrição. Esta tabela descreve como cada um é usado:

|Cabeçalhos|Descrição|
|:----|:----|
|OCP-Apim-Subscription-Key|*Utilize com a subscrição dos Serviços Cognitivos se estiver*a passar a sua chave secreta .<br/>O valor é a chave secreta Azure para a sua subscrição da API de Texto tradutor.|
|Autorização|*Utilize com subscrição de Serviços Cognitivos se estiver a passar um símbolo de autenticação.*<br/>O valor é o símbolo do Portador: `Bearer <token>`.|
|Ocp-Apim-Região de Assinatura|*Utilizar com recursos multi-serviços e tradutores regionais de Serviços Cognitivos.*<br/>O valor é a região do recurso tradutor multi-serviço ou regional. Este valor é opcional quando se utiliza um recurso de tradutor global.|

###  <a name="secret-key"></a>Chave secreta
A primeira opção é autenticar usando o cabeçalho `Ocp-Apim-Subscription-Key`. Adicione o cabeçalho `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` ao seu pedido.

#### <a name="authenticating-with-a-global-resource"></a>Autenticação com recurso global

Quando utiliza um [recurso de tradutor global,](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)tem de incluir um cabeçalho para chamar a API do tradutor.

|Cabeçalhos|Descrição|
|:-----|:----|
|OCP-Apim-Subscription-Key| O valor é a chave secreta Azure para a sua subscrição da API de Texto tradutor.|

Aqui está um pedido de exemplo para chamar a API tradutora usando o recurso de tradutor global

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autenticação com recurso regional

Quando se utiliza um [recurso de tradutor regional.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)
Há 2 cabeçalhos que precisa chamar de tradutor API.

|Cabeçalhos|Descrição|
|:-----|:----|
|OCP-Apim-Subscription-Key| O valor é a chave secreta Azure para a sua subscrição da API de Texto tradutor.|
|Ocp-Apim-Região de Assinatura| O valor é a região do recurso tradutor. |

Aqui está um pedido de exemplo para chamar a API tradutora usando o recurso tradutor regional

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsoft.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autenticação com recurso multi-serviço

Quando se utiliza o recurso multi-serviço de um Serviço Cognitivo. Isto permite-lhe usar uma única chave secreta para autenticar pedidos de vários serviços. 

Quando utilizar uma chave secreta multi-serviço, deve incluir dois cabeçalhos de autenticação com o seu pedido. Há 2 cabeçalhos que precisa chamar de tradutor API.

|Cabeçalhos|Descrição|
|:-----|:----|
|OCP-Apim-Subscription-Key| O valor é a chave secreta Azure para o seu recurso multi-serviço.|
|Ocp-Apim-Região de Assinatura| O valor é a região do recurso multi-serviço. |

A região é necessária para a subscrição de Texto API multi-serviço. A região que seleciona é a única região que pode utilizar para tradução de texto quando utilizar a chave de subscrição multi-serviço, e deve ser a mesma região selecionada quando se inscreveu para a sua assinatura multi-serviço através do portal Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centralus`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `francecentral`, `japaneast`, `japanwest`, `koreacentral`, `northcentralus`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, `westus2`e `southafricanorth`.

Se passar a chave secreta na corda de consulta com o parâmetro `Subscription-Key`, então deve especificar a região com parâmetro de consulta `Subscription-Region`.

### <a name="authenticating-with-an-access-token"></a>Autenticação com ficha de acesso
Em alternativa, pode trocar a sua chave secreta por um sinal de acesso. Esta ficha está incluída a cada pedido como cabeçalho `Authorization`. Para obter um sinal de autorização, faça um pedido de `POST` para o seguinte URL:

| Tipo de recurso     | URL do serviço de autenticação                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regional ou Multi-Serviço | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Aqui estão os pedidos de exemplo para obter um símbolo dada uma chave secreta:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Um pedido bem sucedido devolve o token de acesso codificado como texto simples no corpo de resposta. O símbolo válido é passado para o serviço tradutor como um símbolo portador na Autorização.

```http
Authorization: Bearer <Base64-access_token>
```

Um símbolo de autenticação é válido por 10 minutos. O símbolo deve ser reutilizado ao fazer várias chamadas para as APIs tradutoras. No entanto, se o seu programa fizer pedidos à API tradutordurante um longo período de tempo, o seu programa deve solicitar um novo sinal de acesso a intervalos regulares (por exemplo, a cada 8 minutos).

## <a name="virtual-network-support"></a>Suporte à Rede Virtual

O serviço de tradutor esnows está agora disponível com capacidades de Rede Virtual em regiões limitadas (`WestUS2`, `EastUS`, `SouthCentralUS`, `WestUS`, `Central US EUAP`, `global`). Para ativar a Rede Virtual, consulte configurar redes virtuais de [Serviços Cognitivos Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal). 

Uma vez ligado esta capacidade, deve utilizar o ponto final personalizado para chamar a API tradutora. Não é possível utilizar o ponto final do tradutor global ("api.cognitive.microsofttranslator.com") e não pode autenticar com um símbolo de acesso.

Pode encontrar o ponto final personalizado assim que criar o [recurso tradutor.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)

|Cabeçalhos|Descrição|
|:-----|:----|
|OCP-Apim-Subscription-Key| O valor é a chave secreta Azure para a sua subscrição da API de Texto tradutor.|
|Ocp-Apim-Região de Assinatura| O valor é a região do recurso tradutor. Este valor é opcional se o recurso estiver `global`|

Aqui está um pedido de exemplo para chamar o Tradutor API usando o ponto final personalizado

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Erros

Uma resposta padrão de erro é um objeto JSON com nome/par de valor chamado `error`. O valor é também um objeto JSON com propriedades:

  * `code`: Um código de erro definido pelo servidor.
  * `message`: Uma corda que dá uma representação legível pelo homem do erro.

Por exemplo, um cliente com uma subscrição de teste gratuito receberia o seguinte erro uma vez esgotada a quota gratuita:

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
| 400000| Uma das inputs de pedido não é válida.|
| 400001| O parâmetro "âmbito" é inválido.|
| 400002| O parâmetro "categoria" é inválido.|
| 400003| Falta um especificador de línguas ou inválido.|
| 400004| Falta um especificador de script-alvo ("Para script") ou inválido.|
| 400005| Falta um texto de entrada ou inválido.|
| 400006| A combinação de linguagem e guião não é válida.|
| 400018| Falta um especificador de script de origem ("From script") ou inválido.|
| 400019| Uma das línguas especificadas não é suportada.|
| 400020| Um dos elementos da gama de texto de entrada não é válido.|
| 400021| O parâmetro da versão API está em falta ou inválido.|
| 400023| Um dos pares de idiomas especificados não é válido.|
| 400035| A linguagem fonte (campo "From") não é válida.|
| 400036| A linguagem-alvo (campo "To") está em falta ou inválida.|
| 400042| Uma das opções especificadas (campo "Opções") não é válida.|
| 400043| O id de rastreio do cliente (campo ClientTraceId ou cabeçalho X-ClientTranceId) está em falta ou inválido.|
| 400050| O texto de entrada é muito longo. Ver [limites de pedido](../request-limits.md).|
| 400064| O parâmetro de "tradução" está em falta ou inválido.|
| 400070| O número de scripts-alvo (parâmetro ToScript) não corresponde ao número de idiomas-alvo (Para parâmetro).|
| 400071| O valor não é válido para TextType.|
| 400072| O conjunto de texto sinuoso tem demasiados elementos.|
| 400073| O parâmetro do guião não é válido.|
| 400074| O corpo do pedido não é válido JSON.|
| 400075| O par de idiomas e a combinação de categorias não são válidos.|
| 400077| O tamanho máximo do pedido foi ultrapassado. Ver [limites de pedido](../request-limits.md).|
| 400079| O sistema personalizado solicitado para a tradução entre e a linguagem não existe.|
| 400080| A transliteração não é suportada para a linguagem ou script.|
| 401000| O pedido não é autorizado porque as credenciais estão em falta ou inválidas.|
| 401015| "As credenciais fornecidas são para a API do discurso. Este pedido requer credenciais para a API de texto. Use uma subscrição da API de Texto tradutor."|
| 403000| A operação não é permitida.|
| 403001| A operação não é permitida porque a subscrição excedeu a sua quota gratuita.|
| 405000| O método de pedido não é suportado para o recurso solicitado.|
| 408001| O sistema de tradução solicitado está a ser preparado. Por favor, tente em alguns minutos.|
| 408002| Pedido cronometrado à espera do fluxo de entrada. O cliente não apresentou um pedido no momento em que o servidor estava preparado para esperar. O cliente pode repetir o pedido sem alterações posteriormente.|
| 415000| O cabeçalho do Tipo conteúdo está em falta ou inválido.|
| 429000, 429001, 429002| O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.|
| 500000| Ocorreu um erro inesperado. Se o erro persistir, informe-o com data/hora de erro, solicite o identificador do cabeçalho de resposta X-RequestId e identificador de cliente do cabeçalho de pedido X-ClientTraceId.|
| 503000| Serviço está temporariamente indisponível. Tente novamente. Se o erro persistir, informe-o com data/hora de erro, solicite o identificador do cabeçalho de resposta X-RequestId e identificador de cliente do cabeçalho de pedido X-ClientTraceId.|

## <a name="metrics"></a>Métricas 
As métricas permitem-lhe visualizar a informação de utilização e disponibilidade do tradutor no portal Azure, na secção de métricas, como mostra a imagem abaixo. Para mais informações, consulte [data e métricas da plataforma.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)

![Métricas de Tradutor](../media/translatormetrics.png)

Esta tabela lista métricas disponíveis com descrição de como são usadas para monitorizar chamadas de Tradução API.

| Métricas | Descrição |
|:----|:-----|
| Totalcall| Número total de chamadas da API.|
| TotalTokenCalls| Número total de chamadas API através de serviço simbólico usando ficha de autenticação.|
| Chamadas bem sucedidas| Número de chamadas bem sucedidas.|
| TotalErrors| Número de chamadas com resposta a erros.|
| BlockedCalls| Número de chamadas que excederam a taxa ou o limite de quota.|
| Erros de servidor| Número de chamadas com erro interno do servidor (5XX).|
| Erros de cliente| Número de chamadas com erro do lado do cliente (4XX).|
| Latência| Duração para completar o pedido em milissegundos.|
| CharactersTranslated| Número total de caracteres no pedido de texto.|
