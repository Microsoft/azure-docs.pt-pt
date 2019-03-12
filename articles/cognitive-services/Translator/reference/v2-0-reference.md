---
title: Versão 2.0 da API de texto do tradutor
titleSuffix: Azure Cognitive Services
description: Documentação de referência para a API de texto do Translator V2.0.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: c87e1b42aa8474aa62e979e0d7ec1f8bf948910f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539654"
---
# <a name="translator-text-api-v20"></a>V2.0 de API de texto do tradutor

> [!IMPORTANT]
> Esta versão da API de texto do Translator foi preterido. [Ver documentação para v3 da API de texto do Translator](v3-0-reference.md).

V2 de API de texto do Translator pode ser integrado diretamente em seus aplicativos, Web sites, ferramentas ou outras soluções para fornecer experiências de usuário de vários idiomas. Tirar partido normas da indústria, ele pode ser usado em qualquer plataforma de hardware e com qualquer sistema operativo para efetuar a tradução de idioma e outras operações relacionadas com a linguagem, como deteção de idioma de texto ou texto em voz. Clique aqui para obter mais informações sobre a API do Microsoft Translator.

## <a name="getting-started"></a>Introdução
Para aceder à API de texto do Translator terá [Inscreva-se para o Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Autorização
Todas as chamadas à API de texto do Microsoft Translator requerem uma chave de subscrição para autenticar. A API suporta dois modos de autenticação:

* Com um token de acesso. Utilizar a chave de subscrição referenciada no **passo** 9 para gerar um token de acesso ao fazer um pedido POST para o serviço de autorização. Consulte a documentação do serviço de token para obter detalhes. Transmita o token de acesso ao serviço de Microsoft Translator usando o cabeçalho de autorização ou o parâmetro de consulta access_token. O token de acesso é válido durante 10 minutos. Obter um novo token de acesso a cada 10 minutos e continuar a utilizar o mesmo acesso o token para pedidos repetidos esses 10 minutos.

* Utilizar uma chave de subscrição diretamente. Passar a sua chave de assinatura como um valor na `Ocp-Apim-Subscription-Key` cabeçalho incluído com o seu pedido para a API do Translator. Neste modo, não é necessário que chamar o serviço de token de autenticação para gerar um token de acesso.

Considere a chave de subscrição e o token de acesso como segredos que devem ser ocultados da vista.

## <a name="profanity-handling"></a>Processamento de linguagem inapropriada
Normalmente, o serviço Microsoft Translator irá reter linguagem inapropriada que está presente na origem na tradução. O grau de linguagem inapropriada e contexto que torna as palavras impróprias diferem entre culturas, e assim o grau de linguagem inapropriada no idioma de destino pode ser Sim os amplificou ou reduzido.

Se quiser evitar que a linguagem inapropriada na tradução, independentemente da presença de linguagem inapropriada em texto de origem, pode utilizar a linguagem inapropriada filtragem opção para os métodos que o suportam. A opção permite-lhe escolher se pretende ver linguagem inapropriada eliminada ou marcada com etiquetas adequadas, ou nenhuma ação executada. Os valores aceites `ProfanityAction` estão `NoAction` (predefinição), assinalado e `Deleted`.


|ProfanityAction    |Ação |Origem de exemplo (japonês)  |Tradução de exemplo (em inglês)  |
|:--|:--|:--|:--|
|NoAction   |Predefinição. Mesmo que não definir a opção. Linguagem inapropriada passará de origem ao destino.        |彼はジャッカスです。     |Ele é um jackass.   |
|Marcado     |Palavras profanas ficará rodeadas por marcas XML <profanity> e </profanity>.     |彼はジャッカスです。 |Ele é um <profanity>jackass</profanity>.    |
|Eliminada    |Palavras profanas serão removidas da saída sem substituição.     |彼はジャッカスです。 |Ele é um.   |

    
## <a name="excluding-content-from-translation"></a>Excluir o conteúdo de tradução
Ao traduzir conteúdo com etiquetas como HTML (`contentType=text/html`), às vezes é útil excluir o conteúdo específico da tradução. Pode usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer em seu idioma original. No exemplo a seguir, o conteúdo dentro da primeira `div` elemento será não ser convertido, enquanto o conteúdo na segunda `div` elemento será convertido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>OBTER / traduzir

### <a name="implementation-notes"></a>Notas de implementação
Converte uma cadeia de texto de um idioma para outro.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**Valor de retorno:** Uma cadeia de caracteres que representa o texto traduzido.

Se tiver utilizado anteriormente `AddTranslation` ou `AddTranslationArray` para introduzir uma tradução com uma classificação de 5 ou superior para a mesma frase de origem, `Translate` retorna apenas a primeira escolha que está disponível para o seu sistema. A "frase de origem mesmo" significa exatamente igual (correspondência de 100%), exceto para a capitalização, espaço em branco, valores de etiqueta e a pontuação no final de uma frase. Se nenhuma classificação é armazenada com uma classificação de 5 ou superior, em seguida, o resultado retornado será a tradução automática ao Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação/xml 

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição    |Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID  |(vazio)    |Obrigatório. Se o cabeçalho de autorização ou Ocp-Apim-Subscription-Key é usado, deixe o campo de appid vazio ou incluir uma cadeia de caracteres que contém "Bearer" + "" + "access_token".|consulta|string|
|texto|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o texto a traduzir. O tamanho do texto não pode exceder os 10000 carateres.|consulta|string|
|de|(vazio)   |Opcional. Uma cadeia de caracteres que representa o código de idioma do texto de tradução. Por exemplo, en para inglês.|consulta|string|
|para|(vazio) |Obrigatório. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.|consulta|string|
|contentType|(vazio)    |Opcional. O formato de texto a ser traduzido. Os formatos suportados são text/plain (predefinição) e texto/html. Qualquer HTML tem de ser um elemento bem formado e completo.|consulta|string|
|categoria|(vazio)   |Opcional. Uma cadeia de caracteres que contém a categoria (domínio) da tradução. Predefinição é "geral".|consulta|string|
|Autorização|(vazio)  |Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + "" + "access_token".|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se o campo de appid ou o cabeçalho de autorização não está especificado.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-translatearray"></a>PUBLICAR /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Utilize o `TranslateArray` método para recuperar as traduções para vários textos de origem.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

O formato do corpo do pedido deve ser o seguinte:

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

Elementos dentro do `TranslateArrayRequest` são:


* `appid`: Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.
* `from`: Opcional. Uma cadeia de caracteres que representa o código de idioma para traduzir texto de. Se deixado em branco a resposta irá incluir o resultado da deteção automática de idioma.
* `options`: Opcional. Um `Options` objeto que contém os valores apresentados abaixo. Eles são todos opcionais e predefinido para as definições mais comuns. Elementos especificados tem de estar listados em ordem alfabética.
    - `Category`: Uma cadeia de caracteres que contém a categoria (domínio) da tradução. Assume a predefinição `general`.
    - `ContentType`: O formato de texto a ser traduzido. Os formatos suportados são `text/plain` (predefinição), `text/xml` e `text/html`. Qualquer HTML tem de ser um elemento bem formado e completo.
    - `ProfanityAction`: Especifica a forma como são processados os profanities tal como explicado anteriormente. Os valores de aceites `ProfanityAction` estão `NoAction` (predefinição), `Marked` e `Deleted`.
    - `State`: Estado do utilizador para o ajudar a correlacionar pedido e resposta. O mesmo conteúdo será devolvido na resposta.
    - `Uri`: Filtre os resultados por este URI. Predefinição: `all`.
    - `User`: Filtre os resultados por este utilizador. Predefinição: `all`.
* `texts`: Obrigatório. Uma matriz que contém os textos para a tradução. Todas as cadeias de caracteres devem ser do mesmo idioma. O total de todos os textos para ser convertido não pode exceder os 10000 carateres. O número máximo de elementos de matriz é 2000.
* `to`: Obrigatório. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.

Podem ser omitidos elementos opcionais. Os elementos que são filhos diretos do TranslateArrayRequest tem de estar listados em ordem alfabética.

Método TranslateArray aceita `application/xml` ou `text/xml` para `Content-Type`.

**Valor de retorno:** A `TranslateArrayResponse` matriz. Cada `TranslateArrayResponse` tem os seguintes elementos:

* `Error`: Indica um erro se Ocorreu uma situação. Caso contrário, definida como nula.
* `OriginalSentenceLengths`: Uma matriz de inteiros indicando o comprimento de cada sentença no texto de origem original. O comprimento da matriz indica o número de frases.
* `TranslatedText`: O texto traduzido.
* `TranslatedSentenceLengths`: Uma matriz de inteiros indicando o comprimento de cada frase o texto traduzido. O comprimento da matriz indica o número de frases.
* `State`: Estado do utilizador para o ajudar a correlacionar pedido e resposta. Devolve o mesmo conteúdo que o pedido.

Segue-se o formato do corpo da resposta.

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

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma resposta com êxito inclui uma matriz de `TranslateArrayResponse` no formato descrito acima.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)) |Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + "" + "access_token".|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o campo de appid ou o cabeçalho de autorização não está especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP   |Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas. Erros comuns incluem: <ul><li>Elemento de matriz não pode estar vazio</li><li>Categoria inválida</li><li>De idioma é inválido</li><li>A linguagem é inválida</li><li>O pedido contém demasiados elementos</li><li>O idioma de From não é suportado</li><li>Não é suportado o idioma em</li><li>Traduzir pedido tem demasiados dados</li><li>HTML não está num formato correto</li><li>Cadeias de caracteres demasiados passadas para a solicitação de traduzir</li></ul>|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Obtém nomes amigáveis para os idiomas passado como o parâmetro `languageCodes`e o localizados usando a linguagem de localidade com êxito.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

O corpo do pedido inclui uma matriz de cadeia que representa os códigos de idioma 639-1 ISO para recuperar os nomes amigáveis para. Por exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor de retorno:** Uma matriz de cadeia que contém nomes de idiomas suportados pelo serviço do Microsoft Translator, localizado para o idioma pedido.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma matriz de cadeia que contém nomes de idiomas suportados pelo serviço do Microsoft Translator, localizado para o idioma pedido.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|região|(vazio) |Obrigatório. Uma cadeia de caracteres que representa uma combinação de um código de cultura de duas letras em minúsculas do ISO 639 associadas com uma linguagem e um código de subcultura de duas letras em maiúsculas do ISO 3166 para localizar os nomes de idiomas ou um código de cultura em minúsculas do ISO 639 por si só.|consulta|string|
|Autorização|(vazio)  |Necessário se o campo de appid ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se o campo de appid ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obter uma lista dos códigos de idioma que representa idiomas suportados pelo serviço de tradução.  `Translate` e `TranslateArray` pode traduzir entre as duas dessas linguagens.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém os códigos de idioma suportados pelos serviços do Microsoft Translator.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma suportados pelos serviços do Microsoft Translator.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|Autorização|(vazio)  |Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Obtém os idiomas disponíveis para síntese de voz.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém os códigos de idioma, o serviço Microsoft Translator suportados para síntese de fala.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma matriz de cadeia de caracteres que contém os códigos de idioma, o serviço Microsoft Translator suportados para síntese de fala.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401|Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-speak"></a>OBTER / falar

### <a name="implementation-notes"></a>Notas de implementação
Devolve um fluxo wave ou mp3 do passado no texto a ser falado no idioma desejado.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**Valor de retorno:** Um fluxo wave ou mp3 do passado no texto a ser falado no idioma desejado.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

binário

Tipo de conteúdo de resposta: aplicação/xml 

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|(vazio)   |Obrigatório. Uma cadeia de caracteres contendo uma frase ou frases de idioma especificado seja falado para o fluxo de wave. O tamanho do texto para fala não pode exceder os 2.000 caracteres.|consulta|string|
|language|(vazio)   |Obrigatório. Uma cadeia de caracteres representando o código de idioma com suporte para dizer o texto no. O código tem de estar presente na lista de códigos retornado do método `GetLanguagesForSpeak`.|consulta|string|
|Formato|(vazio)|Opcional. Uma cadeia que especifica o ID do tipo de conteúdo. Atualmente, `audio/wav` e `audio/mp3` estão disponíveis. O valor predefinido é `audio/wav`.|consulta|string|
|opções|(vazio)    |<ul><li>Opcional. Uma cadeia que especifica as propriedades da fala sintetizada:<li>`MaxQuality` e `MinSize` estão disponíveis para especificar a qualidade dos sinais de áudio. Com o `MaxQuality`, pode obter vozes com a mais alta qualidade e com `MinSize`, pode obter as vozes com o tamanho mais pequeno. A predefinição é `MinSize`.</li><li>`female` e `male` estão disponíveis para especificar o sexo pretendido de voz. A predefinição é `female`. Utilize a barra vertical "|` to include multiple options. For example  `MaxQuality|Masculino ".</li></li></ul> |consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Notas de implementação
Utilize o `Detect` método para identificar o idioma de um elemento selecionado de texto.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**Valor de retorno:** Uma cadeia de caracteres contendo um código de idioma de dois caracteres para o texto especificado. .

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)  |Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|(vazio)|Obrigatório. Uma cadeia de caracteres que contém algum texto cujo idioma é ser identificadas. O tamanho do texto não pode exceder os 10000 carateres.|consulta| string|
|Autorização|(vazio)|Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key  |(vazio)    |Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|


## <a name="post-detectarray"></a>PUBLICAR /DetectArray

### <a name="implementation-notes"></a>Notas de implementação
Utilize o `DetectArray` método para identificar o idioma de uma matriz de cadeia de caracteres de uma só vez. Executa a deteção independente de cada elemento de matriz individuais e devolve um resultado para cada linha da matriz.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

O formato do corpo do pedido deve ser o seguinte.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O tamanho do texto não pode exceder os 10000 carateres.

**Valor de retorno:** Uma matriz de cadeia de caracteres que contém um códigos de idioma de dois caracteres para cada linha da matriz de entrada.

Segue-se o formato do corpo da resposta.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
DetectArray foi concluída com êxito. Devolve uma matriz de cadeia de caracteres contendo um códigos de idioma de dois caracteres para cada linha da matriz de entrada.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo ou cabeçalho de autorização não está especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **NOTA DE PRETERIÇÃO:** Após 31 de Janeiro de 2018, esse método não aceitará novos envios de sentença e receberá uma mensagem de erro. Consulte este anúncio sobre as alterações para as funções de tradução colaborativa.

Adiciona uma tradução para a memória de tradução.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação: xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados   |
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|originalText|(vazio)|Obrigatório. Uma cadeia de caracteres que contém o texto a traduzir. A cadeia de caracteres tem um comprimento máximo de 1000 carateres.|consulta|string|
|translatedText|(vazio) |Obrigatório. Uma cadeia de caracteres que contém traduzido texto no idioma de destino. A cadeia de caracteres tem um comprimento máximo de 2000 caracteres.|consulta|string|
|de|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto de tradução. EN = inglês, Alemanha = Alemão etc...|consulta|string|
|para|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.|consulta|string|
|classificação|(vazio) |Opcional. Um número inteiro que representa a classificação de qualidade para essa cadeia de caracteres. Valor entre -10 e 10. Predefinição é 1.|consulta|inteiro|
|contentType|(vazio)    |Opcional. O formato de texto a ser traduzido. Os formatos suportados são "text/plain" e "text/html". Qualquer HTML tem de ser um elemento bem formado e completo.   |consulta|string|
|categoria|(vazio)|Opcional. Uma cadeia de caracteres que contém a categoria (domínio) da tradução. Predefinição é "geral".|consulta|string|
|Utilizador|(vazio)|Obrigatório. Uma cadeia utilizada para controlar o originador de envio.|consulta|string|
|uri|(vazio)|Opcional. Uma cadeia de caracteres que contém a localização do conteúdo dessa conversão.|consulta|string|
|Autorização|(vazio)|Necessário se o campo de appid ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.    |cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|410|AddTranslation já não é suportada.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **NOTA DE PRETERIÇÃO:** Após 31 de Janeiro de 2018, esse método não aceitará novos envios de sentença e receberá uma mensagem de erro. Consulte este anúncio sobre as alterações para as funções de tradução colaborativa.

Adiciona uma matriz de traduções para adicionar memória de tradução. Esta é uma versão de matriz de `AddTranslation`.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

Segue-se o formato do corpo do pedido.

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

Elementos dentro do elemento de AddtranslationsRequest são:

* `AppId`: Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.
* `From`: Obrigatório. Uma cadeia de caracteres contendo o código de idioma do idioma de origem. Tem de ser um dos idiomas retornados pelo `GetLanguagesForTranslate` método.
* `To`: Obrigatório. Uma cadeia de caracteres contendo o código de idioma do idioma de destino. Tem de ser um dos idiomas retornados pelo `GetLanguagesForTranslate` método.
* `Translations`: Obrigatório. Uma matriz de traduções para adicionar a memória de tradução. Cada tradução deve conter: originalText, translatedText e classificação. O tamanho de cada originalText e translatedText está limitado a 1000 carateres. O total de todas as originalText(s) e translatedText(s) não pode exceder os 10000 carateres. O número máximo de elementos de matriz é 100.
* `Options`: Obrigatório. Um conjunto de opções, incluindo a categoria, ContentType, Uri e utilizador. É necessário um utilizador. Categoria, ContentType e Uri são opcionais. Elementos especificados tem de estar listados em ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Método AddTranslationArray foi concluída com êxito. Após 31 de Janeiro de 2018, não serão aceite envios de sentença. O serviço irá responder com o código de erro 410.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + "" + "access_token".|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o campo de appid ou o cabeçalho de autorização não está especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|410    |AddTranslation já não é suportada.|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Divide um pedaço de texto em frases e retorna uma matriz que contém os comprimentos de cada sentença.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**Valor de retorno:** Uma matriz de inteiros que representam os comprimentos das frases. O comprimento da matriz é o número de frases e os valores são o comprimento de cada sentença.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma matriz de inteiros que representam os comprimentos das frases. O comprimento da matriz é o número de frases e os valores são o comprimento de cada sentença.

inteiro

Tipo de conteúdo de resposta: aplicação/xml 

### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)  |Obrigatório. Se o cabeçalho de autorização ou Ocp-Apim-Subscription-Key é usado, deixe o campo de appid vazio ou incluir uma cadeia de caracteres que contém "Bearer" + "" + "access_token".|consulta| string|
|texto|(vazio)   |Obrigatório. Uma cadeia de caracteres que representa o texto a dividir em frases. O tamanho do texto não pode exceder os 10000 carateres.|consulta|string|
|language   |(vazio)    |Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto de entrada.|consulta|string|
|Autorização|(vazio)|Necessário se o campo de appid ou cabeçalho Ocp-Apim-Subscription-Key não for especificado. Token de autorização:  "Bearer" + "" + "access_token".    |cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)|Necessário se o campo de appid ou o cabeçalho de autorização não está especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401|Credenciais inválidas|
|500|Erro de servidor. Se o erro persistir, fale conosco. Envie-na aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta X-MS-Trans-Info.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma matriz de traduções para um par de determinado idioma na store e o mecanismo de MT. GetTranslations difere Translate devolve todas as traduções disponíveis.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

O corpo do pedido inclui o objeto de TranslationOptions opcional, que tem o seguinte formato.

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

O `TranslateOptions` objeto contém os valores apresentados abaixo. Eles são todos opcionais e predefinido para as definições mais comuns. Elementos especificados tem de estar listados em ordem alfabética.

* `Category`: Uma cadeia de caracteres que contém a categoria (domínio) da tradução. Predefinição é "geral".
* `ContentType`: O único suportado e a predefinição, a opção é "text/plain".
* `IncludeMultipleMTAlternatives`: um sinalizador booleano para determinar se as alternativas mais do que um devem ser devolvido do motor de MT. Valores válidos são VERDADEIRO ou FALSO (diferencia maiúsculas de minúsculas). Padrão é false e inclui apenas 1 alternativa. Definir o sinalizador como true permite gerar alternativas artificiais na tradução, totalmente integrada com o framework de traduções colaborativa (CTF). A funcionalidade permite para retornar alternativas para as frases que não tenham nenhum alternativas no CTF, ao adicionar artificiais alternativas a partir da lista de múltipla do Decodificador.
    - As classificações de classificações são aplicadas da seguinte forma: 1) a tradução automática melhor tem uma classificação de 5. 2) as alternativas da CTF refletem a autoridade de revisor, de -10 para + 10. 3) as alternativas de tradução do gerada automaticamente (múltipla) tem uma classificação de 0 e tem um nível de correspondência de 100.
    - Número de alternativas, o número de alternativas retornados é até maxTranslations, mas pode ser inferior.
    - Pares de idiomas essa funcionalidade não está disponível para conversões entre simplificado e chinês tradicional, ambas as direções. Está disponível para todos os outros pares de idiomas suportados do Microsoft Translator.
* `State`: Estado do utilizador para o ajudar a correlacionar pedido e resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtre os resultados por este URI. Se nenhum valor for definido, a predefinição é tudo.
* `User`: Filtre os resultados por este utilizador. Se nenhum valor for definido, a predefinição é tudo.

Pedir `Content-Type` deve ser `text/xml`.

**Valor de retorno:** Segue-se o formato da resposta.

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

Isto inclui um `GetTranslationsResponse` elemento que contém os seguintes valores:

* `Translations`: Foi encontrada uma matriz de correspondências, armazenados em objetos de TranslationMatch (ver abaixo). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% em primeiro lugar, corresponde a correspondências difusas abaixo.
* `From`: Se o método não especificou uma linguagem de From, este será o resultado da deteção de idioma automática. Caso contrário, é o dado de idioma.
* `State`: Estado do utilizador para o ajudar a correlacionar pedido e resposta. Contém o mesmo valor que o especificado no parâmetro TranslateOptions.

Objeto de TranslationMatch consiste no seguinte:

* `Error`: Se Ocorreu um erro para uma cadeia de caracteres de entrada específica, o código de erro é armazenado. Caso contrário, o campo estiver vazio.
* `MatchDegree`: O sistema corresponde a entrada frases em relação ao armazenamento, incluindo inexata correspondências.  MatchDegree indica como o texto de entrada se aproxima o texto original encontrado no arquivo. O valor devolvido intervalos de 0 a 100, em que 0 é não semelhança e 100 é uma correspondência exata de maiúsculas de minúsculas.
MatchedOriginalText: Texto original que foi correspondido para este resultado. Só devolvido se o texto original correspondente era diferente do que o texto de entrada. Utilizado para devolver o texto de origem de uma correspondência por semelhantes. Não é devolvido para resultados de Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa a tomar a decisão de qualidade. Resultados de tradução automática terão de 5. Traduções anonimamente fornecidas geralmente terá uma classificação de 1 a 4, embora as traduções fornecidas de forma autoritativa geralmente terá uma classificação de 6 a 10.
* `Count`: O número de vezes que essa conversão com essa classificação foi selecionado. O valor será 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
A `GetTranslationsResponse` objeto no formato descrito acima.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|AppID|(vazio)|Obrigatório. Se o `Authorization` ou `Ocp-Apim-Subscription-Key` cabeçalho é usado, deixe o campo de appid vazio incluir, caso contrário, uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.|consulta|string|
|texto|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o texto a traduzir. O tamanho do texto não pode exceder os 10000 carateres.|consulta|string|
|de|(vazio)|Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto de tradução.|consulta|string|
|para |(vazio)    |Obrigatório. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.|consulta|string|
|maxTranslations|(vazio)|Obrigatório. Um número inteiro que representa o número máximo de traduções para retornar.|consulta|inteiro|
|Autorização| (vazio)|Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|string| cabeçalho|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503|Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Utilize o `GetTranslationsArray` método para recuperar vários candidatos de tradução para vários textos de origem.

O URI do pedido é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

Segue-se o formato do corpo do pedido.

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

`GetTranslationsArrayRequest` inclui os seguintes elementos:

* `AppId`: Obrigatório. Se o cabeçalho Authorization é usado, deixe o campo de appid vazio incluir outro uma cadeia de caracteres que contém `"Bearer" + " " + "access_token"`.
* `From`: Obrigatório. Uma cadeia de caracteres que representa o código de idioma do texto de tradução.
* `MaxTranslations`: Obrigatório. Um número inteiro que representa o número máximo de traduções para retornar.
* `Options`: Opcional. Um objeto de opções que contém os valores apresentados abaixo. Eles são todos opcionais e predefinido para as definições mais comuns. Elementos especificados tem de estar listados em ordem alfabética.
    - Category`: Uma cadeia de caracteres que contém a categoria (domínio) da tradução. Predefinição é geral.
    - `ContentType`: O único suportado e a predefinição, a opção é text/plain.
    - `IncludeMultipleMTAlternatives`: um sinalizador booleano para determinar se as alternativas mais do que um devem ser devolvido do motor de MT. Valores válidos são VERDADEIRO ou FALSO (diferencia maiúsculas de minúsculas). Padrão é false e inclui apenas 1 alternativa. Definir o sinalizador como true permite gerar alternativas artificiais na tradução, totalmente integrada com o framework de traduções colaborativa (CTF). A funcionalidade permite para retornar alternativas para as frases que não tenham nenhum alternativas no CTF, ao adicionar artificiais alternativas a partir da lista de múltipla do Decodificador.
        - As classificações de classificações são aplicadas da seguinte forma: 1) a tradução automática melhor tem uma classificação de 5. 2) as alternativas da CTF refletem a autoridade de revisor, de -10 para + 10. 3) as alternativas de tradução do gerada automaticamente (múltipla) tem uma classificação de 0 e tem um nível de correspondência de 100.
        - Número de alternativas, o número de alternativas retornados é até maxTranslations, mas pode ser inferior.
        - Pares de idiomas essa funcionalidade não está disponível para conversões entre simplificado e chinês tradicional, ambas as direções. Está disponível para todos os outros pares de idiomas suportados do Microsoft Translator.
* `State`: Estado do utilizador para o ajudar a correlacionar pedido e resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtre os resultados por este URI. Se nenhum valor for definido, a predefinição é tudo.
* `User`: Filtre os resultados por este utilizador. Se nenhum valor for definido, a predefinição é tudo.
* `Texts`: Obrigatório. Uma matriz que contém os textos para a tradução. Todas as cadeias de caracteres devem ser do mesmo idioma. O total de todos os textos para ser convertido não pode exceder os 10000 carateres. O número máximo de elementos de matriz é 10.
* `To`: Obrigatório. Uma cadeia de caracteres que representa o código de idioma para traduzir o texto em.

Podem ser omitidos elementos opcionais. Direcionar os elementos que são filhos do `GetTranslationsArrayRequest` tem de estar listada em ordem alfabética.

Pedir `Content-Type` deve ser `text/xml`.

**Valor de retorno:** Segue-se o formato da resposta.

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

Cada `GetTranslationsResponse` elemento contém os seguintes valores:

* `Translations`: Foi encontrada uma matriz de correspondências, armazenados no `TranslationMatch` objetos (ver abaixo). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% em primeiro lugar, corresponde a correspondências difusas abaixo.
* `From`: Se o método não especificou um `From` linguagem, este será o resultado da deteção de idioma automática. Caso contrário, é o dado de idioma.
* `State`: Estado do utilizador para o ajudar a correlacionar pedido e resposta. Com o mesmo valor como indicado no `TranslateOptions` parâmetro.

`TranslationMatch` objeto consiste no seguinte:
* `Error`: Se Ocorreu um erro para uma cadeia de caracteres de entrada específica, o código de erro é armazenado. Caso contrário, o campo estiver vazio.
* `MatchDegree`: O sistema corresponde a entrada frases em relação ao armazenamento, incluindo inexata correspondências.  `MatchDegree` indica como o texto de entrada se aproxima o texto original encontrado no arquivo. O valor devolvido intervalos de 0 a 100, em que 0 é não semelhança e 100 é uma correspondência exata de maiúsculas de minúsculas.
* `MatchedOriginalText`: Texto original que foi correspondido para este resultado. Só devolvido se o texto original correspondente era diferente do que o texto de entrada. Utilizado para devolver o texto de origem de uma correspondência por semelhantes. Não é devolvido para resultados de Microsoft Translator.
* `Rating`: Indica a autoridade da pessoa a tomar a decisão de qualidade. Resultados de tradução automática terão de 5. Traduções anonimamente fornecidas geralmente terá uma classificação de 1 a 4, embora as traduções fornecidas de forma autoritativa geralmente terá uma classificação de 6 a 10.
* `Count`: O número de vezes que essa conversão com essa classificação foi selecionado. O valor será 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Value|Descrição|Tipo de parâmetro|Tipo de Dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |Necessário se o `appid` campo ou `Ocp-Apim-Subscription-Key` cabeçalho não for especificado. Token de autorização: `"Bearer" + " " + "access_token"`.|cabeçalho|string|
|OCP-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo ou `Authorization` cabeçalho não for especificado.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Pedido incorreto. Verifique os parâmetros de entrada e a resposta de erro detalhadas.|
|401    |Credenciais inválidas|
|500    |Erro de servidor. Se o erro persistir, fale conosco. Envie-nos com a aproximado data e hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info`.|
|503    |Serviço indisponível temporariamente. Tente novamente e informe-nos se o erro persistir.|

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Migrar para a API de texto do Microsoft Translator v3](../migrate-to-v3.md)










