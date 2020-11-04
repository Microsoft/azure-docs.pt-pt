---
title: Linguagem de marcação de síntese de fala (SSML) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Utilizando a Linguagem de Marcação de Síntese de Discurso para controlar a pronúncia e a prosódica em texto-a-discurso.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e0625fd257ed9995fb567785ce07dcb0b0422c61
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311634"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Melhorar a síntese com a linguagem de marcação da síntese de fala (SSML)

A Linguagem de Marcação de Síntese de Fala (SSML) é uma linguagem de marcação baseada em XML que permite que os desenvolvedores especifiquem como o texto de entrada é convertido em discurso sintetizado usando o serviço de texto-a-fala. Em comparação com o texto simples, o SSML permite que os desenvolvedores afinem o tom, a pronúncia, a taxa de fala, o volume e mais da saída texto-a-discurso. Pontuação normal, como fazer uma pausa após um período, ou usar a entoação correta quando uma frase termina com um ponto de interrogação são automaticamente manuseados.

A implementação do serviço de voz do SSML baseia-se na [versão 1.0](https://www.w3.org/TR/speech-synthesis)da Síntese de Síntese de Discurso do World Wide Web Consortium.

> [!IMPORTANT]
> Os caracteres chineses, japoneses e coreanos contam como dois caracteres para a faturação. Para mais informações, consulte [a Fixação de Preços.](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)

## <a name="standard-neural-and-custom-voices"></a>Vozes padrão, neurais e personalizadas

Escolha entre vozes padrão e neurais ou crie a sua própria voz personalizada única ao seu produto ou marca. Mais de 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, e 5 vozes neurais estão disponíveis em quatro línguas e locais. Para obter uma lista completa de línguas, locais e vozes apoiadas (neurais e padrão), consulte [o suporte linguístico.](language-support.md)

Para saber mais sobre vozes padrão, neurais e personalizadas, consulte [a visão geral do texto para a fala](text-to-speech.md).

## <a name="special-characters"></a>Carateres especiais

Ao utilizar o SSML, lembre-se de que caracteres especiais, tais como aspas, apóstrofos e suportes, devem ser escapados. Para obter mais informações, consulte [a linguagem de marcação extensível (XML) 1.0: Apêndice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementos SSML suportados

Cada documento SSML é criado com elementos SSML (ou tags). Estes elementos são usados para ajustar o tom, a prosódia, o volume e muito mais. As secções seguintes detalham como cada elemento é utilizado e quando um elemento é necessário ou opcional.  

> [!IMPORTANT]
> Não se esqueça de usar cotações duplas em torno dos valores de atributos. Normas para XML bem formado e válido requer valores de atributos a serem incluídos em marcas duplas de aspas. Por exemplo, `<prosody volume="90">` é um elemento bem formado e válido, mas não `<prosody volume=90>` é. A SSML pode não reconhecer valores de atributos que não estejam em cotações.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak` é o elemento raiz, e é **necessário** para todos os documentos SSML. O `speak` elemento contém informações importantes, tais como versão, linguagem e a definição de vocabulário de marcação.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `version` | Indica a versão da especificação SSML utilizada para interpretar a marcação do documento. A versão atual é 1.0. | Necessário |
| `xml:lang` | Especifica a linguagem do documento raiz. O valor pode conter um código linguístico de duas letras (por exemplo), `en` ou o código linguístico e o país/região de maiúsculas (por exemplo, `en-US` ). | Necessário |
| `xmlns` | Especifica o URI ao documento que define o vocabulário de marcação (os tipos de elementos e os nomes de atributos) do documento SSML. O URI atual http://www.w3.org/2001/10/synthesis é. | Necessário |

## <a name="choose-a-voice-for-text-to-speech"></a>Escolha uma voz para texto-a-discurso

O `voice` elemento é necessário. É utilizado para especificar a voz que é usada para texto-a-voz.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz utilizada para a saída texto-a-voz. Para obter uma lista completa de vozes apoiadas, consulte [o suporte linguístico](language-support.md#text-to-speech). | Necessário |

**Exemplo**

> [!NOTE]
> Este exemplo usa a `en-US-AriaRUS` voz. Para obter uma lista completa de vozes apoiadas, consulte [o suporte linguístico](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Use várias vozes

Dentro do `speak` elemento, pode especificar várias vozes para a saída texto-a-voz. Estas vozes podem estar em línguas diferentes. Para cada voz, o texto deve ser embrulhado num `voice` elemento. 

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz utilizada para a saída texto-a-voz. Para obter uma lista completa de vozes apoiadas, consulte [o suporte linguístico](language-support.md#text-to-speech). | Necessário |

> [!IMPORTANT]
> Várias vozes são incompatíveis com a função de limite da palavra. A função de limite da palavra precisa de ser desativada para utilizar várias vozes.

### <a name="disable-word-boundary"></a>Desativar o limite da palavra

Dependendo da linguagem SDK do discurso, você definirá `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` a propriedade em um caso do `false` `SpeechConfig` objeto.

# <a name="c"></a>[C#](#tab/csharp)

Para obter mais informações, veja <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Para obter mais informações, veja <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Para obter mais informações, veja <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Para obter mais informações, veja <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para obter mais informações, veja <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest&preserve-view=true#setproperty-string--string-" target="_blank"> `setProperty` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Para obter mais informações, veja <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Para obter mais informações, veja <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Aria!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajuste os estilos de fala

> [!IMPORTANT]
> O ajuste dos estilos de fala só funcionará com vozes neurais.

Por predefinição, o serviço de texto-a-fala sintetiza o texto utilizando um estilo de fala neutro tanto para vozes padrão como neurais. Com vozes neurais, pode ajustar o estilo de fala para expressar diferentes emoções como alegria, empatia e calma, ou otimizar a voz para diferentes cenários como atendimento ao cliente, noticiário e assistente de voz, usando o `mstts:express-as` elemento. Este é um elemento opcional exclusivo do serviço Discurso.

Atualmente, os ajustes de estilo de fala são suportados para estas vozes neurais:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`

As alterações são aplicadas ao nível da frase, e os estilos variam por voz. Se um estilo não for suportado, o serviço devolverá o discurso no estilo de fala neutro padrão. Pode consultar os estilos suportados por cada voz através da [lista de voz API](rest-text-to-speech.md#get-a-list-of-voices).

Para a voz chinesa XiaoxiaoNeural, a intensidade do estilo de fala pode ser alterada para melhor se adaptar ao seu caso de uso. Pode especificar um estilo mais forte ou mais suave para `styledegree` tornar o discurso mais expressivo ou moderado.

**Syntax**

```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
> [!NOTE]
> Neste momento, `styledegree` só apoia a XiaoxiaoNeural. 

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `style` | Especifica o estilo de falar. Atualmente, os estilos de fala são específicos da voz. | Necessário se ajustar o estilo de fala para uma voz neural. Se `mstts:express-as` utilizar, deve ser fornecido o estilo. Se for fornecido um valor inválido, este elemento será ignorado. |
| `styledegree` | Especifica a intensidade do estilo de fala. **Valores aceites** : 0,01 a 2 inclusive. O valor predefinido é 1, o que significa a intensidade de estilo predefinida. A unidade mínima é de 0,01, o que resulta numa ligeira tendência para o estilo alvo. Um valor de 2 resulta numa duplicação da intensidade do estilo padrão.  | Opcional (Neste momento, `styledegree` apenas suporta XiaoxiaoNeural.)|

Utilize esta tabela para determinar quais os estilos de fala suportados por cada voz neural.

| Voz                   | Estilo                     | Description                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Expressa um tom formal, confiante e autoritário para a entrega de notícias |
|                         | `style="newscast-casual"` | Expressa um tom versátil e casual para a entrega de notícias gerais        |
|                         | `style="customerservice"` | Expressa um tom amigável e útil para o apoio ao cliente  |
|                         | `style="chat"`            | Expressa um tom casual e descontraído                         |
|                         | `style="cheerful"`        | Expressa um tom positivo e feliz                         |
|                         | `style="empathetic"`      | Expressa uma sensação de carinho e compreensão               |
| `en-US-JennyNeural`     | `style="customerservice"` | Expressa um tom amigável e útil para o apoio ao cliente  |
|                         | `style="chat"`            | Expressa um tom casual e descontraído                         |
|                         | `style="assistant"`       | Expressa um tom caloroso e descontraído para assistentes digitais    |
|                         | `style="newscast"`        | Expressa um tom versátil e casual para a entrega de notícias gerais   |
| `en-US-GuyNeural`       | `style="newscast"`        | Expressa um tom formal e profissional para narrar notícias |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Expressa um tom formal e profissional para narrar notícias |
|                         | `style="customerservice"` | Expressa um tom amigável e útil para o apoio ao cliente  |
|                         | `style="assistant"`       | Expressa um tom caloroso e descontraído para assistentes digitais    |
|                         | `style="chat"`            | Expressa um tom casual e descontraído para chit-chat           |
|                         | `style="calm"`            | Expressa uma atitude fria, recolhida e composta ao falar. Tom, tom, prosódia é muito mais uniforme comparado com outros tipos de fala.                                |
|                         | `style="cheerful"`        | Expressa um tom animado e entusiasta, com maior tom e energia vocal                         |
|                         | `style="sad"`             | Expressa um tom triste, com tom mais alto, menos intensidade e menor energia vocal. Indicadores comuns desta emoção seriam choros ou choros durante a fala.            |
|                         | `style="angry"`           | Expressa um tom irritado e irritado, com tom mais baixo, maior intensidade e maior energia vocal. O orador está em estado de irado, desagradado e ofendido.       |
|                         | `style="fearful"`         | Expressa um tom assustado e nervoso, com tom mais alto, energia vocal mais alta e taxa mais rápida. O orador está num estado de tensão e inquietação.                          |
|                         | `style="disgruntled"`     | Expressa um tom desdenhoso e queixoso. O discurso desta emoção mostra descontentamento e desprezo.              |
|                         | `style="serious"`         | Expressa um tom rigoroso e comandado. O orador soa frequentemente mais duro e muito menos relaxado com cadência firme.          |
|                         | `style="affectionate"`    | Expressa um tom caloroso e afetuoso, com maior tom e energia vocal. O orador está em estado de chamar a atenção do ouvinte. A "personalidade" do orador é muitas vezes cativante na natureza.          |     
|                         | `style="gentle"`          | Expressa um tom suave, educado e agradável, com menor tom e energia vocal         |   
|                         | `style="lyrical"`         | Expressa emoções de forma melódica e sentimental         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Expressa um tom amigável e útil para o apoio ao cliente  | 

**Exemplo**

Este snippet SSML ilustra como o `<mstts:express-as>` elemento é usado para mudar o estilo de fala para `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Este snippet SSML ilustra como o `styledegree` atributo é usado para alterar a intensidade do estilo de fala para XiaoxiaoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicione ou remova uma pausa/pausa

Utilize o `break` elemento para inserir pausas (ou quebras) entre palavras ou evitar pausas automaticamente adicionadas pelo serviço de texto-a-voz.

> [!NOTE]
> Utilize este elemento para anular o comportamento padrão de texto-a-voz (TTS) para uma palavra ou frase se o discurso sintetizado para essa palavra ou frase não soar natural. Configurado `strength` para evitar uma rutura `none` prosódica, que é automaticamente inserida pelo serviço text-to-speech.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `strength` | Especifica a duração relativa de uma pausa utilizando um dos seguintes valores:<ul><li>nenhum</li><li>x-fraco</li><li>fraco</li><li>meio (padrão)</li><li>forte</li><li>x-forte</li></ul> | Opcional |
| `time` | Especifica a duração absoluta de uma pausa em segundos ou milissegundos. Exemplos de valores válidos são `2s` e `500` | Opcional |

| Força                      | Description |
|-------------------------------|-------------|
| Nenhum, ou se nenhum valor fornecido | 0 ms        |
| x-fraco                        | 250 ms      |
| fraco                          | 500 ms      |
| médio                        | 750 ms      |
| forte                        | 1000 ms     |
| x-forte                      | 1250 ms     |

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especificar parágrafos e frases

`p` e `s` os elementos são usados para denotar parágrafos e frases, respectivamente. Na ausência destes elementos, o serviço de texto-a-fala determina automaticamente a estrutura do documento SSML.

O `p` elemento pode conter texto e os seguintes elementos: , , , `audio` , , , , , , e `break` `phoneme` `prosody` `say-as` `sub` `mstts:express-as` `s` .

O `s` elemento pode conter texto e os seguintes elementos: , , `audio` , , , , , e `break` `phoneme` `prosody` `say-as` `mstts:express-as` `sub` .

**Syntax**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Use fonmes para melhorar a pronúncia

O `ph` elemento é utilizado para pronúncia fonética em documentos SSML. O `ph` elemento só pode conter texto, nenhum outro elemento. Sempre forneça um discurso legível humano como um recuo.

Os alfabetos fonéticos são compostos por telefones, que são compostos por letras, números ou caracteres, às vezes em combinação. Cada telefone descreve um som único de fala. Isto contrasta com o alfabeto latino, onde qualquer letra pode representar múltiplos sons falados. Considere as diferentes pronúncias da letra "c" nas palavras "doces" e "cessar", ou as diferentes pronúncias da letra combinada "th" nas palavras "coisa" e "aquelas".

**Syntax**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `alphabet` | Especifica o alfabeto fonético para usar ao sintetizar a pronúncia da corda no `ph` atributo. A cadeia que especifica o alfabeto deve ser especificada em letras minúsculas. Seguem-se os possíveis alfabetos que pode especificar.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto fonético internacional <span class="docon docon-navigate-external x-hidden-focus"></span> </a></li><li>`sapi`&ndash; [Alfabeto fonético de serviço de fala](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Conjunto de telefone universal</a></li></ul><br>O alfabeto aplica-se apenas ao `phoneme` elemento. | Opcional |
| `ph` | Uma cadeia contendo telefones que especificam a pronúncia da palavra no `phoneme` elemento. Se a cadeia especificada contiver telefones não reconhecidos, o serviço text-to-speech (TTS) rejeita todo o documento SSML e não produz nenhuma das saídas de fala especificadas no documento. | Necessário se utilizar fones de telefone. |

**Exemplos**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Use léxico personalizado para melhorar a pronúncia

Por vezes, o serviço de texto-a-fala não consegue pronunciar uma palavra com precisão. Por exemplo, o nome de uma empresa, ou um termo médico. Os desenvolvedores podem definir como as entidades únicas são lidas em SSML usando as `phoneme` etiquetas e `sub` etiquetas. No entanto, se precisar de definir como várias entidades são lidas, pode criar um léxico personalizado usando a `lexicon` etiqueta.

> [!NOTE]
> O léxico personalizado suporta atualmente a codificação UTF-8. 

**Syntax**

```XML
<lexicon uri="string"/>
```

**Atributos**

| Atributo | Descrição                               | Obrigatório / Opcional |
|-----------|-------------------------------------------|---------------------|
| `uri`     | O endereço do documento pls externo. | Obrigatório.           |

**Utilização**

Para definir como várias entidades são lidas, pode criar um léxico personalizado, que é armazenado como um ficheiro .xml ou .pls. Segue-se uma amostra .xml.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

O `lexicon` elemento contém pelo menos um `lexeme` elemento. Cada `lexeme` elemento contém pelo menos um elemento e um ou `grapheme` `grapheme` `alias` mais, e `phoneme` elementos. O `grapheme` elemento contém texto que descreve a <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">orthografia. <span class="docon docon-navigate-external x-hidden-focus"></span> </a> Os `alias` elementos são usados para indicar a pronúncia de um acrónimo ou um termo abreviado. O `phoneme` elemento fornece texto que descreve como o `lexeme` pronuncia-se.

É importante notar que não se pode definir diretamente a pronúncia de uma frase usando o léxico personalizado. Se precisar de definir a pronúncia para um acrónimo ou um termo abreviado, primeiro forneça um `alias` , em seguida, associe a `phoneme` isso `alias` . Por exemplo:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>ScotlandMV</alias> 
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme> 
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

Também pode fornecer diretamente o seu esperado `alias` para o acrónimo ou termo abreviado. Por exemplo:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> O `phoneme` elemento não pode conter espaços brancos durante a utilização do IPA.

Para obter mais informações sobre o ficheiro léxico personalizado, consulte [a Versão 1.0 da Especificação lexicon de Pronúncia (PLS).](https://www.w3.org/TR/pronunciation-lexicon/)

Em seguida, publique o seu ficheiro de léxico personalizado. Embora não tenhamos restrições sobre onde este ficheiro pode ser armazenado, recomendamos a utilização do [Azure Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Depois de publicar o seu léxico personalizado, pode fazê-lo referência a partir do seu SSML.

> [!NOTE]
> O `lexicon` elemento deve estar dentro do `voice` elemento.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Ao utilizar este léxico personalizado, "BTW" será lido como "By the way". "Benigni" será lido com o "bɛˈniːnji" do IPA.  

**Limitações**
- Tamanho do ficheiro: o tamanho máximo do ficheiro de léxico personalizado é de 100KB, se além deste tamanho, o pedido de síntese falhará.
- Lexicon cache refresh: o léxico personalizado será emconsecado com URI como chave no Serviço TTS quando estiver carregado pela primeira vez. O léxico com o mesmo URI não será recarregado dentro de 15 minutos, por isso a mudança de léxico personalizada precisa de esperar no máximo 15 minutos para fazer efeito.

**Conjuntos fonéticos de serviço de fala**

Na amostra acima, estamos a usar o Alfabeto Fonético Internacional, também conhecido como o conjunto de telefones IPA. Sugerimos que os desenvolvedores utilizem o IPA, porque é o padrão internacional. Para alguns caracteres IPA, eles têm a versão 'pré-comcomposta' e 'decomposta' quando estão representados com o Unicode. O léxico personalizado só suporta os unicódigos decompostos.

Tendo em conta que o IPA não é fácil de lembrar, o serviço de Discurso define um conjunto fonético para sete línguas ( `en-US` , , , , , , e `fr-FR` `de-DE` `es-ES` `ja-JP` `zh-CN` `zh-TW` .

Pode utilizar o `sapi` vale como vale para o atributo com `alphabet` léxicos personalizados, como demonstrado abaixo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Para obter mais informações sobre o alfabeto fonético detalhado do serviço de fala, consulte os [conjuntos fonéticos do serviço speech](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Ajustar a prosódia

O `prosody` elemento é utilizado para especificar alterações ao tom, contorno, intervalo, duração e volume para a saída texto-a-voz. O `prosody` elemento pode conter texto e os seguintes elementos: , , , `audio` , , , , , , e `break` `p` `phoneme` `prosody` `say-as` `sub` `s` .

Como os valores prosódicos de atributos podem variar em relação a uma ampla gama, o reconhecimento da fala interpreta os valores atribuídos como uma sugestão do que devem ser os valores prosódicos reais da voz selecionada. O serviço de texto-a-fala limita ou substitui valores que não são suportados. Exemplos de valores não suportados são um pitch de 1 MHz ou um volume de 120.

**Syntax**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `pitch` | Indica o tom de base para o texto. Pode expressar o tom como:<ul><li>Um valor absoluto, expresso em número seguido de "Hz" (Hertz). Por exemplo, `<prosody pitch="600Hz">some text</prosody>`.</li><li>Um valor relativo, expresso em número precedido por "+" ou "-" e seguido por "Hz" ou "st", que especifica uma quantidade para alterar o tom. Por exemplo: `<prosody pitch="+80Hz">some text</prosody>` ou `<prosody pitch="-2st">some text</prosody>` . . O "st" indica que a unidade de mudança é semitona, que é metade de um tom (meio passo) na escala diatónica padrão.</li><li>Um valor constante:<ul><li>x-baixo</li><li>baixo</li><li>médio</li><li>alta</li><li>x-alto</li><li>predefinição</li></ul></li></ul> | Opcional |
| `contour` |O contorno suporta agora vozes neurais e padrão. O contorno representa mudanças no tom. Estas alterações são representadas como um conjunto de alvos em posições de tempo especificadas na saída da fala. Cada alvo é definido por conjuntos de pares de parâmetros. Por exemplo: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor em cada conjunto de parâmetros especifica a localização da alteração do tom em percentagem da duração do texto. O segundo valor especifica a quantidade para elevar ou baixar o tom, utilizando um valor relativo ou um valor de enumeração para o pitch (ver `pitch` ). | Opcional |
| `range` | Um valor que representa a gama de arremesso para o texto. Pode `range` expressar-se utilizando os mesmos valores absolutos, valores relativos ou valores de enumeração utilizados para descrever `pitch` . | Opcional |
| `rate` | Indica a taxa de fala do texto. Pode `rate` expressar-se como:<ul><li>Um valor relativo, expresso como um número que atua como um multiplicador do padrão. Por exemplo, um valor de *1* não resulta em nenhuma alteração na taxa. Um valor de *0,5* resulta numa redução para metade da taxa. Um valor de *3* resulta num triplicar da taxa.</li><li>Um valor constante:<ul><li>x-lento</li><li>lento</li><li>médio</li><li>rápido</li><li>x-fast</li><li>predefinição</li></ul></li></ul> | Opcional |
| `duration` | O período de tempo que deve decorrer enquanto o serviço de síntese de fala (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. A duração suporta apenas vozes padrão.| Opcional |
| `volume` | Indica o nível de volume da voz falante. Pode expressar o volume como:<ul><li>Um valor absoluto, expresso em número no intervalo de 0,0 a 100,0, do *mais silencioso* ao *mais alto*. Por exemplo, 75. O padrão é 100.0.</li><li>Um valor relativo, expresso em número precedido por "+" ou "-" que especifica um montante para alterar o volume. Por exemplo, +10 ou -5,5.</li><li>Um valor constante:<ul><li>silenciosa</li><li>x-soft</li><li>suave</li><li>médio</li><li>alto</li><li>x-alto</li><li>predefinição</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Alterar taxa de fala

A taxa de fala pode ser aplicada às vozes neurais e às vozes padrão ao nível da palavra ou da frase. 

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Alterar volume

As alterações de volume podem ser aplicadas às vozes padrão ao nível da palavra ou da frase. Enquanto as alterações de volume só podem ser aplicadas às vozes neurais ao nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Mudar o tom

As alterações de tom podem ser aplicadas às vozes padrão ao nível da palavra ou da frase. Enquanto as alterações de tom só podem ser aplicadas às vozes neurais ao nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Alterar contorno de tom

> [!IMPORTANT]
> As mudanças de contorno de tom são agora suportadas com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room? 
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>dizer-como elemento

`say-as` é um elemento opcional que indica o tipo de conteúdo (como número ou data) do texto do elemento. Isto fornece orientação para o motor da síntese da fala sobre como pronunciar o texto.

**Syntax**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `interpret-as` | Indica o tipo de conteúdo do texto do elemento. Para obter uma lista de tipos, consulte a tabela abaixo. | Necessário |
| `format` | Fornece informações adicionais sobre a formatação precisa do texto do elemento para tipos de conteúdo que podem ter formatos ambíguos. SSML define formatos para tipos de conteúdo que os utilizam (ver tabela abaixo). | Opcional |
| `detail` | Indica o nível de detalhe a ser falado. Por exemplo, este atributo pode solicitar que o motor de síntese da fala pronuncie marcas de pontuação. Não existem valores padrão definidos para `detail` . | Opcional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Seguem-se os tipos de conteúdo suportado para os `interpret-as` atributos e `format` atributos. Inclua o `format` atributo apenas se `interpret-as` estiver definido até à data e hora.

| interpretação-como | formato | Interpretação |
|--------------|--------|----------------|
| `address` | | O texto é falado como um endereço. O motor da síntese da fala pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"Estou na 150ª corte a nordeste de Redmond Washington." |
| `cardinal`, `number` | | O texto é falado como um número cardeal. O motor da síntese da fala pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Como "Há três alternativas." |
| `characters`, `spell-out` | | O texto é falado como letras individuais (escritas). O motor da síntese da fala pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Como "T E S T". |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | O texto é falado como uma data. O `format` atributo especifica o formato da data *(d=dia, m=mês e y=ano).* O motor da síntese da fala pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />"Hoje é outubro, 19.º 16.000". |
| `digits`, `number_digit` | | O texto é falado como uma sequência de dígitos individuais. O motor da síntese da fala pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Como "1 2 3 4 5 6 7 8 9." |
| `fraction` | | O texto é falado como um número fracional. O motor da síntese da fala pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Como "três oitavos de polegada". |
| `ordinal` | | O texto é falado como um número ordinal. O motor da síntese da fala pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Como "Selecione a terceira opção". |
| `telephone` | | O texto é falado como um número de telefone. O `format` atributo pode conter dígitos que representam um código de país. Por exemplo, "1" para os Estados Unidos ou "39" para a Itália. O motor de síntese da fala pode utilizar esta informação para orientar a sua pronúncia de um número de telefone. O número de telefone também pode incluir o código do país, e em caso afirmativo, tem precedência sobre o código do país no `format` . O motor da síntese da fala pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"O meu número é código de área oito oito, oito, cinco, um, dois, dois." |
| `time` | hms12, hms24 | O texto é falado como um tempo. O `format` atributo especifica se o tempo é especificado usando um relógio de 12 horas (hms12) ou um relógio de 24 horas (hms24). Use um cólon para separar números que representem horas, minutos e segundos. Seguem-se os exemplos de tempo válidos: 12:35, 1:14:32, 08:15 e 02:50:45. O motor da síntese da fala pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />"O comboio parte às quatro da manhã." |

**Utilização**

O `say-as` elemento pode conter apenas texto.

**Exemplo**

O motor da síntese do discurso fala o seguinte exemplo: "O seu primeiro pedido foi para uma sala em outubro de 1920 com chegada antecipada às 12:35."
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Adicionar áudio gravado

`audio` é um elemento opcional que permite inserir áudio MP3 num documento SSML. O corpo do elemento áudio pode conter texto simples ou marcação SSML que é falada se o ficheiro de áudio não estiver disponível ou não for retocável. Além disso, o `audio` elemento pode conter texto e os seguintes elementos: , , , `audio` , , , , , , e `break` `p` `s` `phoneme` `prosody` `say-as` `sub` .

Qualquer áudio incluído no documento SSML deve satisfazer estes requisitos:

* O MP3 deve ser alojado num ponto final HTTPS acessível à Internet. O HTTPS é necessário e o domínio que hospeda o ficheiro MP3 deve apresentar um certificado TLS/SSL válido e fidedigno.
* O MP3 deve ser um ficheiro MP3 válido (MPEG v2).
* A taxa de bits deve ser de 48 kbps.
* A taxa de amostra deve ser de 16.000 Hz.
* O tempo total combinado para todos os ficheiros de texto e áudio numa única resposta não pode exceder 90 (90) segundos.
* O MP3 não deve conter nenhuma informação específica do cliente ou outra informação sensível.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Atributos**

| Atributo | Descrição                                   | Obrigatório / Opcional                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Especifica a localização/URL do ficheiro áudio. | Necessário se utilizar o elemento áudio no documento SSML. |

**Exemplo**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaRUS">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Adicionar áudio de fundo

O `mstts:backgroundaudio` elemento permite-lhe adicionar áudio de fundo aos seus documentos SSML (ou misturar um ficheiro áudio com texto-a-fala). Com `mstts:backgroundaudio` você pode dar um loop um ficheiro áudio em segundo plano, desvanecer-se no início do texto-a-fala, e desaparecer no final do texto-a-discurso.

Se o áudio de fundo fornecido for mais curto do que o texto-a-fala ou o desvanecimento, irá dar a volta. Se for mais comprido do que o texto-a-discurso, deixará de ser o desvanecimento.

Apenas um ficheiro áudio de fundo é permitido por documento SSML. No entanto, pode intercalar `audio` tags dentro do `voice` elemento para adicionar áudio adicional ao seu documento SSML.

**Syntax**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório / Opcional |
|-----------|-------------|---------------------|
| `src` | Especifica a localização/URL do ficheiro áudio de fundo. | Necessário se utilizar áudio de fundo no seu documento SSML. |
| `volume` | Especifica o volume do ficheiro áudio de fundo. **Valores aceites:** `0` a `100` inclusivo. O valor predefinido é `1`. | Opcional |
| `fadein` | Especifica a duração do áudio de fundo "desvanece-se" como milissegundos. O valor predefinido é `0` , que é o equivalente a não desaparecer. **Valores aceites:** `0` a `10000` inclusivo.  | Opcional |
| `fadeout` | Especifica a duração do áudio de fundo em milissegundos. O valor predefinido é `0` , que é o equivalente a não desaparecer. **Valores aceites:** `0` a `10000` inclusivo.  | Opcional |

**Exemplo**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Passos seguintes

* [Suporte linguístico: vozes, locais, línguas](language-support.md)
