---
title: Visão geral da voz neural personalizada - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Custom Neural Voice é uma funcionalidade de texto-a-voz que lhe permite criar uma voz sintética personalizada única para as suas aplicações, fornecendo os seus próprios dados de áudio como uma amostra.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2020
ms.author: trbye
ms.openlocfilehash: 9e230ff4e84eba7b61846540fef2915765fa4f77
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509700"
---
# <a name="what-is-custom-neural-voice"></a>O que é voz neural personalizada?

Custom Neural Voice é uma funcionalidade [text-to-Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) (TTS) que lhe permite criar uma voz sintética personalizada única para as suas aplicações, fornecendo os seus próprios dados áudio como uma amostra. O texto-a-voz funciona convertendo texto em discurso sintético usando um modelo de aprendizagem automática que soa como uma voz escolhida. Com a [API REST,](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)pode permitir que as suas aplicações falem com [vozes pré-construídas](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#neural-voices) ou com os seus próprios modelos [de voz personalizados](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-custom-voice-prepare-data) desenvolvidos através da funcionalidade Voz Neural Personalizada. A Voz Neural Personalizada é baseada na tecnologia Neural TTS que cria uma voz sonora natural que é muitas vezes indistinguível quando comparada com uma voz humana.
A voz sonora realista e natural da Custom Neural Voice pode representar marcas, personificar máquinas e permitir que os utilizadores interajam com aplicações de forma natural.

> [!NOTE]
> A funcionalidade Voz Neural Personalizada requer registo, e o acesso ao mesmo é limitado com base nos critérios de elegibilidade e utilização da Microsoft. Os clientes que desejem utilizar esta funcionalidade são obrigados a registar os seus casos de utilização através do [formulário de admissão.](https://aka.ms/customneural)

## <a name="the-basics-of-custom-neural-voice"></a>Os fundamentos da Voz Neural Personalizada

A tecnologia Neural TTS subjacente utilizada para voz neural personalizada é composta por três componentes principais: Analisador de Texto, Modelo Acústico Neural e Vocoder Neural. Para gerar a fala sintética natural a partir do texto, o texto é a primeira entrada no Analisador de Texto, que fornece a saída sob a forma de sequência de fonme. Um fonme é uma unidade básica de som que distingue uma palavra da outra numa determinada língua. Uma sequência de fones define as pronúncias das palavras fornecidas no texto. 

Em seguida, a sequência de fonme vai para o Modelo Acústico Neural para prever características acústicas que definem sinais de fala, tais como o timbre, o estilo de fala, velocidade, entoações e padrões de stress. Finalmente, o Vocoder Neural converte as características acústicas em ondas audíveis para que a fala sintética seja gerada.

![Imagem de introdução para voz neural personalizada.](./media/custom-voice/cnv-intro.png)

Os modelos de voz Neural TTS são treinados usando redes neuronais profundas com base nas amostras de gravação de vozes humanas. Neste [blog,](https://techcommunity.microsoft.com/t5/azure-ai/neural-text-to-speech-extends-support-to-15-more-languages-with/ba-p/1505911)descrevemos como o Neural TTS funciona com modelos de síntese de fala neural de última geração. O blog também explica como um modelo de base universal pode ser adaptado com menos de 2 horas de dados de fala (ou menos de 2.000 declarações gravadas) de um orador-alvo, e aprender a falar na voz desse orador-alvo. Para ler sobre como um vocoder neural é treinado, consulte o post do [blog](https://techcommunity.microsoft.com/t5/azure-ai/azure-neural-tts-upgraded-with-hifinet-achieving-higher-audio/ba-p/1847860).

Com a capacidade de personalização da Voz Neural Personalizada, pode adaptar o motor Neural TTS para melhor se adaptar aos cenários do utilizador. Para criar uma voz neural personalizada, utilize o [Speech Studio](https://speech.microsoft.com/customvoice) para carregar os scripts áudio e correspondentes gravados, treine o modelo e implante a voz num ponto final personalizado. Dependendo do caso de utilização, a Voz Neural Personalizada pode ser usada para converter texto em linguagem em tempo real (por exemplo, usado num assistente virtual inteligente) ou gerar conteúdo sonoro offline (por exemplo, usado como em audiolus ou instruções em aplicações de e-learning) com a entrada de texto fornecida pelo utilizador. Isto é disponibilizado através da [API REST,](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)do [Speech SDK,](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started-text-to-speech?tabs=script%2Cwindowsinstall&pivots=programming-language-csharp)ou de um [portal web](https://speech.microsoft.com/audiocontentcreation).

## <a name="terms-and-definitions"></a>Termos e definições

| **Termo**      | **Definição**                                                                                                                                                                                                                                                                                                                                                                                       |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Modelo de voz   | Um modelo de texto-a-discurso que pode imitar as características vocais únicas de um orador alvo. Um *modelo de voz* também é conhecido como fonte de *voz* ou *voz sintética.* Um modelo de voz é um conjunto de parâmetros em formato binário que não é legível para o homem e não contém gravações áudio. Não pode ser projetado para derivar ou construir o áudio de uma voz humana. |
| Talento de voz  | Indivíduos ou altifalantes-alvo cujas vozes são gravadas e usadas para criar modelos de voz que se destinam a soar como a voz do talento de voz.                                                                                                                                                                                                                                                   |
| TTS padrão  | O método padrão, ou "tradicional", de TTS que decompõe a linguagem falada em snippets fonéticos para que possam ser remisturados e combinados usando programação clássica ou métodos estatísticos.                                                                                                                                                                                                    |
| TTS neurais    | O TTS neural sintetiza o discurso usando redes neuronais profundas que "aprenderam" a forma como a fonética é combinada na fala humana natural, em vez de usar programação processual ou métodos estatísticos. Além das gravações de um talento de voz alvo, o Neural TTS utiliza uma biblioteca/modelo base de origem que é construído com gravações de voz de muitos alto-falantes diferentes.          |
| Dados de preparação | Um conjunto de dados de treino de voz neural personalizado que inclui as gravações áudio do talento de voz e as transcrições de texto associadas.                                                                                                                                                                                                                                                               |
| Persona       | Uma pessoa descreve quem queres que esta voz seja. Um bom design de persona irá informar toda a criação de voz se está a escolher um modelo de voz disponível já criado, ou a começar do zero, lançando e gravando um novo talento de voz.                                                                                                |
| Script        | Um script é um ficheiro de texto que contém as expressões a serem ditas pelo seu talento de voz. (O termo "*expressões*" abrange frases completas e frases mais curtas.)                                                                                                                                                                                                                               |

## <a name="responsible-use-of-ai"></a>Utilização responsável da IA

Para aprender a utilizar a Voz Neural Personalizada de forma responsável, consulte a nota de [transparência](https://docs.microsoft.com/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice). As notas de transparência da Microsoft destinam-se a ajudá-lo a entender como funciona a nossa tecnologia de IA, as escolhas que os proprietários do sistema podem fazer que influenciam o desempenho e o comportamento do sistema, e a importância de pensar em todo o sistema, incluindo a tecnologia, as pessoas e o ambiente.

## <a name="next-steps"></a>Passos seguintes

* [Introdução à Voz Personalizada](how-to-custom-voice.md)
* [Criar e utilizar um ponto final de voz personalizado](how-to-custom-voice-create-voice.md)