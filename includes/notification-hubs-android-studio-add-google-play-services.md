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
ms.openlocfilehash: cd202c98ed605209f5600965ecdb6c0b4c03c17e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509084"
---
1. No Android Studio, selecione **ferramentas** no menu e, em seguida, selecione **SDK Manager**. 
2. Selecione a versão de destino do Android SDK utilizada no seu projeto. Em seguida, selecione **Mostrar detalhes do pacote**. 

    ![Android SDK Manager – versão de destino selecione](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google**, se ainda não estiver instalado.

    ![Android SDK Manager - selecionado de APIs do Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Mude para o **ferramentas do SDK** separador. Se ainda não instalou serviços do Google Play, selecione **serviços do Google Play** conforme mostrado na imagem seguinte. Em seguida, selecione **aplicar** para instalar. Anote o caminho do SDK, para utilização num passo posterior.

    ![Android SDK Manager – serviços do Google Play selecionado](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se vir a **Confirmar alteração** caixa de diálogo, selecione **OK**. O componente de instalador instala os componentes solicitados. Selecione **concluir** depois dos componentes são instalados.
4. Selecione **OK** para fechar a **definições para novos projetos** caixa de diálogo.  
5. Abra o ficheiro de gradle na **app** directory e, em seguida, adicione a seguinte linha em `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecione **sincronizar agora** ícone na barra de ferramentas.

    ![Sincronização com o Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Abra o ficheiro androidmanifest. XML e, em seguida, adicione a seguinte marca para o *aplicativo* marca.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
