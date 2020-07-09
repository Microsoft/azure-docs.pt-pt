---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85081560"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Crie um projeto Firebase e permita mensagens em nuvem de base de fogo para Android

1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase que **introduza o PushDemo** como **nome do Projeto.**

    > [!NOTE]
    > Um nome único será gerado para si. Por predefinição, esta é composta por uma variante minúscula do nome que forneceu mais um número gerado separado por um traço. Pode mudar isto se quiser, desde que ainda seja globalmente único.

1. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**.

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Na **base de adicionar fogo à sua** página de aplicativo Android, tome os seguintes passos.
    1. Para o nome do **pacote Android,** insira um nome para o seu pacote. Por exemplo: `com.<organization_identifier>.<package_name>`.

        ![Especificar o nome do pacote](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Selecione **o aplicativo Registar**.  
    1. Selecione **Baixar google-services.jsem**. Em seguida, guarde o ficheiro numa pasta local para utilização mais tarde e selecione **Next**.  

        ![Baixar google-services.jsem](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Selecione **Seguinte**.
    1. **Selecione Continue a consolar**

        > [!NOTE]
        > Se o botão **Continuar a consolar** não estiver ativado, devido à verificação da verificação da *instalação,* então escolha **'Saltar este passo'**

1. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Se ainda não descarregou o **google-services.jsno** ficheiro, pode fazê-lo baixar nesta página.

1. Mude para o **separador Cloud Messaging** no topo. Copie e guarde a **tecla Server** para utilização posterior. Utilize este valor para configurar o seu centro de notificação.

    ![Chave do servidor de cópia](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
