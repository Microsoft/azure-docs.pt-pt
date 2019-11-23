---
title: API de Tradução de Texto v 2.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o API de Tradução de Texto v 2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242488"
---
# <a name="translator-text-api-v20"></a>API de Tradução de Texto v 2.0

> [!IMPORTANT]
> Esta versão do API de Tradução de Texto foi preterida. [Exiba a documentação da versão 3 do API de tradução de texto](v3-0-reference.md).

A versão 2 do API de Tradução de Texto pode ser integrada diretamente em seus aplicativos, sites, ferramentas ou outras soluções para fornecer experiências de usuário em vários idiomas. Você pode usá-lo em qualquer plataforma de hardware e em qualquer sistema operacional para executar a tradução de linguagem e outras tarefas relacionadas à linguagem, como detecção de idioma de texto e conversão de texto em fala, de acordo com os padrões do setor. Para obter mais informações, consulte [API de tradução de texto](../translator-info-overview.md).

## <a name="getting-started"></a>Introdução
Para acessar o API de Tradução de Texto, você precisa [se inscrever para Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autenticação 
Todas as chamadas para o API de Tradução de Texto exigem uma chave de assinatura para autenticação. A API dá suporte a três métodos de autenticação:

- Um token de acesso. Use a chave de assinatura para criar um token de acesso fazendo uma solicitação POST para o serviço de autenticação. Consulte a documentação do serviço de token para obter detalhes. Passe o token de acesso para o serviço do tradutor usando o cabeçalho `Authorization` ou o parâmetro de consulta `access_token`. O token de acesso é válido por 10 minutos. Obtenha um novo token de acesso a cada 10 minutos e continue usando o mesmo token de acesso para solicitações repetidas durante os 10 minutos.
- Uma chave de assinatura usada diretamente. Passe sua chave de assinatura como um valor no cabeçalho `Ocp-Apim-Subscription-Key` incluído com sua solicitação para o API de Tradução de Texto. Quando você usa a chave de assinatura diretamente, não precisa chamar o serviço de autenticação de token para criar um token de acesso.
- Uma [assinatura de vários serviços do Azure cognitiva Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Esse método permite que você use uma única chave secreta para autenticar solicitações para vários serviços.
Ao usar uma chave secreta de vários serviços, você precisa incluir dois cabeçalhos de autenticação com sua solicitação. O primeiro cabeçalho passa a chave secreta. O segundo cabeçalho especifica a região associada à sua assinatura:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A região é necessária para a assinatura de API de texto de vários serviços. A região selecionada é a única região que você pode usar para a tradução de texto ao usar a chave de assinatura de vários serviços. Ele precisa ser a mesma região que você selecionou quando se inscreveu para sua assinatura de vários serviços no portal do Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`e `westus2`.

Sua chave de assinatura e o token de acesso são segredos que devem ser ocultados da exibição.

## <a name="profanity-handling"></a>Manipulação de obscenidades
Normalmente, o serviço do tradutor reterá profanação que está presente na origem. O grau de profanação e o contexto que torna as palavras obscenas diferem de acordo com a cultura. Portanto, o grau de profanação no idioma de destino pode ser aumentado ou reduzido.

Se você quiser evitar profanação na tradução mesmo quando ela estiver no texto de origem, você poderá usar a opção de filtragem de profanação para os métodos que dão suporte a ela. A opção permite que você escolha se deseja ver a profanação excluída ou marcada com as marcas apropriadas, ou se deseja permitir a profanação no destino. Os valores aceitos de `ProfanityAction` são `NoAction` (padrão), `Marked`e `Deleted`.


|ProfanityAction    |Ação |Exemplo de origem (japonês)  |Tradução de exemplo (inglês)  |
|:--|:--|:--|:--|
|NoAction   |Predefinição. O mesmo que não definir a opção. A profanação passará da origem para a de destino.        |彼はジャッカスです。     |Ele é um Jackass.   |
|Marca     |Palavras obscenas serão circundadas por marcas XML \<profanação > e \</profanity >.       |彼はジャッカスです。 |Ele é uma profanação \<> Jackass\</profanity >.  |
|Eliminado    |Palavras obscenas serão removidas da saída sem substituição.     |彼はジャッカスです。 |Ele é um.   |

    
## <a name="excluding-content-from-translation"></a>Excluindo conteúdo da tradução
Quando você traduz o conteúdo com marcas, como HTML (`contentType=text/html`), às vezes é útil excluir conteúdo específico da tradução. Você pode usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer em seu idioma original. No exemplo a seguir, o conteúdo no primeiro elemento `div` não será convertido, mas o conteúdo no segundo elemento `div` será traduzido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>OBTER/translate

### <a name="implementation-notes"></a>Notas de implementação
Traduz uma cadeia de texto de um idioma para outro.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valor de retorno:** Uma cadeia de caracteres que representa o texto traduzido.

Se você usou anteriormente `AddTranslation` ou `AddTranslationArray` para inserir uma tradução com uma classificação de 5 ou superior para a mesma sentença de origem, `Translate` retornará apenas a escolha mais alta disponível para o seu sistema. "Mesma sentença de origem" significa exatamente o mesmo (100% de correspondência), exceto para capitalização, espaço em branco, valores de marca e pontuação no final de uma sentença. Se nenhuma classificação for armazenada com uma classificação de 5 ou superior, o resultado retornado será a tradução automática pelo Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo da resposta: Application/XML

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição    |Tipo de parâmetro|tipo de dados|
|:--|:--|:--|:--|:--|
|appid  |esvaziá    |Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|esvaziá   |Necessário. Uma cadeia de caracteres que representa o texto a ser traduzido. O texto não pode conter mais de 10.000 caracteres.|consulta|string|
|from|esvaziá   |Opcional. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo traduzido. Por exemplo, en para inglês.|consulta|string|
|para|esvaziá |Necessário. Uma cadeia de caracteres que representa o código do idioma no qual converter o texto.|consulta|string|
|contentType|esvaziá    |Opcional. O formato do texto que está sendo traduzido. Os formatos com suporte são `text/plain` (padrão) e `text/html`. Todos os elementos HTML precisam ser bem formados, elementos completos.|consulta|string|
|categoria|esvaziá   |Opcional. Uma cadeia de caracteres que contém a categoria (domínio) da tradução. A predefinição é `general`.|consulta|string|
|Autorização|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="post-translatearray"></a>POSTAR/TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera traduções para vários textos de origem.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Este é o formato do corpo da solicitação:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Esses elementos estão em `TranslateArrayRequest`:


* `AppId`: obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `AppId` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.
* `From`: opcional. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo traduzido. Se esse campo for deixado vazio, a resposta incluirá o resultado da detecção automática de idioma.
* `Options`: opcional. Um objeto `Options` que contém os valores a seguir. Eles são todos opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser listados em ordem alfabética.
    - `Category`: uma cadeia de caracteres que contém a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: o formato do texto que está sendo traduzido. Os formatos com suporte são `text/plain` (padrão), `text/xml`e `text/html`. Todos os elementos HTML precisam ser bem formados, elementos completos.
    - `ProfanityAction`: especifica como as obscenidades são tratadas, conforme explicado anteriormente. Os valores aceitos são `NoAction` (padrão), `Marked`e `Deleted`.
    - `State`: estado do usuário para ajudar a correlacionar a solicitação e a resposta. O mesmo conteúdo será retornado na resposta.
    - `Uri`: filtrar os resultados por este URI. Padrão: `all`.
    - `User`: filtrar os resultados por este usuário. Padrão: `all`.
* `Texts`: obrigatório. Uma matriz que contém o texto para tradução. Todas as cadeias de caracteres devem estar no mesmo idioma. O total de todo o texto a ser traduzido não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é 2.000.
* `To`: obrigatório. Uma cadeia de caracteres que representa o código do idioma no qual converter o texto.

Você pode omitir elementos opcionais. Os elementos que são filhos diretos de `TranslateArrayRequest` devem ser listados em ordem alfabética.

O método `TranslateArray` aceita `application/xml` ou `text/xml` para `Content-Type`.

**Valor de retorno:** Uma matriz de `TranslateArrayResponse`. Cada `TranslateArrayResponse` tem estes elementos:

* `Error`: indica um erro se ocorrer um. Caso contrário, defina como nulo.
* `OriginalSentenceLengths`: uma matriz de inteiros que indica o comprimento de cada sentença no texto de origem. O comprimento da matriz indica o número de sentenças.
* `TranslatedText`: o texto traduzido.
* `TranslatedSentenceLengths`: uma matriz de inteiros que indica o comprimento de cada sentença no texto traduzido. O comprimento da matriz indica o número de sentenças.
* `State`: estado do usuário para ajudar a correlacionar a solicitação e a resposta. Retorna o mesmo conteúdo da solicitação.

Este é o formato do corpo da resposta:

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma resposta bem-sucedida inclui uma matriz de matrizes de `TranslateArrayResponse` no formato descrito anteriormente.

string

Tipo de conteúdo da resposta: Application/XML

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|Autorização|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP   |Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada. Erros comuns incluem: <ul><li>O elemento da matriz não pode ficar vazio.</li><li>Categoria inválida.</li><li>Do idioma inválido.</li><li>O idioma é inválido.</li><li>A solicitação contém muitos elementos.</li><li>Não há suporte para o idioma de.</li><li>Não há suporte para o idioma to.</li><li>A solicitação de conversão tem muitos dados.</li><li>O HTML não está em um formato correto.</li><li>Muitas cadeias de caracteres foram passadas na solicitação de conversão.</li></ul>|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POSTAR/GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Recupera nomes amigáveis para os idiomas passados como o parâmetro `languageCodes`, localizado na linguagem passada `locale`.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

O corpo da solicitação inclui uma matriz de cadeia de caracteres que representa os códigos de idioma ISO 639-1 para os quais recuperar os nomes amigáveis. Segue-se um exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém nomes de idiomas com suporte pelo serviço do tradutor, localizado no idioma solicitado.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém nomes de idiomas com suporte pelo serviço do tradutor, localizado no idioma solicitado.

string

Tipo de conteúdo da resposta: Application/XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|localidade|esvaziá |Necessário. Uma cadeia de caracteres que representa um dos seguintes, usado para localizar os nomes dos idiomas: <ul><li>A combinação de um código de cultura de letra minúscula ISO 639 2 associado a uma linguagem e um código de subcultura em maiúsculas de letra ISO 3166 2. <li>Um código de cultura ISO 639 minúsculo por si só.|consulta|string|
|Autorização|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma lista de códigos de idioma que representam os idiomas com suporte no serviço de tradução.  `Translate` e `TranslateArray` podem ser convertidos entre dois desses idiomas.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte no serviço do tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte no serviço do tradutor.

string

Tipo de conteúdo da resposta: Application/XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|Autorização|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Recupera os idiomas disponíveis para síntese de fala.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte para a síntese de fala pelo serviço do tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma com suporte para a síntese de fala pelo serviço do tradutor.

string

Tipo de conteúdo da resposta: Application/XML

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="get-speak"></a>OBTER/Speak

### <a name="implementation-notes"></a>Notas de implementação
Retorna um fluxo WAV ou MP3 do texto passado, falado na linguagem desejada.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valor de retorno:** Um fluxo WAV ou MP3 do texto passado, falado na linguagem desejada.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

binary

Tipo de conteúdo da resposta: Application/XML

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|esvaziá   |Necessário. Uma cadeia de caracteres que contém uma ou mais frases a serem faladas para o fluxo, no idioma especificado. O texto não deve exceder 2.000 caracteres.|consulta|string|
|language|esvaziá   |Necessário. Uma cadeia de caracteres que representa o código de idioma com suporte do idioma no qual falar o texto. O código deve ser um dos códigos retornados pelo método `GetLanguagesForSpeak`.|consulta|string|
|format|esvaziá|Opcional. Uma cadeia de caracteres que especifica a ID do tipo de conteúdo. Atualmente, `audio/wav` e `audio/mp3` estão disponíveis. O valor predefinido é `audio/wav`.|consulta|string|
|options|esvaziá    |Opcional. Uma cadeia de caracteres que especifica as propriedades da fala sintetizada:<ul><li>`MaxQuality` e `MinSize` especifique a qualidade do sinal de áudio. `MaxQuality` fornece a qualidade mais alta. `MinSize` fornece o menor tamanho de arquivo. O padrão é `MinSize`.</li><li>`female` e `male` especifique o gênero desejado da voz. A predefinição é `female`. Use a barra vertical (<code>\|</code>) para incluir várias opções. Por exemplo, `MaxQuality|Male`.</li></li></ul>  |consulta|string|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="get-detect"></a>OBTER/Detect

### <a name="implementation-notes"></a>Notas de implementação
Identifica o idioma de uma seção de texto.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valor de retorno:** Uma cadeia de caracteres que contém um código de idioma de dois caracteres para o texto.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo da resposta: Application/XML

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá  |Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|esvaziá|Necessário. Uma cadeia de caracteres que contém o texto cujo idioma deve ser identificado. O texto não deve exceder 10.000 caracteres.|consulta|  string|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key  |esvaziá    |Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|


## <a name="post-detectarray"></a>POSTAR/DetectArray

### <a name="implementation-notes"></a>Notas de implementação

Identifica os idiomas em uma matriz de cadeias de caracteres. Detecta de forma independente o idioma de cada elemento da matriz individual e retorna um resultado para cada linha da matriz.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Este é o formato do corpo da solicitação:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O texto não pode exceder 10.000 caracteres.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém um código de idioma de dois caracteres para cada linha na matriz de entrada.

Este é o formato do corpo da resposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`DetectArray` foi bem-sucedida. Retorna uma matriz de cadeia de caracteres que contém um código de idioma de dois caracteres para cada linha da matriz de entrada.

string

Tipo de conteúdo da resposta: Application/XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="get-addtranslation"></a>OBTER/AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Observação de substituição:** Após 31 de janeiro de 2018, esse método não aceitará novos envios de frase. Você receberá uma mensagem de erro. Consulte o comunicado sobre as alterações na estrutura de tradução colaborativa (CTF).

Adiciona uma tradução à memória de tradução.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo da resposta: Application: XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type   |
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|originalText|esvaziá|Necessário. Uma cadeia de caracteres que contém o texto a ser traduzido. O comprimento máximo da cadeia de caracteres é de 1.000 caracteres.|consulta|string|
|translatedText|esvaziá |Necessário. Uma cadeia de caracteres que contém texto traduzido no idioma de destino. O comprimento máximo da cadeia de caracteres é de 2.000 caracteres.|consulta|string|
|from|esvaziá   |Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma original do texto. Por exemplo, en para inglês e de alemão.|consulta|string|
|para|esvaziá|Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma no qual converter o texto.|consulta|string|
|classificação|esvaziá |Opcional. Um inteiro que representa a classificação de qualidade da cadeia de caracteres. O valor está entre-10 e 10. O padrão é 1.|consulta|número inteiro|
|contentType|esvaziá    |Opcional. O formato do texto que está sendo traduzido. Os formatos com suporte são `text/plain` e `text/html`. Todos os elementos HTML precisam ser bem formados, elementos completos.    |consulta|string|
|categoria|esvaziá|Opcional. Uma cadeia de caracteres que contém a categoria (domínio) da tradução. A predefinição é `general`.|consulta|string|
|Utilizador|esvaziá|Necessário. Uma cadeia de caracteres que é usada para rastrear o originador do envio.|consulta|string|
|URI|esvaziá|Opcional. Uma cadeia de caracteres que contém o local do conteúdo da tradução.|consulta|string|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.  |cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410|Não há mais suporte para `AddTranslation`.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Observação de substituição:** Após 31 de janeiro de 2018, esse método não aceitará novos envios de frase. Você receberá uma mensagem de erro. Consulte o comunicado sobre as alterações na estrutura de tradução colaborativa (CTF).

Adiciona uma matriz de traduções à memória de tradução. Este método é uma versão de matriz do `AddTranslation`.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Este é o formato do corpo da solicitação:

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Esses elementos estão em `AddtranslationsRequest`:

* `AppId`: obrigatório. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `AppId` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.
* `From`: obrigatório. Uma cadeia de caracteres que contém o código de idioma do idioma de origem. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `To`: obrigatório. Uma cadeia de caracteres que contém o código de idioma do idioma de destino. Deve ser um dos idiomas retornados pelo método `GetLanguagesForTranslate`.
* `Translations`: obrigatório. Uma matriz de traduções a serem adicionadas à memória de tradução. Cada tradução deve conter `OriginalText`, `TranslatedText`e `Rating`. O tamanho máximo de cada `OriginalText` e `TranslatedText` é de 1.000 caracteres. O total de todos os elementos `OriginalText` e `TranslatedText` não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é 100.
* `Options`: obrigatório. Um conjunto de opções, incluindo `Category`, `ContentType`, `Uri`e `User`. `User` é necessário. `Category`, `ContentType`e `Uri` são opcionais. Os elementos especificados devem ser listados em ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
método de `AddTranslationArray` bem-sucedido. 

Após 31 de janeiro de 2018, os envios de frase não serão aceitos. O serviço responderá com o código de erro 410.

string

Tipo de conteúdo da resposta: Application/XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410    |Não há mais suporte para `AddTranslation`.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Quebra uma seção de texto em sentenças e retorna uma matriz que contém os comprimentos de cada sentença.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valor de retorno:** Uma matriz de inteiros que representa os comprimentos das frases. O comprimento da matriz representa o número de sentenças. Os valores representam o comprimento de cada frase.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de inteiros que representa os comprimentos das frases. O comprimento da matriz representa o número de sentenças. Os valores representam o comprimento de cada frase.

número inteiro

Tipo de conteúdo da resposta: Application/XML

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá  |Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta| string|
|texto|esvaziá   |Necessário. Uma cadeia de caracteres que representa o texto a ser dividido em sentenças. O tamanho máximo do texto é de 10.000 caracteres.|consulta|string|
|language   |esvaziá    |Necessário. Uma cadeia de caracteres que representa o código de idioma do texto de entrada.|consulta|string|
|Autorização|esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.   |cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá|Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500|Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POSTAR/GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Recupera uma matriz de traduções para um determinado par de idiomas da loja e do mecanismo MT. `GetTranslations` difere de `Translate` no que retorna todas as traduções disponíveis.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

O corpo da solicitação inclui o objeto `TranslationOptions` opcional, que tem este formato:

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

O objeto `TranslateOptions` contém os valores na lista a seguir. Eles são todos opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser listados em ordem alfabética.

* `Category`: uma cadeia de caracteres que contém a categoria (domínio) da tradução. A predefinição é `general`.
* `ContentType`: a única opção com suporte e o padrão é `text/plain`.
* `IncludeMultipleMTAlternatives`: um sinalizador booliano para especificar se mais de uma alternativa deve ser retornada do mecanismo MT. Os valores válidos são `true` e `false` (diferencia maiúsculas de minúsculas). O padrão é `false`, que retorna apenas uma alternativa. Definir o sinalizador como `true` permite a criação de alternativas artificiais, totalmente integradas à estrutura de tradução colaborativa (CTF). O recurso permite retornar alternativas para frases que não têm nenhuma tradução em CTF adicionando alternativas artificiais da lista *n*melhores do decodificador.
    - As. As classificações são aplicadas desta forma: 
         - A melhor tradução automática tem uma classificação de 5.
       - As alternativas de CTF refletem a autoridade do revisor. Elas variam de-10 a + 10.
       - As alternativas de tradução (*n*-melhor) geradas automaticamente têm uma classificação de 0 e um grau de correspondência de 100.
    - Número de alternativas. O número de alternativas retornadas pode ser tão alto quanto o valor especificado em `maxTranslations`, mas pode ser menor.
    - Pares de idiomas. Essa funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Ele está disponível para todos os outros pares de idiomas com suporte do Microsoft Translator.
* `State`: estado do usuário para ajudar a correlacionar a solicitação e a resposta. O mesmo conteúdo será retornado na resposta.
* `Uri`: filtrar os resultados por este URI. Se nenhum valor for definido, o padrão será `all`.
* `User`: filtrar os resultados por este usuário. Se nenhum valor for definido, o padrão será `all`.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor de retorno:** Este é o formato da resposta:

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Essa resposta inclui um elemento `GetTranslationsResponse` que contém os seguintes valores:

* `Translations`: uma matriz das correspondências encontradas, armazenadas em `TranslationMatch` objetos (descritos na seção a seguir). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde primeiro, correspondências difusas em seguida.
* `From`: se o método não especificar um idioma de `From`, esse valor será proveniente da detecção automática de idioma. Caso contrário, será o idioma de `From` especificado.
* `State`: estado do usuário para ajudar a correlacionar a solicitação e a resposta. Contém o valor fornecido no parâmetro `TranslateOptions`.

O objeto `TranslationMatch` consiste nesses valores:

* `Error`: o código de erro, se ocorrer um erro para uma cadeia de caracteres de entrada específica. Caso contrário, esse campo estará vazio.
* `MatchDegree`: indica o quão próximo o texto de entrada corresponde ao texto original encontrado no repositório. O sistema faz a correspondência de frases de entrada em relação à loja, incluindo correspondências inexatas. O valor retornado varia de 0 a 100, em que 0 é nenhuma similaridade e 100 é uma correspondência exata, que diferencia maiúsculas de minúsculas.
* `MatchedOriginalText`: texto original que foi correspondido para esse resultado. Esse valor será retornado somente se o texto original correspondente tiver sido diferente do texto de entrada. Ele é usado para retornar o texto de origem de uma correspondência difusa. Esse valor não é retornado para os resultados do Microsoft Translator.
* `Rating`: indica a autoridade da pessoa que está tomando a decisão de qualidade. Os resultados da tradução automática têm uma classificação de 5. As traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas de forma autoritativa geralmente terão uma classificação de 6 a 10.
* `Count`: o número de vezes que esta tradução com esta classificação foi selecionada. O valor é 0 para a resposta convertida automaticamente.
* `TranslatedText`: o texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Um objeto `GetTranslationsResponse` no formato descrito anteriormente.

string

Tipo de conteúdo da resposta: Application/XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|appid|esvaziá|Necessário. Se o cabeçalho `Authorization` ou `Ocp-Apim-Subscription-Key` for usado, deixe o campo `appid` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|esvaziá|Necessário. Uma cadeia de caracteres que representa o texto a ser traduzido. O tamanho máximo do texto é de 10.000 caracteres.|consulta|string|
|from|esvaziá|Necessário. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo traduzido.|consulta|string|
|para |esvaziá    |Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma no qual converter o texto.|consulta|string|
|maxTranslations|esvaziá|Necessário. Um inteiro que representa o número máximo de traduções a serem retornadas.|consulta|número inteiro|
|Autorização| esvaziá|Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios. Token de autorização: `"Bearer" + " " + "access_token"`.|string|  cabeçalho|
|OCP-Apim-Subscription-Key|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POSTAR/GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera vários candidatos à tradução para vários textos de origem.

O URI de solicitação é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Este é o formato do corpo da solicitação:

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` inclui estes elementos:

* `AppId`: obrigatório. Se o cabeçalho de `Authorization` for usado, deixe o campo de `AppId` vazio. Caso contrário, inclua uma cadeia de caracteres que contenha `"Bearer" + " " + "access_token"`.
* `From`: obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto que está sendo traduzido.
* `MaxTranslations`: obrigatório. Um inteiro que representa o número máximo de traduções a serem retornadas.
* `Options`: opcional. Um objeto `Options` que contém os valores a seguir. Eles são todos opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser listados em ordem alfabética.
    - `Category`: uma cadeia de caracteres que contém a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: a única opção com suporte e o padrão é `text/plain`.
    - `IncludeMultipleMTAlternatives`: um sinalizador booliano para especificar se mais de uma alternativa deve ser retornada do mecanismo MT. Os valores válidos são `true` e `false` (diferencia maiúsculas de minúsculas). O padrão é `false`, que retorna apenas uma alternativa. Definir o sinalizador para `true` permite a geração de alternativas artificiais na tradução, totalmente integrada à estrutura de traduções colaborativa (CTF). O recurso permite retornar alternativas para frases que não têm alternativas em CTF adicionando alternativas artificiais da lista *n*melhores do decodificador.
        - As classificações são aplicadas como esta:
          - A melhor tradução automática tem uma classificação de 5.
          - As alternativas de CTF refletem a autoridade do revisor. Elas variam de-10 a + 10.
          - As alternativas de tradução (*n*-melhor) geradas automaticamente têm uma classificação de 0 e um grau de correspondência de 100.
        - Número de alternativas. O número de alternativas retornadas pode ser tão alto quanto o valor especificado em `maxTranslations`, mas pode ser menor.
        - Pares de idiomas. Essa funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Ele está disponível para todos os outros pares de idiomas com suporte do Microsoft Translator.
* `State`: estado do usuário para ajudar a correlacionar a solicitação e a resposta. O mesmo conteúdo será retornado na resposta.
* `Uri`: filtrar os resultados por este URI. Se nenhum valor for definido, o padrão será `all`.
* `User`: filtrar os resultados por este usuário. Se nenhum valor for definido, o padrão será `all`.
* `Texts`: obrigatório. Uma matriz que contém o texto para tradução. Todas as cadeias de caracteres devem estar no mesmo idioma. O total de todo o texto a ser traduzido não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é 10.
* `To`: obrigatório. Uma cadeia de caracteres que representa o código de idioma do idioma no qual converter o texto.

Você pode omitir elementos opcionais. Os elementos que são filhos diretos de `GetTranslationsArrayRequest` devem ser listados em ordem alfabética.

A solicitação `Content-Type` deve ser `text/xml`.

**Valor de retorno:** Este é o formato da resposta:

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Cada elemento de `GetTranslationsResponse` contém estes valores:

* `Translations`: uma matriz das correspondências encontradas, armazenadas em `TranslationMatch` objetos (descritos na seção a seguir). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde primeiro, correspondências difusas em seguida.
* `From`: se o método não especificar um idioma de `From`, esse valor será proveniente da detecção automática de idioma. Caso contrário, será o idioma de `From` especificado.
* `State`: estado do usuário para ajudar a correlacionar a solicitação e a resposta. Contém o valor fornecido no parâmetro `TranslateOptions`.

O objeto `TranslationMatch` contém os seguintes valores:
* `Error`: o código de erro, se ocorrer um erro para uma cadeia de caracteres de entrada específica. Caso contrário, esse campo estará vazio.
* `MatchDegree`: indica o quão próximo o texto de entrada corresponde ao texto original encontrado no repositório. O sistema faz a correspondência de frases de entrada em relação à loja, incluindo correspondências inexatas. O valor retornado varia de 0 a 100, em que 0 é nenhuma similaridade e 100 é uma correspondência exata, que diferencia maiúsculas de minúsculas.
* `MatchedOriginalText`: texto original que foi correspondido para esse resultado. Esse valor será retornado somente se o texto original correspondente tiver sido diferente do texto de entrada. Ele é usado para retornar o texto de origem de uma correspondência difusa. Esse valor não é retornado para os resultados do Microsoft Translator.
* `Rating`: indica a autoridade da pessoa que está tomando a decisão de qualidade. Os resultados da tradução automática têm uma classificação de 5. As traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas com autorização geralmente têm uma classificação de 6 a 10.
* `Count`: o número de vezes que esta tradução com esta classificação foi selecionada. O valor é 0 para a resposta convertida automaticamente.
* `TranslatedText`: o texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo da resposta: Application/XML
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Data type|
|:--|:--|:--|:--|:--|
|Autorização  |esvaziá    |Necessário se o campo `appid` e o cabeçalho `Ocp-Apim-Subscription-Key` forem deixados vazios.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|esvaziá  |Necessário se o campo `appid` e o cabeçalho `Authorization` forem deixados vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Solicitação inadequada. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, informe-nos. Forneça a data aproximada & hora da solicitação e com a ID da solicitação incluída no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço temporariamente indisponível. Tente novamente e avise-nos se o erro persistir.|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para o API de Tradução de Texto v3](../migrate-to-v3.md)


