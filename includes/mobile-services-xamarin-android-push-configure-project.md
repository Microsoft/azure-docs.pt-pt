---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66140275"
---
1. Na vista solução (ou **Explorador de soluções** no Visual Studio), com o botão direito a **componentes** pasta, clique em **obter mais componentes...** , procure o **cliente Google Cloud Messaging** componente e adicioná-lo ao projeto.
2. Abra o ficheiro de projeto ToDoActivity.cs e adicione o seguinte usando a instrução a classe:

    ```csharp
    using Gcm.Client;
    ```

3. Na **ToDoActivity** , adicione o seguinte código novo: 

    ```csharp
    // Create a new instance field for this activity.
    static ToDoActivity instance = new ToDoActivity();

    // Return the current activity instance.
    public static ToDoActivity CurrentActivity
    {
        get
        {
            return instance;
        }
    }
    // Return the Mobile Services client.
    public MobileServiceClient CurrentClient
    {
        get
        {
            return client;
        }
    }
    ```

    Isto permite-lhe aceder à instância de cliente móvel do processo de serviço do manipulador de push.
4. Adicione o seguinte código para o **OnCreate** método, após o **MobileServiceClient** é criado:

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

Sua **ToDoActivity** está agora preparado para adicionar notificações push.
