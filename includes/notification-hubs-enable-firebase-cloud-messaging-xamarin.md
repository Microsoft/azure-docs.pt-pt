---
title: incluir ficheiro
description: incluir ficheiro
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e047ba57a61d2f327544ec795f640f5066962f6
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509900"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Sobre o **adicionar Firebase à sua aplicação Android** página, siga os passos seguintes: 
1. 
    1. Para o **nome do pacote Android**, introduza um nome para o seu pacote. Por exemplo: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Especifique o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)

2. Selecione **registar aplicação**. 
1. 
1. Selecione **transferir google-Services. JSON**. Em seguida, guarde o ficheiro para o **app** pasta do seu projeto e selecione **próxima**. 

    ![Baixe o google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)

6. Selecione **Seguinte**. 
7. Selecione **ignore este passo**. 

    ![Ignore o último](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)

8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

4. Se ainda não transferiu os **google-Services. JSON** arquivo, pode fazer baixá-lo nesta página. 

1. Mude para o **Cloud Messaging** separador na parte superior. 

1. Copie e guarde o **chave de servidor do legado** para utilização posterior. Utilize este valor para configurar o notification hub.
