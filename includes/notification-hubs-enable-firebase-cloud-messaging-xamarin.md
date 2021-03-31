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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "68728831"
---
1. Inicie sessão na [consola Firebase](https://firebase.google.com/console/). Crie um novo projeto Firebase se ainda não tiver um.
2. Depois de criar o projeto, selecione **Adicionar Firebase à aplicação Android**. 

    ![Adicionar Firebase à aplicação Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

3. Na **base de adicionar fogo à sua** página de aplicativo Android, tome os seguintes passos: 
    1. Para o nome do **pacote Android,** insira um nome para o seu pacote. Por exemplo: `tutorials.tutoria1.xamarinfcmapp`. 

        ![Especificar o nome do pacote](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Selecione **o aplicativo Registar**.  
    1. Selecione **Baixar google-services.jsem**. Em seguida, guarde o ficheiro na pasta do seu projeto e selecione **Seguinte**. Se ainda não criou o projeto Visual Studio, pode dar este passo depois de criar o projeto. 

        ![Baixar google-services.jsem](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
    6. Selecione **Seguinte**. 
    7. **Selecione Saltar este passo**. 

        ![Salta o último passo](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. Na consola da Firebase, selecione o cog do seu projeto. Em seguida, selecione **Definições do projeto**.

    ![Selecione Definições do projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Se ainda não descarregou o **google-services.jsno** ficheiro, pode fazê-lo baixar nesta página. 

    ![Faça o download google-services.jsa partir do separador Geral](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-services-json-general-page.png)
1. Mude para o **separador Cloud Messaging** no topo. Copie e guarde a **tecla Server** para utilização posterior. Utilize este valor para configurar o seu centro de notificação.

    ![Chave do servidor de cópia](./media/notification-hubs-enable-firebase-cloud-messaging/server-key.png)
