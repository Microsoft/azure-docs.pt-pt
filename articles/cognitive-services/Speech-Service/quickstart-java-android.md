---
title: 'Início Rápido: reconhecer voz em Java no Android com o SDK de Serviço de Voz'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconhecer voz em Java no Android com o SDK de Serviço de Voz
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 8c974b3d2a53210b49c3f29a8984038da93dd64c
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466534"
---
# <a name="quickstart-recognize-speech-in-java-on-android-by-using-the-speech-sdk"></a>Início Rápido: reconhecer voz em Java no Android com o SDK de Voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, vai aprender a criar uma aplicação Java para Android com o SDK de Voz dos Serviços Cognitivos para fazer a conversão de voz em texto.
A aplicação baseia-se no Pacote Maven, versão 1.0.1, do SDK de Voz dos Serviços Cognitivos da Microsoft e no Android Studio 3.1.
Atualmente, o SDK de Voz é compatível com dispositivos Android com processadores ARM de 32 bits ou 64 bits.

> [!NOTE]
> Para o SDK de Dispositivos de Voz e o dispositivo Roobo, veja [SDK de Dispositivos de Voz](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma chave de subscrição do serviço de Voz para concluir este Guia de Início Rápido. Pode obter uma gratuitamente. Veja [Experimente o serviço de Voz gratuitamente](get-started.md) para obter detalhes.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

1. Inicie o Android Studio e escolha **Start a new Android Studio project** (Iniciar um novo projeto do Android Studio) na janela de boas-vindas.

    ![Captura de ecrã da janela de boas-vindas do Android Studio](media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Aparece o assistente **Create New Project** (Criar Novo Projeto). No ecrã **Create Android Project** (Criar Projeto Android) introduza **Quickstart** como o **nome da aplicação**, **samples.speech.cognitiveservices.microsoft.com** como **domínio da empresa** e escolha um diretório para o projeto. Deixe as caixas de verificação de C++ e Kotlin desmarcadas e selecione **Next** (Seguinte).

   ![Captura de ecrã do assistente Criar Novo Projeto](media/sdk/qs-java-android-02-create-android-project.png)

1. No ecrã **Target Android Devices** (Dispositivos Android de Destino), selecione apenas **Phone and Tablet** (Telemóvel e Tablet). Na lista pendente abaixo, escolha **API 23: Android 6.0 (Marshmallow)** e selecione **Next** (Seguinte).

   ![Captura de ecrã do assistente Criar Novo Projeto](media/sdk/qs-java-android-03-target-android-devices.png)

1. No ecrã **Add an Activity to Mobile** (Adicionar uma Atividade ao Telemóvel), selecione **Empty Activity** (Atividade Vazia) e clique em **Next** (Seguinte).

   ![Captura de ecrã do assistente Criar Novo Projeto](media/sdk/qs-java-android-04-add-an-activity-to-mobile.png)

1. No ecrã **Configure Activity** (Configurar Atividade), utilize **MainActivity** como o nome da atividade e **activity\_main** como o nome do esquema. Selecione as caixas de verificação e selecione **Finish** (Concluir).

   ![Captura de ecrã do assistente Criar Novo Projeto](media/sdk/qs-java-android-05-configure-activity.png)

O Android Studio demora algum tempo a preparar o seu novo projeto Android. Em seguida, configure o projeto para saber sobre o SDK de Voz e para utilizar Java 8.

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Voz dos Serviços Cognitivos é `1.0.1`.

O SDK de Voz para Android é empacotado como uma [AAR (Biblioteca Android)](https://developer.android.com/studio/projects/android-library), que inclui as bibliotecas necessárias, bem como as permissões Android necessárias para utilizá-lo.
Está alojado num repositório Maven em https://csspeechstorage.blob.core.windows.net/maven/.

Configure o projeto para utilizar o SDK de Voz. Abra a janela Project Structure (Estrutura do Projeto), escolhendo **File** (Ficheiro)  > **Project Structure** (Estrutura do Projeto) na barra de menus do Android Studio. Na janela de estrutura do projeto, faça as seguintes alterações: 

1. Na lista no lado esquerdo da janela, selecione **Project** (Projeto). Edite as definições de **Default Library Repository** (Repositório de Biblioteca Predefinido), acrescentando uma vírgula e colocando o URL do repositório do Maven entre plicas. "https://csspeechstorage.blob.core.windows.net/maven/"

   ![Captura de ecrã da janela Estrutura do Projeto](media/sdk/qs-java-android-06-add-maven-repository.png)

1. No mesmo ecrã, no lado esquerdo, selecione **app**. Em seguida, selecione o separador **Dependencies** (Dependências) na parte superior da janela. Selecione o sinal de adição verde (+) e escolha **Library dependency** (Dependência da biblioteca) no menu pendente.

   ![Captura de ecrã da janela Estrutura do Projeto](media/sdk/qs-java-android-07-add-module-dependency.png)

1. Na janela apresentada, introduza o nome e a versão do nosso SDK de Voz para Android, `com.microsoft.cognitiveservices.speech:client-sdk:1.0.1`. Em seguida, selecione **OK**.
   Agora, o SDK de Voz deve ser adicionado à lista de dependências, conforme apresentado abaixo:

   ![Captura de ecrã da janela Estrutura do Projeto](media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Selecione o separador **Properties** (Propriedades). Para **Source Compatibility** (Compatibilidade de Origem) e **Target Compatibility** (Compatibilidade de Destino), selecione **1.8**.

   ![](media/sdk/qs-java-android-09-dependency-added.png)

1. Selecione **OK** para fechar a janela de estrutura do projeto e aplicar as alterações ao projeto.

## <a name="create-user-interface"></a>Criar a interface de utilizador

Vamos criar uma interface de utilizador básica para a aplicação. Edite o esquema da atividade principal, `activity_main.xml`. Inicialmente, o esquema inclui uma barra de título com o nome da aplicação e uma TextView que contém o texto "Hello World!"

* Clique no elemento TextView. Altere o atributo ID no canto superior direito para `hello`.

* Na paleta no canto superior esquerdo da janela `activity_main.xml`, arraste um botão para o espaço vazio acima do texto.

* Nos atributos do botão à direita, no valor para o atributo `onClick`, introduza `onSpeechButtonClicked`. Vamos escrever um método com este nome para processar o evento do botão.  Altere o atributo ID no canto superior direito para `button`.

* Utilize o ícone de varinha mágica na parte superior do designer para inferir restrições de esquema.

  ![Captura de ecrã do ícone de varinha mágica](media/sdk/qs-java-android-10-infer-layout-constraints.png)

O texto e a representação gráfica da sua IU devem agora ser semelhantes à figura seguinte.

<table>
<tr>
<td valign="top">
![](media/sdk/qs-java-android-11-gui.png)
</td>
<td valign="top">
[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/res/layout/activity_main.xml)]
</td>
</tr>
</table>

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra o ficheiro de origem `MainActivity.java`. Substitua todo o código neste ficheiro pelo seguinte.

   [!code-java[](~/samples-cognitive-services-speech-sdk/quickstart/java-android/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/quickstart/MainActivity.java#code)]

   * O método `onCreate` inclui código que solicita as permissões de microfone e internet, e inicializa o enlace de plataforma nativa. A configuração dos enlaces de plataforma nativa só é precisa uma vez. Deve ser feita no início durante a inicialização da aplicação.
   
   * O método `onSpeechButtonClicked` é, como observado anteriormente, o processador do clique do botão. Um clique no botão aciona a conversão de voz em texto.

1. No mesmo ficheiro, substitua a cadeia de carateres `YourSubscriptionKey` pela sua chave de subscrição.

1. Substitua também a cadeia de carateres `YourServiceRegion`pela [região](regions.md) associada à subscrição (por exemplo, `westus` para a subscrição de avaliação gratuita).

## <a name="build-and-run-the-app"></a>Compilar e executar a aplicação

1. Ligue o seu dispositivo Android ao PC de desenvolvimento. Certifique-se de que ativou o [modo de desenvolvimento e depuração USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para criar a aplicação, prima Ctrl+F9 ou escolha **Build** (Criar)  > **Make Project** (Criar Projeto) na barra de menus.

1. Para iniciar a aplicação, prima Shift+F10 ou escolha **Run** (Executar)  > **Run 'app'** (Executar 'app').

1. Na janela de destino de implementação que aparece, selecione o seu dispositivo Android.

   ![Captura de ecrã da janela de seleção de destino de implementação](media/sdk/qs-java-android-12-deploy.png)

Prima o botão na aplicação para iniciar uma secção de reconhecimento de voz. Os próximos 15 segundos de voz em inglês serão enviados para o serviço de Voz e transcritos. O resultado é apresentado na aplicação Android e na janela logcat no Android Studio.

![Captura de ecrã da aplicação Android](media/sdk/qs-java-android-13-gui-on-device.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Procure este exemplo na pasta `quickstart/java-android`.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Reconhecer intenções de voz com o SDK de Voz para Java](how-to-recognize-intents-from-speech-java.md)

## <a name="see-also"></a>Consulte também

- [Traduzir voz](how-to-translate-speech-csharp.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de idioma](how-to-customize-language-model.md)
