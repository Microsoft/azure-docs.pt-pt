---
title: incluir ficheiro
description: incluir ficheiro
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: f55bdc774437d280db51fb69f060bea7dc579a26
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203770"
---
## <a name="set-up-your-project"></a>Configurar seu projeto

Pretende transferir o projeto do Android Studio este exemplo em vez disso? [Transfira um projeto](https://github.com/Azure-Samples/active-directory-android-native-v2/archive/master.zip)e avançar para o [passo de configuração](#register-your-application) para configurar o exemplo de código antes de executá-lo.

### <a name="create-a-new-project"></a>Criar um novo projeto

1. Abra o Android Studio e, em seguida, selecione **arquivo** > **New** > **novo projeto**.
2. Nome à aplicação e, em seguida, selecione **seguinte**.
3. Selecione **21 de API ou mais recente (Android 5.0)** e, em seguida, selecione **próxima**.
4. Deixe **atividade vazia** como é, selecione **próxima**e, em seguida, selecione **concluir**.

### <a name="add-msal-to-your-project"></a>Adicionar MSAL ao seu projeto

1. No Android Studio, selecione **Scripts de Gradle** > **gradle (módulo: aplicação)**.
2. Sob **dependências**, cole o seguinte código:

    ```gradle  
    compile ('com.microsoft.identity.client:msal:0.1.+') {
        exclude group: 'com.android.support', module: 'appcompat-v7'
    }
    compile 'com.android.volley:volley:1.0.0'
    ```

<!--start-collapse-->
### <a name="about-this-package"></a>Sobre este pacote

O pacote no código anterior instala o Microsoft Authentication Library (MSAL), que manipula todas as operações de token, incluindo aquisição, colocação em cache, atualizar e eliminar. Os tokens são necessárias para aceder às APIs do protegidos por plataforma de identidades da Microsoft.
<!--end-collapse-->

## <a name="create-the-apps-ui"></a>Criar a IU da aplicação

1. Aceda a **res** > **layout**e, em seguida, abra **ctivity_main**.
2. Alterar o esquema de atividade de `android.support.constraint.ConstraintLayout` ou outras para `LinearLayout`.
3. Adicionar a `android:orientation="vertical"` propriedade o `LinearLayout` nó.
4. Cole o código seguinte para o `LinearLayout` nó, substituindo o conteúdo atual:

    ```xml
    <TextView
        android:text="Welcome, "
        android:textColor="#3f3f3f"
        android:textSize="50px"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="10dp"
        android:layout_marginTop="15dp"
        android:id="@+id/welcome"
        android:visibility="invisible"/>

    <Button
        android:id="@+id/callGraph"
        android:text="Call Microsoft Graph"
        android:textColor="#FFFFFF"
        android:background="#00a1f1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginTop="200dp"
        android:textAllCaps="false" />

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dip"
        android:layout_weight="1"
        android:gravity="center|bottom"
        android:orientation="vertical" >

        <Button
            android:text="Sign Out"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_marginBottom="15dp"
            android:textColor="#FFFFFF"
            android:background="#00a1f1"
            android:textAllCaps="false"
            android:id="@+id/clearCache"
            android:visibility="invisible" />
    </LinearLayout>
    ```
