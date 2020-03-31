---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 69dc0e1c14bc88cdbf0aa48700f95058ba759cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "67184765"
---
1. Na visão Solution (ou **Solution Explorer** in Visual Studio), clique na pasta **Componentes,** clique em **Get More Components...**, procure o componente do Cliente de **Mensagens Cloud** do Google e adicione-o ao projeto.
2. Abra o ficheiro ToDoActivity.cs projeto e adicione o seguinte usando a declaração à classe:

    ```csharp
    using Gcm.Client;
    ```

3. Na classe **ToDoActivity,** adicione o seguinte novo código: 

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

    Isto permite-lhe aceder à instância do cliente móvel a partir do processo de serviço push handler.
4. Adicione o seguinte código ao método **OnCreate,** depois de criado o **MobileServiceClient:**

    ```csharp
    // Set the current instance of TodoActivity.
    instance = this;

    // Make sure the GCM client is set up correctly.
    GcmClient.CheckDevice(this);
    GcmClient.CheckManifest(this);

    // Register the app for push notifications.
    GcmClient.Register(this, ToDoBroadcastReceiver.senderIDs);
    ```

O Seu **ToDoActivity** está agora preparado para adicionar notificações push.
