---
title: Criar quickstart de palavras-chave - serviço de fala
titleSuffix: Azure Cognitive Services
description: O seu aparelho está sempre a ouvir uma palavra-chave (ou frase). Quando o utilizador diz a palavra-chave, o dispositivo envia todo o áudio subsequente para a nuvem, até que o utilizador deixe de falar. Personalizar a sua palavra-chave é uma forma eficaz de diferenciar o seu dispositivo e fortalecer a sua marca.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/02/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: f2f333cd01057ff5f6f904924880f88b0685c72f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91667698"
---
# <a name="get-started-with-custom-keyword"></a>Introdução à Palavra-passe Personalizada

Neste arranque rápido, aprende-se o básico de trabalhar com palavras-chave personalizadas, utilizando o Speech Studio e o Speech SDK. Uma palavra-chave é uma palavra ou frase curta que permite ativar a voz do seu produto. Crias modelos de palavras-chave no Speech Studio e depois exportas um ficheiro modelo que usas com o Speech SDK nas tuas aplicações.

## <a name="prerequisites"></a>Pré-requisitos

Os passos neste artigo requerem uma assinatura de Discurso, e o Discurso SDK. Se ainda não tiver uma subscrição, [experimente gratuitamente o serviço Desemação](overview.md#try-the-speech-service-for-free). Para obter o SDK, consulte o [guia de instalação](quickstarts/setup-platform.md) da sua plataforma.

## <a name="create-a-keyword-in-speech-studio"></a>Crie uma palavra-chave no Estúdio da Fala

Antes de utilizar uma palavra-chave personalizada, tem de criar uma palavra-chave utilizando a página [de palavras-chave personalizada](https://aka.ms/sdsdk-wakewordportal) no [Speech Studio](https://aka.ms/sdsdk-speechportal). Depois de fornecer uma palavra-chave, produz um `.table` ficheiro que pode utilizar com o SDK de discurso.

> [!IMPORTANT]
> Os modelos de palavras-chave personalizadas e os `.table` ficheiros resultantes **só** podem ser criados no Speech Studio.
> Não é possível criar palavras-chave personalizadas a partir do SDK ou com chamadas REST.

1. Vá ao Estúdio de [Discurso](https://aka.ms/sdsdk-speechportal) e **Inscreva-se** ou, se ainda não tiver uma subscrição de discurso, escolha [**Criar uma subscrição**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na página [De Palavras-Chave Personalizadas,](https://aka.ms/sdsdk-wakewordportal) crie um **novo projeto.** 

1. Introduza um **Nome**, uma **Descrição**opcional, e selecione o idioma. Você precisa de um projeto por idioma, e o apoio está atualmente limitado à `en-US` língua.

    ![Descreva o seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selecione o seu projeto na lista. 

    ![Selecione o seu projeto de palavra-chave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Para criar um novo modelo de palavra-chave, clique no **modelo Train**.

1. Introduza um **Nome** para o modelo, uma **descrição**opcional, e a **palavra-chave** à sua escolha, em seguida, clique em **Seguinte**. Consulte as [diretrizes](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) sobre a escolha de uma palavra-chave eficaz.

    ![Insira a sua palavra-chave](media/custom-keyword/custom-kws-portal-new-model.png)

1. O portal cria pronúncias de candidatos para a sua palavra-chave. Ouça cada candidato clicando nos botões de reprodução e retire as verificações ao lado de quaisquer pronúncias incorretas. Uma vez verificadas apenas boas pronúncias, clique em **Comboio** para começar a gerar o modelo de palavras-chave. 

    ![Reveja a sua palavra-chave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Pode levar até 30 minutos para o modelo ser gerado. A lista de palavras-chave mudará de **Processamento** para **Bem sucedido** quando o modelo estiver completo. Em seguida, pode descarregar o ficheiro.

    ![Reveja a sua palavra-chave](media/custom-keyword/custom-kws-portal-download-model.png)

1. O ficheiro descarregado é um `.zip` arquivo. Extraia o arquivo e verá um ficheiro com a `.table` extensão. Este é o ficheiro que utiliza com o SDK na secção seguinte, por isso certifique-se de registar o seu caminho. o nome do ficheiro espelha o nome da palavra-chave, por exemplo, um **dispositivo ativar** palavra-chave tem o nome do ficheiro `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Use um modelo de palavra-chave com o SDK

Em primeiro lugar, carregue o ficheiro do modelo de palavra-chave utilizando a `FromFile()` função estática, que devolve um `KeywordRecognitionModel` . Use o caminho para o `.table` ficheiro que descarregou do Speech Studio. Além disso, cria-se um `AudioConfig` microfone padrão e, em seguida, instantaneamente um novo `KeywordRecognizer` utilizando a configuração de áudio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Em seguida, executar o reconhecimento de palavras-chave é feito com uma chamada para `RecognizeOnceAsync()` passar o seu objeto modelo. Isto inicia uma sessão de reconhecimento de palavras-chave que dura até que a palavra-chave seja reconhecida. Assim, você geralmente usa este padrão de design em aplicações multi-roscadas, ou em casos em que você pode estar esperando por uma palavra de despertar indefinidamente.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> O exemplo aqui mostrado utiliza o reconhecimento de palavras-chave locais, uma vez que não requer um `SpeechConfig` objeto para o contexto de autenticação, e não contacta o back-end. No entanto, pode executar reconhecimento e verificação de [palavras-chave utilizando uma ligação contínua de back-end](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).

## <a name="next-steps"></a>Passos seguintes

Teste a sua palavra-chave personalizada com os [dispositivos de fala SDK Quickstart](https://aka.ms/sdsdk-quickstart).
