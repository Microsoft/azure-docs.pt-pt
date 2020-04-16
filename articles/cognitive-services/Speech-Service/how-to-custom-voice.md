---
title: Melhorar a síntese com voz personalizada - serviço de voz
titleSuffix: Azure Cognitive Services
description: Custom Voice é um conjunto de ferramentas online que lhe permitem criar uma voz única e reconhecível para a sua marca. Tudo o que é preciso para começar são um punhado de ficheiros áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 2aacf8881a57f0677177cd341a0ddc63224b26b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402056"
---
# <a name="get-started-with-custom-voice"></a>Introdução à Voz Personalizada

[Custom Voice](https://aka.ms/customvoice) é um conjunto de ferramentas online que lhe permitem criar uma voz única e reconhecível para a sua marca. Tudo o que é preciso para começar são um punhado de ficheiros áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência personalizada de texto-a-fala.

## <a name="whats-in-custom-voice"></a>O que há na Voz Personalizada?

Antes de começar com a Custom Voice, você precisará de uma conta Azure e uma subscrição de serviço speech. Depois de criar uma conta, pode preparar os seus dados, treinar e testar os seus modelos, avaliar a qualidade de voz e, em última análise, implementar o seu modelo de voz personalizado.

O diagrama abaixo realça os passos para criar um modelo de voz personalizado usando o [portal Voz Personalizada](https://aka.ms/customvoice). Use os links para saber mais.

![Diagrama de arquitetura de voz personalizada](media/custom-voice/custom-voice-diagram.png)

1. [Subscreva e crie um projeto](#set-up-your-azure-account) - Crie uma conta Azure e crie uma subscrição de serviço speech. Esta subscrição unificada dá-lhe acesso ao discurso-a-texto, texto-a-fala, tradução da fala e portal voz personalizada. Em seguida, usando a subscrição do serviço Speech, crie o seu primeiro projeto Custom Voice.

2. [Enviar dados](how-to-custom-voice-create-voice.md#upload-your-datasets) - Enviar dados (áudio e texto) utilizando o portal De Voz Personalizada ou API de Voz Personalizada. A partir do portal, você pode investigar e avaliar pontuações de pronúncia e rácios sinal-ruído. Para mais informações, consulte [Como preparar dados para Voz Personalizada](how-to-custom-voice-prepare-data.md).

3. [Treine o seu modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Utilize os seus dados para criar um modelo personalizado de voz de texto a voz. Pode treinar um modelo em diferentes línguas. Depois do treino, teste o seu modelo, e se estiver satisfeito com o resultado, pode implementar o modelo.

4. [Implemente o seu modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - Crie um ponto final personalizado para o seu modelo de voz de texto a voz e use-o para síntese de fala nos seus produtos, ferramentas e aplicações.

## <a name="custom-neural-voices"></a>Vozes neurais personalizadas

A capacidade de personalização de voz neural encontra-se atualmente na pré-visualização pública, limitada a clientes selecionados. Preencha este formulário de [inscrição](https://go.microsoft.com/fwlink/?linkid=2108737) para começar.

> [!NOTE]
> Como parte do compromisso da Microsoft em conceber IA responsável, a nossa intenção é proteger os direitos dos indivíduos e da sociedade, e fomentar interações transparentes entre humanos e computadores. Por esta razão, a Custom Neural Voice não está geralmente disponível para todos os clientes. Só poderá ter acesso à tecnologia depois de as suas aplicações serem revistas e comprometeu-se a utilizá-la de acordo com os nossos princípios éticos. Saiba mais sobre o nosso processo de gating de [aplicação.](https://aka.ms/custom-neural-gating-overview)

## <a name="set-up-your-azure-account"></a>Configurar a sua conta Azure

É necessária uma subscrição do serviço Speech antes de poder utilizar o portal De Discurso Personalizado para criar um modelo personalizado. Siga estas instruções para criar uma subscrição de serviço de Fala em Azure. Se não tiver uma conta Azure, pode inscrever-se para uma nova.  

Depois de criar uma conta Azure e uma subscrição de serviço speech, terá de iniciar sessão no portal Custom Voice e ligar a sua subscrição.

1. Obtenha a sua chave de subscrição do serviço Speech no portal Azure.
2. Inscreva-se no [portal Voz Personalizada](https://aka.ms/custom-voice).
3. Selecione a sua subscrição e crie um projeto de fala.
4. Se quiser mudar para outra subscrição do Speech, utilize o ícone de engrenagem localizado na navegação superior.

> [!NOTE]
> O serviço Custom Voice NÃO suporta a chave de teste gratuita de 30 dias. Você deve ter uma chave F0 ou S0 criada em Azure antes de poder usar o serviço.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no portal Voz Personalizada. Cada projeto é específico de um país/língua e do sexo da voz que quer criar. Por exemplo, você pode criar um projeto para uma voz feminina para os bots de chat do seu call center que usam inglês nos Estados Unidos (en-EUA).

Para criar o seu primeiro projeto, selecione o separador **Texto-a-Voz/Voz Personalizada** e, em seguida, clique em **New Project**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de ter criado um projeto, verá quatro separadores: **Dados,** **Treino,** **Teste**e **Implementação.** Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [portal Custom Voice](https://aka.ms/custom-voice) foi recentemente atualizado! Se criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, é necessário criar um novo projeto no novo portal para se conectar a estas entidades antigas.

## <a name="next-steps"></a>Passos seguintes

- [Preparar dados de voz personalizadas](how-to-custom-voice-prepare-data.md)
- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave as amostras de voz](record-custom-voice-samples.md)
