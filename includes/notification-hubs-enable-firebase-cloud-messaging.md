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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "70935111"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Na base de fogo adicionar à sua página de **aplicações Android,** tome os seguintes passos: 
    1. Para o nome do **pacote Android,** copie o valor da sua **aplicaçãoId** no ficheiro build.gradle da sua aplicação. Neste exemplo, `com.fabrikam.fcmtutorial1app`é. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **a aplicação Register**. 
4. Selecione **Descarregue o google-services.json,** guarde o ficheiro na pasta de **aplicações** do seu projeto e, em seguida, selecione **Next**. 

    ![Descarregue google-services.json](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Faça as **seguintes alterações** de configuração no seu projeto no Android Studio. 
    1.  No seu ficheiro build.gradle&lt;ao&gt;nível do projeto (project /build.gradle), adicione a seguinte declaração à secção **de dependências.** 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. No seu ficheiro build.gradle&lt;ao&gt;/&lt;nível&gt;da aplicação (project app-module /build.gradle), adicione as seguintes declarações à secção **de dependências.** 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.8'
        implementation 'com.google.firebase:firebase-messaging:17.3.4'
        ```

    3. Adicione a seguinte linha ao fim do ficheiro build.gradle de nível de aplicação após a secção de dependências. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Selecione **Sync agora** na barra de ferramentas. 
 
        ![build.gradle alterações de configuração](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Selecione **Seguinte**. 
7. Selecione **Saltar este passo**. 

    ![Salta o último passo](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se ainda não descarregou o ficheiro google-services.json na pasta de **aplicações** do seu projeto Android Studio, pode fazê-lo nesta página. 
5. Mude para o separador **Cloud Messaging** na parte superior. 
6. Copie e guarde a **tecla Server** para posterior utilização. Usa este valor para configurar o seu hub.
