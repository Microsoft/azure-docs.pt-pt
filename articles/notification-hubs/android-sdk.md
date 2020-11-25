---
title: Envie notificações push para Android usando Azure Notification Hubs e Firebase SDK versão 1.0.0-preview1
description: Neste tutorial, aprende a usar os Hubs de Notificação Azure e as Mensagens Cloud do Google Firebase para enviar notificações push para dispositivos Android (versão 1.0.0-preview1).
author: sethmanheim
ms.author: sethm
ms.date: 5/28/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 05/27/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 63841bd603373d0fb325bcf82511ce3fb07b4136
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017258"
---
# <a name="tutorial-send-push-notifications-to-android-devices-using-firebase-sdk-version-100-preview1"></a>Tutorial: Enviar notificações push para dispositivos Android usando a versão 1.0.0-preview do Firebase SDK

Este tutorial mostra como usar os Hubs de Notificação do Azure e a versão atualizada do Firebase Cloud Messaging (FCM) SDK (versão 1.0.0-preview1) para enviar notificações push para uma aplicação Android. Neste tutorial, cria-se uma aplicação Android em branco que recebe notificações push usando mensagens Firebase Cloud (FCM).

Você pode baixar o código preenchido para este tutorial a partir de [GitHub](https://github.com/Azure/azure-notificationhubs-android/tree/v1-preview/notification-hubs-test-app-refresh).

Este tutorial contém os seguintes passos:

- Criar um projeto do Android Studio.
- Criar um projeto do Firebase que suporte o Firebase Cloud Messaging.
- Criar um hub de notificação.
- Ligue a sua aplicação ao centro.
- Testar a aplicação.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, tem de ter uma conta ativa do Azure. Se não tiver uma conta, pode criar uma de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/free/).

Também precisa dos seguintes itens:

- A versão mais recente do [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797) é recomendada.
- O suporte mínimo é o nível 16 da API.

## <a name="create-an-android-studio-project"></a>Criar um projeto Android Studio

O primeiro passo é criar um projeto no Android Studio:

1. Lançamento Do Android Studio.

2. Selecione **'Ficheiro'** e, em seguida, selecione **Novo**, e depois **Novo Projeto**.

3. Na página Escolha a **página do projeto,** selecione **Atividade Vazia** e, em seguida, selecione **Seguinte**.

4. Na página **Configure o seu projeto,** faça o seguinte:
   1. Introduza um nome para a aplicação.
   2. Especifique um local para guardar os ficheiros do projeto.
   3. Selecione **Concluir**.

   :::image type="content" source="media/android-sdk/configure-project.png" alt-text="Projeto configure":::

## <a name="create-a-firebase-project-that-supports-fcm"></a>Criar um projeto do Firebase que suporte o FCM

1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.

2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**.

   :::image type="content" source="media/android-sdk/get-started.png" alt-text="Adicionar Base de Fogo":::

3. Na **base de adicionar fogo à sua** página de aplicativo Android, faça o seguinte:

   1. Para **o nome do pacote Android,** copie o valor do **applicationId** no ficheiro **build.gradle** da sua aplicação. Neste exemplo, `com.fabrikam.fcmtutorial1app` é.

      :::image type="content" source="media/android-sdk/specify-package-name-fcm-settings.png" alt-text="Especificar o nome do pacote":::

   2. Selecione **o aplicativo Registar**.

4. Selecione **Descarregue google-services.jsligado**, guarde o ficheiro na pasta de **aplicações** do seu projeto e, em seguida, selecione **Next**.

   :::image type="content" source="media/android-sdk/download-google-service-button.png" alt-text="Baixar o serviço Google":::

5. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

   :::image type="content" source="media/android-sdk/notification-hubs-firebase-console-project-settings.png" alt-text="Definições do projeto":::

6. Se ainda não descarregou o **google-services.jsficheiro na** pasta de **aplicações** do seu projeto Android Studio, pode fazê-lo nesta página.

7. Mude para o **separador Mensagens cloud.**

8. Copie e guarde a **tecla Server** para utilização posterior. Usa este valor para configurar o teu hub.

## <a name="configure-a-notification-hub"></a>Configurar um hub de notificação

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **todos os serviços** no menu esquerdo e, em seguida, selecione **'Centros de Notificação'** na secção **Mobile.** Selecione o ícone estrela ao lado do nome de serviço para adicionar o serviço à secção **FAVORITOS** no menu esquerdo. Depois de adicionar **Os Centros de Notificação** aos **FAVORITOS,** selecione-o no menu esquerdo.

3. Na página **'Centros de Notificação',** selecione **Adicionar** na barra de ferramentas.

   :::image type="content" source="media/android-sdk/add-hub.png" alt-text="Adicionar hub":::

4. Na página **'Centros de Notificação',** faça o seguinte:

   1. Introduza um nome no **Centro de Notificação**.

   2. Introduza um nome em **Criar um novo espaço de nome.** Um espaço com nomes contém um ou mais centros.

   3. Selecione um valor a partir do **drop-down localização.** Este valor especifica a localização em que pretende criar o hub.

   4. Selecione um grupo de recursos existente no **Grupo de Recursos** ou crie um novo.

   5. Selecione **Criar**.

      :::image type="content" source="media/android-sdk/create-hub.png" alt-text="Criar hub":::

5. Selecione **Notificações** (o ícone da campainha) e, em seguida, selecione **Ir para o recurso**. Também pode atualizar a lista na página **'Centros de Notificação'** e selecionar o seu hub.

   :::image type="content" source="media/android-sdk/notification-hubs.png" alt-text="Selecione hub":::

6. Selecione **Políticas de Acesso** na lista. Note que estão disponíveis duas cordas de ligação. Vai precisar deles mais tarde para lidar com notificações push.

   :::image type="content" source="media/android-sdk/access-policies.png" alt-text="Políticas de acesso":::

   > [!IMPORTANT]
   > Não utilize a política **de assinatura DefaultFulFuldAccesss na** sua aplicação. Esta política deve ser utilizada apenas no back-end da aplicação.

## <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>Configurar as definições do Firebase Cloud Messaging para o hub

1. No painel esquerdo, em **Definições,** selecione **Google (GCM/FCM)**.

2. Introduza a **tecla** do servidor para o projeto FCM que guardou anteriormente.

3. Na barra de ferramentas, **selecione Guardar**.

   :::image type="content" source="media/android-sdk/fcm-server-key.png" alt-text="Chave do servidor":::

4. O portal Azure apresenta uma mensagem de que o hub foi atualizado com sucesso. O botão **Guardar** está desativado.

O seu centro de notificação está agora configurado para trabalhar com mensagens cloud firebase. Também tem as cadeias de ligação necessárias para enviar notificações para um dispositivo e registar uma aplicação para receber notificações.

## <a name="connect-your-app-to-the-notification-hub"></a>Ligar a aplicação ao Notification Hub

### <a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play para o projeto

1. No Android Studio, selecione **Tools** no menu e, em seguida, selecione **SDK Manager**.

2. Selecione a versão alvo do Android SDK que é usado no seu projeto. Em seguida, selecione **Mostrar Detalhes do Pacote**.

   :::image type="content" source="media/android-sdk/notification-hubs-android-studio-sdk-manager.png" alt-text="Gestor SDK":::

3. Selecione **APIs do Google,** se ainda não estiver instalado.

   :::image type="content" source="media/android-sdk/google-apis-selected.png" alt-text="APIs":::

4. Mude para o separador **Ferramentas SDK.** Se ainda não instalou o Google Play Services, selecione **o Google Play Services** como mostrado na imagem seguinte. Em seguida, **selecione Aplicar** para instalar. Anote o caminho do SDK, para utilização num passo posterior.

   :::image type="content" source="media/android-sdk/google-play-services-selected.png" alt-text="Serviços de jogo":::

5. Se vir a caixa de diálogo **'Alterar'** confirmando,selecione **OK**. O instalador do componente instala os componentes solicitados. **Selecione Acabamento** após a instalação dos componentes.

6. Selecione **OK** para fechar a caixa de diálogo **De Definições para Novos Projetos.**

### <a name="add-azure-notification-hubs-libraries"></a>Adicionar bibliotecas Azure Notification Hubs

1. No ficheiro **build.gradle** para a aplicação, adicione as seguintes linhas na secção dependências:

   ```gradle
   implementation 'com.microsoft.azure:notification-hubs-android-sdk:1.0.0-preview1@aar'
   implementation 'androidx.appcompat:appcompat:1.0.0'

   implementation 'com.google.firebase:firebase-messaging:20.1.5'

   implementation 'com.android.volley:volley:1.1.1'
   ```

2. Adicione o seguinte repositório após a secção de dependências:

   ```gradle
   repositories {
      maven {
         url "https://dl.bintray.com/microsoftazuremobile/SDK"
      }
   }
   ```

### <a name="add-google-firebase-support"></a>Adicionar suporte do Google Firebase

1. Adicione o seguinte plug-in no final do ficheiro se ainda não estiver lá.

   ```gradle
   apply plugin: 'com.google.gms.google-services'
   ```

2. **Selecione Sync Now** na barra de ferramentas.

### <a name="add-code"></a>Adicionar código

1. Crie um objeto **NotificationHubListener,** que lida com a interceção das mensagens dos Hubs de Notificação do Azure.

   ```csharp
   public class CustomNotificationListener implements NotificationHubListener {

      @override

      public void onNotificationReceived(Context context, NotificationMessage message) {

      /* The following notification properties are available. */

      String title = message.getTitle();
      String message = message.getMessage();
      Map<String, String> data = message.getData();

      if (message != null) {
         Log.d(TAG, "Message Notification Title: " + title);
         Log.d(TAG, "Message Notification Body: " + message);
         }
      }
   }
   ```

2. No `OnCreate` método da classe, adicione o seguinte código para iniciar o processo de `MainActivity` inicialização dos Centros de Notificação quando a atividade for criada:

   ```java
   @Override
   protected void onCreate(Bundle savedInstanceState) {

      super.onCreate(savedInstanceState);
      setContentView(R.layout.activity\_main);
      NotificationHub.setListener(new CustomNotificationListener());
      NotificationHub.initialize(this.getApplication(), “Connection-String”, "Hub Name");

   }
   ```

3. No Android Studio, na barra de menus, selecione **Build,** em seguida, selecione **Rebuild Project** para se certificar de que não há erros no seu código. Se receber um erro sobre o ícone **ic_launcher,** remova a seguinte declaração do ficheiro AndroidManifest.xml:

   ```xml
   android:icon="@mipmap/ic_launcher"
   ```

4. Certifique-se de que tem um dispositivo virtual para executar a aplicação. Se não tiver uma, adicione uma da seguinte forma:

   1. :::image type="content" source="media/android-sdk/open-device-manager.png" alt-text="Gestor de dispositivos":::
   2. :::image type="content" source="media/android-sdk/your-virtual-devices.png" alt-text="Dispositivos virtuais":::
   3. Execute a aplicação no seu dispositivo selecionado e verifique se se regista com sucesso com o hub.

      :::image type="content" source="media/android-sdk/device-registration.png" alt-text="Registo de dispositivo":::

      > [!NOTE]
      > O registo pode falhar durante o lançamento inicial, até que o `onTokenRefresh()` método do serviço de identificação de instância seja chamado. Uma atualização deve iniciar um registo bem sucedido com o centro de notificação.

## <a name="send-a-test-notification"></a>Enviar uma notificação de teste

Pode enviar notificações push para o seu centro de notificações a partir do [portal Azure,](https://portal.azure.com/)da seguinte forma:

1. No portal Azure, na página do centro de notificação para o seu hub, selecione **Enviar** na secção **resolução de problemas.**

2. Em **Plataformas,** selecione **Android**.

3. Selecione **Send** (Enviar). Ainda não vai ver uma notificação no dispositivo Android porque ainda não executou a aplicação móvel no mesmo. Depois de executar a aplicação móvel, selecione novamente o botão **Enviar** para ver a mensagem de notificação.

4. Consulte o resultado da operação na lista na parte inferior da página do portal.

   :::image type="content" source="media/android-sdk/notification-hubs-test-send.png" alt-text="Enviar notificação de teste":::

5. Vê a mensagem de notificação no seu dispositivo.

As notificações push são normalmente enviadas num serviço back-end, como Aplicações Móveis ou ASP.NET usando uma biblioteca compatível. Se uma biblioteca não estiver disponível para a sua parte de trás, também pode utilizar a API REST diretamente para enviar mensagens de notificação.

## <a name="run-the-mobile-app-on-emulator"></a>Executar a aplicação móvel no emulador

Antes de testar notificações push dentro de um emulador, certifique-se de que a sua imagem emuladora suporta o nível de API do Google que escolheu para a sua aplicação. Se a sua imagem não suportar APIs nativos do Google, poderá obter uma **SERVICE_NOT_AVAILABLE** exceção.

Certifique-se também de que adicionou a sua conta Google ao seu emulador de funcionamento em **Contas de**  >  **Definições.** Caso contrário, as suas tentativas de se registar na FCM podem resultar numa **AUTHENTICATION_FAILED** exceção.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, utilizou o Firebase Cloud Messaging para transmitir notificações a todos os dispositivos Android que foram registados no serviço. Para saber como enviar notificações push para dispositivos específicos, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
>[Tutorial: Enviar notificações a utilizadores específicos](push-notifications-android-specific-users-firebase-cloud-messaging.md)

Segue-se uma lista de outros tutoriais para envio de notificações:

- Aplicativos Azure Mobile: Para um exemplo de como enviar notificações de uma aplicação móvel de volta integrada com Os Hubs de Notificação, consulte [Adicionar Notificações push à sua aplicação iOS](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push).

- ASP.NET: Utilize [os Centros de Notificação para enviar notificações push aos utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md).

- SDK Java dos Hubs de Notificação do Azure: veja [Como utilizar Hubs de Notificação de Java](notification-hubs-java-push-notification-tutorial.md) para enviar notificações de Java. Este processo foi testado em Eclipse para Programação Android.

- PHP: [Como utilizar os Hubs de Notificação de PHP](notification-hubs-php-push-notification-tutorial.md).