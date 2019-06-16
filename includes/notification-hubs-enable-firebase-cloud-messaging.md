---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 5f919a04b47aa6fdef9500f3d7e6bef4ddaa239e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66140320"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Sobre o **adicionar Firebase à sua aplicação Android** página, efetue os seguintes passos: 
    1. Para **nome do pacote Android**, copie o valor da sua **applicationId** em seu aplicativo **gradle** ficheiro. Neste exemplo, ele tem `com.fabrikam.fcmtutorial1app`. 

        ![Especifique o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **registar aplicação**. 
4. Selecione **transferir google-Services. JSON**, guarde o ficheiro para o **app** pasta do seu projeto e, em seguida, selecione **seguinte**. 

    ![Baixe o google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Faça o seguinte procedimento **alterações de configuração** ao seu projeto no Android Studio. 
    1.  No seu **nível de projeto gradle** ficheiro (&lt;projeto&gt;/build.gradle), adicione a seguinte instrução para o **dependências** secção. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. No seu **ao nível da aplicação gradle** ficheiro (&lt;projeto&gt;/&lt;app-module&gt;/build.gradle), adicione a seguinte instrução para o  **dependências** secção. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Adicione a seguinte linha ao final dos **ao nível da aplicação gradle** ficheiro depois da secção de dependências. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecione **sincronizar agora*** na barra de ferramentas. 
 
        ![alterações de configuração de gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecione **seguinte** na página. 
7. Selecione **ignore este passo** na página. 

    ![Ignore o último](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)1. 
8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se ainda não transferiu os **google-Services. JSON** de ficheiros para o **aplicação** pasta do seu projeto do Android Studio, pode fazer assim por diante nesta página. 
5. Mude para o **Cloud Messaging** separador na parte superior. 
6. Copie e guarde o **chave de servidor** para utilização posterior. Utilize este valor para configurar o notification hub.
