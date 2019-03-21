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
ms.openlocfilehash: 90ffebf5f3375e94545f82a95f5c240ad845bd94
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57908291"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. Sobre o **adicionar Firebase à sua aplicação Android** página, efetue os seguintes passos: 
    1. Para **nome do pacote Android**, introduza um nome para o seu pacote. Por exemplo: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Especifique o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **registar aplicação**. 
4. Selecione **transferir google-Services. JSON**, guarde o ficheiro para o **app** pasta do seu projeto e, em seguida, selecione **seguinte**. 

    ![Baixe o google-Services. JSON](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
6. Selecione **seguinte** na página. 
7. Selecione **ignore este passo** na página. 

    ![Ignore o último](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se ainda não transferiu os **google-Services. JSON** arquivo, pode fazer assim por diante nesta página. 
5. Mude para o **Cloud Messaging** separador na parte superior. 
6. Copie e guarde o **chave de servidor** para utilização posterior. Utilize este valor para configurar o notification hub.
