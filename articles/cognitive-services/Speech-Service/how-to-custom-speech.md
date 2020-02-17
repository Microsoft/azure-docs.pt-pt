---
title: Começar com discurso personalizado - serviço de discurso
titleSuffix: Azure Cognitive Services
description: Custom Speech é um conjunto de ferramentas online que lhe permitem avaliar e melhorar a nossa precisão de discurso a texto para as suas aplicações, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de ficheiros áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: dapine
ms.openlocfilehash: 0788ff84cd5b0d50e0798d0d72ee84b47cb5f0b1
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368323"
---
# <a name="what-is-custom-speech"></a>O que é discurso personalizado?

[Custom Speech](https://aka.ms/customspeech) é um conjunto de ferramentas online que lhe permitem avaliar e melhorar a precisão de discurso a texto da Microsoft para as suas aplicações, ferramentas e produtos. Tudo o que é preciso para começar são um punhado de ficheiros áudio de teste. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.

## <a name="whats-in-custom-speech"></a>O que há no Discurso Personalizado?

Antes de poder fazer qualquer coisa com discurso personalizado, precisará de uma conta Azure e uma subscrição de serviço de Fala. Uma vez que tenha uma conta, pode preparar os seus dados, treinar e testar os seus modelos, inspecionar a qualidade de reconhecimento, avaliar a precisão e, em última análise, implementar e usar o modelo personalizado de fala a texto.

Este diagrama destaca as peças que compõem o [portal de Discurso Personalizado](https://aka.ms/customspeech). Use os links abaixo para saber mais sobre cada passo.

![Destaca os diferentes componentes que compõem o portal de Discurso Personalizado.](./media/custom-speech/custom-speech-overview.png)

1. [Subscreva e crie um projeto](#set-up-your-azure-account) - Crie uma conta Azure e subscreva o serviço de Fala. Esta subscrição unificada dá-lhe acesso ao discurso-a-texto, texto-a-fala, tradução da fala e portal de [discurso personalizado](https://speech.microsoft.com/customspeech). Em seguida, usando a subscrição do serviço Speech, crie o seu primeiro projeto De Discurso Personalizado.

2. [Enviar dados](how-to-custom-speech-test-data.md) de teste - Enviar dados de teste (ficheiros áudio) para avaliar a oferta de discurso sms da Microsoft para as suas aplicações, ferramentas e produtos.

3. [Inspecione](how-to-custom-speech-inspect-data.md) a qualidade do reconhecimento - Utilize o [portal De Discurso Personalizado](https://speech.microsoft.com/customspeech) para reproduzir o áudio carregado e inspecionar a qualidade de reconhecimento da fala dos seus dados de teste. Para medições quantitativas, consulte [Inspecione os dados](how-to-custom-speech-inspect-data.md).

4. [Avaliar a precisão](how-to-custom-speech-evaluate-data.md) - Avaliar a precisão do modelo de fala-a-texto. O [portal de discurso personalizado](https://speech.microsoft.com/customspeech) fornecerá uma Taxa de Erro de *Palavras,* que pode ser usada para determinar se é necessário treino adicional. Se estiver satisfeito com a precisão, pode utilizar diretamente o serviço de Fala APIs. Se quiser melhorar a precisão em uma média relativa de 5% - 20%, use o separador **Formação** no portal para fazer upload de dados de formação adicionais, como transcrições com rótulo humano e texto relacionado.

5. [Treine o modelo](how-to-custom-speech-train-model.md) - Melhore a precisão do seu modelo de discurso a texto, fornecendo transcrições escritas (10-1.000 horas) e texto relacionado (<200 MB) juntamente com os seus dados de teste de áudio. Estes dados ajudam a formar o modelo de discurso a texto. Depois do treino, teste novamente, e se estiver satisfeito com o resultado, pode implementar o seu modelo.

6. [Implemente o modelo](how-to-custom-speech-deploy-model.md) - Crie um ponto final personalizado para o seu modelo de discurso a texto e use-o nas suas aplicações, ferramentas ou produtos.

## <a name="set-up-your-azure-account"></a>Configurar a sua conta Azure

É necessária uma subscrição do serviço Speech antes de poder utilizar o [portal De Discurso Personalizado](https://speech.microsoft.com/customspeech) para criar um modelo personalizado. Siga estas instruções para criar uma subscrição padrão do serviço de Discurso: [Criar uma subscrição de discurso](get-started.md#try-the-speech-service-using-a-new-azure-account).

> [!NOTE]
> Por favor, certifique-se de criar subscrições padrão (S0), as subscrições de teste gratuito (F0) não são suportadas.

Depois de criar uma conta Azure e uma subscrição de serviço speech, terá de iniciar sessão no [portal Custom Speech](https://speech.microsoft.com/customspeech) e ligar a sua subscrição.

1. Obtenha a sua chave de subscrição do serviço Speech no portal Azure.
2. Inscreva-se no [portal de discurso personalizado.](https://aka.ms/custom-speech)
3. Selecione a subscrição em que precisa de trabalhar e crie um projeto de fala.
4. Se quiser modificar a sua subscrição, utilize o ícone **de engrenagem** localizado na navegação superior.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no [portal de Discurso Personalizado](https://speech.microsoft.com/customspeech). Cada projeto é específico de um domínio e país/língua. Por exemplo, você pode criar um projeto para call centers que usam inglês nos Estados Unidos.

Para criar o seu primeiro projeto, selecione o **discurso Discurso-a-texto/Discurso personalizado,** em seguida, clique em **New Project**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de ter criado um projeto, deve ver quatro separadores: **Dados,** **Testes,** **Treino**e **Implantação**. Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [portal Custom Speech](https://aka.ms/custom-speech) foi recentemente atualizado! Se criou projetos anteriores com dados, modelos, testes e pontos finais publicados no portal CRIS ou com APIs, é necessário criar um novo projeto no novo portal para se conectar a estas entidades antigas.

## <a name="next-steps"></a>Passos seguintes

* [Prepare e teste os seus dados](how-to-custom-speech-test-data.md)
* [Inspecione os seus dados](how-to-custom-speech-inspect-data.md)
* [Avaliar os seus dados](how-to-custom-speech-evaluate-data.md)
* [Treine o seu modelo](how-to-custom-speech-train-model.md)
* [Implante o seu modelo](how-to-custom-speech-deploy-model.md)
