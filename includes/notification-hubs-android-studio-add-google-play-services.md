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
ms.openlocfilehash: fb27386881e89cd9056d0efccb7d3c301867bd83
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156846"
---
1. Na **Android Studio**, selecione **ferramentas** no menu e selecione **SDK Manager**. 
2. Selecione a versão de destino do Android SDK utilizada no seu projeto e selecione **Mostrar detalhes do pacote**. 

    ![Android SDK Manager – versão de destino selecione](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google**, se ainda não estiver instalado.

    ![Android SDK Manager - selecionado de APIs do Google](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Mude para o **ferramentas do SDK** separador. Se ainda não tiver instalado o serviço do Google Play, selecione **serviços do Google Play** conforme mostrado na imagem seguinte. Em seguida, clique em **Aplicar** para instalar. Anote o caminho do SDK, para utilização num passo posterior.

    ![Android SDK Manager – serviços do Google Play selecionado](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se vir a **Confirmar alteração** caixa de diálogo, selecione **OK**. O componente de instalador instala os componentes solicitados. Selecione **concluir** depois dos componentes são instalados.
4. Selecione **OK** para fechar a **definições para novos projetos** caixa de diálogo.  
5. Abra o `build.gradle` de ficheiros a **aplicação** directory e adicione esta linha sob `dependencies`. 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecione **sincronizar agora** ícone na barra de ferramentas.

    ![Sincronização com o Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Abra **AndroidManifest.xml** e adicione esta etiqueta à etiqueta *aplicação*.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
