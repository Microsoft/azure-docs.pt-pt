---
title: Começar com Discurso Personalizado - Serviço de Fala
titleSuffix: Azure Cognitive Services
description: O Custom Speech é um conjunto de ferramentas online que lhe permitem avaliar e melhorar a nossa precisão fala-texto para as suas aplicações, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de ficheiros de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: b9ac473c3864b4be06c978e7d6a3555bb4550d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056844"
---
# <a name="what-is-custom-speech"></a>O que é Discurso Personalizado?

[O Custom Speech](https://aka.ms/customspeech) é um conjunto de ferramentas online que lhe permitem avaliar e melhorar a precisão fala-texto da Microsoft para as suas aplicações, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de ficheiros de áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.

## <a name="whats-in-custom-speech"></a>O que há no Discurso Personalizado?

Antes de poder fazer qualquer coisa com o Discurso Personalizado, vai precisar de uma conta Azure e de uma subscrição de serviço de discurso. Assim que tiver uma conta, pode preparar os seus dados, treinar e testar os seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, em última análise, implementar e utilizar o modelo personalizado de voz a texto.

Este diagrama destaca as peças que compõem o [portal Discurso Personalizado.](https://aka.ms/customspeech) Use os links abaixo para saber mais sobre cada passo.

![Destaca os diferentes componentes que compõem o portal Discurso Personalizado.](./media/custom-speech/custom-speech-overview.png)

1. [Inscreva-se e crie um projeto](#set-up-your-azure-account) - Crie uma conta Azure e subscreva o serviço Discurso. Esta subscrição unificada dá-lhe acesso a discurso-a-texto, texto-a-discurso, tradução de fala e portal de [discurso personalizado](https://speech.microsoft.com/customspeech). Em seguida, utilizando a subscrição do serviço Speech, crie o seu primeiro projeto de Discurso Personalizado.

2. [Upload datas de teste](how-to-custom-speech-test-data.md) - Carreie os dados do teste (ficheiros áudio) para avaliar a oferta de voz para texto da Microsoft para as suas aplicações, ferramentas e produtos.

3. [Inspecione a qualidade](how-to-custom-speech-inspect-data.md) do reconhecimento - Utilize o [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecione a qualidade de reconhecimento da fala dos seus dados de teste. Para medições quantitativas, consulte [os dados do Inspecione](how-to-custom-speech-inspect-data.md).

4. [Avaliar a precisão](how-to-custom-speech-evaluate-data.md) - Avaliar a precisão do modelo fala-a-texto. O [portal De Discurso Personalizado](https://speech.microsoft.com/customspeech) fornecerá uma Taxa de Erro de *Palavras,* que pode ser usada para determinar se é necessária formação adicional. Se estiver satisfeito com a precisão, pode utilizar diretamente os APIs do serviço de fala. Se quiser melhorar a precisão numa média relativa de 5% - 20%, use o **separador Training** no portal para carregar dados de formação adicionais, tais como transcrições com rótulo humano e texto relacionado.

5. [Melhore a precisão](how-to-custom-speech-improve-accuracy.md) - Escolha dados de formação adicionais estrategicamente para melhorar a qualidade do modelo de fala-texto com base no seu cenário.

6. [Treine o modelo](how-to-custom-speech-train-model.md) - Melhore a precisão do seu modelo de discurso-texto fornecendo transcrições escritas (10-1.000 horas) e texto relacionado (<200 MB) juntamente com os seus dados de teste áudio. Estes dados ajudam a formar o modelo de discurso-texto. Depois do treino, reteste e se estiver satisfeito com o resultado, pode implementar o seu modelo.

7. [Implementar o modelo](how-to-custom-speech-deploy-model.md) - Crie um ponto final personalizado para o seu modelo de voz a texto e use-o nas suas aplicações, ferramentas ou produtos.

## <a name="set-up-your-azure-account"></a>Crie a sua conta Azure

É necessária uma subscrição de serviço de fala antes de poder utilizar o [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Siga estas instruções para criar uma subscrição padrão do serviço de discurso: [Criar uma subscrição de discurso](get-started.md#new-resource).

> [!NOTE]
> Por favor, certifique-se de criar subscrições padrão (S0), subscrições gratuitas (F0) não são suportadas.

Depois de criar uma conta Azure e uma subscrição de serviço de Discurso, terá de iniciar seduca no [portal Discurso Personalizado](https://speech.microsoft.com/customspeech) e ligar a sua subscrição.

1. Obtenha a chave de subscrição do serviço Speech a partir do portal Azure.
2. Iniciar s-in no [portal de discurso personalizado](https://aka.ms/custom-speech).
3. Selecione a subscrição em que precisa para trabalhar e criar um projeto de fala.
4. Se quiser modificar a sua subscrição, utilize o ícone **de engrenagem** localizado na navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no [portal Discurso Personalizado.](https://speech.microsoft.com/customspeech) Cada projeto é específico de um domínio e país/língua. Por exemplo, pode criar um projeto para call centers que usam inglês nos Estados Unidos.

Para criar o seu primeiro projeto, selecione o **discurso Discurso-a-Texto/Personalizado,** em seguida, clique em **Novo Projeto**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de ter criado um projeto, deverá ver quatro separadores: **Dados,** **Testes,** **Formação**e **Implantação.** Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [portal De Discurso Personalizado](https://aka.ms/custom-speech) foi recentemente atualizado! Se criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, precisa de criar um novo projeto no novo portal para ligar a estas entidades antigas.

## <a name="next-steps"></a>Passos seguintes

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Preparar o seu modelo](how-to-custom-speech-train-model.md)
* [Implemente o seu modelo](how-to-custom-speech-deploy-model.md)
