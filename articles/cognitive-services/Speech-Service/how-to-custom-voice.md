---
title: Melhorar a síntese com voz personalizada - serviço de fala
titleSuffix: Azure Cognitive Services
description: Custom Voice é um conjunto de ferramentas on-line que lhe permitem criar uma voz única e reconhecível para a sua marca. Tudo o que é preciso para começar são um punhado de ficheiros áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência personalizada de discurso a texto.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: 6ae164b1300119481e495e647dd491beafdd5241
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025083"
---
# <a name="get-started-with-custom-voice"></a>Introdução à Voz Personalizada

[Custom Voice](https://aka.ms/customvoice) é um conjunto de ferramentas on-line que lhe permitem criar uma voz única e reconhecível para a sua marca. Tudo o que é preciso para começar são um punhado de ficheiros áudio e as transcrições associadas. Siga os links abaixo para começar a criar uma experiência de texto-a-fala personalizada.

## <a name="whats-in-custom-voice"></a>O que há na Voz Personalizada?

Antes de começar com a Voz Personalizada, vai precisar de uma conta Azure e uma subscrição de serviço de discurso. Uma vez criada uma conta, pode preparar os seus dados, treinar e testar os seus modelos, avaliar a qualidade da voz e, em última análise, implementar o seu modelo de voz personalizado.

O diagrama abaixo destaca os passos para criar um modelo de voz personalizado utilizando o [portal Voz Personalizada](https://aka.ms/customvoice). Use os links para aprender mais.

![Diagrama de arquitetura de voz personalizada](media/custom-voice/custom-voice-diagram.png)

1. [Inscreva-se e crie um projeto](#set-up-your-azure-account) - Crie uma conta Azure e crie uma subscrição de serviço de discurso. Esta subscrição unificada dá-lhe acesso a discurso-a-texto, texto-a-discurso, tradução de voz e portal de Voz Personalizada. Em seguida, utilizando a subscrição do serviço Speech, crie o seu primeiro projeto De Voz Personalizada.

2. [Upload datas](how-to-custom-voice-create-voice.md#upload-your-datasets) - Upload datas (áudio e texto) utilizando o portal De Voz Personalizada ou API de Voz Personalizada. A partir do portal, pode investigar e avaliar pontuações de pronúncia e rácios sinal-ruído. Para obter mais informações, consulte [Como preparar dados para a Voz Personalizada.](how-to-custom-voice-prepare-data.md)

3. [Treine o seu modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model) – Utilize os seus dados para criar um modelo de voz text-to-speech personalizado. Pode treinar um modelo em diferentes línguas. Depois do treino, teste o seu modelo, e se estiver satisfeito com o resultado, pode implementar o modelo.

4. [Implemente o seu modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint) - Crie um ponto final personalizado para o seu modelo de voz text-to-speech e use-o para síntese de fala nos seus produtos, ferramentas e aplicações.

## <a name="custom-neural-voices"></a>Vozes neurais personalizadas

A capacidade de personalização de voz neural está atualmente em pré-visualização pública, limitada a clientes selecionados. Preencha este [formulário de inscrição](https://go.microsoft.com/fwlink/?linkid=2108737) para começar.

> [!NOTE]
> Como parte do compromisso da Microsoft em conceber IA responsável, a nossa intenção é proteger os direitos das pessoas e da sociedade, e fomentar interações transparentes entre humanos e computadores. Por esta razão, a Custom Neural Voice não está geralmente disponível para todos os clientes. Só poderá aceder à tecnologia depois de as suas aplicações serem revistas e comprometeu-se a usá-la em alinhamento com os nossos princípios éticos. Saiba mais sobre o nosso [processo de aplicação.](./concepts-gating-overview.md)

## <a name="set-up-your-azure-account"></a>Crie a sua conta Azure

É necessária uma subscrição de serviço de fala antes de poder utilizar o portal Discurso Personalizado para criar um modelo personalizado. Siga estas instruções para criar uma subscrição de serviço de fala em Azure. Se não tiver uma conta Azure, pode inscrever-se para uma nova.  

Depois de criar uma conta Azure e uma subscrição de serviço de Discurso, terá de iniciar scontabilidade no portal 'Voz Personalizada' e ligar a sua subscrição.

1. Obtenha a chave de subscrição do serviço Speech a partir do portal Azure.
2. Inscreva-se no [portal Voz Personalizada.](https://aka.ms/custom-voice)
3. Selecione a sua subscrição e crie um projeto de fala.
4. Se quiser mudar para outra subscrição do Speech, utilize o ícone de engrenagem localizado na navegação superior.

> [!NOTE]
> Deve ter uma tecla F0 ou S0 criada em Azure antes de poder utilizar o serviço.

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no portal Voz Personalizada. Cada projeto é específico de um país/língua e do sexo da voz que pretende criar. Por exemplo, pode criar um projeto para uma voz feminina para os bots de chat do seu call center que usam inglês nos Estados Unidos (en-US).

Para criar o seu primeiro projeto, selecione o **separador Texto-a-Voz/Voz Personalizada** e, em seguida, clique em **Novo Projeto**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de ter criado um projeto, verá quatro separadores: **Dados,** **Formação,** **Testes** e **Implementação.** Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [portal Voz Personalizada](https://aka.ms/custom-voice) foi recentemente atualizado! Se criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, precisa de criar um novo projeto no novo portal para ligar a estas entidades antigas.

## <a name="next-steps"></a>Próximos passos

- [Preparar dados de voz personalizados](how-to-custom-voice-prepare-data.md)
- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave as suas amostras de voz](record-custom-voice-samples.md)