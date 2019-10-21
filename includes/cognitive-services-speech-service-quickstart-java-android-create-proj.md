---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 5/23/2019
ms.author: travisw
ms.openlocfilehash: 5c0b6400c6dc346597329c82e82801403a888a4d
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675573"
---
1. Inicie o Android Studio e selecione **Iniciar um novo projeto de Android Studio** na janela de **boas-vindas** .

    ![Captura de ecrã da janela de boas-vindas do Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. O assistente **escolher seu projeto** é exibido. Selecione **telefone e Tablet** e **atividade vazia** na caixa seleção de atividade. Selecione **Seguinte**.

   ![Captura de tela do assistente escolher seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Na tela **configurar seu projeto** , insira *início rápido* como **nome** e insira *Samples.Speech.cognitiveservices.Microsoft.com* como **nome do pacote**. Em seguida, selecione um diretório do projeto. Para **nível mínimo de API**, selecione **API 23: Android 6,0 (marshmallow)** . Deixe todas as outras caixas de seleção desmarcadas e selecione **concluir**.

   ![Captura de tela do assistente para configurar seu projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

O Android Studio demora algum tempo a preparar o seu novo projeto Android. Em seguida, configure o projeto para saber mais sobre o SDK de fala dos serviços cognitivas do Azure e para usar o Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de fala dos serviços cognitivas é 1.7.0.

O SDK de fala para Android é empacotado como um [aar (biblioteca do Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias e as permissões do Android necessárias.
Ele é hospedado em um repositório Maven em https: \//csspeechstorage.blob.core.windows.net/maven/.

Configure o projeto para utilizar o SDK de Voz. Abra a janela **estrutura do projeto** selecionando **arquivo**  >  estrutura do**projeto** na barra de menus Android Studio. Na janela **estrutura do projeto** , faça as seguintes alterações:

1. Na lista no lado esquerdo da janela, selecione **Project** (Projeto). Edite as configurações **padrão do repositório de biblioteca** acrescentando uma vírgula e a URL do repositório do Maven entre aspas simples: ' https: \//csspeechstorage.blob.Core.Windows.net/Maven/'

   ![Captura de ecrã da janela Estrutura do Projeto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Na mesma tela, no lado esquerdo, selecione **aplicativo**. Em seguida, selecione o separador **Dependencies** (Dependências) na parte superior da janela. Selecione o sinal de adição verde ( **+** ) e selecione **dependência de biblioteca** no menu suspenso.

   ![Captura de tela da dependência de biblioteca](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela exibida, insira o nome e a versão do SDK de fala para Android, *com. Microsoft. cognitivaservices. fala: Client-SDK: 1.7.0*. Em seguida, selecione **OK**.
   O SDK de fala deve ser adicionado à lista de dependências agora, conforme mostrado:

   ![Captura de tela do SDK de fala na lista de dependências](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecione a guia **Propriedades** . Para compatibilidade de **origem** e **compatibilidade de destino**, selecione **1,8**.

   ![Captura de tela de compatibilidade de origem e compatibilidade de destino](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela **estrutura do projeto** e aplicar as alterações ao projeto.
