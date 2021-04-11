---
title: Visão geral da fala personalizada - Serviço de fala
titleSuffix: Azure Cognitive Services
description: O Custom Speech é um conjunto de ferramentas online que lhe permitem avaliar e melhorar a precisão de voz a texto da Microsoft para as suas aplicações, ferramentas e produtos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 5ffae530bcd8a7274b4b75c447591cf619012661
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387065"
---
# <a name="what-is-custom-speech"></a>O que é a Voz Personalizada?

[Custom Speech](https://aka.ms/customspeech) é um conjunto de ferramentas baseadas em UI que lhe permitem avaliar e melhorar a precisão de fala-texto da Microsoft para as suas aplicações e produtos. Tudo o que é preciso para começar é um punhado de ficheiros de áudio de teste. Siga os links deste artigo para começar a criar uma experiência personalizada de discurso a texto.

## <a name="whats-in-custom-speech"></a>O que há no Discurso Personalizado?

Antes de poder fazer qualquer coisa com o Discurso Personalizado, vai precisar de uma conta Azure e de uma subscrição de serviço de discurso. Depois de ter uma conta, pode preparar os seus dados, treinar e testar os seus modelos, inspecionar a qualidade do reconhecimento, avaliar a precisão e, em última análise, implementar e utilizar o modelo personalizado de voz a texto.

Este diagrama destaca as peças que compõem a [área de Discurso Personalizado do Estúdio da Fala.](https://aka.ms/customspeech) Use os links abaixo para saber mais sobre cada passo.

![Diagrama que destaca os componentes que compõem a área de Discurso Personalizado do Estúdio de Discurso.](./media/custom-speech/custom-speech-overview.png)

1. [Inscreva-se e crie um projeto.](#set-up-your-azure-account) Crie uma conta Azure e subscreva o serviço Discurso. Esta subscrição unificada dá-lhe acesso a discurso-a-texto, texto-a-discurso, tradução de fala e ao Estúdio da [Fala](https://speech.microsoft.com/customspeech). Em seguida, utilize a sua subscrição de serviço de Discurso para criar o seu primeiro projeto de Discurso Personalizado.

1. [Carregar dados de teste](./how-to-custom-speech-test-and-train.md). Faça upload de dados de teste (ficheiros áudio) para avaliar a oferta de voz para texto da Microsoft para as suas aplicações, ferramentas e produtos.

1. [Inspecione a qualidade do reconhecimento.](how-to-custom-speech-inspect-data.md) Utilize o [Estúdio de Fala](https://speech.microsoft.com/customspeech) para reproduzir áudio carregado e inspecione a qualidade de reconhecimento de voz dos seus dados de teste. Para medições quantitativas, consulte [os dados do Inspecione](how-to-custom-speech-inspect-data.md).

1. [Avaliar e melhorar a precisão.](how-to-custom-speech-evaluate-data.md) Avaliar e melhorar a precisão do modelo de discurso-texto. O [Estúdio de Fala](https://speech.microsoft.com/customspeech) fornecerá uma Taxa de Erro de *Palavras,* que pode utilizar para determinar se é necessária formação adicional. Se estiver satisfeito com a precisão, pode utilizar diretamente os APIs do serviço de fala. Se quiser melhorar a precisão numa média relativa de 5% a 20%, utilize o **separador Formação** no portal para carregar dados de formação adicionais, como transcrições com rótulo humano e textos relacionados.

1. [Treine e implemente um modelo.](how-to-custom-speech-train-model.md) Melhore a precisão do seu modelo de discurso-texto fornecendo transcrições escritas (10 a 1.000 horas) e texto relacionado (<200 MB) juntamente com os dados do seu teste áudio. Estes dados ajudam a formar o modelo de discurso-texto. Depois do treino, reteste. Se estiver satisfeito com o resultado, pode colocar o seu modelo num ponto final personalizado.

## <a name="set-up-your-azure-account"></a>Crie a sua conta Azure

Precisa de ter uma subscrição de serviço de conta Azure e Speech antes de poder utilizar o [Estúdio da Fala](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Se não tiver uma conta e subscrição, [experimente gratuitamente o serviço Desemação](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Por favor, certifique-se de criar uma subscrição padrão (S0). As assinaturas gratuitas (F0) não são suportadas.

Se planeia formar um modelo personalizado com **dados áudio,** escolha uma das seguintes regiões que tenham hardware dedicado disponível para treino. Isto reduzirá o tempo que leva para treinar um modelo e permitir-lhe-á usar mais áudio para treinar. Nestas regiões, o serviço Discurso utilizará até 20 horas de áudio para a formação; noutras regiões só utilizará até 8 horas.

* Leste da Austrália
* Canadá Central
* Índia Central
* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Centro-Norte
* Europa do Norte
* E.U.A. Centro-Sul
* Sudeste Asiático
* Sul do Reino Unido
* US Gov - Arizona
* US Gov - Virginia
* Europa Ocidental
* E.U.A. Oeste 2

Depois de criar uma conta Azure e uma subscrição de serviço de discurso, terá de iniciar sessão no Estúdio da [Fala](https://speech.microsoft.com/customspeech) e ligar a sua subscrição.

1. Inscreva-se no [Estúdio de Discurso.](https://aka.ms/custom-speech)
1. Selecione a subscrição em que precisa para trabalhar e crie um projeto de fala.
1. Se quiser modificar a sua subscrição, selecione o botão de engrenagem no menu superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em *projetos* no [Estúdio da Fala.](https://speech.microsoft.com/customspeech) Cada projeto é específico de um domínio e país/língua. Por exemplo, você pode criar um projeto para call centers que usam inglês nos Estados Unidos.

Para criar o seu primeiro projeto, selecione **Speech-to-text/Custom speech** e, em seguida, selecione **New Project**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de criar um projeto, deverá ver quatro separadores: **Dados,** **Testes,** **Formação** e **Implantação.** Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [Estúdio de Fala](https://aka.ms/custom-speech) anteriormente conhecido como "Portal da Fala Personalizada" foi recentemente atualizado! Se criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, precisa de criar um novo projeto no novo portal para ligar a estas entidades antigas.

## <a name="model-and-endpoint-lifecycle"></a>Modelo e ciclo de vida Endpoint

Os modelos mais antigos costumam tornar-se menos úteis ao longo do tempo, porque o modelo mais recente geralmente tem maior precisão. Por isso, os modelos base, bem como os modelos personalizados e os pontos finais criados através do portal estão sujeitos a expiração após 1 ano para adaptação e 2 anos para descodição. Consulte uma descrição detalhada no artigo do [modelo e do ciclo de vida do ponto final.](./how-to-custom-speech-model-and-endpoint-lifecycle.md)

## <a name="next-steps"></a>Passos seguintes

* [Prepare e teste os seus dados](./how-to-custom-speech-test-and-train.md)
* [Fiscalizar os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar e melhorar a precisão do modelo](how-to-custom-speech-evaluate-data.md)
* [Preparar e implementar um modelo](how-to-custom-speech-train-model.md)
