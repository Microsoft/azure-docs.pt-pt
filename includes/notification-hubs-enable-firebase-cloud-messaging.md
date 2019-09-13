---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ccb5e40738680181e7339b8652d029597c7d0bd4
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935111"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na página **Adicionar firebase ao seu aplicativo Android** , execute as seguintes etapas: 
    1. Para o **nome do pacote do Android**, copie o valor de seu **ApplicationId** no arquivo Build. gradle do seu aplicativo. Neste exemplo, `com.fabrikam.fcmtutorial1app`é. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **registrar aplicativo**. 
4. Selecione **baixar Google-Services. JSON**, salve o arquivo na pasta do **aplicativo** do seu projeto e, em seguida, selecione **Avançar**. 

    ![Baixar o Google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Faça as seguintes **alterações de configuração** em seu projeto no Android Studio. 
    1.  No arquivo Build. gradle de nível de projeto (&lt;projeto&gt;/Build.gradle), adicione a instrução a seguir à seção **dependências** . 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. No arquivo Build. gradle de nível de aplicativo (&lt;Project&gt;/&lt;app-module&gt;/Build.gradle), adicione as instruções a seguir à seção **Dependencies** . 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Adicione a seguinte linha ao final do arquivo Build. gradle no nível do aplicativo após a seção Dependencies. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecione **sincronizar agora** na barra de ferramentas. 
 
        ![alterações de configuração do Build. gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecione **Seguinte**. 
7. Selecione **ignorar esta etapa**. 

    ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você não tiver baixado o arquivo Google-Services. JSON na pasta do **aplicativo** do seu projeto Android Studio, poderá fazer isso nesta página. 
5. Alterne para a guia de **mensagens de nuvem** na parte superior. 
6. Copie e salve a **chave do servidor** para uso posterior. Use esse valor para configurar o Hub.
