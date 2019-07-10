---
title: V2.0 de API de texto do tradutor
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do Translator v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: a29e123c44ca198ce19db451ee4c624b6f993538
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705433"
---
# <a name="translator-text-api-v20"></a>V2.0 de API de texto do tradutor

> [!IMPORTANT]
> Esta versão da API de texto do Translator foi preterido. [Ver documentação para a versão 3 da API de texto do Translator](v3-0-reference.md).

Versão 2 do API de texto do Translator pode ser integrado de forma totalmente integrada em seus aplicativos, Web sites, ferramentas ou outras soluções para fornecer experiências de usuário multilíngüe. Pode utilizá-lo em qualquer plataforma de hardware e com qualquer sistema operativo para efetuar a tradução de idioma e outras tarefas relacionadas com a linguagem, como deteção de idioma de texto e o texto em voz, de acordo com padrões da indústria. Para obter mais informações, consulte [API de texto do Translator](../translator-info-overview.md).

## <a name="getting-started"></a>Introdução
Para aceder à API de texto do Translator, precisa [Inscreva-se para o Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autenticação 
Todas as chamadas à API de texto do Translator requerem uma chave de subscrição para a autenticação. A API oferece suporte a três métodos de autenticação:

- Um token de acesso. Utilize a chave de subscrição referenciada no passo 9 para criar um token de acesso ao fazer um pedido POST ao serviço de autenticação. Consulte a documentação do serviço de token para obter detalhes. Transmitir o token de acesso ao serviço de Microsoft Translator, utilizando o `Authorization` cabeçalho ou o `access_token` parâmetro de consulta. O token de acesso é válido durante 10 minutos. Obter um novo token de acesso a cada 10 minutos e continuar a utilizar o mesmo acesso o token para pedidos repetidos durante 10 minutos.
- Uma chave de subscrição utilizada diretamente. Passar a sua chave de assinatura como um valor no `Ocp-Apim-Subscription-Key` cabeçalho incluído com o seu pedido para a API de texto do Translator. Quando utiliza a chave de subscrição diretamente, não é preciso chamar o serviço de autenticação de token para criar um token de acesso.
- Uma [subscrição dos serviços cognitivos do Azure de múltiplos serviço](https://azure.microsoft.com/pricing/details/cognitive-services/). Este método permite-lhe utilizar uma única chave secreta para autenticar pedidos para vários serviços.
Quando utiliza uma chave secreta múltiplos serviço, precisa incluir dois cabeçalhos de autenticação com o seu pedido. O cabeçalho de primeiro transmite a chave secreta. O cabeçalho de segundo Especifica a região associada à sua subscrição:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A região é necessária para a subscrição múltiplos serviço de API de texto. A região que selecionou é a região que pode utilizar para a tradução de texto quando utiliza a chave de subscrição de múltiplos serviços. Tem de ser a mesma região que selecionou quando se inscreveu para a sua subscrição múltiplos serviço no portal do Azure.

As regiões disponíveis são `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, e `westus2`.

Sua chave de subscrição e o access token são segredos que devem ser ocultados da vista.

## <a name="profanity-handling"></a>Processamento de linguagem inapropriada
Normalmente, o serviço Microsoft Translator irá reter a linguagem inapropriada que está presente na origem. O grau de linguagem inapropriada e contexto que torna as palavras impróprias diferem de acordo com a cultura. Então, o grau de linguagem inapropriada no idioma de destino foi aumentado ou reduzido.

Se quiser evitar que linguagem inapropriada na tradução, mesmo quando está a ser o texto de origem, pode utilizar a linguagem inapropriada filtragem opção para os métodos que o suportam. A opção permite-lhe escolher se pretende ver linguagem inapropriada eliminada ou marcada com etiquetas adequadas, ou se pretende permitir que a linguagem inapropriada no destino. Os valores aceites `ProfanityAction` estão `NoAction` (predefinição), `Marked`, e `Deleted`.


|ProfanityAction    |Action |Origem de exemplo (japonês)  |Tradução de exemplo (em inglês)  |
|:--|:--|:--|:--|
|NoAction   |Predefinição. Mesmo que não definir a opção. Linguagem inapropriada passará de origem ao destino.        |彼はジャッカスです。     |Ele é um jackass.   |
|Marcado     |Palavras profanas ficará rodeadas por marcas XML \<linguagem inapropriada > e \</profanity >.       |彼はジャッカスです。 |Ele é um \<linguagem inapropriada > jackass\</profanity >.  |
|Eliminado    |Palavras profanas serão removidas da saída sem substituição.     |彼はジャッカスです。 |Ele é um.   |

    
## <a name="excluding-content-from-translation"></a>Excluir o conteúdo de tradução
Quando a converter conteúdo com marcas, como o HTML (`contentType=text/html`), às vezes é útil excluir o conteúdo específico da tradução. Pode usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer em seu idioma original. No exemplo a seguir, o conteúdo na primeira `div` elemento não ser traduzido, mas o conteúdo na segunda `div` elemento será convertido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>OBTER / traduzir

### <a name="implementation-notes"></a>Notas de implementação
Converte uma cadeia de texto de um idioma para outro.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valor de retorno:** Uma cadeia de caracteres que representa o texto traduzido.

Se tiver utilizado anteriormente `AddTranslation` ou `AddTranslationArray` para introduzir uma tradução com uma classificação de 5 ou superior para a mesma frase de origem, `Translate` retorna apenas a primeira escolha que está disponível para o seu sistema. "Frase de mesma origem" significa exatamente o mesmo (100% correspondência), exceto para a capitalização, espaço em branco, valores de etiqueta e a pontuação no final de uma frase. Se nenhuma classificação é armazenada com uma classificação de 5 ou superior, o resultado retornado será a tradução automática ao Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição    |Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid  |(vazio)    |Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|texto|(vazio)   |Necessário. Uma cadeia de caracteres que representa o texto a traduzir. O texto não pode conter mais de 10.000 carateres.|query|string|
|from|(vazio)   |Opcional. Uma cadeia de caracteres que representa o código de idioma do texto que está a ser traduzido. Por exemplo, en para inglês.|query|string|
|para|(vazio) |Necessário. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.|query|string|
|contentType|(vazio)    |Opcional. O formato de texto a ser traduzido. Formatos suportados são `text/plain` (predefinição) e `text/html`. Todos os elementos HTML tem de ser elementos bem formados e completos.|query|string|
|category|(vazio)   |Opcional. Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.|query|string|
|Autorização|(vazio)  |Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-translatearray"></a>PUBLICAR /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Obtém as traduções para vários textos de origem.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

Este é o formato do corpo do pedido:

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

Estes elementos são no `TranslateArrayRequest`:


* `AppId`: Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `AppId` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.
* `From`: Opcional. Uma cadeia de caracteres que representa o código de idioma do texto que está a ser traduzido. Se este campo é deixado em branco, a resposta irá incluir o resultado da deteção de idioma automática.
* `Options`: Opcional. Um `Options` objeto que contém os seguintes valores. Eles são todos opcionais e predefinido para as definições mais comuns. Elementos especificados tem de estar listados em ordem alfabética.
    - `Category`: Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: O formato de texto a ser traduzido. Os formatos suportados são `text/plain` (predefinição), `text/xml`, e `text/html`. Todos os elementos HTML tem de ser elementos bem formados e completos.
    - `ProfanityAction`: Especifica como são tratadas profanities, conforme explicado anteriormente. Aceite os valores são `NoAction` (predefinição), `Marked`, e `Deleted`.
    - `State`: Estado do utilizador para o ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será devolvido na resposta.
    - `Uri`: Filtre os resultados por este URI. Predefinição: `all`.
    - `User`: Filtre os resultados por este utilizador. Predefinição: `all`.
* `Texts`: Necessário. Uma matriz que contém o texto para a tradução. Todas as cadeias de caracteres devem estar no mesmo idioma. O total de todo o texto para ser convertido não pode ter mais de 10.000 carateres. O número máximo de elementos de matriz é de 2000.
* `To`: Necessário. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.

Pode omitir elementos opcionais. Direcionar os elementos que são filhos do `TranslateArrayRequest` tem de estar listada em ordem alfabética.

O `TranslateArray` método aceita `application/xml` ou `text/xml` para `Content-Type`.

**Valor de retorno:** A `TranslateArrayResponse` matriz. Cada `TranslateArrayResponse` tem esses elementos:

* `Error`: Indica um erro se encontrarmos um. Caso contrário, definida como nula.
* `OriginalSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada sentença no texto de origem. O comprimento da matriz indica o número de frases.
* `TranslatedText`: O texto traduzido.
* `TranslatedSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada frase o texto traduzido. O comprimento da matriz indica o número de frases.
* `State`: Estado do utilizador para o ajudar a correlacionar a solicitação e resposta. Devolve o mesmo conteúdo da solicitação.

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
Uma resposta com êxito inclui uma matriz de `TranslateArrayResponse` matrizes no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)  |Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP   |Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas. Erros comuns incluem: <ul><li>Elemento de matriz não pode estar vazio.</li><li>Categoria inválida.</li><li>De idioma é inválido.</li><li>A linguagem é inválida.</li><li>O pedido contém demasiados elementos.</li><li>O idioma de From não é suportado.</li><li>O idioma de em não é suportado.</li><li>Traduzir pedido tem demasiados dados.</li><li>HTML není ve správném formátu.</li><li>Cadeias de caracteres demasiados passadas para a solicitação de traduzir.</li></ul>|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Obtém nomes amigáveis para os idiomas passado como o parâmetro `languageCodes`, localizada em transmitido `locale` idioma.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

O corpo do pedido inclui uma matriz de cadeia que representa os códigos de idioma 639-1 ISO para o qual pretende obter os nomes amigáveis. Segue-se um exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém nomes de idiomas suportados pelo serviço do Microsoft Translator, localizado para o idioma pedido.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia que contém nomes de idiomas suportados pelo serviço do Microsoft Translator, localizado para o idioma pedido.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|Localidade|(vazio) |Necessário. Uma cadeia de caracteres que representa um dos seguintes, utilizado para localizar os nomes de idiomas: <ul><li>A combinação de um código de cultura de duas letras em minúsculas do ISO 639 associadas com uma linguagem e um código de subcultura de duas letras em maiúsculas do ISO 3166. <li>Um código de cultura em minúsculas do ISO 639 por si só.|query|string|
|Autorização|(vazio)  |Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma lista de códigos de idioma que representam os idiomas suportados pelo serviço de tradução.  `Translate` e `TranslateArray` pode traduzir entre as duas dessas linguagens.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém os códigos de idioma suportados pelo serviço Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma suportados pelo serviço Microsoft Translator.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|Autorização|(vazio)  |Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Obtém os idiomas disponíveis para síntese de fala.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém os códigos de idioma, o serviço Microsoft Translator suportados para síntese de fala.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma, o serviço Microsoft Translator suportados para síntese de fala.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401|Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-speak"></a>OBTER / falar

### <a name="implementation-notes"></a>Notas de implementação
Devolve um fluxo WAV ou MP3 do texto no passado, falado no idioma desejado.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valor de retorno:** Um fluxo WAV ou MP3 do texto no passado, falado no idioma desejado.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

binary

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|texto|(vazio)   |Necessário. Uma cadeia que contém um ou mais frases seja falado para o fluxo de, no idioma especificado. O texto não pode exceder 2000 carateres.|query|string|
|language|(vazio)   |Necessário. Uma cadeia de caracteres que representa o código de idioma com suporte de idioma no qual dizer o texto. O código tem de ser um dos códigos de retornado pelo método `GetLanguagesForSpeak`.|query|string|
|format|(vazio)|Opcional. Uma cadeia de caracteres que especifica o ID de tipo de conteúdo. Atualmente, `audio/wav` e `audio/mp3` estão disponíveis. O valor predefinido é `audio/wav`.|query|string|
|options|(vazio)    |Opcional. Uma cadeia de caracteres que especifica as propriedades da fala sintetizada:<ul><li>`MaxQuality` e `MinSize` especifique a qualidade do sinal de áudio. `MaxQuality` Fornece a melhor qualidade. `MinSize` Fornece o tamanho de ficheiro mais pequeno. A predefinição é `MinSize`.</li><li>`female` e `male` especificar o sexo pretendido de voz. A predefinição é `female`. Utilize a barra vertical (<code>\|</code>) para incluir várias opções. Por exemplo, `MaxQuality|Male`.</li></li></ul>  |query|string|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Notas de implementação
Identifica o idioma de uma seção de texto.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valor de retorno:** Uma cadeia que contém um código de idioma de dois caracteres do texto.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|texto|(vazio)|Necessário. Uma cadeia que contém o texto cujo idioma é ser identificadas. O texto não pode exceder os 10.000 carateres.|query|  string|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key  |(vazio)    |Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|


## <a name="post-detectarray"></a>PUBLICAR /DetectArray

### <a name="implementation-notes"></a>Notas de implementação

Identifica os idiomas numa matriz de cadeias de caracteres. Deteta o idioma de cada elemento de matriz individuais e devolve um resultado para cada linha da matriz de forma independente.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

Este é o formato do corpo do pedido:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O texto não pode ter mais de 10.000 carateres.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém um código de idioma de dois caracteres para cada linha na matriz de entrada.

Este é o formato do corpo da resposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`DetectArray` foi efetuada com êxito. Devolve uma matriz de cadeia de caracteres que contém um código de idioma de dois caracteres para cada linha da matriz de entrada.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de preterição:** Após 31 de Janeiro de 2018, esse método não aceita novos envios de sentença. Obterá uma mensagem de erro. Veja o anúncio sobre as alterações a estrutura de tradução colaborativa (CTF).

Adiciona uma tradução para a memória de tradução.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicação: xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados   |
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(vazio)|Necessário. Uma cadeia que contém o texto a traduzir. O comprimento máximo da cadeia de caracteres é de 1000 carateres.|query|string|
|translatedText|(vazio) |Necessário. Uma cadeia que contém o texto traduzido no idioma de destino. O comprimento máximo da cadeia de caracteres é 2000 carateres.|query|string|
|from|(vazio)   |Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma do texto original. Por exemplo, en para inglês e Alemanha para o alemão.|query|string|
|para|(vazio)|Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma para traduzir o texto em.|query|string|
|classificação|(vazio) |Opcional. Um número inteiro que representa a classificação de qualidade para a cadeia de caracteres. O valor está entre -10 e 10. A predefinição é 1.|query|integer|
|contentType|(vazio)    |Opcional. O formato de texto a ser traduzido. Os formatos suportados são `text/plain` e `text/html`. Todos os elementos HTML tem de ser elementos bem formados e completos.    |query|string|
|category|(vazio)|Opcional. Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.|query|string|
|Utilizador|(vazio)|Necessário. Uma cadeia que é utilizada para controlar o originador do envio de mensagens em fila.|query|string|
|uri|(vazio)|Opcional. Uma cadeia que contém a localização do conteúdo da tradução.|query|string|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.  |cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|410|`AddTranslation` já não é suportada.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de preterição:** Após 31 de Janeiro de 2018, esse método não aceita novos envios de sentença. Obterá uma mensagem de erro. Veja o anúncio sobre as alterações a estrutura de tradução colaborativa (CTF).

Adiciona uma matriz de traduções para a memória de tradução. Este método é uma versão de matriz de `AddTranslation`.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Este é o formato do corpo do pedido:

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

Estes elementos são no `AddtranslationsRequest`:

* `AppId`: Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `AppId` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.
* `From`: Necessário. Uma cadeia que contém o código de idioma do idioma de origem. Tem de ser um dos idiomas retornados pelo `GetLanguagesForTranslate` método.
* `To`: Necessário. Uma cadeia que contém o código de idioma do idioma de destino. Tem de ser um dos idiomas retornados pelo `GetLanguagesForTranslate` método.
* `Translations`: Necessário. Uma matriz de traduções para adicionar a memória de tradução. Cada tradução tem de conter `OriginalText`, `TranslatedText`, e `Rating`. O tamanho máximo de cada `OriginalText` e `TranslatedText` é de 1000 carateres. O total de todos os `OriginalText` e `TranslatedText` elementos não podem ter mais de 10.000 carateres. O número máximo de elementos de matriz é 100.
* `Options`: Necessário. Um conjunto de opções, incluindo `Category`, `ContentType`, `Uri`, e `User`. `User` é necessário. `Category`, `ContentType`, e `Uri` são opcionais. Elementos especificados tem de estar listados em ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
`AddTranslationArray` método foi concluída com êxito. 

Após 31 de Janeiro de 2018, os envios de sentença não aceite. O serviço irá responder com o código de erro 410.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|410    |`AddTranslation` já não é suportada.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Divide uma secção de texto em frases e retorna uma matriz que contém os comprimentos de cada sentença.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valor de retorno:** Uma matriz de inteiros que representa os comprimentos das frases. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada sentença.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
Uma matriz de inteiros que representa os comprimentos das frases. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada sentença.

integer

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query| string|
|texto|(vazio)   |Necessário. Uma cadeia de caracteres que representa o texto a dividir em frases. O tamanho máximo do texto é 10.000 carateres.|query|string|
|language   |(vazio)    |Necessário. Uma cadeia de caracteres que representa o código de idioma do texto de entrada.|query|string|
|Autorização|(vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.   |cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401|Credenciais inválidas.|
|500|Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma matriz de traduções para um par de determinado idioma na store e o mecanismo de MT. `GetTranslations` é diferente do `Translate` em que ele retorna todas as traduções disponíveis.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

O corpo do pedido inclui o opcional `TranslationOptions` objeto, que tem este formato:

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

O `TranslateOptions` objeto contém os valores na lista seguinte. Eles são todos opcionais e predefinido para as definições mais comuns. Elementos especificados tem de estar listados em ordem alfabética.

* `Category`: Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.
* `ContentType`: A única opção suportada e a predefinição é `text/plain`.
* `IncludeMultipleMTAlternatives`: Um sinalizador booleano para especificar se a alternativa mais do que um deve ser devolvida do motor de MT. Os valores válidos são `true` e `false` (diferencia maiúsculas de minúsculas). A predefinição é `false`, que retorna apenas uma alternativa. Definir o sinalizador `true` permite a criação de alternativas artificiais, totalmente integrado com o Framework de tradução colaborativa (CTF). A funcionalidade permite-vracející alternativas para as frases que não tem nenhum traduções em CTF adicionando alternativas artificiais dos *n*-lista melhor do Decodificador.
    - Classificações. As classificações são aplicadas como este: 
         - A tradução automática melhor tem uma classificação de 5.
       - As alternativas da CTF refletem a autoridade do revisor. Eles variam de negociação-10 significa para + 10.
       - Gerado automaticamente (*n*-melhor) alternativas de tradução tem uma classificação de 0 e um grau de correspondência de 100.
    - Número de alternativas. O número de alternativas retornados pode ser tão elevado como o valor especificado no `maxTranslations`, mas pode ser mais baixo.
    - Pares de idiomas. Esta funcionalidade não está disponível para conversões entre o chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Translator.
* `State`: Estado do utilizador para o ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtre os resultados por este URI. Se nenhum valor for definido, a predefinição é `all`.
* `User`: Filtre os resultados por este utilizador. Se nenhum valor for definido, a predefinição é `all`.

Pedir `Content-Type` deve ser `text/xml`.

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

Essa resposta inclui uma `GetTranslationsResponse` elemento que contém os seguintes valores:

* `Translations`: Uma matriz das correspondências encontradas, armazenados em `TranslationMatch` objetos (descritos na secção seguinte). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% em primeiro lugar, corresponde difusas correspondências em seguida.
* `From`: Se o método não especifica um `From` linguagem, este valor será proveniente de deteção de idioma automática. Caso contrário, será especificado `From` idioma.
* `State`: Estado do utilizador para o ajudar a correlacionar a solicitação e resposta. Contém o valor fornecido no `TranslateOptions` parâmetro.

O `TranslationMatch` objeto consiste dos seguintes valores:

* `Error`: O código de erro, se ocorrer um erro para uma cadeia de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: indica como o texto de entrada se aproxima o texto original encontrado no arquivo. O sistema corresponde a entrada frases em relação ao armazenamento, incluindo inexata correspondências. O valor devolvido intervalos de 0 a 100, em que 0 é não semelhança e 100 for uma correspondência exata, maiúsculas e minúsculas.
* `MatchedOriginalText`: Texto original que foi correspondido para este resultado. Este valor é devolvido se o texto original correspondente for diferente do texto de entrada. É utilizado para devolver o texto de origem de uma correspondência por semelhantes. Este valor não é devolvido resultados de Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa a tomar a decisão de qualidade. Resultados de tradução automática tem de 5. Traduções anonimamente fornecidas geralmente têm uma classificação de 1 a 4. Traduções de forma autoritativa fornecidas geralmente terá uma classificação de 6 a 10.
* `Count`: O número de vezes que essa conversão com essa classificação foi selecionado. O valor for 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (status 200)
A `GetTranslationsResponse` objeto no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o `appid` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.|query|string|
|texto|(vazio)|Necessário. Uma cadeia de caracteres que representa o texto a traduzir. O tamanho máximo do texto é 10.000 carateres.|query|string|
|from|(vazio)|Necessário. Uma cadeia de caracteres que representa o código de idioma do texto que está a ser traduzido.|query|string|
|para |(vazio)    |Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma para traduzir o texto em.|query|string|
|maxTranslations|(vazio)|Necessário. Um número inteiro que representa o número máximo de traduções para retornar.|query|integer|
|Autorização| (vazio)|Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco. Token de autorização: `"Bearer" + " " + "access_token"`.|string|  cabeçalho|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Obtém vários candidatos de tradução para vários textos de origem.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Este é o formato do corpo do pedido:

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

`GetTranslationsArrayRequest` inclui esses elementos:

* `AppId`: Necessário. Se o `Authorization` cabeçalho é usado, deixe o `AppId` campo vazio. Caso contrário, incluir uma cadeia que contém `"Bearer" + " " + "access_token"`.
* `From`: Necessário. Uma cadeia de caracteres que representa o código de idioma do texto que está a ser traduzido.
* `MaxTranslations`: Necessário. Um número inteiro que representa o número máximo de traduções para retornar.
* `Options`: Opcional. Um `Options` objeto que contém os seguintes valores. Eles são todos opcionais e predefinido para as definições mais comuns. Elementos especificados tem de estar listados em ordem alfabética.
    - `Category`: Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: A única opção suportada e a predefinição é `text/plain`.
    - `IncludeMultipleMTAlternatives`: Um sinalizador booleano para especificar se a alternativa mais do que um deve ser devolvida do motor de MT. Os valores válidos são `true` e `false` (diferencia maiúsculas de minúsculas). A predefinição é `false`, que retorna apenas uma alternativa. Definir o sinalizador `true` permite a geração de alternativas artificiais na tradução, totalmente integrado com o Framework de traduções colaborativa (CTF). A funcionalidade permite-vracející alternativas para as frases que tem não alternativas no CTF adicionando alternativas artificiais dos *n*-lista melhor do Decodificador.
        - As classificações de classificações são aplicadas como este:
          - A tradução automática melhor tem uma classificação de 5.
          - As alternativas da CTF refletem a autoridade do revisor. Eles variam de negociação-10 significa para + 10.
          - Gerado automaticamente (*n*-melhor) alternativas de tradução tem uma classificação de 0 e um grau de correspondência de 100.
        - Número de alternativas. O número de alternativas retornados pode ser tão elevado como o valor especificado no `maxTranslations`, mas pode ser mais baixo.
        - Pares de idiomas. Esta funcionalidade não está disponível para conversões entre o chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Translator.
* `State`: Estado do utilizador para o ajudar a correlacionar a solicitação e resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtre os resultados por este URI. Se nenhum valor for definido, a predefinição é `all`.
* `User`: Filtre os resultados por este utilizador. Se nenhum valor for definido, a predefinição é `all`.
* `Texts`: Necessário. Uma matriz que contém o texto para a tradução. Todas as cadeias de caracteres devem estar no mesmo idioma. O total de todo o texto para ser convertido não pode ter mais de 10.000 carateres. O número máximo de elementos de matriz é 10.
* `To`: Necessário. Uma cadeia de caracteres que representa o código de idioma do idioma para traduzir o texto em.

Pode omitir elementos opcionais. Direcionar os elementos que são filhos do `GetTranslationsArrayRequest` tem de estar listada em ordem alfabética.

Pedir `Content-Type` deve ser `text/xml`.

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

Cada `GetTranslationsResponse` elemento contém estes valores:

* `Translations`: Uma matriz das correspondências encontradas, armazenados em `TranslationMatch` objetos (descritos na secção seguinte). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% em primeiro lugar, corresponde difusas correspondências em seguida.
* `From`: Se o método não especifica um `From` linguagem, este valor será proveniente de deteção de idioma automática. Caso contrário, será especificado `From` idioma.
* `State`: Estado do utilizador para o ajudar a correlacionar a solicitação e resposta. Contém o valor fornecido no `TranslateOptions` parâmetro.

O `TranslationMatch` objeto contém os seguintes valores:
* `Error`: O código de erro, se ocorrer um erro para uma cadeia de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: indica como o texto de entrada se aproxima o texto original encontrado no arquivo. O sistema corresponde a entrada frases em relação ao armazenamento, incluindo inexata correspondências. O valor devolvido intervalos de 0 a 100, em que 0 é não semelhança e 100 for uma correspondência exata, maiúsculas e minúsculas.
* `MatchedOriginalText`: Texto original que foi correspondido para este resultado. Este valor é devolvido se o texto original correspondente for diferente do texto de entrada. É utilizado para devolver o texto de origem de uma correspondência por semelhantes. Este valor não é devolvido resultados de Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa a tomar a decisão de qualidade. Resultados de tradução automática tem de 5. Traduções anonimamente fornecidas geralmente têm uma classificação de 1 a 4. Traduções de forma autoritativa fornecidas geralmente têm uma classificação de 6 a 10.
* `Count`: O número de vezes que essa conversão com essa classificação foi selecionado. O valor for 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (status 200)

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parameters

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |Necessário se ambas as a `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho forem deixados em branco.  Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se ambas as a `appid` campo e o `Authorization` cabeçalho forem deixados em branco.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Reason|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Migrar para a API de texto do Microsoft Translator v3 ](../migrate-to-v3.md)


