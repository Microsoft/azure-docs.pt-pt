---
title: API de texto do Translator traduzir o método
titleSuffix: Azure Cognitive Services
description: Use o método API de texto do Translator tradução.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 4f6c420ab76462818fb17308d062cc9d881af7df
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58091040"
---
# <a name="translator-text-api-30-translate"></a>Texto do Translator API 3.0: Translate

Traduz texto.

## <a name="request-url"></a>URL do Pedido

Enviar um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros do pedido

Parâmetros de pedido passados na seqüência de consulta são:

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>versão de API</td>
    <td><em>Parâmetro necessário</em>.<br/>Versão da API do pedido pelo cliente. Valor tem de ser <code>3.0</code>.</td>
  </tr>
  <tr>
    <td>de</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica o idioma do texto de entrada. Localizar quais idiomas estão disponíveis para efetuar a conversão de pesquisando [idiomas suportados](./v3-0-languages.md) usando o <code>translation</code> âmbito. Se o <code>from</code> parâmetro não for especificado, a deteção de idioma automática é aplicada para determinar o idioma de origem.</td>
  </tr>
  <tr>
    <td>para</td>
    <td><em>Parâmetro necessário</em>.<br/>Especifica o idioma do texto de saída. O idioma de destino tem de ser o [idiomas suportados](./v3-0-languages.md) incluído no <code>translation</code> âmbito. Por exemplo, utilizar <code>to=de</code> para efetuar a conversão para o alemão.<br/>É possível converter para vários idiomas em simultâneo, repetindo o parâmetro na cadeia de consulta. Por exemplo, utilizar <code>to=de&to=it</code> para efetuar a conversão para o alemão e italiano.</td>
  </tr>
  <tr>
    <td>textType</td>
    <td><em>O parâmetro opcional</em>.<br/>Define se o texto a ser traduzido é texto sem formatação ou HTML. Qualquer HTML tem de ser um elemento bem formado e completo. Os valores possíveis são: <code>plain</code> (predefinição) ou <code>html</code>.</td>
  </tr>
  <tr>
    <td>categoria</td>
    <td><em>O parâmetro opcional</em>.<br/>Uma cadeia que especifica a categoria (domínio) da tradução. Este parâmetro é utilizado para obter as traduções de um sistema personalizado criado com [Translator personalizado](../customization.md). Adicione o ID de categoria do seu projeto personalizado Translator para este parâmetro para utilizar o seu sistema personalizado implementado. Valor predefinido é: <code>general</code>.</td>
  </tr>
  <tr>
    <td>profanityAction</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica como profanities devem ser tratados de traduções. Os valores possíveis são: <code>NoAction</code> (predefinição), <code>Marked</code> ou <code>Deleted</code>. Para compreender as maneiras de tratar a linguagem inapropriada, veja [processamento de linguagem inapropriada](#handle-profanity).</td>
  </tr>
  <tr>
    <td>profanityMarker</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica como profanities devem ser marcados em traduções. Os valores possíveis são: <code>Asterisk</code> (predefinição) ou <code>Tag</code>. Para compreender as maneiras de tratar a linguagem inapropriada, veja [processamento de linguagem inapropriada](#handle-profanity).</td>
  </tr>
  <tr>
    <td>includeAlignment</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica se pretende incluir a projeção de alinhamento do texto de origem para o texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (predefinição). </td>
  </tr>
  <tr>
    <td>includeSentenceLength</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica se pretende incluir limites de frase para a introdução de texto e o texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (predefinição).</td>
  </tr>
  <tr>
    <td>suggestedFrom</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica um idioma de fallback se não é possível identificar o idioma do texto de entrada. Deteção automática de idioma é aplicada quando o <code>from</code> parâmetro for omitido. Se a deteção falhar, o <code>suggestedFrom</code> idioma será assumido.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica o script pelo texto de entrada.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica o script do texto traduzido.</td>
  </tr>
  <tr>
    <td>allowFallback</td>
    <td><em>O parâmetro opcional</em>.<br/>Especifica que o serviço tem permissão para contingência para um sistema geral, quando um sistema personalizado não existe. Os valores possíveis são: <code>true</code> (predefinição) ou <code>false</code>.<br/><br/><code>allowFallback=false</code> Especifica que a tradução só deve utilizar sistemas de preparação para o <code>category</code> especificado pelo pedido. Se precisar de uma tradução para a linguagem X para a linguagem Y encadeamento por meio de uma linguagem dinâmica E, em seguida, todos os sistemas na cadeia (X -> i e I -> Y) tem de ser personalizado e ter a mesma categoria. Se não for encontrado nenhum sistema com uma categoria específica, o pedido irá devolver um código de 400 estado. <code>allowFallback=true</code> Especifica que o serviço tem permissão para contingência para um sistema geral, quando um sistema personalizado não existe.
</td>
  </tr>
</table> 

Cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>_Uma autorização_<br/>_header_</td>
    <td><em>Cabeçalho do pedido necessário</em>.<br/>Ver [as opções disponíveis para autenticação](./v3-0-reference.md#authentication).</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Cabeçalho do pedido necessário</em>.<br/>Especifica o tipo de conteúdo da carga. Os valores possíveis são: <code>application/json</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Cabeçalho do pedido necessário</em>.<br/>O comprimento do corpo do pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Opcional</em>.<br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o ID de rastreio na cadeia de consulta com um parâmetro de consulta com o nome <code>ClientTraceId</code>.</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento da matriz é um objeto JSON com uma propriedade de cadeia de caracteres chamada `Text`, que representa a cadeia a converter.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

As seguintes limitações aplicam-se:

* A matriz pode ter no máximo de 100 elementos.
* Todo o texto incluído no pedido não pode exceder os 5000 carateres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta com êxito é uma matriz JSON com um resultado para cada cadeia de caracteres na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `detectedLanguage`: Um objeto que descreve o idioma detetado através das seguintes propriedades:

      * `language`: Uma cadeia de caracteres representando o código do idioma detetado.

      * `score`: Um valor de vírgula flutuante que indica a confiança no resultado. A classificação é entre zero e outro e uma pontuação baixa indica uma confiança de baixa.

    O `detectedLanguage` propriedade só está presente no objeto de resultado quando é solicitada a deteção automática de idioma.

  * `translations`: Uma matriz de resultados de tradução. O tamanho da matriz corresponde ao número de idiomas de destino especificado por meio do `to` parâmetro de consulta. Cada elemento na matriz inclui:

    * `to`: Uma cadeia de caracteres que representa o código de idioma do idioma de destino.

    * `text`: Uma cadeia de caracteres dando o texto traduzido.

    * `transliteration`: Um objeto dando o texto traduzido no script especificado pelo `toScript` parâmetro.

      * `script`: Uma cadeia que especifica o script de destino.   

      * `text`: Uma cadeia de caracteres dando o texto traduzido no script de destino.

    O `transliteration` objeto não estará incluído se Transliteração não aconteça.

    * `alignment`: Um objeto com uma propriedade de cadeia única chamada `proj`, que é mapeado entrada de texto para o texto traduzido. As informações de alinhamento só são fornecidas quando o parâmetro de pedido `includeAlignment` é `true`. Alinhamento é retornado como um valor de cadeia de caracteres no seguinte formato: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]`.  Início de separa os dois pontos e índice final, o traço separa as linguagens e espaço separa as palavras. Uma palavra pode alinhar com zero, uma ou várias palavras no outro idioma e as palavras alinhadas podem estar não contíguos. Quando não existem informações de alinhamento estiverem disponíveis, o elemento de alinhamento estará vazio. Ver [obter informações de alinhamento](#obtain-alignment-information) para um exemplo e restrições.

    * `sentLen`: Um objeto retornando limites de sentença em textos de entrada e saídos.

      * `srcSentLen`: Uma matriz de número inteiro que representa os comprimentos das sentenças no texto introduzido. O comprimento da matriz é o número de frases e os valores são o comprimento de cada sentença.

      * `transSentLen`:  Uma matriz de número inteiro que representa os comprimentos das sentenças no texto traduzido. O comprimento da matriz é o número de frases e os valores são o comprimento de cada sentença.

    Limites de sentença só estão incluídos quando o parâmetro de pedido `includeSentenceLength` é `true`.

  * `sourceText`: Um objeto com uma propriedade de cadeia única chamada `text`, que dá a introdução de texto do script padrão do idioma de origem. `sourceText` propriedade está presente apenas quando a entrada é expressa num script que não é o script habitual para o idioma. Por exemplo, se a entrada foram escrito no script Latino, em seguida, de Árabe `sourceText.text` poderia ser o mesmo texto em Árabe convertido em script árabe.

Exemplo de respostas JSON são fornecidos na [exemplos](#examples) secção.

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
    <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É utilizado para fins de resolução de problemas.</td>
  </tr>
  <tr>
    <td>Sistema de MT de X</td>
    <td>Especifica o tipo de sistema que foi utilizado para a tradução para cada idioma "para" pedido para a tradução. O valor é uma lista separada por vírgulas de cadeias de caracteres. Cada cadeia indica um tipo:<br/><ul><li>Custom - pedido inclui um sistema personalizado e, pelo menos, um sistema personalizado foi utilizado durante a tradução.</li><li>Equipe - todos os outros pedidos</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que retorna um pedido. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corrija os parâmetros de pedido antes de tentar novamente.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Não foi possível autenticar o pedido. Verifique se as credenciais estão especificado e válido.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique os detalhes da mensagem de erro. Isso geralmente indica que foram utilizadas todas as conversões gratuitas fornecidas com uma subscrição de avaliação cópia de segurança.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>O pedido não foi possível ser concluído porque um recurso está em falta. Verifique os detalhes da mensagem de erro. Quando utilizar um personalizado <code>category</code>, isso geralmente indica que o sistema de tradução personalizadas ainda não está disponível para servir pedidos. O pedido deve ser repetido após um período de espera (por exemplo, 1 minuto).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O chamador está enviando demasiados pedidos.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o o erro persistir, reporte-o com: data e hora da falha, o identificador de pedido do cabeçalho de resposta <code>X-RequestId</code>e o identificador de cliente a partir do cabeçalho de pedido <code>X-ClientTraceId</code>.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Repita o pedido. Se o o erro persistir, reporte-o com: data e hora da falha, o identificador de pedido do cabeçalho de resposta <code>X-RequestId</code>e o identificador de cliente a partir do cabeçalho de pedido <code>X-ClientTraceId</code>.</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também irá devolver uma resposta de erro do JSON. O código de erro é uma combinação de número de 6 dígitos o código de estado HTTP de 3 dígitos seguido por uma série de 3 dígitos para ainda mais categorizar o erro. Códigos de erro comuns que podem ser encontrados no [página de referência de API de texto do Microsoft Translator v3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

### <a name="translate-a-single-input"></a>Traduzir uma entrada única

Este exemplo mostra como traduzir uma frase única de inglês para chinês simplificado.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo de resposta é:

```
[
    {
        "translations":[
            {"text":"你好, 你叫什么名字？","to":"zh-Hans"}
        ]
    }
]
```

O `translations` matriz inclui um elemento, que fornece a tradução da única parte de texto na entrada.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Traduzir uma entrada única com deteção automática de idioma

Este exemplo mostra como traduzir uma frase única de inglês para chinês simplificado. O pedido não especifica o idioma de entrada. Deteção automática de idioma de origem é utilizada em vez disso.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo de resposta é:

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
A resposta é semelhante à resposta do exemplo anterior. Uma vez que foi solicitada a deteção automática de idioma, a resposta também inclui informações sobre o idioma detetado para a introdução de texto. 

### <a name="translate-with-transliteration"></a>Traduzir com Transliteração

Vamos ampliar o exemplo anterior, adicionando Transliteração. O pedido seguinte solicita uma tradução chinês escrita em scripts latinos.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo de resposta é:

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

O resultado de tradução agora inclui uma `transliteration` propriedade, que fornece o texto traduzido usando caracteres latinos.

### <a name="translate-multiple-pieces-of-text"></a>Traduzir várias partes de texto

A tradução de várias cadeias de caracteres de uma só vez é simplesmente uma questão de especificar uma matriz de cadeias de caracteres no corpo do pedido.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

---

O corpo de resposta é:

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

### <a name="translate-to-multiple-languages"></a>Traduzir a vários idiomas

Este exemplo mostra como traduzir a mesma entrada para vários idiomas numa solicitação.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

---

O corpo de resposta é:

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

### <a name="handle-profanity"></a>Lidar com linguagem inapropriada

Normalmente, o serviço Microsoft Translator irá reter linguagem inapropriada que está presente na origem na tradução. O grau de linguagem inapropriada e contexto que torna as palavras impróprias diferem entre culturas, e assim o grau de linguagem inapropriada no idioma de destino pode ser Sim os amplificou ou reduzido.

Se quiser evitar que a linguagem inapropriada na tradução, independentemente da presença de linguagem inapropriada em texto de origem, pode utilizar a linguagem inapropriada filtragem de opção. A opção permite-lhe escolher se pretende ver linguagem inapropriada eliminada, se pretende marcar profanities com as etiquetas adequadas (fornecendo a a opção de adicionar seu próprio pós-processamento) ou não mesmo nenhuma ação executada. Os valores aceites `ProfanityAction` estão `Deleted`, `Marked` e `NoAction` (predefinição).

<table width="100%">
  <th width="20%">ProfanityAction</th>
  <th>Ação</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Este é o comportamento padrão. Linguagem inapropriada passará de origem ao destino.<br/><br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (em inglês)</strong>: Ele é um jackass.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Palavras profanas serão removidas da saída sem substituição.<br/><br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (em inglês)</strong>: Ele é um.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>Palavras profanas são substituídas por um marcador no resultado. Depende de marcador a <code>ProfanityMarker</code> parâmetro.<br/><br/>
Para <code>ProfanityMarker=Asterisk</code>, palavras profanas são substituídas por <code>***</code>:<br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (em inglês)</strong>: Ele é um \* \* \*.<br/><br/>
Para <code>ProfanityMarker=Tag</code>, palavras profanas são cercadas por marcas XML &lt;linguagem inapropriada&gt; e &lt;/profanity&gt;:<br/>
    <strong>Origem de exemplo (japonês)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (em inglês)</strong>: Ele é um &lt;linguagem inapropriada&gt;jackass&lt;/profanity&gt;.
  </tr>
</table> 

Por exemplo:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Esta ação devolve:

```
[
    {
        "translations":[
            {"text":"Das ist eine *** gute Idee.","to":"de"}
        ]
    }
]
```

Compare com:

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'This is a fucking good idea.'}]"
```

---

Devolve a que a última solicitação:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduzir conteúdo com marcação e decidir o que é traduzido

É comum para efetuar a conversão de conteúdo que inclui a marcação, como o conteúdo a partir de uma página HTML ou conteúdo a partir de um documento XML. Incluir o parâmetro de consulta `textType=html` ao traduzir conteúdo com etiquetas. Além disso, às vezes, é útil excluir o conteúdo específico da tradução. Pode usar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer em seu idioma original. No exemplo a seguir, o conteúdo dentro da primeira `div` elemento será não ser convertido, enquanto o conteúdo na segunda `div` elemento será convertido.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Aqui está um pedido de exemplo para ilustrar.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&textType=html" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'<div class=\"notranslate\">This will not be translated.</div><div>This will be translated.</div>'}]"
```

---

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

Para receber informações de alinhamento, especifique `includeAlignment=true` na seqüência de consulta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeAlignment=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation.'}]"
```

---

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

As informações de alinhamento começa com `0:2-0:1`, que significa que os três primeiros carateres no texto de origem (`The`) o mapa para os dois primeiros caracteres no texto traduzido (`La`).

Tenha em atenção as seguintes restrições:

* Alinhamento só é devolvido para um subconjunto de pares de idiomas:
  - inglês para qualquer outra linguagem;
  - em qualquer outra linguagem para inglês, exceto o chinês simplificado, chinês tradicional e Letão para inglês;
  - do japonês de coreano ou coreano para japonês.
* Não receberá alinhamento se a frase é uma tradução em. Exemplo de uma tradução em é "Este é um teste", "Eu gosto do" e outras frases de elevada frequência.

### <a name="obtain-sentence-boundaries"></a>Obter os limites de frase

Para receber informações sobre o tamanho da sentença no texto de origem e o texto traduzido, especifique `includeSentenceLength=true` na seqüência de consulta.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=fr&includeSentenceLength=true" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The answer lies in machine translation. The best machine translation technology cannot always provide translations tailored to a site or users like a human. Simply copy and paste a code snippet anywhere.'}]"
```

---

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

Se já sabe a tradução que pretende aplicar a uma palavra ou frase, pode fornecer como marcação dentro do pedido. O dicionário dinâmico só é seguro para substantivos compostos, como nomes próprios e nomes de produtos.

A marcação para fornecer utiliza a seguinte sintaxe.

``` 
<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>
```

Por exemplo, considere a sentença em inglês "wordomatic o word é uma entrada de dicionário." Para preservar a palavra _wordomatic_ na tradução, enviar o pedido:

```
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.'}]"
```

O resultado é:

```
[
    {
        "translations":[
            {"text":"Das Wort "wordomatic" ist ein Wörterbucheintrag.","to":"de"}
        ]
    }
]
```

Esta funcionalidade funciona da mesma forma com `textType=text` ou com `textType=html`. O recurso deve ser usado com moderação. A forma adequada e muito melhor de personalização de tradução é com o Translator personalizado. Tradutor personalizado utiliza completa de contexto e probabilidades de estatísticas. Se tiver ou pode pagar criar dados de treinamento que mostra a sua empresa ou frase no contexto, obtém resultados muito melhores. [Saiba mais sobre o Translator personalizado](../customization.md).
 





