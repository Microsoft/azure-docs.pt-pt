---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/01/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 45bdd569741dc13181bcaf9e8587a02b3d02c621
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728831"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na página **Adicionar firebase ao seu aplicativo Android** , execute as seguintes etapas: 
    1. Para o **nome do pacote do Android**, insira um nome para o pacote. Por exemplo: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **registrar aplicativo**.  
    1. Selecione **baixar Google-Services. JSON**. Em seguida, salve o arquivo na pasta do seu projeto e selecione **Avançar**. Se você ainda não criou o projeto do Visual Studio, poderá fazer essa etapa depois de criar o projeto. 

        ![Baixar o Google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selecione **Seguinte**. 
    7. Selecione **ignorar esta etapa**. 

        ![Ignorar a última etapa](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se você não tiver baixado o arquivo **Google-Services. JSON** , poderá baixá-lo nesta página. 

    ![Baixar o Google-Services. JSON na guia geral](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Alterne para a guia de **mensagens de nuvem** na parte superior. Copie e salve a **chave do servidor** para uso posterior. Use esse valor para configurar o Hub de notificação.

    ![Copiar chave do servidor](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
