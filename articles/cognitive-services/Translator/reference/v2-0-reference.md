---
title: Tradutor v2.0
titleSuffix: Azure Cognitive Services
description: Documentação de referência para o Tradutor v2.0.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: fd0dbe5912b7c4df3c666c648dbf9a92d5398cf1
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369516"
---
# <a name="translator-v20"></a>Tradutor v2.0

> [!IMPORTANT]
> Esta versão do Tradutor foi depreciada. [Ver documentação para a versão 3 do Tradutor.](v3-0-reference.md)

A versão 2 do Tradutor pode ser integrada perfeitamente nas suas apps, websites, ferramentas ou outras soluções para fornecer experiências multi-fiéis ao utilizador. Pode usá-lo em qualquer plataforma de hardware e com qualquer sistema operativo para executar tradução linguística e outras tarefas relacionadas com a linguagem, como deteção de linguagem de texto e texto para a fala, de acordo com os padrões da indústria. Para mais informações, consulte [o Tradutor.](../translator-info-overview.md)

## <a name="getting-started"></a>Introdução
Para aceder ao Tradutor, tem de [se inscrever no Microsoft Azure](../translator-how-to-signup.md).

## <a name="authentication"></a>Autenticação 
Todas as chamadas para o Tradutor requerem uma chave de subscrição para a autenticação. A API suporta três métodos de autenticação:

- Um símbolo de acesso. Utilize a chave de subscrição para criar um token de acesso fazendo um pedido DEM para o serviço de autenticação. Consulte a documentação do serviço simbólico para mais detalhes. Passe o token de acesso ao serviço Tradutor utilizando o `Authorization` cabeçalho ou o `access_token` parâmetro de consulta. O token de acesso é válido por 10 minutos. Obtenha um novo token de acesso a cada 10 minutos e continue a usar o mesmo token de acesso para pedidos repetidos durante os 10 minutos.
- Uma chave de subscrição usada diretamente. Passe a sua chave de subscrição como um valor no `Ocp-Apim-Subscription-Key` cabeçalho incluído com o seu pedido ao Tradutor. Quando utiliza a chave de subscrição diretamente, não precisa de ligar para o serviço de autenticação simbólica para criar um token de acesso.
- Uma [subscrição multi-serviço da Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/). Este método permite-lhe utilizar uma única chave secreta para autenticar pedidos de múltiplos serviços.
Quando utilizar uma chave secreta multi-serviço, tem de incluir dois cabeçalhos de autenticação com o seu pedido. O primeiro cabeceamento passa a chave secreta. O segundo cabeçalho especifica a região associada à sua subscrição:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A região é necessária para a subscrição de API de texto multi-serviço. A região que seleciona é a única região que pode utilizar para tradução de texto quando utilizar a chave de subscrição multi-serviço. Tem de ser a mesma região que selecionou quando se inscreveu para a sua subscrição de vários serviços no portal Azure.

As regiões disponíveis são, , , , , , , , , , , , , , `australiaeast` , , , `brazilsouth` , , , `canadacentral` `centralindia` `centraluseuap` `eastasia` e `eastus` `eastus2` `japaneast` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` .

A chave de subscrição e o token de acesso são segredos que devem ser escondidos da vista.

## <a name="profanity-handling"></a>Manipulação de profanidades
Normalmente, o serviço de Tradutor manterá profanação que está presente na fonte. O grau de profanação e o contexto que faz as palavras profanas diferem de acordo com a cultura. Assim, o grau de profanação na língua-alvo poderia ser aumentado ou reduzido.

Se quiser evitar palavrões na tradução mesmo quando está no texto de origem, pode utilizar a opção de filtragem de profanidades para os métodos que a suportam. A opção permite-lhe escolher se pretende ver profanação apagada ou marcada com etiquetas apropriadas, ou se pretende permitir a profanação no alvo. Os valores aceites `ProfanityAction` são `NoAction` (padrão), `Marked` e `Deleted` .


|ProfanidadeAcção    |Ação |Fonte de exemplo (japonesa)  |Tradução de exemplo (inglês)  |
|:--|:--|:--|:--|
|NoAction   |Predefinição. O mesmo que não definir a opção. Profanidade passará de fonte para alvo.        |彼はジャッカスです。     |É um idiota.   |
|Marcado     |Palavras profanas serão rodeadas por tags XML \<profanity> e \</profanity> .       |彼はジャッカスです。 |É um \<profanity> \</profanity> idiota.  |
|Eliminado    |Palavras profanas serão removidas da saída sem substituição.     |彼はジャッカスです。 |É um.   |

    
## <a name="excluding-content-from-translation"></a>Excluindo o conteúdo da tradução
Quando se traduz conteúdo com tags, como HTML `contentType=text/html` (), por vezes é útil excluir conteúdo específico da tradução. Pode utilizar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer na sua língua original. No exemplo seguinte, o conteúdo do primeiro `div` elemento não será traduzido, mas o conteúdo do segundo `div` elemento será traduzido.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Traduzir

### <a name="implementation-notes"></a>Notas de implementação
Traduz uma cadeia de texto de uma língua para outra.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/Translate` .

**Valor de devolução:** Uma corda que representa o texto traduzido.

Se usou previamente `AddTranslation` ou para introduzir uma `AddTranslationArray` tradução com uma classificação igual ou superior para a mesma frase de origem, `Translate` devolve apenas a escolha de topo que está disponível para o seu sistema. "Mesma frase de origem" significa exatamente o mesmo (100% correspondente), exceto para capitalização, espaço branco, valores de etiqueta e pontuação no final de uma frase. Se nenhuma classificação for armazenada com uma classificação de 5 ou superior, o resultado devolvido será a tradução automática pelo Microsoft Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição    |Tipo de parâmetro|tipo de dados|
|:--|:--|:--|:--|:--|
|appid  |(vazio)    |Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|texto|(vazio)   |Obrigatório. Uma corda que representa o texto para traduzir. O texto não pode conter mais de 10.000 caracteres.|consulta|string|
|De|(vazio)   |Opcional. Uma cadeia que representa o código linguístico do texto que está a ser traduzido. Por exemplo, en para inglês.|consulta|string|
|para|(vazio) |Obrigatório. Uma cadeia que representa o código da linguagem para traduzir o texto em.|consulta|string|
|conteúdoType|(vazio)    |Opcional. O formato do texto a ser traduzido. Os formatos suportados são `text/plain` (predefinidos) e  `text/html` . Quaisquer elementos HTML devem ser elementos completos e bem formados.|consulta|string|
|categoria|(vazio)   |Opcional. Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.|consulta|string|
|Autorização|(vazio)  |Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|


### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera traduções para textos de origem múltiplas.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray` .

Aqui está o formato do corpo de pedido:

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

Estes elementos estão `TranslateArrayRequest` em:


* `AppId`: Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `AppId` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .
* `From`: Opcional. Uma cadeia que representa o código linguístico do texto que está a ser traduzido. Se este campo ficar vazio, a resposta incluirá o resultado da deteção automática da linguagem.
* `Options`: Opcional. Um `Options` objeto que contém os seguintes valores. São todas opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser listados por ordem alfabética.
    - `Category`: Uma cadeia que contenha a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: O formato do texto a ser traduzido. Os formatos suportados são `text/plain` (predefinidos) `text/xml` e `text/html` . Quaisquer elementos HTML devem ser elementos completos e bem formados.
    - `ProfanityAction`: Especifica como as profanidades são tratadas, como explicado anteriormente. Os valores aceites são `NoAction` (padrão), `Marked` e `Deleted` .
    - `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
    - `Uri`: Filtrar os resultados deste URI. Predefinição: `all`.
    - `User`: Filtrar os resultados deste utilizador. Predefinição: `all`.
* `Texts`: Obrigatório. Uma matriz que contém o texto para tradução. Todas as cordas devem estar na mesma língua. O total de todo o texto a traduzir não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é de 2.000.
* `To`: Obrigatório. Uma cadeia que representa o código da linguagem para traduzir o texto em.

Pode omitir elementos opcionais. Os elementos que são crianças diretas `TranslateArrayRequest` devem ser listados por ordem alfabética.

O `TranslateArray` método aceita ou para `application/xml` `text/xml` `Content-Type` .

**Valor de devolução:** Uma `TranslateArrayResponse` matriz. Cada um `TranslateArrayResponse` tem estes elementos:

* `Error`: Indica um erro se ocorrer um. Caso contrário, definido para nulo.
* `OriginalSentenceLengths`: Uma matriz de inteiros que indica a duração de cada frase no texto de origem. O comprimento da matriz indica o número de frases.
* `TranslatedText`: O texto traduzido.
* `TranslatedSentenceLengths`: Uma matriz de inteiros que indica o comprimento de cada frase no texto traduzido. O comprimento da matriz indica o número de frases.
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

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma resposta bem sucedida inclui uma variedade de `TranslateArrayResponse` matrizes no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)  |Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP   |Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada. Os erros comuns incluem: <ul><li>O elemento de matriz não pode estar vazio.</li><li>Categoria inválida.</li><li>Da linguagem é inválido.</li><li>A linguagem é inválida.</li><li>O pedido contém demasiados elementos.</li><li>A língua From não é apoiada.</li><li>A linguagem To não é apoiada.</li><li>Traduzir O Pedido tem muitos dados.</li><li>HTML não está num formato correto.</li><li>Demasiadas cordas foram passadas no Pedido de Tradução.</li></ul>|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Notas de implementação
Recupera nomes amigáveis para as línguas passadas como o `languageCodes` parâmetro, localizado na `locale` língua passada.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames` .

O corpo de pedido inclui uma matriz de cordas que representa os códigos linguísticos ISO 639-1 para os quais recuperar os nomes amigáveis. Eis um exemplo:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**Valor de devolução:** Um conjunto de cordas que contém nomes linguísticos suportados pelo serviço Tradutor, localizado na língua solicitada.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Um conjunto de cordas que contém nomes de línguas suportados pelo serviço Tradutor, localizado na língua solicitada.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|região|(vazio) |Obrigatório. Uma corda que representa um dos seguintes, usado para localizar os nomes linguísticos: <ul><li>A combinação de um código de cultura inferior ISO 639 de duas letras associado a uma língua e um código de subcultura superior ISO 3166 de duas letras. <li>Um código de cultura inferior ISO 639 por si só.|consulta|string|
|Autorização|(vazio)  |Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Notas de implementação
Obtém uma lista de códigos linguísticos que representam línguas suportadas pelo serviço de Tradução.  `Translate` e `TranslateArray` pode traduzir entre qualquer uma destas duas línguas.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate` .

**Valor de devolução:** Uma matriz de cordas que contém os códigos linguísticos suportados pelo serviço Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma matriz de cordas que contém os códigos linguísticos suportados pelo serviço Tradutor.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|Autorização|(vazio)  |Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503|Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Notas de implementação
Recupera as línguas disponíveis para a síntese da fala.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak` .

**Valor de devolução:** Uma matriz de cordas que contém os códigos linguísticos suportados para a síntese da fala pelo serviço Tradutor.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma matriz de cordas que contém os códigos linguísticos suportados para a síntese da fala pelo serviço Tradutor.

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|
 
### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Notas de implementação
Devolve um fluxo DE WAV ou MP3 do texto passado, falado na língua desejada.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/Speak` .

**Valor de devolução:** Um fluxo DE WAV ou MP3 do texto passado, falado na língua desejada.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

binary

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|texto|(vazio)   |Obrigatório. Uma corda que contém uma ou mais frases a serem faladas para o fluxo, na língua especificada. O texto não deve exceder 2.000 caracteres.|consulta|string|
|language|(vazio)   |Obrigatório. Uma cadeia que representa o código linguístico suportado da língua para falar o texto. O código deve ser um dos códigos devolvidos pelo método `GetLanguagesForSpeak` .|consulta|string|
|formato|(vazio)|Opcional. Uma cadeia que especifica o ID do tipo de conteúdo. Atualmente,  `audio/wav` e `audio/mp3` estão disponíveis. O valor predefinido é `audio/wav`.|consulta|string|
|opções|(vazio)    |Opcional. Uma cadeia que especifica propriedades da fala sintetizada:<ul><li>`MaxQuality` e `MinSize` especificar a qualidade do sinal de áudio. `MaxQuality` fornece a mais alta qualidade. `MinSize` fornece o menor tamanho de arquivo. O padrão é  `MinSize` .</li><li>`female` e `male` especificar o sexo desejado da voz. A predefinição é `female`. Utilize a barra vertical <code>\|</code> para incluir várias opções. Por exemplo, `MaxQuality|Male`.</li></li></ul>  |consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Notas de implementação
Identifica a linguagem de uma secção de texto.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/Detect` .

**Valor de devolução:** Uma cadeia que contém um código linguístico de dois caracteres para o texto.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|texto|(vazio)|Obrigatório. Uma cadeia que contém texto cuja linguagem deve ser identificada. O texto não deve exceder 10.000 caracteres.|consulta|  string|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key  |(vazio)    |Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|


## <a name="post-detectarray"></a>POST /DetectArray

### <a name="implementation-notes"></a>Notas de implementação

Identifica as línguas numa variedade de cordas. De forma independente, deteta a linguagem de cada elemento de matriz individual e devolve um resultado para cada linha da matriz.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/DetectArray` .

Aqui está o formato do corpo de pedido:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

O texto não pode exceder 10.000 caracteres.

**Valor de devolução:** Uma matriz de cordas que contém um código linguístico de dois caracteres para cada linha na matriz de entrada.

Aqui está o formato do corpo de resposta:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
`DetectArray` foi bem sucedido. Retorna uma matriz de cordas que contém um código linguístico de dois caracteres para cada linha da matriz de entrada.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios.  Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de depreciação:** Depois de 31 de janeiro de 2018, este método não aceita novas submissões de sentenças. Receberá uma mensagem de erro. Consulte o anúncio sobre as alterações ao Quadro de Tradução Colaborativa (CTF).

Adiciona uma tradução à memória de tradução.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation` .

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação: xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados   |
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|texto original|(vazio)|Obrigatório. Uma corda que contém o texto para traduzir. O comprimento máximo da corda é de 1.000 caracteres.|consulta|string|
|traduzidoText|(vazio) |Obrigatório. Uma cadeia que contém texto traduzido para a linguagem-alvo. O comprimento máximo da corda é de 2.000 caracteres.|consulta|string|
|De|(vazio)   |Obrigatório. Uma cadeia que representa o código linguístico da língua original do texto. Por exemplo, en para inglês e de para alemão.|consulta|string|
|para|(vazio)|Obrigatório. Uma cadeia que representa o código linguístico da língua para traduzir o texto em.|consulta|string|
|classificação|(vazio) |Opcional. Um inteiro que representa a classificação de qualidade para a corda. O valor está entre -10 e 10. A predefinição é 1.|consulta|número inteiro|
|conteúdoType|(vazio)    |Opcional. O formato do texto a ser traduzido. Os formatos suportados são `text/plain` `text/html` e. Quaisquer elementos HTML devem ser elementos completos e bem formados.    |consulta|string|
|categoria|(vazio)|Opcional. Uma cadeia que contém a categoria (domínio) da tradução. A predefinição é `general`.|consulta|string|
|utilizador|(vazio)|Obrigatório. Uma corda que é usada para rastrear o criador da submissão.|consulta|string|
|uri|(vazio)|Opcional. Uma cadeia que contém a localização do conteúdo da tradução.|consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios.  Ficha de autorização:  `"Bearer" + " " + "access_token"` .  |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410|`AddTranslation` já não é apoiado.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Notas de implementação

> [!IMPORTANT]
> **Nota de depreciação:** Depois de 31 de janeiro de 2018, este método não aceita novas submissões de sentenças. Receberá uma mensagem de erro. Consulte o anúncio sobre as alterações ao Quadro de Tradução Colaborativa (CTF).

Adiciona uma série de traduções à memória de tradução. Este método é uma versão de matriz de `AddTranslation` .

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray` .

Aqui está o formato do corpo de pedido:

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

Estes elementos estão `AddtranslationsRequest` em:

* `AppId`: Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `AppId` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .
* `From`: Obrigatório. Uma cadeia que contém o código linguístico da língua de origem. Deve ser uma das línguas devolvidas pelo `GetLanguagesForTranslate` método.
* `To`: Obrigatório. Uma cadeia que contém o código linguístico da linguagem-alvo. Deve ser uma das línguas devolvidas pelo `GetLanguagesForTranslate` método.
* `Translations`: Obrigatório. Uma série de traduções para adicionar à memória de tradução. Cada tradução deve `OriginalText` `TranslatedText` conter, e `Rating` . . O tamanho máximo de cada `OriginalText` um e é de `TranslatedText` 1.000 caracteres. O total de todos `OriginalText` e `TranslatedText` elementos não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é de 100.
* `Options`: Obrigatório. Um conjunto de opções, `Category` `ContentType` incluindo, , e `Uri` `User` . `User` é obrigatório. `Category`, `ContentType` e `Uri` são opcionais. Os elementos especificados devem ser listados por ordem alfabética.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
`AddTranslationArray` método conseguiu. 

Depois de 31 de janeiro de 2018, as candidaturas à sentença não serão aceites. O serviço responderá com o código de erro 410.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios.  Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|410    |`AddTranslation` já não é apoiado.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503|Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Notas de implementação
Quebra uma secção de texto em frases e devolve uma matriz que contém os comprimentos de cada frase.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences` .

**Valor de devolução:** Uma série de inteiros que representam o comprimento das frases. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada frase.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Uma série de inteiros que representam o comprimento das frases. O comprimento da matriz representa o número de frases. Os valores representam o comprimento de cada frase.

número inteiro

Tipo de conteúdo de resposta: aplicação/xml

### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)  |Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta| string|
|texto|(vazio)   |Obrigatório. Uma corda que representa o texto para dividir em frases. O tamanho máximo do texto é de 10.000 caracteres.|consulta|string|
|language   |(vazio)    |Obrigatório. Uma cadeia que representa o código linguístico do texto de entrada.|consulta|string|
|Autorização|(vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .   |cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)|Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400|Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401|Credenciais inválidas.|
|500|Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503|Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Notas de implementação
Recupera uma série de traduções para um determinado par de idiomas da loja e do motor MT. `GetTranslations` difere de `Translate` que devolve todas as traduções disponíveis.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations` .

O corpo do pedido inclui o `TranslationOptions` objeto opcional, que tem este formato:

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

O `TranslateOptions` objeto contém os valores na lista seguinte. São todas opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser listados por ordem alfabética.

* `Category`: Uma cadeia que contenha a categoria (domínio) da tradução. A predefinição é `general`.
* `ContentType`: A única opção suportada, e o padrão, é `text/plain` .
* `IncludeMultipleMTAlternatives`: Uma bandeira booleana para especificar se mais do que uma alternativa deve ser devolvida do motor MT. Valores válidos são `true` e `false` (sensível a maiuscamente). O padrão é `false` , que devolve apenas uma alternativa. A colocação da bandeira `true` permite a criação de alternativas artificiais, totalmente integradas com o Quadro de Tradução Colaborativa (CTF). A funcionalidade permite o regresso de alternativas para frases que não têm traduções no CTF, adicionando alternativas artificiais da lista *n-melhor* do descodificador.
    - As audiências. As classificações são aplicadas assim: 
         - A melhor tradução automática tem uma classificação de 5.
       - As alternativas da CTF refletem a autoridade do revisor. Variam de -10 a +10.
       - As alternativas de tradução geradas automaticamente *(n-best)* têm uma classificação de 0 e um grau de correspondência de 100.
    - Número de alternativas. O número de alternativas devolvidas pode ser tão elevado quanto o valor especificado em `maxTranslations` , mas pode ser menor.
    - Pares de linguagem. Esta funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Tradutor.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtrar os resultados deste URI. Se não for definido qualquer valor, o padrão é `all` .
* `User`: Filtrar os resultados deste utilizador. Se não for definido qualquer valor, o padrão é `all` .

O pedido `Content-Type` deve `text/xml` ser.

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

Esta resposta inclui um `GetTranslationsResponse` elemento que contém os seguintes valores:

* `Translations`: Uma matriz das correspondências `TranslationMatch` encontradas, armazenadas em objetos (descritos na seguinte secção). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde primeiro, jogos confusos a seguir.
* `From`: Se o método não especificar uma `From` língua, este valor virá da deteção automática da linguagem. Caso contrário, será a `From` língua especificada.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. Contém o valor fornecido no `TranslateOptions` parâmetro.

O `TranslationMatch` objeto consiste nestes valores:

* `Error`: O código de erro, se ocorrer um erro para uma cadeia de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: Indica o quanto o texto de entrada corresponde ao texto original encontrado na loja. O sistema corresponde às frases de entrada contra a loja, incluindo fósforos inexatos. O valor devolvido varia de 0 a 100, onde 0 não é semelhança e 100 é um jogo exato e sensível a casos.
* `MatchedOriginalText`: Texto original que foi combinado para este resultado. Este valor só é devolvido se o texto original combinado for diferente do texto de entrada. É usado para devolver o texto de origem de um fósforo confuso. Este valor não é devolvido para os resultados do Microsoft Tradutor.
* `Rating`: Indica a autoridade da pessoa que está a tomar a decisão de qualidade. Os resultados da tradução automática têm uma classificação de 5. As traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas por autoridade terão geralmente uma classificação de 6 a 10.
* `Count`: Foi selecionado o número de vezes que esta tradução com esta classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.

### <a name="response-class-status-200"></a>Classe de resposta (estado 200)
Um `GetTranslationsResponse` objeto no formato descrito anteriormente.

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|appid|(vazio)|Obrigatório. Se for `Authorization` utilizado o ou o `Ocp-Apim-Subscription-Key` cabeçalho, deixe o `appid` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .|consulta|string|
|texto|(vazio)|Obrigatório. Uma corda que representa o texto para traduzir. O tamanho máximo do texto é de 10.000 caracteres.|consulta|string|
|De|(vazio)|Obrigatório. Uma cadeia que representa o código linguístico do texto que está a ser traduzido.|consulta|string|
|para |(vazio)    |Obrigatório. Uma cadeia que representa o código linguístico da língua para traduzir o texto em.|consulta|string|
|maxTranslations|(vazio)|Obrigatório. Um número inteiro que representa o número máximo de traduções para devolver.|consulta|número inteiro|
|Autorização| (vazio)|Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios. Ficha de autorização:  `"Bearer" + " " + "access_token"` .|string|  cabeçalho|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta `X-MS-Trans-Info` .|
|503|Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Notas de implementação
Recupera vários candidatos de tradução para textos de origem múltiplas.

O pedido URI é `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray` .

Aqui está o formato do corpo de pedido:

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

* `AppId`: Obrigatório. Se o `Authorization` cabeçalho for utilizado, deixe o `AppId` campo vazio. Caso contrário, inclua uma corda que contenha `"Bearer" + " " + "access_token"` .
* `From`: Obrigatório. Uma cadeia que representa o código linguístico do texto que está a ser traduzido.
* `MaxTranslations`: Obrigatório. Um número inteiro que representa o número máximo de traduções para devolver.
* `Options`: Opcional. Um `Options` objeto que contém os seguintes valores. São todas opcionais e padrão para as configurações mais comuns. Os elementos especificados devem ser listados por ordem alfabética.
    - `Category`: Uma cadeia que contenha a categoria (domínio) da tradução. A predefinição é `general`.
    - `ContentType`: A única opção suportada, e o padrão, é `text/plain` .
    - `IncludeMultipleMTAlternatives`: Uma bandeira booleana para especificar se mais do que uma alternativa deve ser devolvida do motor MT. Valores válidos são `true` e `false` (sensível a maiuscamente). O padrão é `false` , que devolve apenas uma alternativa. A colocação da bandeira `true` permite a geração de alternativas artificiais na tradução, totalmente integradas com o Quadro de Traduções Colaborativas (CTF). A funcionalidade permite o regresso de alternativas para frases que não têm alternativas no CTF, adicionando alternativas artificiais da lista *n-melhor* do descodificador.
        - Classificações As classificações são aplicadas desta forma:
          - A melhor tradução automática tem uma classificação de 5.
          - As alternativas da CTF refletem a autoridade do revisor. Variam de -10 a +10.
          - As alternativas de tradução geradas automaticamente *(n-best)* têm uma classificação de 0 e um grau de correspondência de 100.
        - Número de alternativas. O número de alternativas devolvidas pode ser tão elevado quanto o valor especificado em `maxTranslations` , mas pode ser menor.
        - Pares de linguagem. Esta funcionalidade não está disponível para traduções entre chinês simplificado e chinês tradicional, em qualquer direção. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Tradutor.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
* `Uri`: Filtrar os resultados deste URI. Se não for definido qualquer valor, o padrão é `all` .
* `User`: Filtrar os resultados deste utilizador. Se não for definido qualquer valor, o padrão é `all` .
* `Texts`: Obrigatório. Uma matriz que contém o texto para tradução. Todas as cordas devem estar na mesma língua. O total de todo o texto a traduzir não pode exceder 10.000 caracteres. O número máximo de elementos de matriz é de 10.
* `To`: Obrigatório. Uma cadeia que representa o código linguístico da língua para traduzir o texto em.

Pode omitir elementos opcionais. Os elementos que são crianças diretas `GetTranslationsArrayRequest` devem ser listados por ordem alfabética.

O pedido `Content-Type` deve `text/xml` ser.

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

* `Translations`: Uma matriz das correspondências `TranslationMatch` encontradas, armazenadas em objetos (descritos na seguinte secção). As traduções podem incluir pequenas variantes do texto original (correspondência difusa). As traduções serão classificadas: 100% corresponde primeiro, jogos confusos a seguir.
* `From`: Se o método não especificar uma `From` língua, este valor virá da deteção automática da linguagem. Caso contrário, será a `From` língua especificada.
* `State`: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. Contém o valor fornecido no `TranslateOptions` parâmetro.

O `TranslationMatch` objeto contém os seguintes valores:
* `Error`: O código de erro, se ocorrer um erro para uma cadeia de entrada específica. Caso contrário, este campo está vazio.
* `MatchDegree`: Indica o quanto o texto de entrada corresponde ao texto original encontrado na loja. O sistema corresponde às frases de entrada contra a loja, incluindo fósforos inexatos. O valor devolvido varia de 0 a 100, onde 0 não é semelhança e 100 é um jogo exato e sensível a casos.
* `MatchedOriginalText`: Texto original que foi combinado para este resultado. Este valor só é devolvido se o texto original combinado for diferente do texto de entrada. É usado para devolver o texto de origem de um fósforo confuso. Este valor não é devolvido para os resultados do Microsoft Tradutor.
* `Rating`: Indica a autoridade da pessoa que está a tomar a decisão de qualidade. Os resultados da tradução automática têm uma classificação de 5. As traduções fornecidas anonimamente geralmente têm uma classificação de 1 a 4. As traduções fornecidas por autoridade geralmente têm uma classificação de 6 a 10.
* `Count`: Foi selecionado o número de vezes que esta tradução com esta classificação foi selecionada. O valor é 0 para a resposta traduzida automaticamente.
* `TranslatedText`: O texto traduzido.


### <a name="response-class-status-200"></a>Classe de resposta (estado 200)

string

Tipo de conteúdo de resposta: aplicação/xml
 
### <a name="parameters"></a>Parâmetros

|Parâmetro|Valor|Descrição|Tipo de parâmetro|Tipo de dados|
|:--|:--|:--|:--|:--|
|Autorização  |(vazio)    |Necessário se o `appid` campo e o `Ocp-Apim-Subscription-Key` cabeçalho ficarem vazios.  Ficha de autorização:  `"Bearer" + " " + "access_token"` .|cabeçalho|string|
|Ocp-Apim-Subscription-Key|(vazio)  |Necessário se o `appid` campo e o `Authorization` cabeçalho ficarem vazios.|cabeçalho|string|

### <a name="response-messages"></a>Mensagens de resposta

|Código de estado de HTTP|Razão|
|:--|:--|
|400    |Mau pedido. Verifique os parâmetros de entrada e a resposta de erro detalhada.|
|401    |Credenciais inválidas.|
|500    |Erro do servidor. Se o erro persistir, avise-nos. Por favor, forneça-nos a data aproximada & hora do pedido e com o ID do pedido incluído no cabeçalho de resposta  `X-MS-Trans-Info` .|
|503    |Serviço temporariamente indisponível. Por favor, volte a tentar e avise-nos se o erro persistir.|

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Migrar para Tradutor v3](../migrate-to-v3.md)