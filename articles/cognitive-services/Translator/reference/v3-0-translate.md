---
title: Método de Tradução de Tradutor
titleSuffix: Azure Cognitive Services
description: Compreenda os parâmetros, cabeçalhos e mensagens corporais para o método de Tradução do Tradutor de Serviços Cognitivos Azure para traduzir texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 59e064dc2b9d33bda966eb50544c8383b0394dd3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89566607"
---
# <a name="translator-30-translate"></a>Tradutor 3.0: Traduzir

Traduz texto.

## <a name="request-url"></a>URL do Pedido

Envie um `POST` pedido para:

```HTTP
https://api.cognitive.microsofttranslator.com/translate?api-version=3.0
```

## <a name="request-parameters"></a>Parâmetros de pedido

Os parâmetros de pedido passados na cadeia de consulta são:

### <a name="required-parameters"></a>Parâmetros necessários

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>api-version</td>
    <td><em>Parâmetro requerido</em>.<br/>Versão da API solicitada pelo cliente. O valor deve <code>3.0</code> ser.</td>
  </tr>
  <tr>
    <td>para</td>
    <td><em>Parâmetro requerido</em>.<br/>Especifica a linguagem do texto de saída. A língua-alvo deve ser uma das <a href="./v3-0-languages.md">línguas apoiadas incluídas</a> no <code>translation</code> âmbito. Por exemplo, use <code>to=de</code> para traduzir para alemão.<br/>É possível traduzir para várias línguas simultaneamente repetindo o parâmetro na cadeia de consulta. Por exemplo, use <code>to=de&to=it</code> para traduzir para alemão e italiano.</td>
  </tr>
</table>

### <a name="optional-parameters"></a>Parâmetros opcionais

<table width="100%">
  <th width="20%">Parâmetro de consulta</th>
  <th>Descrição</th>
  <tr>
    <td>De</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica a linguagem do texto de entrada. Descubra quais línguas estão disponíveis para traduzir a partir da procura de <a href="./v3-0-languages.md">idiomas suportados</a> usando o <code>translation</code> âmbito. Se o <code>from</code> parâmetro não for especificado, a deteção automática da linguagem é aplicada para determinar a língua de origem. <br/><br/>Deve utilizar o <code>from</code> parâmetro em vez de autodetecção quando utilizar a função dinâmica do <a href="https://docs.microsoft.com/azure/cognitive-services/translator/dynamic-dictionary">dicionário.</a></td>
  </tr>  
  <tr>
    <td>textoType</td>
    <td><em>Parâmetro opcional.</em><br/>Define se o texto que está a ser traduzido é texto simples ou texto HTML. Qualquer HTML tem de ser um elemento completo e bem formado. Os valores possíveis são: <code>plain</code> (padrão) ou <code>html</code>.</td>
  </tr>
  <tr>
    <td>categoria</td>
    <td><em>Parâmetro opcional.</em><br/>Uma cadeia especificando a categoria (domínio) da tradução. Este parâmetro é utilizado para obter traduções de um sistema personalizado construído com <a href="../customization.md">Tradutor Personalizado.</a> Adicione o ID de categoria dos detalhes do seu projeto De <a href="https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/how-to-create-project#view-project-details">Tradutor</a> Personalizado a este parâmetro para utilizar o seu sistema personalizado implantado. O valor predefinido é: <code>general</code> .</td>
  </tr>
  <tr>
    <td>profanidadeAcção</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica como as profanidades devem ser tratadas nas traduções. Os valores possíveis são: <code>NoAction</code> (padrão), <code>Marked</code> ou <code>Deleted</code> . Para compreender formas de tratar a profanação, consulte <a href="#handle-profanity">o tratamento da Profanação.</a></td>
  </tr>
  <tr>
    <td>profanidadeMarker</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica como as profanidades devem ser marcadas nas traduções. Os valores possíveis são: <code>Asterisk</code> (padrão) ou <code>Tag</code> . Para compreender formas de tratar a profanação, consulte <a href="#handle-profanity">o tratamento da Profanação.</a></td>
  </tr>
  <tr>
    <td>incluirAlignment</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica se deve incluir a projeção de alinhamento do texto de origem para o texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (padrão). </td>
  </tr>
  <tr>
    <td>incluiSentenceLength</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica se deve incluir limites de frase para o texto de entrada e o texto traduzido. Os valores possíveis são: <code>true</code> ou <code>false</code> (padrão).</td>
  </tr>
  <tr>
    <td>sugeriuFrom</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica uma linguagem de recuo se a linguagem do texto de entrada não puder ser identificada. A deteção automática da linguagem é aplicada quando o <code>from</code> parâmetro é omitido. Se a deteção falhar, a <code>suggestedFrom</code> linguagem será assumida.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica o script do texto de entrada.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica o script do texto traduzido.</td>
  </tr>
  <tr>
    <td>permitir o Retorno</td>
    <td><em>Parâmetro opcional.</em><br/>Especifica que o serviço é permitido recorrer a um sistema geral quando um sistema personalizado não existe. Os valores possíveis são: <code>true</code> (padrão) ou <code>false</code> .<br/><br/><code>allowFallback=false</code> especifica que a tradução deve utilizar apenas sistemas treinados para o <code>category</code> especificado pelo pedido. Se uma tradução para a linguagem X para a língua Y requer acorrentar através de uma linguagem pivô E, então todos os sistemas da cadeia (X->E e E->Y) terão de ser personalizados e ter a mesma categoria. Se não for encontrado nenhum sistema com a categoria específica, o pedido devolverá um código de estado de 400. <code>allowFallback=true</code> especifica que o serviço é autorizado a recorrer a um sistema geral quando um sistema personalizado não existe.
</td>
  </tr>
</table> 

Os cabeçalhos de pedido incluem:

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
  <tr>
    <td>Cabeçalho de autenticação</td>
    <td><em>Cabeçalho de pedido necessário</em>.<br/>Consulte <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">as opções disponíveis para autenticação.</a></td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Cabeçalho de pedido necessário</em>.<br/>Especifica o tipo de conteúdo da carga útil.<br/> O valor aceite <code>application/json; charset=UTF-8</code> é.</td>
  </tr>
  <tr>
    <td>Comprimento do conteúdo</td>
    <td><em>Cabeçalho de pedido necessário</em>.<br/>O comprimento do corpo do pedido.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Opcional.</em><br/>Um GUID gerado pelo cliente para identificar exclusivamente o pedido. Pode omitir este cabeçalho se incluir o ID de traço na cadeia de consulta utilizando um parâmetro de consulta denominado <code>ClientTraceId</code> .</td>
  </tr>
</table> 

## <a name="request-body"></a>Corpo do pedido

O corpo do pedido é uma matriz JSON. Cada elemento de matriz é um objeto JSON com uma propriedade de corda chamada `Text` , que representa a corda para traduzir.

```json
[
    {"Text":"I would really like to drive your car around the block a few times."}
]
```

Aplicam-se as seguintes limitações:

* A matriz pode ter no máximo 100 elementos.
* Todo o texto incluído no pedido não pode exceder 10.000 caracteres, incluindo espaços.

## <a name="response-body"></a>Corpo da resposta

Uma resposta bem sucedida é uma matriz JSON com um resultado para cada cadeia na matriz de entrada. Um objeto de resultado inclui as seguintes propriedades:

  * `detectedLanguage`: Um objeto que descreve a linguagem detetada através das seguintes propriedades:

      * `language`: Uma cadeia que representa o código da língua detetada.

      * `score`: Um valor flutuante que indique a confiança no resultado. A pontuação é entre zero e um e uma pontuação baixa indica uma baixa confiança.

    A `detectedLanguage` propriedade só está presente no objeto de resultado quando é solicitada a deteção automática da linguagem.

  * `translations`: Uma série de resultados de tradução. O tamanho da matriz corresponde ao número de línguas-alvo especificados através do `to` parâmetro de consulta. Cada elemento da matriz inclui:

    * `to`: Uma cadeia que representa o código linguístico da língua-alvo.

    * `text`: Uma corda que dá o texto traduzido.

    * `transliteration`: Um objeto que dá o texto traduzido no roteiro especificado pelo `toScript` parâmetro.

      * `script`: Uma cadeia especificando o script do alvo.   

      * `text`: Uma cadeia que dá o texto traduzido no script-alvo.

    O `transliteration` objeto não está incluído se a transliteração não ocorrer.

    * `alignment`: Um objeto com uma única propriedade de corda chamada `proj` , que mapeia texto de entrada para texto traduzido. A informação de alinhamento só é fornecida quando o parâmetro de pedido `includeAlignment` é `true` . O alinhamento é devolvido como um valor de cadeia do seguinte formato: `[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]]` .  O cólon separa o índice de início e fim, o traço separa as línguas, e o espaço separa as palavras. Uma palavra pode alinhar-se com zero, uma ou múltiplas palavras na outra língua, e as palavras alinhadas podem ser não contíguas. Quando não houver informação de alinhamento disponível, o elemento de alinhamento estará vazio. Consulte [obter informações de alinhamento](#obtain-alignment-information) para um exemplo e restrições.

    * `sentLen`: Um objeto que devolve limites de frase nos textos de entrada e saída.

      * `srcSentLen`: Uma matriz inteiro que representa os comprimentos das frases no texto de entrada. O comprimento da matriz é o número de frases, e os valores são o comprimento de cada frase.

      * `transSentLen`: Uma matriz inteiro que representa os comprimentos das frases no texto traduzido. O comprimento da matriz é o número de frases, e os valores são o comprimento de cada frase.

    Os limites da frase só são incluídos quando o parâmetro de pedido `includeSentenceLength` é `true` .

  * `sourceText`: Um objeto com uma única propriedade de corda denominada `text` , que dá o texto de entrada no script padrão do idioma de origem. `sourceText` a propriedade só está presente quando a entrada é expressa num script que não é o script habitual para a língua. Por exemplo, se a entrada fosse em árabe escrita em alfabeto latino, então `sourceText.text` seria o mesmo texto árabe convertido em script árabe.

Exemplo das respostas do JSON são fornecidas na secção [de exemplos.](#examples)

## <a name="response-headers"></a>Cabeçalhos de resposta

<table width="100%">
  <th width="20%">Cabeçalhos</th>
  <th>Descrição</th>
    <tr>
    <td>X-RequestId</td>
    <td>Valor gerado pelo serviço para identificar o pedido. É utilizado para resolução de problemas.</td>
  </tr>
  <tr>
    <td>X-MT-Sistema</td>
    <td>Especifica o tipo de sistema que foi utilizado para a tradução de cada idioma 'para' solicitado para tradução. O valor é uma lista de cordas separada por vírgulas. Cada corda indica um tipo:<br/><ul><li>Personalizado - O pedido inclui um sistema personalizado e pelo menos um sistema personalizado foi utilizado durante a tradução.</li><li>Equipa - Todos os outros pedidos</li></td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Códigos de estado de resposta

Seguem-se os possíveis códigos de estado HTTP que um pedido devolve. 

<table width="100%">
  <th width="20%">Código de Estado</th>
  <th>Descrição</th>
  <tr>
    <td>200</td>
    <td>Com êxito.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Um dos parâmetros de consulta está em falta ou não é válido. Corretos parâmetros de pedido antes de voltar a tentar.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>O pedido não pôde ser autenticado. Verifique se as credenciais são especificadas e válidas.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>O pedido não está autorizado. Verifique a mensagem de erro dos detalhes. Isto indica frequentemente que todas as traduções gratuitas fornecidas com uma subscrição experimental foram utilizadas.</td>
  </tr>
  <tr>
    <td>408</td>
    <td>O pedido não pôde ser satisfeito porque falta um recurso. Verifique a mensagem de erro dos detalhes. Ao utilizar um <code>category</code> personalizado, isto indica frequentemente que o sistema de tradução personalizado ainda não está disponível para atender pedidos. O pedido deve ser novamente julgado após um período de espera (por exemplo, 1 minuto).</td>
  </tr>
  <tr>
    <td>429</td>
    <td>O servidor rejeitou o pedido porque o cliente excedeu os limites de pedido.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Ocorreu um erro inesperado. Se o erro persistir, informe-o com: data e hora da falha, solicite o identificador do cabeçalho de resposta <code>X-RequestId</code> e o identificador do cliente do cabeçalho do pedido <code>X-ClientTraceId</code> .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Servidor temporariamente indisponível. Recandidutar o pedido. Se o erro persistir, informe-o com: data e hora da falha, solicite o identificador do cabeçalho de resposta <code>X-RequestId</code> e o identificador do cliente do cabeçalho do pedido <code>X-ClientTraceId</code> .</td>
  </tr>
</table> 

Se ocorrer um erro, o pedido também devolverá uma resposta de erro JSON. O código de erro é um número de 6 dígitos que combina o código de estado HTTP de 3 dígitos seguido de um número de 3 dígitos para categorizar ainda mais o erro. Os códigos de erro comuns podem ser encontrados na [página de referência do Tradutor V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors). 

## <a name="examples"></a>Exemplos

### <a name="translate-a-single-input"></a>Traduzir uma única entrada

Este exemplo mostra como traduzir uma única frase do inglês para o chinês simplificado.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

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

A `translations` matriz inclui um elemento, que fornece a tradução da única peça de texto na entrada.

### <a name="translate-a-single-input-with-language-auto-detection"></a>Traduza uma única entrada com a deteção automática da linguagem

Este exemplo mostra como traduzir uma única frase do inglês para o chinês simplificado. O pedido não especifica o idioma de entrada. Em vez disso, é utilizada a deteção automática do idioma de origem.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

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
A resposta é semelhante à resposta do exemplo anterior. Uma vez que a deteção automática da língua foi solicitada, a resposta também inclui informações sobre a linguagem detetada para o texto de entrada. A deteção automática do idioma funciona melhor com texto de entrada mais longo.

### <a name="translate-with-transliteration"></a>Traduzir com transliteração

Vamos estender o exemplo anterior adicionando transliteração. O seguinte pedido pede uma tradução chinesa escrita em escrita em latim.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=zh-Hans&toScript=Latn" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

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

O resultado da tradução inclui agora uma `transliteration` propriedade, que dá o texto traduzido usando caracteres latinos.

### <a name="translate-multiple-pieces-of-text"></a>Traduzir várias peças de texto

Traduzir várias cordas ao mesmo tempo é simplesmente uma questão de especificar uma variedade de cordas no corpo de pedido.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}, {'Text':'I am fine, thank you.'}]"
```

A resposta contém a tradução de todas as peças de texto na mesma ordem que no pedido.
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

### <a name="translate-to-multiple-languages"></a>Traduzir para várias línguas

Este exemplo mostra como traduzir a mesma entrada para vários idiomas num único pedido.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=zh-Hans&to=de" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'Hello, what is your name?'}]"
```

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

### <a name="handle-profanity"></a>Lidar com a profanação

Normalmente, o serviço de Tradutor manterá a profanação presente na fonte da tradução. O grau de profanação e o contexto que faz as palavras profanas diferem entre culturas, e como resultado o grau de profanação na linguagem-alvo pode ser amplificado ou reduzido.

Se quiser evitar obter profanação na tradução, independentemente da presença de profanação no texto de origem, pode utilizar a opção de filtragem de profanidades. A opção permite-lhe escolher se pretende ver profanação apagada, se pretende marcar profanidades com etiquetas apropriadas (dando-lhe a opção de adicionar o seu próprio pós-processamento), ou se não quer nenhuma ação tomada. Os valores aceites `ProfanityAction` são `Deleted` , e `Marked` `NoAction` (padrão).

<table width="100%">
  <th width="20%">ProfanidadeAcção</th>
  <th>Ação</th>
  <tr>
    <td><code>NoAction</code></td>
    <td>Este é o comportamento padrão. Profanidade passará de fonte para alvo.<br/><br/>
    <strong>Exemplo Fonte (Japonesa)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: É um idiota.
    </td>
  </tr>
  <tr>
    <td><code>Deleted</code></td>
    <td>Palavras profanas serão removidas da saída sem substituição.<br/><br/>
    <strong>Exemplo Fonte (Japonesa)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: Ele é um.
    </td>
  </tr>
  <tr>
    <td><code>Marked</code></td>
    <td>As palavras profanas são substituídas por um marcador na saída. O marcador depende do <code>ProfanityMarker</code> parâmetro.<br/><br/>
Para <code>ProfanityMarker=Asterisk</code> , palavras profanas são substituídas <code>***</code> por:<br/>
    <strong>Exemplo Fonte (Japonesa)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: Ele é um \* \* \* .<br/><br/>
Para <code>ProfanityMarker=Tag</code> , palavras profanas são rodeadas por &lt; profanidade &gt; &lt; e/profanação de etiquetas &gt; XML:<br/>
    <strong>Exemplo Fonte (Japonesa)</strong>: 彼はジャッカスです。<br/>
    <strong>Tradução de exemplo (inglês)</strong>: É um &lt; &gt; idiota de &lt; profanação/profanação. &gt;
  </tr>
</table> 

Por exemplo:

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```
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

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de&profanityAction=Marked&profanityMarker=Tag" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json; charset=UTF-8" -d "[{'Text':'This is a freaking good idea.'}]"
```

O último pedido devolve:

```
[
    {
        "translations":[
            {"text":"Das ist eine <profanity>verdammt</profanity> gute Idee.","to":"de"}
        ]
    }
]
```

### <a name="translate-content-with-markup-and-decide-whats-translated"></a>Traduza o conteúdo com marcação e decida o que é traduzido

É comum traduzir conteúdo que inclui marcação, como conteúdo de uma página HTML ou conteúdo de um documento XML. Incluir o parâmetro de consulta `textType=html` ao traduzir o conteúdo com etiquetas. Além disso, às vezes é útil excluir conteúdo específico da tradução. Pode utilizar o atributo `class=notranslate` para especificar o conteúdo que deve permanecer na sua língua original. No exemplo seguinte, o conteúdo dentro do primeiro `div` elemento não será traduzido, enquanto o conteúdo do segundo `div` elemento será traduzido.

```
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

Aqui está um pedido de amostra para ilustrar.

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

O alinhamento é devolvido como um valor de cadeia do seguinte formato para cada palavra da fonte. A informação para cada palavra é separada por um espaço, incluindo para línguas não separadas do espaço (scripts) como o chinês:

[[SourceTextStartIndex]:[SourceTextEndIndex]-[TgtTextStartIndex]:[TgtTextEndIndex]] *

Corda de alinhamento de exemplo: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Por outras palavras, o cólon separa o índice de início e fim, o traço separa as línguas, e o espaço separa as palavras. Uma palavra pode alinhar-se com zero, uma ou múltiplas palavras na outra língua, e as palavras alinhadas podem ser não contíguas. Quando não houver informação de alinhamento disponível, o elemento alinhamento estará vazio. O método não retorna a nenhum erro nesse caso.

Para receber informações de alinhamento, especifique `includeAlignment=true` na cadeia de consulta.

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

A informação de alinhamento começa com `0:2-0:1` , o que significa que os três primeiros caracteres no texto de origem ( ) `The` mapeam para os dois primeiros caracteres no texto traduzido ( `La` ).

#### <a name="limitations"></a>Limitações
Obter informações de alinhamento é uma característica experimental que permitimos para prototipagem de pesquisa e experiências com potenciais mapeamentos de frases. Podemos optar por deixar de apoiar isto no futuro. Estas são algumas das notáveis restrições em que os alinhamentos não são suportados:

* O alinhamento não está disponível para texto em formato HTML i.e., textType=html
* O alinhamento só é devolvido para um subconjunto dos pares linguísticos:
  - Inglês de/para qualquer outra língua, exceto chinês tradicional, cantonês (tradicional) ou sérvio (cirílico).
  - de japonês a coreano ou de coreano a japonês.
  - do japonês ao chinês simplificado e chinês simplificado para japonês. 
  - do chinês simplificado ao tradicional chinês tradicional e chinês tradicional ao chinês simplificado. 
* Não receberá alinhamento se a frase for uma tradução enlatada. O exemplo de uma tradução enlatada é "Isto é um teste", "Amo-te" e outras frases de alta frequência.
* O alinhamento não está disponível quando aplica qualquer uma das abordagens para impedir a tradução, como descrito [aqui](../prevent-translation.md)

### <a name="obtain-sentence-boundaries"></a>Obtenha limites de sentença

Para receber informações sobre a duração da frase no texto de origem e texto traduzido, especifique `includeSentenceLength=true` na cadeia de consulta.

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

Se já sabe a tradução que pretende aplicar a uma palavra ou frase, pode fornecê-la como marcação dentro do pedido. O dicionário dinâmico só é seguro para substantivos adequados, tais como nomes pessoais e nomes de produtos.

A marcação a fornecer utiliza a seguinte sintaxe.

``` 
<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>
```

Por exemplo, considere a frase inglesa "A palavra palavra palavra palavramática é uma entrada no dicionário." Para preservar a palavra _palavra palavra-palavramática_ na tradução, envie o pedido:

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

Esta característica funciona da mesma forma com `textType=text` ou com `textType=html` . . A função deve ser utilizada com moderação. A forma adequada e muito melhor de personalizar a tradução é utilizando o Tradutor Personalizado. O Tradutor Personalizado faz uso total do contexto e das probabilidades estatísticas. Se tiver ou puder dar-se ao luxo de criar dados de formação que mostram o seu trabalho ou frase em contexto, obtém resultados muito melhores. [Saiba mais sobre o Tradutor Personalizado.](../customization.md)
