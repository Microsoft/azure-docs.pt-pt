---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: travisw
ms.openlocfilehash: 2efe17bdf597b7e3ba7d0277f06d4b24e9b51db3
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187763"
---
1. Lance o Android Studio e selecione **Iniciar um novo projeto Android Studio** na janela **Welcome.**

    ![Captura de ecrã da janela de boas-vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O **assistente de escolha** do seu projeto aparece. Selecione **Telefone e Tablet** e Atividade **Vazia** na caixa de seleção de atividades. Selecione **Seguinte**.

   ![Screenshot de Escolher o seu assistente de projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. No **ecrã do seu projeto,** insira *o Quickstart* como **Nome** e introduza *samples.speech.cognitiveservices.microsoft.com* como nome **de pacote**. Em seguida, selecione um diretório de projeto. Para **o nível mínimo de API**, selecione **API 23: Android 6.0 (Marshmallow)**. Deixe todas as outras caixas de verificação limpas e **selecione Acabamento**.

   ![Screenshot de Configurar o seu assistente de projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo a preparar o seu novo projeto Android. Em seguida, configurar o projeto para saber sobre o Azure Cognitive Services Speech SDK e usar Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK do Discurso dos Serviços Cognitivos é 1.13.0.

O Speech SDK para Android é embalado como [um AAR (Android Library),](https://developer.android.com/studio/projects/android-library)que inclui as bibliotecas necessárias e permissões necessárias para Android.
Está hospedado num repositório de Maven em https: \/ /csspeechstorage.blob.core.windows.net/maven/.

Configure o projeto para utilizar o SDK de Voz. Abra a janela **Da Estrutura** do Projeto selecionando a Estrutura do Projeto **de Ficheiros** a partir da barra de  >  **Project Structure** menus Android Studio. Na janela Estrutura do **Projeto,** faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Project** (Projeto). Editar as definições **de Repositório de Biblioteca Padrão,** anexando uma vírgula e o nosso URL repositório maven incluído em aspas únicas: 'https: \/ /csspeechstorage.blob.core.windows.net/maven/'

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. No mesmo ecrã, no lado esquerdo, selecione **app**. Em seguida, selecione o separador **Dependencies** (Dependências) na parte superior da janela. Selecione o sinal verde mais **+** () e selecione **a dependência** da Biblioteca a partir do menu suspenso.

   ![Screenshot da dependência da Biblioteca](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela que aparece, insira o nome e versão do Speech SDK para Android, *com.microsoft.cognitiveservices.speech:client-sdk:1.13.0*. Em seguida, selecione **OK**.
   O SDK de discurso deve ser adicionado à lista de dependências agora, como mostrado:

   ![Screenshot da Speech SDK na lista de dependências](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecione o **separador Propriedades.** Tanto para **a compatibilidade de origem** como **para a compatibilidade do alvo,** selecione **1.9**.

   ![Screenshot da Compatibilidade de Origem e Compatibilidade do Alvo](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela **Project Structure** e aplique as suas alterações no projeto.
