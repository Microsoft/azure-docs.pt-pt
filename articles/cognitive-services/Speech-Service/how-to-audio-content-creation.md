---
title: Criação de Conteúdo Sonoro - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: Audio Content Creation é uma ferramenta online que permite personalizar e afinar a saída de texto-a-voz da Microsoft para as suas apps e produtos.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: ab0d2b8d95b4cb5996dd93fa0bb24085c9de26d5
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331541"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Melhorar a síntese com a criação de Conteúdo Áudio

[Audio Content Creation](https://aka.ms/audiocontentcreation) é uma ferramenta online que permite personalizar e afinar a saída de texto-a-voz da Microsoft para as suas apps e produtos. Pode utilizar esta ferramenta para afinar vozes públicas e personalizadas para expressões naturais mais precisas e gerir a sua saída na nuvem.

A ferramenta audio content creation baseia-se na linguagem de marcação da síntese da [fala (SSML)](speech-synthesis-markup.md). Para simplificar a personalização e a sintonização, a Audio Content Creation permite-lhe inspecionar visualmente as suas saídas de texto para a fala em tempo real.

## <a name="how-does-it-work"></a>Como funciona?

Este diagrama mostra os passos que é necessário para afinar e exportar saídas personalizadas de fala-texto. Use os links abaixo para saber mais sobre cada passo.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. O primeiro passo é [criar uma conta Azure, registar um recurso da Fala e obter uma chave de subscrição.](#create-a-speech-resource) Depois de ter uma chave de subscrição, pode usá-la para ligar para o serviço de Fala e para aceder à [Criação](https://aka.ms/audiocontentcreation)de Conteúdos Áudio .
2. [Crie um ficheiro de afinação](#create-an-audio-tuning-file) de áudio utilizando texto simples ou SSML.
3. Escolha a voz e a linguagem que gostaria de sintonizar. A Criação de Conteúdos áudio inclui todas as vozes de texto para discurso da [Microsoft.](language-support.md#text-to-speech) Pode usar a voz padrão, neural ou personalizada.
   >[!NOTE]
   > O acesso fechado está disponível para Vozes Neurais Personalizadas, que lhe permitem criar vozes de alta definição semelhantes à fala de som natural. Para mais detalhes, consulte o [processo de Gating](https://aka.ms/ignite2019/speech/ethics).

4. Reveja o resultado predefinido. Em seguida, use a ferramenta de afinação para ajustar a pronúncia, o pitch, o ritmo, a entoação, o estilo de voz e muito mais. Para obter uma lista completa de opções, consulte linguagem de [marcação](speech-synthesis-markup.md)de síntese de fala .
5. Guarde e [exporte o seu áudio afinado.](#export-tuned-audio) Quando guardar a pista de afinação do sistema, pode continuar a trabalhar e a iterar na saída. Quando estiver satisfeito com a saída, pode criar uma tarefa de criação de áudio com a funcionalidade de exportação. Pode observar o estado da tarefa de exportação e descarregar a saída para utilização com as suas apps e produtos.
6. O último passo é usar a voz afinada personalizada nas suas apps e produtos.

## <a name="create-a-speech-resource"></a>Criar um recurso da Fala

Siga estes passos para criar um recurso da Fala e conectá-lo com o Speech Studio.

1. Siga estas instruções para [se inscrever para uma conta Azure](get-started.md#new-resource) e criar um recurso da [Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource). Certifique-se de que o seu nível de preços está definido para **S0**. Se estiver a usar uma das vozes neurais, certifique-se de que cria o seu recurso numa [região apoiada.](regions.md#standard-and-neural-voices)
2. Inscreva-se na [Criação](https://aka.ms/audiocontentcreation)de Conteúdos Áudio .
3. Selecione um projeto existente ou clique em **Criar Novo**.
4. Pode modificar a sua subscrição a qualquer momento com a opção **Definições,** localizada na navegação superior.

## <a name="create-an-audio-tuning-file"></a>Criar um ficheiro de afinação de áudio

Existem duas formas de colocar o seu conteúdo na ferramenta Audio Content Creation.

**Opção 1:**

1. Depois de iniciar sessão na [Audio Content Creation,](https://aka.ms/audiocontentcreation)clique em **Audio Tuning** para criar um novo ficheiro de afinação de áudio.
2. Quando a janela de edição aparecer, pode inserir até 10.000 caracteres.
3. Não se esqueça de salvar.

**Opção 2:**

1. Depois de iniciar sessão na [Audio Content Creation,](https://aka.ms/audiocontentcreation)clique em **Carregar** para importar um ou mais ficheiros de texto. Tanto o texto simples como o SSML são suportados.
2. Ao fazer o upload dos seus ficheiros de texto, certifique-se de que o conteúdo satisfaz estes requisitos.

   | Propriedade | Valor / Notas |
   |----------|---------------|
   | Formato de ficheiro | Texto simples (.txt)<br/> Texto SSML (.txt)<br/> Ficheiros zip não são suportados |
   | Formato de codificação | UTF-8 |
   | Nome de ficheiro | Cada ficheiro deve ter um nome único. Os duplicados não são suportados. |
   | Comprimento do texto | Os ficheiros de texto não devem exceder 10.000 caracteres. |
   | Restrições SSML | Cada ficheiro SSML só pode conter uma única peça de SSML. |

### <a name="plain-text-example"></a>Exemplo de texto simples

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Exemplo de texto SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportar áudio afinado

Depois de ter revisto a sua saída de áudio e estar satisfeito com a sua afinação e ajuste, pode exportar o áudio.

1. A partir da ferramenta [Audio Content Creation,](https://aka.ms/audiocontentcreation) clique em **Exportar** para criar uma tarefa de criação de áudio.
2. Escolha o formato de saída para o seu áudio afinado. Uma lista de formatos suportados e taxas de amostra estão disponíveis abaixo.
3. Pode visualizar o estado da tarefa no separador de **tarefas exportação.** Se a tarefa falhar, consulte a página de informação detalhada para obter um relatório completo.
4. Quando a tarefa estiver concluída, o seu áudio está disponível para download no separador **Audio Library.**
5. Clique em **Transferir**. Agora está pronto para usar o seu áudio personalizado nas suas apps ou produtos.

### <a name="supported-audio-formats"></a>Formatos de áudio suportados

| Formato | Taxa de amostra de 16 kHz | Taxa de amostra geminada de 24 kHz |
|--------|--------------------|--------------------|
| acenar | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| mp3 | áudio-16khz-128kbitrate-mono-mp3 | áudio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Consulte também

* [API de áudio longo](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Estúdio de Discurso](https://speech.microsoft.com)
