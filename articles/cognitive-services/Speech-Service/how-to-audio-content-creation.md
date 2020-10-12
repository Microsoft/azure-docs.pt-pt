---
title: Criação de Conteúdo Sonoro - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Audio Content Creation é uma ferramenta online que permite personalizar e afinar a saída de texto-a-voz da Microsoft para as suas apps e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224592"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Melhorar a síntese com a ferramenta de Criação de Conteúdos Áudio

[Audio Content Creation](https://aka.ms/audiocontentcreation) é uma ferramenta online que permite personalizar e afinar a saída de texto-a-voz da Microsoft para as suas apps e produtos. Pode utilizar esta ferramenta para afinar vozes públicas e personalizadas para expressões naturais mais precisas e gerir a sua saída na nuvem.

A ferramenta de criação de conteúdo sonoro baseia-se na [linguagem de marcação de síntese de fala (SSML)](speech-synthesis-markup.md). Para simplificar a personalização e afinação, a Audio Content Creation permite-lhe inspecionar visualmente as suas saídas de texto-a-discurso em tempo real.

Veja o [tutorial de vídeo](https://www.youtube.com/watch?v=O1wIJ7mts_w) para Audio Content Creation.

## <a name="how-does-it-work"></a>Como funciona?

Este diagrama mostra os passos que é preciso para afinar as saídas de texto para discurso. Use os links abaixo para saber mais sobre cada passo.

![Um diagrama dos passos que é preciso para afinar as saídas de texto para discurso.](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Crie a sua conta Azure e o recurso Speech](#set-up-your-azure-account-and-speech-resource) para começar.
2. [Crie um ficheiro de afinação de áudio](#create-an-audio-tuning-file) utilizando scripts simples ou SSML.
3. Escolha a voz e o idioma para o seu conteúdo de script. A Criação de Conteúdo sonoro inclui todas as [vozes texto-a-voz](language-support.md#text-to-speech)da Microsoft. Pode usar a voz normal, neural ou personalizada.
   >[!NOTE]
   > O acesso fechado está disponível para Vozes Neurais Personalizadas, que permitem criar vozes de alta definição semelhantes à fala de som natural. Para mais detalhes, consulte [o processo De Gating.](https://aka.ms/ignite2019/speech/ethics)

4. Reveja a saída de síntese predefinida. Em seguida, melhore a saída ajustando a pronúncia, quebra, tom, taxa, entoação, estilo de voz, e muito mais. Para obter uma lista completa de opções, consulte [a linguagem de marcação de síntese de discursos](speech-synthesis-markup.md). Aqui está um [vídeo](https://youtu.be/mUvf2NbfuYU) para mostrar como afinar a saída da fala com a Audio Content Creation. 
5. Guarde e [exporte o seu áudio afinado.](#export-tuned-audio) Quando guardar a faixa de afinação no sistema, pode continuar a trabalhar e iterar na saída. Quando estiver satisfeito com a saída, pode criar uma tarefa de criação de áudio com a funcionalidade de exportação. Pode observar o estado da tarefa de exportação e descarregar a saída para uso com as suas apps e produtos.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Crie a sua conta Azure e o recurso Speech

1. Para trabalhar com a Audio Content Creation, tem de ter uma conta Azure. Pode criar uma conta Azure utilizando a sua Conta Microsoft. Siga estas instruções para [criar uma conta Azure](get-started.md#new-resource). 
2. [Crie um recurso de Discurso](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) na sua conta Azure. Certifique-se de que o seu nível de preços está definido para **S0**. Se estiver a utilizar uma das vozes neurais, certifique-se de que cria o seu recurso numa [região suportada.](regions.md#standard-and-neural-voices)
2. Depois de obter a conta Azure e o recurso de fala, pode utilizar os serviços de fala e aceder à [Criação de Conteúdos Áudio.](https://aka.ms/audiocontentcreation)
3. Selecione o recurso Speech em que precisa de trabalhar. Também pode criar um novo recurso de Discurso aqui. 
4. Pode modificar o seu recurso Speech a qualquer momento com a opção **Definições,** localizada na navegação superior.

## <a name="create-an-audio-tuning-file"></a>Criar um ficheiro de afinação de áudio

Existem duas formas de colocar o seu conteúdo na ferramenta de Criação de Conteúdos Áudio.

**Opção 1:**

1. Clique em **Novo ficheiro** para criar um novo ficheiro de sintonização áudio.
2. Digite ou cole o seu conteúdo na janela de edição. Os caracteres de cada ficheiro são até 20.000. Se o seu script for superior a 20.000 caracteres, pode utilizar a Opção 2 para dividir automaticamente o seu conteúdo em vários ficheiros. 
3. Não se esqueça de salvar.

**Opção 2:**

1. Clique **em Upload** para importar um ou mais ficheiros de texto. Tanto o texto simples como o SSML são suportados.
2. Se o seu ficheiro de script for superior a 20.000 caracteres, por favor divida o ficheiro por parágrafos, por caráter ou por expressões regulares. 
3. Ao fazer o upload dos seus ficheiros de texto, certifique-se de que o ficheiro satisfaz estes requisitos.

   | Propriedade | Valor / Notas |
   |----------|---------------|
   | Formato do ficheiro | Texto simples (.txt)<br/> Texto SSML (.txt)<br/> Os ficheiros zip não são suportados |
   | Formato codificante | UTF-8 |
   | Nome de ficheiro | Cada ficheiro deve ter um nome único. Duplicados não são apoiados. |
   | Comprimento do texto | Os ficheiros de texto não devem exceder 20.000 caracteres. |
   | Restrições SSML | Cada ficheiro SSML só pode conter uma única peça de SSML. |

### <a name="plain-text-example"></a>Exemplo de texto simples

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Exemplo de texto SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportação de áudio afinado

Depois de ter revisto a sua saída de áudio e de estar satisfeito com a sua afinação e ajuste, pode exportar o áudio.

1. Clique **em Exportação** para criar uma tarefa de criação de áudio. **Recomenda-se a exportação para a Audio Library,** uma vez que suporta a longa saída de áudio e a experiência de saída áudio completa. Também pode baixar o áudio para o seu disco local diretamente, mas apenas os primeiros 10 minutos estão disponíveis. 
2. Escolha o formato de saída para o seu áudio afinado. Uma lista de formatos suportados e taxas de amostra está disponível abaixo.
3. Pode ver o estado da tarefa no separador **tarefa Exportação.** Se a tarefa falhar, consulte a página de informação detalhada para um relatório completo.
4. Quando a tarefa estiver concluída, o seu áudio está disponível para download no separador **Audio Library.**
5. Clique **em Baixar**. Agora está pronto para usar o seu áudio afinado personalizado nas suas apps ou produtos.

### <a name="supported-audio-formats"></a>Formatos áudio suportados

| Formato | Taxa de amostra de 16 kHz | Taxa de amostra de 24 kHz |
|--------|--------------------|--------------------|
| ondulação | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | áudio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Consulte também

* [API de áudio longo](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
