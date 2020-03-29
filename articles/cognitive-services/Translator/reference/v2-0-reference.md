---
title: Texto tradutor API v2.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o Texto tradutor API v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: f111169558118a80602bcb2136bc63ce54c9e0d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242488"
---
# <a name="translator-text-api-v20"></a>Texto tradutor API v2.0

> [!IMPORTANT]
> Esta versão da API de Texto tradutor foi depreciada. [Ver documentação para a versão 3 da API de Texto tradutor](v3-0-reference.md).

A versão 2 da API de Texto tradutor pode ser integrada perfeitamente nas suas apps, websites, ferramentas ou outras soluções para proporcionar experiências multilingidiomas ao utilizador. Pode usá-lo em qualquer plataforma de hardware e com qualquer sistema operativo para realizar traduções linguísticas e outras tarefas relacionadas com a linguagem, como deteção de linguagem de texto e texto para fala, de acordo com os padrões da indústria. Para mais informações, consulte [a API de Texto tradutor](../translator-info-overview.md).

## <a name="getting-started"></a>Introdução
Para aceder à API de texto de tradutor, tem de [se inscrever no Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Autenticação 
Todas as chamadas para a API de Texto tradutor requerem uma chave de subscrição para autenticação. A API suporta três métodos de autenticação:

- Um sinal de acesso. Utilize a chave de subscrição para criar um sinal de acesso fazendo um pedido de CORREIO para o serviço de autenticação. Consulte a documentação do serviço simbólico para obter mais detalhes. Passe o sinal de acesso ao `Authorization` serviço tradutor `access_token` utilizando o cabeçalho ou o parâmetro de consulta. O sinal de acesso é válido por 10 minutos. Obtenha um novo sinal de acesso a cada 10 minutos e continue a usar o mesmo sinal de acesso para pedidos repetidos durante os 10 minutos.
- Uma chave de subscrição utilizada diretamente. Passe a sua chave de `Ocp-Apim-Subscription-Key` subscrição como um valor no cabeçalho incluído com o seu pedido para a API de Texto tradutor. Quando utiliza a chave de subscrição diretamente, não é preciso ligar para o serviço de autenticação simbólica para criar um sinal de acesso.
- Uma [subscrição multi-serviço de Serviços Cognitivos Azure.](https://azure.microsoft.com/pricing/details/cognitive-services/) Este método permite-lhe utilizar uma única chave secreta para autenticar pedidos de vários serviços.
Quando utilizar uma chave secreta multi-serviço, precisa de incluir dois cabeçalhos de autenticação com o seu pedido. O primeiro cabeceamento passa a chave secreta. O segundo cabeçalho especifica a região associada à sua subscrição:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A região é necessária para a subscrição de Texto API multi-serviço. A região que seleciona é a única região que pode utilizar para tradução de texto quando utiliza a chave de subscrição de vários serviços. Tem de ser a mesma região que selecionou quando se inscreveu para a sua subscrição multi-serviço no portal Azure.

As regiões `australiaeast`disponíveis `brazilsouth` `canadacentral` `centralindia`são, `centraluseuap` `eastasia`, `eastus` `eastus2`, `japaneast` `northeurope`, `southcentralus` `southeastasia`, `uksouth` `westcentralus`, `westeurope` `westus`, `westus2`, , , , , , , , e .

A sua chave de subscrição e o token de acesso são segredos que devem ser escondidos da vista.

## <a name="profanity-handling"></a>Manipulação de profanidades
Normalmente, o serviço tradutor vai reter profanação que está presente na fonte. O grau de profanação e o contexto que torna as palavras profanas diferem de acordo com a cultura. Assim, o grau de profanação na língua-alvo poderia ser aumentado ou reduzido.

Se quiser evitar a profanação na tradução mesmo quando está no texto de origem, pode utilizar a opção de filtragem de palavrões para os métodos que a suportam. A opção permite-lhe escolher se pretende ver a profanação apagada ou marcada com etiquetas apropriadas, ou se pretende permitir a profanação no alvo. Os valores `ProfanityAction` `NoAction` aceites `Marked`de `Deleted`são (padrão), e .


|ProfanityAction    |Ação |Fonte de exemplo (japonesa)  |Tradução de exemplo (inglês)  |
|:--|:--|:--|:--|
|NoAction   |Predefinição. O mesmo que não definir a opção. A profanação passará de origem para alvo.        |彼はジャッカスです。     |É um idiota.   |
|Marcado     |As palavras profanas serão rodeadas \<por etiquetas \<XML profanadas> e /profanação>.       |彼はジャッカスです。 |É um \<profanidade>\<idiota /profanação>.  |
|Eliminado    |As palavras profanas serão removidas da saída sem substituição.     |彼はジャッカスです。 |É um.   |

    
## <a name="excluding-content-from-translation"></a>Excluindo os conteúdos da tradução
Quando se traduz conteúdo com tags, como HTML (),`contentType=text/html`às vezes é útil excluir conteúdo específico da tradução. Pode utilizar o `class=notranslate` atributo para especificar o conteúdo que deve permanecer na sua língua original. No exemplo seguinte, o conteúdo `div` no primeiro elemento não será traduzido, `div` mas o conteúdo no segundo elemento será traduzido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Traduzir

### <a name="implementation-notes"></a>Notas de implementação
Traduz uma cadeia de texto de uma língua para outra.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/Translate`URI é .

**Valor de devolução:** Uma corda que representa o texto traduzido.

Se usou `AddTranslation` anteriormente `AddTranslationArray` ou introduz uma tradução com uma classificação `Translate` igual ou superior a 5 para a mesma frase de origem, devolve apenas a escolha máxima disponível para o seu sistema. "Mesma frase de origem" significa exatamente a mesma (100% correspondente), com exceção da capitalização, espaço branco, valores de etiqueta e pontuação no final de uma frase. Se nenhuma classificação for armazenada com uma classificação de 5 ou superior, o resultado devolvido será a tradução automática pelo Microsoft Tradutor.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição    |Tipo parâmetro|tipo de dados|
|:--|:--|:--|:--|:--|
|appid  |(vazio)    |Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|texto|(vazio)   |Necessário. Uma corda que representa o texto para traduzir. O texto não pode conter mais de 10.000 caracteres.|consulta|string|
|De|(vazio)   |Opcional. Uma cadeia que representa o código linguístico do texto que está a ser traduzido. Por exemplo, en para inglês.|consulta|string|
|para|(vazio) |Necessário. Uma cadeia que representa o código da linguagem para traduzir o texto.|consulta|string|
|conteúdoType|(vazio)    |Opcional. O formato do texto a ser traduzido. Os formatos `text/plain` suportados `text/html`são (predefinidos) e . Quaisquer elementos HTML precisam de ser elementos bem formados e completos.|consulta|string|
|categoria|(vazio)   |Opcional. Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.|consulta|string|
|Autorização|(vazio)  |Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera traduções para vários textos de origem.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`URI é .

Aqui está o formato do órgão de pedido:

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

Estes elementos `TranslateArrayRequest`estão em:


* `AppId`: Obrigatório. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `AppId` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .
* `From`: Opcional. Uma cadeia que representa o código linguístico do texto que está a ser traduzido. Se este campo ficar vazio, a resposta incluirá o resultado da deteção automática de linguagem.
* `Options`: Opcional. Um `Options` objeto que contém os seguintes valores. São todos opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser enumerados por ordem alfabética.
    - `Category`: Uma cadeia que contenha a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: O formato do texto a ser traduzido. Os formatos `text/plain` suportados `text/xml`são `text/html`(predefinidos), e . Quaisquer elementos HTML precisam de ser elementos bem formados e completos.
    - `ProfanityAction`: Especifica a forma como as profanidades são tratadas, como explicado anteriormente. Os valores aceites são `NoAction` (padrão), `Marked`e `Deleted`.
    - `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
    - `Uri`: Filtrar os resultados por este URI. Predefinição: `all`.
    - `User`: Filtrar os resultados por este utilizador. Predefinição: `all`.
* `Texts`: Obrigatório. Uma matriz que contém o texto para tradução. Todas as cordas devem estar na mesma língua. O total de todo o texto a traduzir não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é de 2.000.
* `To`: Obrigatório. Uma cadeia que representa o código da linguagem para traduzir o texto.

Pode omitindo elementos opcionais. Os elementos que `TranslateArrayRequest` são crianças diretas devem ser listados por ordem alfabética.

O `TranslateArray` método `application/xml` aceita `text/xml` `Content-Type`ou para .

**Valor de devolução:** Uma `TranslateArrayResponse` matriz. Cada `TranslateArrayResponse` um tem estes elementos:

* `Error`: Indica um erro se ocorrer um erro. Caso contrário, definido para nulo.
* `OriginalSentenceLengths`: Um conjunto de inteiros que indica o comprimento de cada frase no texto de origem. O comprimento da matriz indica o número de frases.
* `TranslatedText`: O texto traduzido.
* `TranslatedSentenceLengths`: Um conjunto de inteiros que indica o comprimento de cada frase no texto traduzido. O comprimento da matriz indica o número de frases.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. Devolve o mesmo conteúdo que o pedido.

Aqui está o formato do corpo de resposta:

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

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
Uma resposta bem sucedida `TranslateArrayResponse` inclui uma série de matrizes no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)  |Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP   |Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro. Os erros comuns incluem: <ul><li>O elemento matriz não pode estar vazio.</li><li>Categoria inválida.</li><li>Da linguagem é inválido.</li><li>A linguagem é inválida.</li><li>O pedido contém demasiados elementos.</li><li>A linguagem From não é apoiada.</li><li>A linguagem To não é apoiada.</li><li>O Pedido de Tradução tem demasiados dados.</li><li>HTML não está num formato correto.</li><li>Muitas cordas foram passadas no Pedido de Tradução.</li></ul>|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Recupera nomes amigáveis para as línguas `languageCodes`passadas como `locale` parâmetro, localizado na língua passada.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`URI é .

O organismo de pedido inclui uma matriz de cordas que representa os códigos linguísticos ISO 639-1 para os quais recuperar os nomes amigáveis. Segue-se um exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor de devolução:** Um conjunto de cordas que contém nomes linguísticos suportados pelo serviço Tradutor, localizados na língua solicitada.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
Um conjunto de cordas que contém nomes de idiomas suportados pelo serviço Tradutor, localizados na língua solicitada.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|região|(vazio) |Necessário. Uma cadeia que representa um dos seguintes, usado para localizar os nomes linguísticos: <ul><li>A combinação de um código de cultura minúscula ISO 639 de duas letras associado a uma língua e um código de subcultura maiúscula iso 3166. <li>Um código de cultura maiúscula ISO 639 por si só.|consulta|string|
|Autorização|(vazio)  |Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma lista de códigos linguísticos que representam idiomas suportados pelo serviço de Tradução.  `Translate`e `TranslateArray` pode traduzir entre qualquer duas destas línguas.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`URI é .

**Valor de devolução:** Uma matriz de cordas que contém os códigos linguísticos suportados pelo serviço Tradutor.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
Uma matriz de cordas que contém os códigos linguísticos suportados pelo serviço Tradutor.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|Autorização|(vazio)  |Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503|Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Recupera os idiomas disponíveis para a síntese da fala.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`URI é .

**Valor de devolução:** Uma matriz de cordas que contém os códigos linguísticos suportados para a síntese da fala pelo serviço Tradutor.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
Uma matriz de cordas que contém os códigos linguísticos suportados para a síntese da fala pelo serviço Tradutor.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401|Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="get-speak"></a>GET /Falar

### <a name="implementation-notes"></a>Notas de implementação
Devolve um fluxo DE WAV ou MP3 do texto passado, falado na linguagem desejada.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/Speak`URI é .

**Valor de devolução:** Um fluxo de WAV ou MP3 do texto passado, falado na linguagem desejada.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)

binary

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|texto|(vazio)   |Necessário. Uma corda que contém uma ou mais frases a serem faladas para o fluxo, na linguagem especificada. O texto não deve exceder 2.000 caracteres.|consulta|string|
|language|(vazio)   |Necessário. Uma cadeia que representa o código linguístico suportado da língua em que falar o texto. O código deve ser um dos códigos devolvidos pelo método `GetLanguagesForSpeak`.|consulta|string|
|formato|(vazio)|Opcional. Uma cadeia que especifica o ID do tipo de conteúdo. Atualmente, `audio/wav` `audio/mp3` e estão disponíveis. O valor predefinido é `audio/wav`.|consulta|string|
|opções|(vazio)    |Opcional. Uma corda que especifica propriedades da fala sintetizada:<ul><li>`MaxQuality`e `MinSize` especificar a qualidade do sinal de áudio. `MaxQuality`fornece a mais alta qualidade. `MinSize`fornece o menor tamanho de arquivo. O padrão `MinSize`é .</li><li>`female`e `male` especificar o sexo desejado da voz. A predefinição é `female`. Utilize a barra<code>\|</code>vertical para incluir várias opções. Por exemplo, `MaxQuality|Male`.</li></li></ul>  |consulta|string|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detete

### <a name="implementation-notes"></a>Notas de implementação
Identifica a linguagem de uma secção de texto.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/Detect`URI é .

**Valor de devolução:** Uma corda que contém um código de linguagem de dois caracteres para o texto.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|texto|(vazio)|Necessário. Uma cadeia que contém texto cuja linguagem deve ser identificada. O texto não deve exceder 10.000 caracteres.|consulta|  string|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key  |(vazio)    |Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Notas de implementação

Identifica as línguas numa série de cordas. De forma independente, deteta a linguagem de cada elemento matriz individual e devolve um resultado para cada linha da matriz.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`URI é .

Aqui está o formato do órgão de pedido:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O texto não pode exceder 10.000 caracteres.

**Valor de devolução:** Uma matriz de cordas que contém um código de linguagem de dois caracteres para cada linha na matriz de entrada.

Aqui está o formato do corpo de resposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
`DetectArray`foi bem sucedido. Devolve uma matriz de cordas que contém um código de linguagem de dois caracteres para cada linha da matriz de entrada.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios.  Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de depreciação:** Depois de 31 de janeiro de 2018, este método não aceitará novas candidaturas à sentença. Vaireceber uma mensagem de erro. Consulte o anúncio sobre as alterações ao Quadro de Tradução Colaborativa (CTF).

Adiciona uma tradução à memória de tradução.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`URI é .

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)

string

Tipo de conteúdo de resposta: aplicação: xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados   |
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|originalTexto|(vazio)|Necessário. Uma corda que contém o texto para traduzir. O comprimento máximo da corda é de 1.000 caracteres.|consulta|string|
|texto traduzido|(vazio) |Necessário. Uma cadeia que contém texto traduzido para a linguagem alvo. O comprimento máximo da corda é de 2.000 caracteres.|consulta|string|
|De|(vazio)   |Necessário. Uma cadeia que representa o código linguístico da língua original do texto. Por exemplo, en para inglês e de para alemão.|consulta|string|
|para|(vazio)|Necessário. Uma cadeia que representa o código linguístico da língua para traduzir o texto.|consulta|string|
|classificação|(vazio) |Opcional. Um inteiro que representa a classificação de qualidade para a corda. O valor é entre -10 e 10. A predefinição é 1.|consulta|número inteiro|
|conteúdoType|(vazio)    |Opcional. O formato do texto a ser traduzido. Os formatos `text/plain` suportados são e `text/html`. Quaisquer elementos HTML precisam de ser elementos bem formados e completos.    |consulta|string|
|categoria|(vazio)|Opcional. Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.|consulta|string|
|utilizador|(vazio)|Necessário. Uma corda que é usada para rastrear o autor da submissão.|consulta|string|
|uri|(vazio)|Opcional. Uma cadeia que contém a localização do conteúdo da tradução.|consulta|string|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios.  Ficha de `"Bearer" + " " + "access_token"`autorização: .  |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|410|`AddTranslation`já não é apoiado.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de depreciação:** Depois de 31 de janeiro de 2018, este método não aceitará novas candidaturas à sentença. Vaireceber uma mensagem de erro. Consulte o anúncio sobre as alterações ao Quadro de Tradução Colaborativa (CTF).

Adiciona uma série de traduções à memória de tradução. Este método é uma `AddTranslation`versão matriz de .

O pedido `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`URI é .

Aqui está o formato do órgão de pedido:

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

Estes elementos `AddtranslationsRequest`estão em:

* `AppId`: Obrigatório. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `AppId` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .
* `From`: Obrigatório. Uma cadeia que contém o código linguístico da linguagem fonte. Deve ser uma das línguas `GetLanguagesForTranslate` devolvidas pelo método.
* `To`: Obrigatório. Uma cadeia que contém o código linguístico da linguagem alvo. Deve ser uma das línguas `GetLanguagesForTranslate` devolvidas pelo método.
* `Translations`: Obrigatório. Uma variedade de traduções para adicionar à memória de tradução. Cada tradução `OriginalText` `TranslatedText`deve `Rating`conter, e . O tamanho máximo `OriginalText` `TranslatedText` de cada um e é de 1.000 caracteres. O total `OriginalText` de `TranslatedText` todos e elementos não podem exceder 10.000 caracteres. O número máximo de elementos de matriz é de 100.
* `Options`: Obrigatório. Um conjunto de `Category`opções, incluindo, `ContentType`e `Uri` `User`. `User` é obrigatório. `Category`E `ContentType` `Uri` são opcionais. Os elementos especificados devem ser enumerados por ordem alfabética.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
`AddTranslationArray`método conseguiu. 

Depois de 31 de janeiro de 2018, as candidaturas à sentença não serão aceites. O serviço responderá com o código de erro 410.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios.  Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|410    |`AddTranslation`já não é apoiado.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503|Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Parte uma secção de texto em frases e devolve uma matriz que contém os comprimentos de cada frase.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`URI é .

**Valor de devolução:** Uma série de inteiros que representam o comprimento das frases. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada frase.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
Uma série de inteiros que representam o comprimento das frases. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada frase.

número inteiro

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta| string|
|texto|(vazio)   |Necessário. Uma corda que representa o texto para dividir em frases. O tamanho máximo do texto é de 10.000 caracteres.|consulta|string|
|language   |(vazio)    |Necessário. Uma cadeia que representa o código linguístico do texto de entrada.|consulta|string|
|Autorização|(vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .   |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401|Credenciais inválidas.|
|500|Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503|Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Recupera uma variedade de traduções para um dado par de idiomas da loja e do motor MT. `GetTranslations`difere da `Translate` medida em que devolve todas as traduções disponíveis.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`URI é .

O corpo do pedido `TranslationOptions` inclui o objeto opcional, que tem este formato:

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

O `TranslateOptions` objeto contém os valores na lista seguinte. São todos opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser enumerados por ordem alfabética.

* `Category`: Uma cadeia que contenha a categoria (domínio) da tradução. A predefinição é `general`.
* `ContentType`: A única opção suportada, `text/plain`e o padrão, é .
* `IncludeMultipleMTAlternatives`: Uma bandeira booleana para especificar se mais do que uma alternativa deve ser devolvida do motor MT. Os valores válidos são `true` e `false` (sensíveis a casos). O padrão `false`é , que devolve apenas uma alternativa. A fixação `true` da bandeira permite a criação de alternativas artificiais, totalmente integradas com o Quadro de Tradução Colaborativa (CTF). A funcionalidade permite a devolução de alternativas para frases que não têm traduções em CTF, adicionando alternativas artificiais *da*melhor lista do descodificador.
    - As audiências. As classificações são aplicadas assim: 
         - A melhor tradução automática tem uma classificação de 5.
       - As alternativas da CTF refletem a autoridade do revisor. Variam de -10 a +10.
       - As alternativas de tradução geradas automaticamente *(n-best)* têm uma classificação de 0 e um grau de correspondência de 100.
    - Número de alternativas. O número de alternativas devolvidas pode ser `maxTranslations`tão elevado quanto o valor especificado, mas pode ser menor.
    - Pares de línguas. Esta funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Tradutor.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtrar os resultados por este URI. Se não for definido qualquer `all`valor, o padrão é .
* `User`: Filtrar os resultados por este utilizador. Se não for definido qualquer `all`valor, o padrão é .

O `Content-Type` pedido `text/xml`deve ser.

**Valor de devolução:** Aqui está o formato da resposta:

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

Esta resposta `GetTranslationsResponse` inclui um elemento que contém os seguintes valores:

* `Translations`: Uma série de fósforos `TranslationMatch` encontrados, armazenados em objetos (descritos na secção seguinte). As traduções podem incluir ligeiras variantes do texto original (correspondência fuzzy). As traduções serão ordenadas: 100% corresponde primeiro, jogos confusos a seguir.
* `From`: Se o método não `From` especificar uma língua, este valor virá da deteção automática de linguagem. Caso contrário, será a `From` língua especificada.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. Contém o valor `TranslateOptions` fornecido no parâmetro.

O `TranslationMatch` objeto consiste nestes valores:

* `Error`: O código de erro, se ocorrer um erro para uma cadeia de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: Indica a proximidade com o texto de entrada encontrado na loja. O sistema corresponde às frases de entrada contra a loja, incluindo fósforos inexatos. O valor devolvido varia de 0 a 100, onde 0 não é semelhança e 100 é uma combinação exata e sensível ao caso.
* `MatchedOriginalText`: Texto original que foi acompanhado por este resultado. Este valor só é devolvido se o texto original combinado fosse diferente do texto de entrada. É usado para devolver o texto de origem de uma correspondência confusa. Este valor não é devolvido para os resultados do Microsoft Tradutor.
* `Rating`: Indica a autoridade da pessoa que está a tomar a decisão de qualidade. Os resultados da Tradução Automática têm uma classificação de 5. As traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas de forma autoritária terão geralmente uma classificação de 6 a 10.
* `Count`: O número de vezes que esta tradução com esta classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.

### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)
Um `GetTranslationsResponse` objeto no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Necessário. Se `Authorization` o `Ocp-Apim-Subscription-Key` cabeçalho ou `appid` cabeçalho for utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .|consulta|string|
|texto|(vazio)|Necessário. Uma corda que representa o texto para traduzir. O tamanho máximo do texto é de 10.000 caracteres.|consulta|string|
|De|(vazio)|Necessário. Uma cadeia que representa o código linguístico do texto que está a ser traduzido.|consulta|string|
|para |(vazio)    |Necessário. Uma cadeia que representa o código linguístico da língua para traduzir o texto.|consulta|string|
|maxTraduções|(vazio)|Necessário. Um inteiro que representa o número máximo de traduções para devolver.|consulta|número inteiro|
|Autorização| (vazio)|Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios. Ficha de `"Bearer" + " " + "access_token"`autorização: .|string|  cabeçalho|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503|Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera vários candidatos de tradução para vários textos de origem.

O pedido `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`URI é .

Aqui está o formato do órgão de pedido:

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

`GetTranslationsArrayRequest`inclui estes elementos:

* `AppId`: Obrigatório. Se `Authorization` o cabeceamento for `AppId` utilizado, deixe o campo vazio. Caso contrário, inclua `"Bearer" + " " + "access_token"`uma corda que contenha .
* `From`: Obrigatório. Uma cadeia que representa o código linguístico do texto que está a ser traduzido.
* `MaxTranslations`: Obrigatório. Um inteiro que representa o número máximo de traduções para devolver.
* `Options`: Opcional. Um `Options` objeto que contém os seguintes valores. São todos opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser enumerados por ordem alfabética.
    - `Category`: Uma cadeia que contenha a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: A única opção suportada, `text/plain`e o padrão, é .
    - `IncludeMultipleMTAlternatives`: Uma bandeira booleana para especificar se mais do que uma alternativa deve ser devolvida do motor MT. Os valores válidos são `true` e `false` (sensíveis a casos). O padrão `false`é , que devolve apenas uma alternativa. A fixação `true` da bandeira permite a geração de alternativas artificiais na tradução, totalmente integradas com o Quadro de Traduções Colaborativas (CTF). A funcionalidade permite a devolução de alternativas para frases que não têm alternativas em CTF adicionando alternativas artificiais *da*melhor lista do descodificador.
        - Classificações As classificações são aplicadas desta forma:
          - A melhor tradução automática tem uma classificação de 5.
          - As alternativas da CTF refletem a autoridade do revisor. Variam de -10 a +10.
          - As alternativas de tradução geradas automaticamente *(n-best)* têm uma classificação de 0 e um grau de correspondência de 100.
        - Número de alternativas. O número de alternativas devolvidas pode ser `maxTranslations`tão elevado quanto o valor especificado, mas pode ser menor.
        - Pares de línguas. Esta funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Tradutor.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtrar os resultados por este URI. Se não for definido qualquer `all`valor, o padrão é .
* `User`: Filtrar os resultados por este utilizador. Se não for definido qualquer `all`valor, o padrão é .
* `Texts`: Obrigatório. Uma matriz que contém o texto para tradução. Todas as cordas devem estar na mesma língua. O total de todo o texto a traduzir não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é de 10.
* `To`: Obrigatório. Uma cadeia que representa o código linguístico da língua para traduzir o texto.

Pode omitindo elementos opcionais. Os elementos que `GetTranslationsArrayRequest` são crianças diretas devem ser listados por ordem alfabética.

O `Content-Type` pedido `text/xml`deve ser.

**Valor de devolução:** Aqui está o formato da resposta:

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

* `Translations`: Uma série de fósforos `TranslationMatch` encontrados, armazenados em objetos (descritos na secção seguinte). As traduções podem incluir ligeiras variantes do texto original (correspondência fuzzy). As traduções serão ordenadas: 100% corresponde primeiro, jogos confusos a seguir.
* `From`: Se o método não `From` especificar uma língua, este valor virá da deteção automática de linguagem. Caso contrário, será a `From` língua especificada.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. Contém o valor `TranslateOptions` fornecido no parâmetro.

O `TranslationMatch` objeto contém os seguintes valores:
* `Error`: O código de erro, se ocorrer um erro para uma cadeia de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: Indica a proximidade com o texto de entrada encontrado na loja. O sistema corresponde às frases de entrada contra a loja, incluindo fósforos inexatos. O valor devolvido varia de 0 a 100, onde 0 não é semelhança e 100 é uma combinação exata e sensível ao caso.
* `MatchedOriginalText`: Texto original que foi acompanhado por este resultado. Este valor só é devolvido se o texto original combinado fosse diferente do texto de entrada. É usado para devolver o texto de origem de uma correspondência confusa. Este valor não é devolvido para os resultados do Microsoft Tradutor.
* `Rating`: Indica a autoridade da pessoa que está a tomar a decisão de qualidade. Os resultados da Tradução Automática têm uma classificação de 5. As traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas de forma autoritária geralmente têm uma classificação de 6 a 10.
* `Count`: O número de vezes que esta tradução com esta classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.


### <a name="response-class-status-200"></a>Classe resposta (estatuto 200)

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |Necessário se tanto `appid` o `Ocp-Apim-Subscription-Key` campo como o cabeçalho ficarem vazios.  Ficha de `"Bearer" + " " + "access_token"`autorização: .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se tanto `appid` o `Authorization` campo como o cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta detalhada ao erro.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido `X-MS-Trans-Info`e com o ID de pedido incluído no cabeçalho de resposta .|
|503    |Serviço temporariamente indisponível. Por favor, tente novamente e avise-nos se o erro persistir.|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para tradutor Texto API v3](../migrate-to-v3.md)


