---
title: Linguagem de marcação da síntese da fala (SSML) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Usando a linguagem de marcação de síntese de voz para controlar pronúncia e prosody em voz.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: dapine
ms.openlocfilehash: 68691ad60542c55db4d381e2923a9f928a22995a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220503"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Melhorar a síntese com a linguagem de marcação da síntese da fala (SSML)

A linguagem de marcação da síntese da fala (SSML) é uma linguagem de marcação baseada em XML que permite aos desenvolvedores especificar como o texto de entrada é convertido em discurso sintetizado usando o serviço de texto-para-fala. Em comparação com o texto simples, o SSML permite que os desenvolvedores afinam o tom, a pronúncia, a taxa de fala, o volume e mais da saída de texto para a fala. Pontuação normal, como pausa após um período, ou utilização da entoação correta quando uma frase termina com um ponto de interrogação é automaticamente manuseada.

A implementação do serviço de fala do SSML baseia-se na [versão 1.0](https://www.w3.org/TR/speech-synthesis)da síntese de markup da Síntese de Discurso sintetizadordo world wide Web Consortium .

> [!IMPORTANT]
> Os caracteres chineses, japoneses e coreanos contam como dois caracteres para faturação. Para mais informações, consulte [Preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Vozes padrão, neurais e personalizadas

Escolha entre vozes padrão e neurais, ou crie a sua própria voz personalizada única para o seu produto ou marca. 75 vozes padrão estão disponíveis em mais de 45 línguas e locais, e 5 vozes neurais estão disponíveis em quatro línguas e locais. Para uma lista completa de línguas, locais e vozes suportadas (neural e standard), consulte o [suporte linguístico.](language-support.md)

Para saber mais sobre vozes padrão, neurais e personalizadas, consulte a [visão geral do texto à fala](text-to-speech.md).

## <a name="special-characters"></a>Personagens especiais

Durante a utilização do SSML, lembre-se que personagens especiais, tais como aspas, apóstrofos e suportes devem ser escapados. Para mais informações, consulte Linguagem de [Marcação Extensível (XML) 1.0: Apêndice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementos SSML suportados

Cada documento SSML é criado com elementos SSML (ou tags). Estes elementos são usados para ajustar o tom, a prosódia, o volume e muito mais. As seguintes secções detalham como cada elemento é utilizado e quando um elemento é necessário ou opcional.  

> [!IMPORTANT]
> Não se esqueça de usar citações duplas em torno dos valores de atributos. As normas para xml bem formado e válido exigem que os valores de atributo sejam incluídos em duas aspas. Por exemplo, `<prosody volume="90">` é um elemento bem formado e válido, mas `<prosody volume=90>` não é. A SSML pode não reconhecer valores de atributos que não estejam em cotações.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak` é o elemento raiz, e é **necessário** para todos os documentos SSML. O elemento `speak` contém informações importantes, como versão, linguagem e definição de vocabulário de marcação.

**Sintaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `version` | Indica a versão da especificação SSML utilizada para interpretar a marcação do documento. A versão atual é 1.0. | Necessário |
| `xml:lang` | Especifica a linguagem do documento raiz. O valor pode conter um código linguístico minúsculo, de duas letras (por exemplo, `en`), ou o código linguístico e o país/região maiúsculo (por exemplo, `en-US`). | Necessário |
| `xmlns` | Especifica o URI no documento que define o vocabulário de marcação (os tipos de elementos e nomes de atributos) do documento SSML. O atual URI é https://www.w3.org/2001/10/synthesis. | Necessário |

## <a name="choose-a-voice-for-text-to-speech"></a>Escolha uma voz para texto-a-fala

É necessário o elemento `voice`. É utilizado para especificar a voz que é usada para texto-a-fala.

**Sintaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz utilizada para a saída de texto à fala. Para obter uma lista completa de vozes apoiadas, consulte o [suporte da Linguagem](language-support.md#text-to-speech). | Necessário |

**Exemplo**

> [!NOTE]
> Este exemplo usa a voz `en-US-Jessa24kRUS`. Para obter uma lista completa de vozes apoiadas, consulte o [suporte da Linguagem](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Utilizar várias vozes

Dentro do elemento `speak`, pode especificar várias vozes para a saída de texto a fala. Estas vozes podem estar em diferentes línguas. Para cada voz, o texto deve ser embrulhado num elemento `voice`. 

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `name` | Identifica a voz utilizada para a saída de texto à fala. Para obter uma lista completa de vozes apoiadas, consulte o [suporte da Linguagem](language-support.md#text-to-speech). | Necessário |

> [!IMPORTANT]
> Várias vozes são incompatíveis com a palavra funcionalidade limite. A função limite da palavra precisa de ser desativada para usar várias vozes.

### <a name="disable-word-boundary"></a>Desativar o limite da palavra

Dependendo da linguagem SDK do discurso, você definirá a propriedade `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` para `false` em uma instância do objeto `SpeechConfig`.

# <a name="c"></a>[C#](#tab/csharp)

Para mais informações, consulte <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-dotnet" target="_blank">`SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Para mais informações, consulte <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank">`SetProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Para mais informações, consulte <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty?view=azure-java-stable#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank">`setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[python](#tab/python)

Para mais informações, consulte <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python#set-property-by-name-property-name--str--value--str-" target="_blank">`set_property_by_name` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Para mais informações, consulte <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig?view=azure-node-latest#setproperty-string--string-" target="_blank">`setProperty` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Para mais informações, consulte <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">`setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Para mais informações, consulte <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank">`setPropertyTo` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajuste os estilos de fala

> [!IMPORTANT]
> O ajuste dos estilos de fala só funcionará com vozes neurais.

Por padrão, o serviço de texto-a-fala sintetiza o texto usando um estilo de fala neutro para vozes padrão e neurais. Com vozes neurais, pode ajustar o estilo de falar para expressar alegria, empatia ou sentimento com o elemento `<mstts:express-as>`. Este é um elemento opcional exclusivo do serviço da Fala.

Atualmente, os ajustes de estilo de fala são suportados para estas vozes neurais:
* `en-US-JessaNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`

As alterações são aplicadas ao nível da frase, e o estilo varia de acordo com a voz. Se um estilo não for suportado, o serviço devolverá o discurso no estilo de fala neutro padrão.

**Sintaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `type` | Especifica o estilo de falar. Atualmente, os estilos de fala são específicos da voz. | Necessário se ajustar o estilo de fala para uma voz neural. Se utilizar `mstts:express-as`, deve ser fornecido o escrevo. Se for fornecido um valor inválido, este elemento será ignorado. |

Utilize esta tabela para determinar quais os estilos de fala suportados para cada voz neural.

| Voz | Tipo | Descrição |
|-------|------|-------------|
| `en-US-JessaNeural` | `type="cheerful"` | Expressa uma emoção positiva e feliz |
| | `type="empathy"` | Expressa uma sensação de carinho e compreensão |
| | `type="chat"` | Fale em tom casual e descontraído |
| | `type="newscast"` | Expressa um tom formal, semelhante às emissões noticiosas |
| | `type="customerservice"` | Fale de forma amigável e paciente como atendimento ao cliente |
| `pt-BR-FranciscaNeural` | `type="cheerful"` | Expressa uma emoção positiva e feliz |
| `zh-CN-XiaoxiaoNeural` | `type="newscast"` | Expressa um tom formal, semelhante às emissões noticiosas |
| | `type="sentiment"` | Transmite uma mensagem comovente ou uma história |

**Exemplo**

Este snippet SSML ilustra como o elemento `<mstts:express-as>` é usado para mudar o estilo de fala para `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicione ou remova uma pausa/pausa

Utilize o elemento `break` para inserir pausas (ou pausas) entre palavras ou evitar pausas adicionadas automaticamente pelo serviço de texto à fala.

> [!NOTE]
> Use este elemento para anular o comportamento padrão do texto-a-fala (TTS) para uma palavra ou frase se o discurso sintetizado para essa palavra ou frase soar pouco natural. Despôs `strength` para `none` para evitar uma rutura prosódica, que é automaticamente inserida pelo serviço de texto-a-fala.

**Sintaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `strength` | Especifica a duração relativa de uma pausa utilizando um dos seguintes valores:<ul><li>nenhuma</li><li>x-fraco</li><li>fraco</li><li>meio (padrão)</li><li>forte</li><li>x-forte</li></ul> | Opcional |
| `time` | Especifica a duração absoluta de uma pausa em segundos ou milissegundos. Exemplos de valores válidos são `2s` e `500` | Opcional |

| Força | Descrição |
|----------|-------------|
| Nenhum, ou se nenhum valor fornecido | 0 ms |
| x-fraco | 250 ms |
| fraco | 500 ms |
| meio | 750 ms |
| forte | 1000 ms |
| x-forte | 1250 ms |


**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especificar parágrafos e frases

`p` e `s` elementos são utilizados para denotar parágrafos e frases, respectivamente. Na ausência destes elementos, o serviço de texto-a-fala determina automaticamente a estrutura do documento SSML.

O elemento `p` pode conter texto e os seguintes elementos: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`e `s`.

O elemento `s` pode conter texto e os seguintes elementos: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`e `sub`.

**Sintaxe**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
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

O elemento `ph` é utilizado para pronúncia fonética em documentos SSML. O elemento `ph` só pode conter texto, nenhum outro elemento. Sempre forneça um discurso legível pelo homem como um recuo.

Os alfabetos fonéticos são compostos por telefones, que são compostos por letras, números ou caracteres, às vezes em combinação. Cada telefone descreve um som único de fala. Isto contrasta com o alfabeto latino, onde qualquer letra pode representar múltiplos sons falados. Considere as diferentes pronúncias da letra "c" nas palavras "doces" e "cessar", ou as diferentes pronúncias da combinação de letras "th" nas palavras "coisa" e "aquelas".

**Sintaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `alphabet` | Especifica o alfabeto fonético para usar ao sintetizar a pronúncia da corda no atributo `ph`. A cadeia que especifica o alfabeto deve ser especificada em letras minúsculas. Seguem-se os possíveis alfabetos que pode especificar.<ul><li>`ipa` &ndash; Alfabeto Fonético Internacional</li><li>`sapi` &ndash; alfabeto fonético do serviço da fala</li><li>`ups` &ndash; conjunto de telefone universal</li></ul><br>O alfabeto aplica-se apenas ao `phoneme` no elemento. Para mais informações, consulte referência do [alfabeto fonético](https://en.wikipedia.org/wiki/International_Phonetic_Alphabet). | Opcional |
| `ph` | Uma cadeia contendo telefones que especificam a pronúncia da palavra no elemento `phoneme`. Se a cadeia especificada contiver telefones não reconhecidos, o serviço de texto-a-fala (TTS) rejeita todo o documento SSML e não produz nenhuma saída de fala especificada no documento. | Necessário se utilizar fonemas. |

**Exemplos**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Use léxico personalizado para melhorar a pronúncia

Por vezes, o TTS não consegue pronunciar com precisão uma palavra, por exemplo, uma empresa ou nome estrangeiro. Os desenvolvedores podem definir a leitura destas entidades no SSML usando `phoneme` e etiqueta `sub`, ou definir a leitura de várias entidades, referindo-se a um ficheiro léxico personalizado usando `lexicon` etiqueta.

**Sintaxe**

```XML
<lexicon uri="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `uri` | O endereço do documento PLS externo. | Necessário. |

**Utilização**

Passo 1: Definir léxico personalizado 

Pode definir a leitura de entidades através de uma lista de itens de léxico personalizados, armazenados como um ficheiro .xml ou .pls.

**Exemplo**

```xml
<?xml version="1.0" encoding="UTF-16"?>
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

Cada elemento `lexeme` é um item de léxico. `grapheme` contém texto que descreve a ortografia de `lexeme`. O formulário de leitura pode ser fornecido de acordo com `alias`. A corda telefónica pode ser fornecida em `phoneme` elemento.

O elemento `lexicon` contém pelo menos um elemento `lexeme`. Cada elemento `lexeme` contém pelo menos um elemento `grapheme` e um ou mais elementos `grapheme`, `alais`e `phoneme`. O elemento `grapheme` contém texto que descreve a <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografia. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a> Os elementos `alias` são usados para indicar a pronúncia de um acrónimo ou termo abreviado. O elemento `phoneme` fornece texto descrevendo como o `lexeme` é pronunciado.

Para obter mais informações sobre o ficheiro lexicon personalizado, consulte a [Versão 1.0 da Pronunciação Lexicon (PLS)](https://www.w3.org/TR/pronunciation-lexicon/) no site da W3C.

Passo 2: Faça upload do ficheiro lexicon personalizado criado no passo 1 online, pode armazená-lo em qualquer lugar, e sugerimos que o guarde no Microsoft Azure, por exemplo, [armazenamento de Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Passo 3: Consulte o ficheiro lexicon personalizado no SSML

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
<lexicon uri="http://www.example.com/customlexicon.xml"/>
BTW, we will be there probably 8:00 tomorrow morning.
Could you help leave a message to Robert Benigni for me?
</speak>
```
"BTW" será lido como "A propósito". "Benigni" será lido com ipa "binânji".  

**Limitação**
- Tamanho do ficheiro: o limite máximo do tamanho do ficheiro léxico personalizado é de 100KB, se para além deste tamanho, o pedido de síntese falhará.
- Atualização da cache lexicon: o léxico personalizado será colocado com URI como chave no Serviço TTS quando for carregado pela primeira vez. O léxico com o mesmo URI não será recarregado dentro de 15 minutos, por isso a mudança de léxico personalizado precisa de esperar no máximo 15 minutos para fazer efeito.

**Conjuntos fonéticos do serviço de fala**

Na amostra acima, estamos a usar o Alfabeto Fonético Internacional, também conhecido como o conjunto telefónico IPA. Sugerimos que os desenvolvedores utilizem o IPA, porque é o padrão internacional. Tendo em conta que o IPA não é fácil de lembrar, o serviço de Fala define um conjunto fonético para sete línguas (`en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN`e `zh-TW`).

Você pode usar o `sapi` como vale para o `alphabet` atributo com léxicos personalizados como demonstrado abaixo:

```xml
<?xml version="1.0" encoding="UTF-16"?>
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

Para obter mais informações sobre o alfabeto fonético detalhado do serviço da Fala, consulte os [conjuntos fonéticos](speech-ssml-phonetic-sets.md)do serviço da Fala.

## <a name="adjust-prosody"></a>Ajuste a prótese

O elemento `prosody` é utilizado para especificar alterações ao pitch, countour, range, rate, duração e volume para a saída texto-a-fala. O elemento `prosody` pode conter texto e os seguintes elementos: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`e `s`.

Como os valores prosódicos podem variar ao longo de uma ampla gama, o reconhecimento da fala interpreta os valores atribuídos como uma sugestão do que os valores prosódicos reais da voz selecionada devem ser. Os limites do serviço de texto à fala ou substitui valores que não são suportados. Exemplos de valores não suportados são um tom de 1 MHz ou um volume de 120.

**Sintaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `pitch` | Indica o tom de base para o texto. Pode expressar o tom como:<ul><li>Um valor absoluto, expresso como um número seguido por "Hz" (Hertz). Por exemplo, 600 Hz.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" e seguido por "Hz" ou "st", que especifica um montante para alterar o tom. Por exemplo: +80 Hz ou -2º. O "st" indica que a unidade de mudança é semitona, que é metade de um tom (meio passo) na escala diatónica padrão.</li><li>Um valor constante:<ul><li>x-baixo</li><li>baixo</li><li>meio</li><li>alto</li><li>x-alto</li><li>predefinição</li></ul></li></ul>. | Opcional |
| `contour` | O contorno não é suportado por vozes neurais. O contorno representa mudanças no tom. Estas alterações são representadas como uma série de alvos em posições de tempo especificadas na saída da fala. Cada alvo é definido por conjuntos de pares de parâmetros. Por exemplo: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor de cada conjunto de parâmetros especifica a localização da alteração do tom em percentagem da duração do texto. O segundo valor especifica o valor para aumentar ou baixar o tom, utilizando um valor relativo ou um valor de enumeração para o pitch (ver `pitch`). | Opcional |
| `range` | Um valor que representa o alcance do pitch para o texto. Pode exprimir `range` utilizando os mesmos valores absolutos, valores relativos ou valores de enumeração utilizados para descrever `pitch`. | Opcional |
| `rate` | Indica a taxa de fala do texto. Pode exprimir `rate`:<ul><li>Um valor relativo, expresso como um número que atua como um multiplicador do padrão. Por exemplo, um valor de *1* resulta em nenhuma alteração da taxa. Um valor de *0,5* resulta numa redução para metade da taxa. Um valor de *3* resulta numa triplicação da taxa.</li><li>Um valor constante:<ul><li>x-lento</li><li>lento</li><li>meio</li><li>rápido</li><li>x-rápido</li><li>predefinição</li></ul></li></ul> | Opcional |
| `duration` | O período de tempo que deve decorrer enquanto o serviço de síntese da fala (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. | Opcional |
| `volume` | Indica o nível de volume da voz falante. Pode expressar o volume como:<ul><li>Um valor absoluto, expresso como número na gama de 0,0 a 100.0, do *mais silencioso* ao *mais alto.* Por exemplo, 75. O padrão é 100.0.</li><li>Um valor relativo, expresso como um número precedido por "+" ou "-" que especifica um montante para alterar o volume. Por exemplo, +10 ou -5,5.</li><li>Um valor constante:<ul><li>silencioso</li><li>x-soft</li><li>macio</li><li>meio</li><li>alto</li><li>x-alto</li><li>predefinição</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Taxa de alteração de fala

A taxa de fala pode ser aplicada às vozes padrão na palavra ou nível de frase. Enquanto que a taxa de fala só pode ser aplicada às vozes neurais ao nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Volume de alterações

As alterações de volume podem ser aplicadas às vozes padrão na palavra ou no nível da frase. Considerando que as alterações de volume só podem ser aplicadas às vozes neurais ao nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Alterar o argumento de venda

As alterações de pitch podem ser aplicadas às vozes padrão na palavra ou nível de frase. Enquanto as alterações de tom só podem ser aplicadas às vozes neurais ao nível da frase.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Contour de argumento de venda de alteração

> [!IMPORTANT]
> As alterações do contorno do pitch não são suportadas com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>dizer-como elemento

`say-as` é um elemento opcional que indica o tipo de conteúdo (como número ou data) do texto do elemento. Isto fornece orientação ao motor de síntese da fala sobre como pronunciar o texto.

**Sintaxe**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `interpret-as` | Indica o tipo de conteúdo do texto do elemento. Para obter uma lista de tipos, consulte a tabela abaixo. | Necessário |
| `format` | Fornece informações adicionais sobre a formatação precisa do texto do elemento para tipos de conteúdo que podem ter formatos ambíguos. O SSML define formatos para tipos de conteúdo que os utilizam (ver tabela abaixo). | Opcional |
| `detail` | Indica o nível de detalhe a ser falado. Por exemplo, este atributo pode solicitar que o motor de síntese da fala pronuncie marcas de pontuação. Não existem valores padrão definidos para `detail`. | Opcional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Seguem-se os tipos de conteúdo suportados para os atributos `interpret-as` e `format`. Incluir o atributo `format` apenas se `interpret-as` for definido até à data e hora.

| interpretar-como | format | Interpretação |
|--------------|--------|----------------|
| `address` | | O texto é falado como um endereço. O motor de síntese da fala pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"Estou na 150ª corte nordeste de Redmond Washington." |
| `cardinal`, `number` | | O texto é falado como um número cardeal. O motor de síntese da fala pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Como "Há três alternativas". |
| `characters`, `spell-out` | | O texto é falado como letras individuais (soletradas). O motor de síntese da fala pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Como "T E S T". |
| `date` | dmy, mdy, ymd, ydm, ym, my, md, dm, d, m, y | O texto é falado como uma data. O atributo `format` especifica o formato da data *(d=day, m=month, e y=year).* O motor de síntese da fala pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Como "Hoje é 19 de outubro dois mil e dezasseis.". |
| `digits`, `number_digit` | | O texto é falado como uma sequência de dígitos individuais. O motor de síntese da fala pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Como "1 2 3 4 5 6 7 8 9." |
| `fraction` | | O texto é falado como um número fracionário. O motor de síntese da fala pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Como "três oitavos de polegada". |
| `ordinal` | | O texto é falado como um número ordinal. O motor de síntese da fala pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Como "Selecione a terceira opção". |
| `telephone` | | O texto é falado como um número de telefone. O atributo `format` pode conter dígitos que representam um código de país. Por exemplo, "1" para os Estados Unidos ou "39" para a Itália. O motor de síntese da fala pode utilizar esta informação para orientar a sua pronúncia de um número de telefone. O número de telefone também pode incluir o código do país, e em caso afirmativo, tem precedência sobre o código do país no `format`. O motor de síntese da fala pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Como "O meu número é o código de área oito oito oito cinco cinco cinco um dois um dois." |
| `time` | hms12, hms24 | O texto é falado como um tempo. O atributo `format` especifica se o tempo é especificado com um relógio de 12 horas (hms12) ou um relógio de 24 horas (hms24). Use um cólon para separar números que representam horas, minutos e segundos. Seguem-se os exemplos de tempo válido: 12:35, 1:14:32, 08:15 e 02:50:45. O motor de síntese da fala pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Como "O comboio parte a quatro A M." |

**Utilização**

O elemento `say-as` pode conter apenas texto.

**Exemplo**

O motor da síntese da fala fala o seguinte exemplo: "O seu primeiro pedido foi para uma sala em 19 de outubro de 200, com chegada antecipada às 12:35."
 
```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Adicionar áudio gravado

`audio` é um elemento opcional que lhe permite inserir áudio MP3 num documento SSML. O corpo do elemento áudio pode conter texto simples ou marcação SSML que é falada se o ficheiro de áudio não estiver disponível ou não reprodução. Além disso, o elemento `audio` pode conter texto e os seguintes elementos: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as`e `sub`.

Qualquer áudio incluído no documento SSML deve satisfazer estes requisitos:

* O MP3 deve ser alojado num ponto final HTTPS acessível à Internet. É necessário HTTPS, e o domínio que acolhe o ficheiro MP3 deve apresentar um certificado SSL válido e fidedigno.
* O MP3 deve ser um ficheiro MP3 válido (MPEG v2).
* A taxa de bitdeve ser de 48 kbps.
* A taxa de amostragem deve ser de 16.000 Hz.
* O tempo total combinado para todos os ficheiros de texto e áudio numa única resposta não pode exceder 90 segundos.
* O MP3 não deve conter qualquer informação sensível ou específica do cliente.

**Sintaxe**

```xml
<audio src="string"/></audio>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `src` | Especifica a localização/URL do ficheiro áudio. | Necessário se utilizar o elemento áudio no seu documento SSML. |

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Jessa24kRUS">
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

O elemento `mstts:backgroundaudio` permite adicionar áudio de fundo aos seus documentos SSML (ou misturar um ficheiro áudio com texto-a-falar). Com `mstts:backgroundaudio` pode ligar um ficheiro áudio em segundo plano, desvanecer-se no início do texto-a-fala e desaparecer no final do texto-a-falar.

Se o áudio de fundo fornecido for mais curto do que o texto-a-fala ou o desvanecimento, irá circular. Se for mais longo do que o texto-a-palavra, acabará quando o desvanecimento terminar.

Apenas um ficheiro áudio de fundo é permitido por documento SSML. No entanto, pode intercalado`audio` etiquetas dentro do elemento `voice` para adicionar áudio adicional ao seu documento SSML.

**Sintaxe**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| `src` | Especifica a localização/URL do ficheiro áudio de fundo. | Necessário se utilizar áudio de fundo no seu documento SSML. |
| `volume` | Especifica o volume do ficheiro áudio de fundo. **Valores aceites**: `0` para `100` inclusivo. O valor predefinido é `1`. | Opcional |
| `fadein` | Especifica a duração do áudio de fundo "desvanecer-se" como milissegundos. O valor predefinido é `0`, o equivalente a não desaparecer. **Valores aceites**: `0` para `10000` inclusivo.  | Opcional |
| `fadeout` | Especifica a duração do áudio de fundo desaparecer em milissegundos. O valor predefinido é `0`, o equivalente a não desaparecer. **Valores aceites**: `0` a `10000` inclusivo.  | Opcional |

**Exemplo**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Passos seguintes

* [Apoio linguístico: vozes, locais, línguas](language-support.md)
