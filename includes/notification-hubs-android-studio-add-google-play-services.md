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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67509084"
---
1. No Android Studio, selecione **Tools** no menu e, em seguida, selecione **SDK Manager**. 
2. Selecione a versão alvo do Android SDK que é usado no seu projeto. Em seguida, selecione **Mostrar Detalhes do Pacote**. 

    ![Android SDK Manager - selecione versão-alvo](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. Selecione **APIs do Google,** se ainda não estiver instalado.

    ![Android SDK Manager - Google APIs selecionado](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. Mude para o separador **Ferramentas SDK.** Se ainda não instalou o Google Play Services, selecione **o Google Play Services** como mostrado na imagem seguinte. Em seguida, **selecione Aplicar** para instalar. Anote o caminho do SDK, para utilização num passo posterior.

    ![Android SDK Manager - Google Play Services selecionados](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. Se vir a caixa de diálogo **'Alterar'** confirmando,selecione **OK**. O Instalador de Componentes instala os componentes solicitados. **Selecione Acabamento** após a instalação dos componentes.
4. Selecione **OK** para fechar a caixa de diálogo **De Definições para Novos Projetos.**  
5. Abra o ficheiro build.gradle no diretório da **aplicação** e, em seguida, adicione a seguinte linha sob `dependencies` . 

    ```gradle
    implementation 'com.google.android.gms:play-services-gcm:16.0.0'
    ```
5. Selecione **Sync Now** ícone na barra de ferramentas.

    ![Sincronização com Gradle](./media/notification-hubs-android-studio-add-google-play-services/gradle-sync.png)
1. Abra o ficheiro AndroidManifest.xml e, em seguida, adicione a seguinte etiqueta à etiqueta *de inscrição.*

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```
