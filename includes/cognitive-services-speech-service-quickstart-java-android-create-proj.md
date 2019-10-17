---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: e1f9b561efb1fd88fe2dd00f8c175f1ebae848d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391742"
---
1. Inicie o Android Studio e escolha **Start a new Android Studio project** (Iniciar um novo projeto do Android Studio) na janela de boas-vindas.

    ![Captura de ecrã da janela de boas-vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O assistente **escolher seu projeto** é exibido, selecione **telefone e Tablet** e **atividade vazia** na caixa seleção de atividade. Selecione **Seguinte**.

   ![Captura de tela do assistente escolher seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na tela **configurar seu projeto** , insira **início rápido** como **nome**, **Samples.Speech.cognitiveservices.Microsoft.com** como **nome do pacote**e escolha um diretório do projeto. Para a API de escolha **mínima de nível de API** **23: Android 6,0 (marshmallow)** , deixe todas as outras caixas de seleção desmarcadas e selecione **concluir**.

   ![Captura de tela do assistente para configurar seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo a preparar o seu novo projeto Android. Em seguida, configure o projeto para saber sobre o SDK de Voz e para utilizar Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.7.0`.

O SDK de fala para Android é empacotado como um [aar (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões do Android necessárias.
Ele é hospedado em um repositório Maven em https: \//csspeechstorage. blob. Core. Windows. net/Maven/.

Configure o projeto para utilizar o SDK de Voz. Abra a janela Project Structure (Estrutura do Projeto), escolhendo **File** (Ficheiro)  > **Project Structure** (Estrutura do Projeto) na barra de menus do Android Studio. Na janela de estrutura do projeto, faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Project** (Projeto). Edite as definições de **Default Library Repository** (Repositório de Biblioteca Predefinido), acrescentando uma vírgula e colocando o URL do repositório do Maven entre plicas. ' https: \//csspeechstorage. blob. Core. Windows. net/Maven/'

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. No mesmo ecrã, no lado esquerdo, selecione **app**. Em seguida, selecione o separador **Dependencies** (Dependências) na parte superior da janela. Selecione o sinal de adição verde (+) e escolha **Library dependency** (Dependência da biblioteca) no menu pendente.

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela apresentada, introduza o nome e a versão do nosso SDK de Voz para Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.7.0`. Em seguida, selecione **OK**.
   Agora, o SDK de Voz deve ser adicionado à lista de dependências, conforme apresentado abaixo:

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecione a guia **Propriedades** . Para compatibilidade de **origem** e **compatibilidade de destino**, selecione **1,8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela de estrutura do projeto e aplicar as alterações ao projeto.
