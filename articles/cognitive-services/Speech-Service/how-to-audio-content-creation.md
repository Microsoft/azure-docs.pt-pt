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
ms.openlocfilehash: 083580435c467a7d4b6a4cede0a821a2c271962f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589657"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Melhorar a síntese com a ferramenta Audio Content Creation

[Audio Content Creation](https://aka.ms/audiocontentcreation) é uma ferramenta online que permite personalizar e afinar a saída de texto-a-voz da Microsoft para as suas apps e produtos. Pode utilizar esta ferramenta para afinar vozes públicas e personalizadas para expressões naturais mais precisas e gerir a sua saída na nuvem.

A ferramenta audio content creation baseia-se na linguagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md). Para simplificar a personalização e a sintonização, a Audio Content Creation permite-lhe inspecionar visualmente as suas saídas de texto para a fala em tempo real.

## <a name="how-does-it-work"></a>Como funciona?

Este diagrama mostra os passos que é necessário para afinar as saídas de texto-a-fala. Use os links abaixo para saber mais sobre cada passo.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Configura risa a sua conta Azure e o recurso Speech](#set-up-your-azure-account-and-speech-resource) para começar.
2. [Crie um ficheiro de afinação](#create-an-audio-tuning-file) de áudio utilizando texto simples ou scripts SSML.
3. Escolha a voz e o idioma para o seu conteúdo de script. A Criação de Conteúdos áudio inclui todas as vozes de texto para discurso da [Microsoft.](language-support.md#text-to-speech) Pode usar a voz padrão, neural ou personalizada.
   >[!NOTE]
   > O acesso fechado está disponível para Vozes Neurais Personalizadas, que lhe permitem criar vozes de alta definição semelhantes à fala de som natural. Para mais detalhes, consulte o [processo de Gating](https://aka.ms/ignite2019/speech/ethics).

4. Reveja a saída de síntese padrão. Em seguida, melhorar a saída ajustando a pronúncia, quebrar, pitch, taxa, entoação, estilo de voz, e muito mais. Para obter uma lista completa de opções, consulte linguagem de [marcação](speech-synthesis-markup.md)de síntese de fala . Aqui está um [vídeo](https://youtu.be/mUvf2NbfuYU) para mostrar como afinar a produção de discurso com audio content Creation. 
5. Guarde e [exporte o seu áudio afinado.](#export-tuned-audio) Quando guardar a pista de afinação do sistema, pode continuar a trabalhar e a iterar na saída. Quando estiver satisfeito com a saída, pode criar uma tarefa de criação de áudio com a funcionalidade de exportação. Pode observar o estado da tarefa de exportação e descarregar a saída para utilização com as suas apps e produtos.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Configurar a sua conta Azure e recurso de Fala

1. Para trabalhar com a Audio Content Creation, tem de ter uma conta Azure. Pode criar uma conta Azure utilizando a sua Conta Microsoft. Siga estas instruções para [configurar uma conta Azure](get-started.md#new-resource). 
2. [Crie um recurso da Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) para a sua conta Azure. Certifique-se de que o seu nível de preços está definido para **S0**. Se estiver a usar uma das vozes neurais, certifique-se de que cria o seu recurso numa [região apoiada.](regions.md#standard-and-neural-voices)
2. Depois de obter a conta Azure e o recurso da fala, pode usar os serviços de fala e aceder à Criação de [Conteúdos Áudio.](https://aka.ms/audiocontentcreation)
3. Selecione o recurso da Fala em que precisa de trabalhar. Também pode criar um novo recurso da Fala aqui. 
4. Pode modificar o seu recurso De discurso a qualquer momento com a opção **Definições,** localizada na navegação superior.

## <a name="create-an-audio-tuning-file"></a>Criar um ficheiro de afinação de áudio

Existem duas formas de colocar o seu conteúdo na ferramenta Audio Content Creation.

**Opção 1:**

1. Clique em **Novo ficheiro** para criar um novo ficheiro de afinação de áudio.
2. Digite ou colhe o seu conteúdo na janela de edição. Os caracteres de cada ficheiro são até 20.000. Se o seu script for superior a 20.000 caracteres, pode utilizar a Opção 2 para dividir automaticamente o seu conteúdo em vários ficheiros. 
3. Não se esqueça de salvar.

**Opção 2:**

1. Clique em **Carregar** para importar um ou mais ficheiros de texto. Tanto o texto simples como o SSML são suportados.
2. Se o seu ficheiro de script for superior a 20.000 caracteres, por favor divida o ficheiro por parágrafos, por carácter ou por expressões regulares. 
3. Ao fazer o upload dos seus ficheiros de texto, certifique-se de que o ficheiro satisfaz estes requisitos.

   | Propriedade | Valor / Notas |
   |----------|---------------|
   | Formato de ficheiro | Texto simples (.txt)<br/> Texto SSML (.txt)<br/> Ficheiros zip não são suportados |
   | Formato de codificação | UTF-8 |
   | Nome de ficheiro | Cada ficheiro deve ter um nome único. Os duplicados não são suportados. |
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

## <a name="export-tuned-audio"></a>Exportar áudio afinado

Depois de ter revisto a sua saída de áudio e estar satisfeito com a sua afinação e ajuste, pode exportar o áudio.

1. Clique em **Exportar** para criar uma tarefa de criação de áudio. **A exportação para** a Audio Library é recomendada, uma vez que suporta a saída de áudio longa e a experiência completa de saída de áudio. Também pode baixar o áudio diretamente para o seu disco local, mas apenas os primeiros 10 minutos estão disponíveis. 
2. Escolha o formato de saída para o seu áudio afinado. Uma lista de formatos suportados e taxas de amostra estão disponíveis abaixo.
3. Pode visualizar o estado da tarefa no separador de **tarefas exportação.** Se a tarefa falhar, consulte a página de informação detalhada para obter um relatório completo.
4. Quando a tarefa estiver concluída, o seu áudio está disponível para download no separador **Audio Library.**
5. Clique em **Baixar**. Agora está pronto para usar o seu áudio personalizado nas suas apps ou produtos.

### <a name="supported-audio-formats"></a>Formatos de áudio suportados

| Formato | Taxa de amostra de 16 kHz | Taxa de amostra geminada de 24 kHz |
|--------|--------------------|--------------------|
| acenar | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | áudio-16khz-128kbitrate-mono-mp3 | áudio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Ver também

* [API de áudio longo](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
