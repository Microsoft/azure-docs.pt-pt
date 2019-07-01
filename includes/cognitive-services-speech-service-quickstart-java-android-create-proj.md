---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: a8118d80e85d562fa4137ed1f1844e6bf9f1793e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485046"
---
1. Inicie o Android Studio e escolha **Start a new Android Studio project** (Iniciar um novo projeto do Android Studio) na janela de boas-vindas.

    ![Captura de ecrã da janela de boas-vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O **escolha o seu projeto** é apresentado o assistente, selecione **telemóveis e tablets** e **atividade vazia** na caixa de seleção de atividade. Selecione **Seguinte**.

   ![Captura de ecrã da escolha seu assistente de projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na **configurar o seu projeto** ecrã, introduza **guia de início rápido** como **nome**, **samples.speech.cognitiveservices.microsoft.com** como **Nome do pacote**e escolha um diretório do projeto. Para **nível mínimo de API** escolher **API 23: Android 6.0 (Marshmallow)** , deixe todas as outras caixas de seleção desmarcada e selecione **concluir**.

   ![Captura de ecrã de configurar o Assistente de projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo a preparar o seu novo projeto Android. Em seguida, configure o projeto para saber sobre o SDK de Voz e para utilizar Java 8.

[!INCLUDE [License Notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.6.0`.

O SDK de voz para Android é empacotado como uma [AAR (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as permissões necessárias de Android e as bibliotecas necessárias.
Ele está hospedado num repositório Maven em https:\//csspeechstorage.blob.core.windows.net/maven/.

Configure o projeto para utilizar o SDK de Voz. Abra a janela Project Structure (Estrutura do Projeto), escolhendo **File** (Ficheiro)  > **Project Structure** (Estrutura do Projeto) na barra de menus do Android Studio. Na janela de estrutura do projeto, faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Project** (Projeto). Edite as definições de **Default Library Repository** (Repositório de Biblioteca Predefinido), acrescentando uma vírgula e colocando o URL do repositório do Maven entre plicas. 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. No mesmo ecrã, no lado esquerdo, selecione **app**. Em seguida, selecione o separador **Dependencies** (Dependências) na parte superior da janela. Selecione o sinal de adição verde (+) e escolha **Library dependency** (Dependência da biblioteca) no menu pendente.

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela apresentada, introduza o nome e a versão do nosso SDK de Voz para Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`. Em seguida, selecione **OK**.
   Agora, o SDK de Voz deve ser adicionado à lista de dependências, conforme apresentado abaixo:

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecione o separador **Properties** (Propriedades). Para **Source Compatibility** (Compatibilidade de Origem) e **Target Compatibility** (Compatibilidade de Destino), selecione **1.8**.

   ![](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela de estrutura do projeto e aplicar as alterações ao projeto.
