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
ms.openlocfilehash: 45f4ca6349c14f21ce48a1fcf34b7fc8998dd278
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733521"
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

ATualmente, a Custom Voice suporta os níveis standard e neural. A Custom Neural Voice capacita os utilizadores a construir modelos de voz de maior qualidade, exigindo menos dados, e fornece medidas para o ajudar a implementar a IA de forma responsável. Recomendamos que utilize a Voz Neural Personalizada para desenvolver vozes mais realistas para interfaces de conversação mais naturais e permitir que os seus clientes e utilizadores finais beneficiem da mais recente tecnologia Text-to-Speech, de forma responsável. [Saiba mais sobre a Voz Neural Personalizada.](/legal/cognitive-services/speech-service/custom-neural-voice/transparency-note-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) 

> [!NOTE]
> Como parte do compromisso da Microsoft em conceber IA responsável, limitámos o uso da Custom Neural Voice. Só poderá aceder à tecnologia depois de as suas aplicações serem revistas e comprometeu-se a usá-la em alinhamento com os nossos princípios responsáveis de IA. Saiba mais sobre a nossa [política sobre o acesso ao limite](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) e aplique [aqui.](https://aka.ms/customneural) As [línguas](language-support.md#customization) e [regiões](regions.md#custom-voices) suportadas para a versão padrão e neural da Voz Personalizada são diferentes. Verifique os detalhes antes de começar.  

## <a name="set-up-your-azure-account"></a>Crie a sua conta Azure

É necessária uma subscrição de serviço de fala antes de poder utilizar o portal Discurso Personalizado para criar um modelo personalizado. Siga estas instruções para criar uma subscrição de serviço de fala em Azure. Se não tiver uma conta Azure, pode inscrever-se para uma nova.  

Depois de criar uma conta Azure e uma subscrição de serviço de Discurso, terá de iniciar scontabilidade no portal 'Voz Personalizada' e ligar a sua subscrição.

1. Obtenha a chave de subscrição do serviço Speech a partir do portal Azure.
2. Inscreva-se no [portal Voz Personalizada.](https://aka.ms/custom-voice)
3. Selecione a sua subscrição e crie um projeto de fala.
4. Se quiser mudar para outra subscrição do Speech, utilize o ícone de engrenagem localizado na navegação superior.

> [!NOTE]
> Tem de ter uma chave de serviço de F0 ou S0 Speech criada em Azure antes de poder utilizar o serviço. A Voz Neural Personalizada só suporta o nível S0. 

## <a name="how-to-create-a-project"></a>Como criar um projeto

Conteúdos como dados, modelos, testes e pontos finais são organizados em **Projetos** no portal Voz Personalizada. Cada projeto é específico de um país/língua e do sexo da voz que pretende criar. Por exemplo, pode criar um projeto para uma voz feminina para os bots de chat do seu call center que usam inglês nos Estados Unidos ('en-US').

Para criar o seu primeiro projeto, selecione o **separador Texto-a-Voz/Voz Personalizada** e, em seguida, clique em **Novo Projeto**. Siga as instruções fornecidas pelo assistente para criar o seu projeto. Depois de ter criado um projeto, verá quatro separadores: **Dados,** **Formação,** **Testes** e **Implementação.** Utilize os links fornecidos nos [próximos passos](#next-steps) para aprender a utilizar cada separador.

> [!IMPORTANT]
> O [portal Voz Personalizada](https://aka.ms/custom-voice) foi recentemente atualizado! Se criou dados anteriores, modelos, testes e pontos finais publicados no portal CRIS.ai ou com APIs, precisa de criar um novo projeto no novo portal para ligar a estas entidades antigas.

## <a name="how-to-migrate-to-custom-neural-voice"></a>Como migrar para a Voz Neural Personalizada

Se estiver a utilizar a voz personalizada não neural (ou padrão), considere migrar para a Voz Neural Personalizada imediatamente após os passos abaixo. Mudar-se para a Custom Neural Voice irá ajudá-lo a desenvolver vozes mais realistas para interfaces de conversação ainda mais naturais e permitirá que os seus clientes e utilizadores finais beneficiem da mais recente tecnologia Text-to-Speech, de forma responsável. 

1. Saiba mais sobre a nossa [política sobre o acesso ao limite](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) e aplique [aqui.](https://aka.ms/customneural) Note que o acesso ao serviço De Voz Neural Personalizado está sujeito à discrição exclusiva da Microsoft com base nos nossos critérios de elegibilidade. Os clientes só podem ter acesso à tecnologia após a revisão da sua aplicação e comprometeram-se a usá-la em alinhamento com os [nossos princípios de IA responsável](https://microsoft.com/ai/responsible-ai) e o [código de conduta.](/legal/cognitive-services/speech-service/tts-code-of-conduct?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) 
2. Uma vez aprovada a sua candidatura, será-lhe fornecido o acesso à função de treino "neural". Certifique-se de que inicia sessão no [portal Voz Personalizada](https://speech.microsoft.com/customvoice) utilizando a mesma subscrição Azure que fornece na sua aplicação. 
    > [!IMPORTANT]
    > Para proteger o talento de voz e impedir a formação de modelos de voz com gravação não autorizada ou sem o reconhecimento do talento de voz, exigimos que o cliente carrememe uma declaração gravada do talento de voz dando o seu consentimento. Ao preparar o seu script de gravação, certifique-se de incluir esta frase. "Eu [declaro o seu primeiro e último nome] estou ciente de que as gravações da minha voz serão usadas [pelo nome da empresa] para criar e usar uma versão sintética da minha voz."
    > Esta frase deve ser enviada para o **separador Voice Talent** como um ficheiro de consentimento verbal. Será utilizado para verificar se as gravações nos seus conjuntos de dados de treino são feitas pela mesma pessoa que faz o consentimento.
3. Após a criação do modelo Voz Neural Personalizada, insulo o modelo de voz para um novo ponto final. Para criar um novo ponto final de voz personalizado com o seu modelo de voz neural, vá para **Text-to-Speech > Custom Voice > Deployment**. Selecione **Implementar o modelo** e introduza um **Nome** e **Descrição** para o seu ponto de finalização personalizado. Em seguida, selecione o modelo de voz neural personalizado que pretende associar a este ponto final e confirme a implementação.  
4. Atualize o seu código nas suas apps se tiver criado um novo ponto final com um novo modelo. 

## <a name="next-steps"></a>Passos seguintes

- [Preparar dados de voz personalizados](how-to-custom-voice-prepare-data.md)
- [Criar uma voz personalizada](how-to-custom-voice-create-voice.md)
- [Guia: Grave as suas amostras de voz](record-custom-voice-samples.md)