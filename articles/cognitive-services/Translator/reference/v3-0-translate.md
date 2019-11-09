---
title: API de Tradução de Texto método de conversão
titleSuffix: Azure Cognitive Services
description: Entenda os parâmetros, os cabeçalhos e as mensagens de corpo para os serviços cognitivas do Azure API de Tradução de Texto o método translate para traduzir o texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 10/16/2019
ms.author: swmachan
ms.openlocfilehash: b4daa04a4dbf87006147fb0d44d7b128a6d8ecf4
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835788"
---
# <a name="translator-text-api-30-translate"></a>API de Tradução de Texto 3,0: traduzir

Traduz o texto.

## <a name="request-url"></a>URL do Pedido

Enviar uma solicitação de `POST` para:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de solicitação

Os parâmetros de solicitação passados na cadeia de caracteres de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão da API</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Versão da API solicitada pelo cliente. O valor deve ser <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>De</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica o idioma do texto de entrada. Encontre quais idiomas estão disponíveis para tradução procurando por <a href="./v3-0-languages.md">idiomas com suporte</a> usando o escopo de <code>translation</code>. Se o parâmetro <code>from</code> não for especificado, a detecção automática de idioma será aplicada para determinar o idioma de origem. <br/><br/>Você deve usar o parâmetro <code>from</code> em vez da detecção automática ao usar o recurso de <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">dicionário dinâmico</a> .</td>
  </tr>
  <tr>
    <td>para</td>
    <td><em>Parâmetro obrigatório</em>.<br/>Especifica o idioma do texto de saída. O idioma de destino deve ser um dos <a href="./v3-0-languages.md">idiomas com suporte</a> incluídos no escopo de <code>translation</code>. Por exemplo, use <code>to=de</code> para converter para alemão.<br/>É possível traduzir para vários idiomas simultaneamente, repetindo o parâmetro na cadeia de caracteres de consulta. Por exemplo, use <code>to=de&to=it</code> para converter para alemão e italiano.</td>
  </tr>
  <tr>
    <td>Tipo de texto</td>
    <td><em>Parâmetro opcional</em>.<br/>Define se o texto que está sendo traduzido é texto sem formatação ou HTML. Qualquer HTML precisa ser um elemento bem formado e completo. Os valores possíveis são: <code>plain</code> (padrão) ou <code>html</code>.</td>
  </tr>
  <tr>
    <td>categoria</td>
    <td><em>Parâmetro opcional</em>.<br/>Uma cadeia de caracteres que especifica a categoria (domínio) da tradução. Esse parâmetro é usado para obter traduções de um sistema personalizado criado com o <a href="../customization.md">Tradutor personalizado</a>. Adicione a ID da categoria de seus <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">detalhes do projeto</a> do tradutor personalizado a esse parâmetro para usar seu sistema personalizado implantado. O valor padrão é: <code>general</code>.</td>
  </tr>
  <tr>
    <td>ProfanityAction</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica como as obscenidades devem ser tratadas em traduções. Os valores possíveis são: <code>NoAction</code> (padrão), <code>Marked</code> ou <code>Deleted</code>. Para entender as maneiras de tratar a profanação, confira <a href="#handle-profanity">manipulação de profanação</a>.</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica como as obscenidades devem ser marcadas em traduções. Os valores possíveis são: <code>Asterisk</code> (padrão) ou <code>Tag</code>. Para entender as maneiras de tratar a profanação, confira <a href="#handle-profanity">manipulação de profanação</a>.</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica se a projeção de alinhamento do texto de origem ao texto traduzido deve ser incluída. Os valores possíveis são: <code>true</code> ou <code>false</code> (padrão). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica se os limites de sentença devem ser incluídos no texto de entrada e no texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (padrão).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica um idioma de fallback se o idioma do texto de entrada não puder ser identificado. A detecção automática de idioma é aplicada quando o parâmetro <code>from</code> é omitido. Se a detecção falhar, o idioma <code>suggestedFrom</code> será considerado.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica o script do texto de entrada.</td>
  </tr>
  <tr>
    <td>toscript</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica o script do texto traduzido.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>Parâmetro opcional</em>.<br/>Especifica que o serviço tem permissão para fazer fallback para um sistema geral quando não existe um sistema personalizado. Os valores possíveis são: <code>true</code> (padrão) ou <code>false</code>.<br/><br/><code>allowFallback=false</code> especifica que a tradução deve usar apenas sistemas treinados para o <code>category</code> especificado pela solicitação. Se uma tradução para o idioma X para a linguagem Y exigir o encadeamento por meio de uma linguagem pivot E, todos os sistemas na cadeia (X-> E e-> Y) precisarão ser personalizados e terão a mesma categoria. Se nenhum sistema for encontrado com a categoria específica, a solicitação retornará um código de status 400. <code>allowFallback=true</code> especifica que o serviço tem permissão para fazer fallback para um sistema geral quando não existe um sistema personalizado.
</td>
  </tr>
</table> 

Os cabeçalhos de solicitação incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho (s) de autenticação</td>
    <td><em>Cabeçalho de solicitação necessário</em>.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação</a>.</td>
  </tr>
  <tr>
    <td>Tipo de conteúdo</td>
    <td><em>Cabeçalho de solicitação necessário</em>.<br/>Especifica o tipo de conteúdo da carga.<br/> O valor aceito é <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td><em>Cabeçalho de solicitação necessário</em>.<br/>O comprimento do corpo da solicitação.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Opcional</em>.<br/>Um GUID gerado pelo cliente para identificar exclusivamente a solicitação. Você pode omitir esse cabeçalho se incluir a ID de rastreamento na cadeia de caracteres de consulta usando um parâmetro de consulta chamado <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo da solicitação é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa a cadeia de caracteres a ser transvertida.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

As seguintes limitações se aplicam:

* A matriz pode ter no máximo 100 elementos.
* O texto inteiro incluído na solicitação não pode exceder 5.000 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem-sucedida é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `detectedLanguage`: um objeto que descreve a linguagem detectada por meio das seguintes propriedades:

      * `language`: uma cadeia de caracteres que representa o código do idioma detectado.

      * `score`: um valor float que indica a confiança no resultado. A pontuação é entre zero e uma e uma pontuação baixa indica uma baixa confiança.

    A propriedade `detectedLanguage` só está presente no objeto de resultado quando a detecção automática de idioma é solicitada.

  * `translations`: uma matriz de resultados de tradução. O tamanho da matriz corresponde ao número de idiomas de destino especificados por meio do parâmetro de consulta `to`. Cada elemento na matriz inclui:

    * `to`: uma cadeia de caracteres que representa o código de idioma do idioma de destino.

    * `text`: uma cadeia de caracteres fornecendo o texto traduzido.

    * `transliteration`: um objeto que oferece o texto traduzido no script especificado pelo parâmetro `toScript`.

      * `script`: uma cadeia de caracteres que especifica o script de destino.   

      * `text`: uma cadeia de caracteres fornecendo o texto traduzido no script de destino.

    O objeto `transliteration` não será incluído se o transliteração não ocorrer.

    * `alignment`: um objeto com uma única propriedade de cadeia de caracteres chamada `proj`, que mapeia o texto de entrada para o texto traduzido. As informações de alinhamento só são fornecidas quando o parâmetro de solicitação `includeAlignment` é `true`. O alinhamento é retornado como um valor de cadeia de caracteres do seguinte formato: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Os dois-pontos separa o índice inicial e final, o traço separa os idiomas e o espaço separa as palavras. Uma palavra pode ser alinhada com zero, uma ou várias palavras na outra linguagem, e as palavras alinhadas podem ser não contíguas. Quando nenhuma informação de alinhamento estiver disponível, o elemento Alignment estará vazio. Consulte [obter informações de alinhamento](#obtain-alignment-information) para obter um exemplo e restrições.

    * `sentLen`: um objeto que retorna limites de sentença nos textos de entrada e de saída.

      * `srcSentLen`: uma matriz de inteiros que representa os comprimentos das frases no texto de entrada. O comprimento da matriz é o número de sentenças e os valores são o comprimento de cada frase.

      * `transSentLen`: uma matriz de inteiros que representa os comprimentos das frases no texto traduzido. O comprimento da matriz é o número de sentenças e os valores são o comprimento de cada frase.

    Os limites de frase são incluídos apenas quando o parâmetro de solicitação `includeSentenceLength` é `true`.

  * `sourceText`: um objeto com uma única propriedade de cadeia de caracteres chamada `text`, que fornece o texto de entrada no script padrão do idioma de origem. `sourceText` Propriedade está presente somente quando a entrada é expressa em um script que não é o script comum para a linguagem. Por exemplo, se a entrada fosse escrita em árabe em latim, `sourceText.text` seria o mesmo texto em árabe convertido no script árabe.

O exemplo de respostas JSON é fornecido na seção de [exemplos](#examples) .

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
    <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar a solicitação. Ele é usado para fins de solução de problemas.</td>
  </tr>
  <tr>
    <td>X-MT-System</td>
    <td>Especifica o tipo de sistema que foi usado para conversão para cada idioma ' to ' solicitado para tradução. O valor é uma lista separada por vírgulas de cadeias de caracteres. Cada cadeia de caracteres indica um tipo:<br/><ul><li>Custom-a solicitação inclui um sistema personalizado e pelo menos um sistema personalizado foi usado durante a conversão.</li><li>Equipe-todas as outras solicitações</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de status de resposta

A seguir estão os códigos de status HTTP possíveis que uma solicitação retorna. 

<table width="100%">
  <th width="20%">Código de status</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está ausente ou não é válido. Corrija os parâmetros de solicitação antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Não foi possível autenticar a solicitação. Verifique se as credenciais estão especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A solicitação não é autorizada. Verifique a mensagem de erro detalhes. Isso geralmente indica que todas as traduções gratuitas fornecidas com uma assinatura de avaliação foram usadas.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>A solicitação não pôde ser atendida porque um recurso está ausente. Verifique a mensagem de erro detalhes. Ao usar um <code>category</code>personalizado, isso geralmente indica que o sistema de tradução personalizado ainda não está disponível para atender às solicitações. A solicitação deve ser repetida após um período de espera (por exemplo, 1 minuto).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou a solicitação porque o cliente excedeu os limites de solicitação.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho de resposta <code>X-RequestId</code>e identificador de cliente do cabeçalho de solicitação <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Repita a solicitação. Se o erro persistir, denuncie-o com: data e hora da falha, identificador de solicitação do cabeçalho de resposta <code>X-RequestId</code>e identificador de cliente do cabeçalho de solicitação <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Se ocorrer um erro, a solicitação também retornará uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de status HTTP de 3 dígitos seguido por um número de 3 dígitos para categorizar ainda mais o erro. Códigos de erro comuns podem ser encontrados na [página de referência do API de tradução de texto v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

### <a name="translate-a-single-input"></a>Traduzir uma única entrada

Este exemplo mostra como converter uma única frase de Inglês para chinês simplificado.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

A matriz de `translations` inclui um elemento, que fornece a tradução da única parte do texto na entrada.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Traduza uma única entrada com detecção automática de idioma

Este exemplo mostra como converter uma única frase de Inglês para chinês simplificado. A solicitação não especifica o idioma de entrada. Em vez disso, a detecção automática do idioma de origem é usada.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

O corpo da resposta é:

```
[
    {
        "detectedLanguage": {"language": "en", "score": 1.0},
        "translations":[
            {"text": "你好, 你叫什么名字？", "to": "zh-Hans"}
        ]
    }
]
```
A resposta é semelhante à resposta do exemplo anterior. Como a detecção automática de idioma foi solicitada, a resposta também inclui informações sobre a linguagem detectada para o texto de entrada. 

### <a name="translate-with-transliteration"></a>Traduzir com transliteração

Vamos estender o exemplo anterior adicionando transliteração. A solicitação a seguir solicita uma tradução chinesa escrita em script latino.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

O corpo da resposta é:

```
[
    {
        "detectedLanguage":{"language":"en","score":1.0},
        "translations":[
            {
                "text":"你好, 你叫什么名字？",
                "transliteration":{"script":"Latn", "text":"nǐ hǎo , nǐ jiào shén me míng zì ？"},
                "to":"zh-Hans"
            }
        ]
    }
]
```

O resultado da conversão agora inclui uma propriedade `transliteration`, que fornece o texto traduzido usando caracteres latinos.

### <a name="translate-multiple-pieces-of-text"></a>Traduzir várias partes de texto

A conversão de várias cadeias de caracteres de uma vez é simplesmente uma questão de especificar uma matriz de cadeias de caracteres no corpo da solicitação.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    },            
    {
        "translations":[
            {"text":"我很好，谢谢你。","to":"zh-Hans"}
        ]
    }
]
```

### <a name="translate-to-multiple-languages"></a>Traduzir para vários idiomas

Este exemplo mostra como converter a mesma entrada em vários idiomas em uma solicitação.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

O corpo da resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"},
            {"text":"Hallo, was ist dein Name?","to":"de"}
        ]
    }
]
```

### <a name="handle-profanity"></a>Identificar profanação

Normalmente, o serviço do tradutor reterá profanação que está presente na origem na tradução. O grau de profanação e o contexto que torna as palavras impróprias diferem entre culturas e, como resultado, o grau de profanação no idioma de destino pode ser amplificado ou reduzido.

Se você quiser evitar a irprofanação na tradução, independentemente da presença de profanação no texto de origem, poderá usar a opção de filtragem de profanação. A opção permite que você escolha se deseja ver a profanação excluída, se deseja marcar as obscenidades com as marcas apropriadas (oferecendo a opção de adicionar seu próprio pós-processamento) ou se deseja que nenhuma ação seja executada. Os valores aceitos de `ProfanityAction` são `Deleted`, `Marked` e `NoAction` (padrão).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Ação</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Esse é o comportamento padrão. A profanação passará da origem para a de destino.<br/><br/>
    <strong>Exemplo de origem (japonês)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Tradução de exemplo (inglês)</strong>: ele é um Jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Palavras obscenas serão removidas da saída sem substituição.<br/><br/>
    <strong>Exemplo de origem (japonês)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Tradução de exemplo (inglês)</strong>: ele é um.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Palavras obscenas são substituídas por um marcador na saída. O marcador depende do parâmetro <code>ProfanityMarker</code>.<br/><br/>
Por <code>ProfanityMarker=Asterisk</code>, palavras obscenas são substituídas por <code>***</code>:<br/>
    <strong>Exemplo de origem (japonês)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Tradução de exemplo (inglês)</strong>: ele é um \*\*\*.<br/><br/>
Por <code>ProfanityMarker=Tag</code>, palavras obscenas são cercadas por marcas XML &lt;profanação&gt; e &lt;/profanity&gt;:<br/>
    <strong>Exemplo de origem (japonês)</strong>: 彼はジャッカスです Marketplace.<br/>
    <strong>Tradução de exemplo (inglês)</strong>: é uma &lt;profana&gt;Jackass&lt;/profanity&gt;.
  </tr>
</table> 

Por exemplo:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
Isso retorna:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Comparar com:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

Essa última solicitação retorna:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduza o conteúdo com marcação e decida o que está traduzido

É comum traduzir o conteúdo que inclui marcação como o conteúdo de uma página HTML ou conteúdo de um documento XML. Inclua o parâmetro de consulta `textType=html` ao traduzir o conteúdo com marcas. Além disso, às vezes é útil excluir conteúdo específico da tradução. Você pode usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer em seu idioma original. No exemplo a seguir, o conteúdo dentro do primeiro elemento `div` não será convertido, enquanto o conteúdo no segundo elemento `div` será traduzido.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Aqui está uma solicitação de exemplo para ilustrar.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

A resposta é:

```
[
    {
        "translations":[
            {"text":"<div class=\"notranslate\">This will not be translated.</div><div>这将被翻译。</div>","to":"zh-Hans"}
        ]
    }
]
```

### <a name="obtain-alignment-information"></a>Obter informações de alinhamento

Para receber informações de alinhamento, especifique `includeAlignment=true` na cadeia de caracteres de consulta.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation.'}]"
```

A resposta é:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique.",
                "to":"fr",
                "alignment":{"proj":"0:2-0:1 4:9-3:9 11:14-11:19 16:17-21:24 19:25-40:50 27:37-29:38 38:38-51:51"}
            }
        ]
    }
]
```

As informações de alinhamento começam com `0:2-0:1`, o que significa que os três primeiros caracteres no texto de origem (`The`) são mapeados para os dois primeiros caracteres no texto traduzido (`La`).

#### <a name="limitations"></a>Limitações
Observe as seguintes restrições:

* O alinhamento não está disponível para texto no formato HTML, ou seja, TextType = HTML
* O alinhamento só é retornado para um subconjunto dos pares de idiomas:
  - do inglês para qualquer outro idioma;
  - de qualquer outro idioma para inglês, exceto para chinês simplificado, chinês tradicional e Letão para inglês;
  - de japonês a coreano ou de coreano para japonês.
* Você não receberá alinhamento se a frase for uma tradução gravada. Um exemplo de uma tradução gravada é "Este é um teste", "Eu adoro você" e outras frases de alta frequência.

### <a name="obtain-sentence-boundaries"></a>Obter limites de frase

Para receber informações sobre o tamanho da sentença no texto de origem e no texto traduzido, especifique `includeSentenceLength=true` na cadeia de caracteres de consulta.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

A resposta é:

```
[
    {
        "translations":[
            {
                "text":"La réponse se trouve dans la traduction automatique. La meilleure technologie de traduction automatique ne peut pas toujours fournir des traductions adaptées à un site ou des utilisateurs comme un être humain. Il suffit de copier et coller un extrait de code n’importe où.",
                "to":"fr",
                "sentLen":{"srcSentLen":[40,117,46],"transSentLen":[53,157,62]}
            }
        ]
    }
]
```

### <a name="translate-with-dynamic-dictionary"></a>Traduzir com dicionário dinâmico

Se você já souber a tradução que deseja aplicar a uma palavra ou frase, poderá fornecê-la como marcação dentro da solicitação. O dicionário dinâmico só é seguro para substantivos compostos, como nomes e nomes de produtos adequados.

A marcação a ser fornecida usa a sintaxe a seguir.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Por exemplo, considere a frase em inglês "a palavra WordOMatic é uma entrada de dicionário". Para preservar a palavra _WordOMatic_ na tradução, envie a solicitação:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

O resultado é:

```
[
    {
        "translations":[
            {"text":"Das Wort \"wordomatic\" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Esse recurso funciona da mesma maneira com `textType=text` ou com `textType=html`. O recurso deve ser usado com moderação. A maneira apropriada e bem melhor de personalizar a tradução é usando o tradutor personalizado. O tradutor personalizado faz uso total de probabilidades de contexto e estatística. Se você tem ou pode pagar para criar dados de treinamento que mostrem seu trabalho ou frase no contexto, você obtém resultados muito melhores. [Saiba mais sobre o tradutor personalizado](../customization.md).
