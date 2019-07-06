---
title: Speech Synthesis Markup Language (SSML) - serviços de voz
titleSuffix: Azure Cognitive Services
description: Usando a linguagem de marcação de síntese de voz para controlar pronúncia e prosody em voz.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8285a76f8cd07863874f9c8e8eebe96f1cb968dd
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604824"
---
# <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Speech Synthesis Markup Language (SSML) é uma linguagem de marcação baseada em XML que permite aos programadores especificar como entrado de texto é convertida em fala sintetizada com o serviço de texto para voz. Em comparação com texto sem formatação, SSML permite aos programadores ajustar o argumento de venda, pronúncia, fala taxa, o volume e mais a saída de texto para voz. Pontuação normal, como colocar em pausa após um período ou usando o intonation correto quando uma sentença termina com um ponto de interrogação são manipulados automaticamente.

A implementação de serviços de voz de SSML baseia-se a World Wide Web Consortium [Speech Synthesis marcação linguagem versão 1.0](https://www.w3.org/TR/speech-synthesis).

> [!IMPORTANT]
> Contagem de caracteres do chinês, japonês e coreano como dois caracteres para faturação. Para obter mais informações, consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Vozes padrão, neurais e personalizadas

Escolha entre padrão e neurais vozes ou criar a sua voz personalizada exclusivo para o produto ou marca. 75 + vozes standard estão disponíveis em mais de 45 idiomas e localidades e 5 vozes neurais estão disponíveis em 4 idiomas e localidades. Para obter uma lista completa de vozes (neurais e padrão), as localidades e idiomas suportados, consulte [suporte de idiomas](language-support.md).

Para saber mais sobre o padrão, neural e vozes personalizadas, veja [descrição geral de voz](text-to-speech.md).

## <a name="supported-ssml-elements"></a>Elementos SSML suportados

Cada documento SSML é criado com elementos SSML (ou etiquetas). Estes elementos são utilizados para ajustar o argumento de venda, prosody, volume e muito mais. As secções seguintes detalham como cada elemento é usado e quando um elemento é obrigatório ou opcional.  

> [!IMPORTANT]
> Não se esqueça de utilizar aspas em redor de valores de atributo. Padrões para XML bem formado, válido exige valores de atributo para estar entre aspas. Por exemplo, `<prosody volume="90">` é um elemento formado, válido, mas `<prosody volume=90>` não é. SSML talvez não reconheça os valores de atributo que não são aspas.

## <a name="create-an-ssml-document"></a>Criar um documento SSML

`speak` é o elemento raiz e está **necessário** para todos os documentos SSML. O `speak` elemento contém informações importantes, como versão, idioma e a definição de vocabulário de marcação.

**Sintaxe**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| version | Indica a versão da especificação SSML usada para interpretar a marcação de documento. A versão atual é 1.0. | Necessário |
| xml:lang | Especifica o idioma do documento de raiz. O valor pode conter um código de linguagem de duas letras em minúsculas (por exemplo, **en**), ou o código de idioma e país/região em maiúsculas (por exemplo, **en-US**). | Necessário |
| xmlns | Especifica o URI para o documento que define o vocabulário de marcação (tipos de elementos e nomes de atributos) do documento SSML. O URI atual é https://www.w3.org/2001/10/synthesis. | Necessário |

## <a name="choose-a-voice-for-text-to-speech"></a>Escolha uma voz para voz

O `voice` elemento é necessário. É utilizado para especificar a voz que é utilizada para voz.

**Sintaxe**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| name | Identifica a utilizada para a saída de texto para discurso de voz. Para obter uma lista completa de vozes suportados, consulte [suporte de idiomas](language-support.md#text-to-speech). | Necessário |

**Exemplo**

> [!NOTE]
> Este exemplo utiliza o `en-US-Jessa24kRUS` voz. Para obter uma lista completa de vozes suportados, consulte [suporte de idiomas](language-support.md#text-to-speech).

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Utilizar várias vozes

Dentro do `speak` elemento, pode especificar várias vozes de saída de texto para voz. Estes vozes podem estar em idiomas diferentes. Para cada voz, o texto deve ser encapsulado num `voice` elemento.

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| name | Identifica a utilizada para a saída de texto para discurso de voz. Para obter uma lista completa de vozes suportados, consulte [suporte de idiomas](language-support.md#text-to-speech). | Necessário |

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Good morning!
    </voice>
    <voice  name="en-US-Guy24kRUS">
        Good morning to you too Jessa!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Ajustar os estilos de falantes

> [!IMPORTANT]
> Esta funcionalidade só funciona com as vozes neurais.

Por predefinição, o serviço de texto para discurso sintetiza texto usando um estilo de fala neutro para vozes padrão e neurais. Com as vozes neurais, pode ajustar o estilo de fala para expressar cheerfulness, empatia ou dados de sentimento com o `<mstts:express-as>` elemento. Este é um elemento opcional exclusivo para serviços de voz do Azure.

Atualmente, os ajustes de estilo de fala são suportados para estes vozes neurais:
* `en-US-JessaNeural`
* `zh-CN-XiaoxiaoNeural`

As alterações são aplicadas ao nível da sentença e estilo variar por meio da voz. Se um estilo não é suportado, o serviço retornará voz predefinida neutro de estilo de fala.

**Sintaxe**

```xml
<mstts:express-as type="string"></mstts:express-as>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| type | Especifica o estilo de fala. Atualmente, estilos de falantes são específico de voz. | Necessário se ajustar o estilo de fala para uma voz neural. Se utilizar `mstts:express-as`, em seguida, o tipo tem de ser fornecido. Se não for fornecido um valor inválido, esse elemento será ignorado. |

Utilize esta tabela para determinar quais estilos de falantes são suportados para cada neural voz.

| Voz | Type | Descrição |
|-------|------|-------------|
| `en-US-JessaNeural` | type=`cheerful` | Expresse emoção positivo e Feliz |
| | type=`empathy` | Expressa uma noção do vençam e compreender |
| `zh-CN-XiaoxiaoNeural` | type=`newscast` | Expresse um tom formal, semelhante às difusões de notícias |
| | type=`sentiment` | Transmite uma mensagem touching ou uma história |

**Exemplo**

Este fragmento SSML ilustra como o `<mstts:express-as>` elemento é usado para alterar o estilo de fala para `cheerful`.

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JessaNeural">
        <mstts:express-as type="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Adicionar ou remover uma garantia de reparação/pausa

Utilize o `break` elemento para inserir coloca em pausa (ou quebras) entre as palavras ou evitar interrupções adicionadas automaticamente pelo serviço de texto para voz.

> [!NOTE]
> Utilize este elemento para substituir o comportamento padrão de voz (TTS) de uma palavra ou frase, se a fala sintetizada para essa palavra ou frase parece não natural. Definir `strength` para `none` para impedir que uma quebra de prosodic, que é inserida automaticamente pelo serviço tex para voz.

**Sintaxe**

```xml
<break strength="string" />
<break time="string" />
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| força | Especifica a duração relativa de uma pausa através de um dos seguintes valores:<ul><li>Nenhum</li><li>x weak</li><li>fraca</li><li>média (predefinição)</li><li>Forte</li><li>x-strong</li></ul> | Opcional |
| time | Especifica a duração absoluta de uma pausa em segundos ou milissegundos. Exemplos de valores válidos são 2s e 500 | Opcional |

| força | Descrição |
|----------|-------------|
| Nenhum, ou se nenhum valor fornecido | 0 ms |
| x weak | 250 ms |
| fraca | 500 ms |
| Médio | 750 ms |
| Forte | 1000 ms |
| x-strong | 1250 ms |


**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Especifique os parágrafos e frases

`p` e `s` elementos são utilizados para denotar parágrafos e frases, respectivamente. Na ausência destes elementos, o serviço de texto para discurso determina automaticamente a estrutura do documento SSML.

O `p` elemento pode conter texto e os seguintes elementos: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as`, e `s`.

O `s` elemento pode conter texto e os seguintes elementos: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as`, e `sub`.

**Sintaxe**

```XML
<p></p>
<s></s>
```

**Exemplo**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
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

## <a name="use-phonemes-to-improve-pronunciation"></a>Utilizar fonemas para melhorar a pronúncia

O `ph` elemento é usado para pronúncia fonética em documentos SSML. O `ph` elemento só pode conter texto, não existem outros elementos. Fornece sempre voz legível por humanos como fallback.

Fonética alfabetos são compostos de telemóveis, que são constituídos por letras, números ou carateres, às vezes em combinação. Cada telefone descreve um som exclusivo de voz. Isso contrasta alfabeto Latino, onde qualquer letra pode representar vários sons faladas. Considere os pronunciations diferentes da letra "c", nas palavras "Colírio para os" e "cessação" ou os pronunciations diferentes da combinação de letras "th" na coisa"palavras" e "as".

**Sintaxe**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| alphabet | Especifica o fonetická a utilizar quando a pronúncia da cadeia de caracteres para resumir o `ph` atributo. A cadeia de caracteres especificando o alfabeto deve ser especificada em letras minúsculas. Seguem-se a alfabetos possíveis que pode especificar.<ul><li>IPA &ndash; Fonetická internacional</li><li>SAPI &ndash; definido de telefone de API de voz</li><li>UPS &ndash; Universal telefone definido</li></ul>O alfabeto aplica-se apenas a phoneme no elemento. Para obter mais informações, consulte [Fonetická referência](https://msdn.microsoft.com/library/hh362879(v=office.14).aspx). | Opcional |
| ph | Uma cadeia de caracteres contendo os telefones que especificar na pronúncia da palavra no `phoneme` elemento. Se a cadeia especificada contém os telefones não reconhecidos, o serviço (TTS) de texto para discurso rejeita o documento inteiro do SSML e produz nenhuma da saída de voz especificada no documento. | Necessário se utilizar fonemas. |

**Exemplos**

```XML
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

## <a name="adjust-prosody"></a>Ajustar prosody

O `prosody` elemento é utilizado para especificar as alterações ao argumento de venda, countour, intervalo, taxa, duração e volume para a saída de texto para voz. O `prosody` elemento pode conter texto e os seguintes elementos: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub`, e `s`.

Como valores de atributo prosodic podem variar ao longo de uma ampla variedade, o reconhecedor de voz interpreta os valores atribuídos como uma sugestão de qual devem ser os valores reais prosodic do voz selecionado. O serviço de texto para discurso limita ou substitui os valores que não são suportadas. Exemplos de valores não suportados são um argumento de venda de 1 MHz ou um volume de 120.

**Sintaxe**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Atributos**

| Atributo | Descrição | Obrigatório / opcional |
|-----------|-------------|---------------------|
| argumento de venda | Indica o argumento de venda de linha de base para o texto. Pode expressar o argumento de venda como:<ul><li>Um valor absoluto, expressado como um número seguido por "Hz" (Hertz). Por exemplo, 600Hz.</li><li>Um valor relativo, expressado como um número precedido por "+" ou "-" e seguido de "Hz" ou "st", que especifica um valor para alterar o argumento de venda. Por exemplo: +80 Hz ou - 2st. "st" indica que a unidade de alteração é semitone, que é a metade de um tom (um passo de metade) na escala diatonic padrão.</li><li>Um valor constante:<ul><li>x-low</li><li>Baixa</li><li>Médio</li><li>Alta</li><li>x-high</li><li>predefinição</li></ul></li></ul>. | Opcional |
| Contour | Não é suportada em Contour vozes neurais. Contour representa as alterações no argumento de venda para conteúdo de voz como uma matriz de destinos em posições de tempo especificado na saída de voz. Cada destino é definido por conjuntos de pares de parâmetro. Por exemplo: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>O primeiro valor em cada conjunto de parâmetros Especifica a localização da alteração pitch como uma percentagem da duração do texto. O segundo valor Especifica a quantidade para aumentar ou diminuir o argumento de venda, com um valor relativo ou um valor de enumeração para o argumento de venda (consulte `pitch`). | Opcional |
| Intervalo  | Um valor que representa o intervalo de argumento de venda do texto. Pode expressar `range` com os mesmos valores absolutos, valores relativos ou valores de enumeração usada para descrever `pitch`. | Opcional |
| Taxa de  | Indica a taxa de fala do texto. Pode expressar `rate` como:<ul><li>Um valor relativo, expressado como um número que age como um multiplicador do padrão. Por exemplo, um valor de *1* resulta em nenhuma alteração na taxa. Um valor de *.5* resulta num então da tarifa. Um valor de *3* resulta num tripling da tarifa.</li><li>Um valor constante:<ul><li>x-slow</li><li>lento</li><li>Médio</li><li>Rápido</li><li>x-fast</li><li>predefinição</li></ul></li></ul> | Opcional |
| duration  | O período de tempo que deve decorrer durante a conversão de voz o serviço de síntese (TTS) lê o texto, em segundos ou milissegundos. Por exemplo, *2s* ou *1800ms*. | Opcional |
| volume  | Indica o nível de volume de voz fala. Pode expressar o volume como:<ul><li>Um valor absoluto, expressado como um número no intervalo de 0,0 a 100,0 partir *quietest* ao *loudest*. Por exemplo, 75. A predefinição é 100,0.</li><li>Um valor relativo, expressado como um número precedido por "+" ou "-" que especifica um valor para alterar o volume. Por exemplo +10 ou-5.5.</li><li>Um valor constante:<ul><li>Silenciosa</li><li>x-soft</li><li>soft</li><li>Médio</li><li>alta</li><li>x-loud</li><li>predefinição</li></ul></li></ul> | Opcional |

### <a name="change-speaking-rate"></a>Taxa de alteração de fala

Taxa de fala pode ser aplicado a vozes padrão no word ou no nível de frases. Ao passo que a taxa de fala só pode ser aplicada para vozes neurais ao nível da sentença.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Volume de alterações

Alterações de volume podem ser aplicadas a vozes padrão no word ou no nível de frases. Ao passo que só podem ser aplicadas alterações de volume para vozes neurais ao nível da sentença.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Alterar o argumento de venda

Pitch alterações podem ser aplicadas a vozes padrão no word ou no nível de frases. Ao passo que só podem ser aplicadas alterações de argumento de venda para vozes neurais ao nível da sentença.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Contour de argumento de venda de alteração

> [!IMPORTANT]
> Alterações de contour Pitch não são suportadas com vozes neurais.

**Exemplo**

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice  name="en-US-Jessa24kRUS">
        <prosody contour="(80%,+20%) (90%,+30%)" >
            Good morning.
        </prosody>
    </voice>
</speak>
```

## <a name="next-steps"></a>Passos Seguintes

* [Suporte de idiomas: vozes, localidades e idiomas](language-support.md)
