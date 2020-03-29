---
title: Medições reais do utilizador com Centro Móvel do Estúdio Visual - Gestor de Tráfego Azure
description: Configurar a sua aplicação móvel desenvolvida utilizando o Visual Studio Mobile Center para enviar medições reais de utilizador para o Traffic Manager
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 3106334e1fb3e3000cbd09e00e413b34a1b55e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76939192"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Como enviar medições reais de utilizador para Gestor de Tráfego com Centro Móvel de Estúdio Visual

Pode configurar a sua aplicação móvel desenvolvida através do Visual Studio Mobile Center para enviar medições reais de utilizador ao Traffic Manager seguindo os passos:

>[!NOTE]
> Atualmente, o envio de Medições reais de Utilizador para o Gestor de Tráfego só é suportado para Android.

Para configurar as Medições reais do Utilizador, é necessário obter uma chave e um instrumento da sua aplicação com o pacote RUM.

## <a name="step-1-obtain-a-key"></a>Passo 1: Obter uma chave
    
As medições que efetua e enviadas para o Traffic Manager a partir da sua aplicação cliente são identificadas pelo serviço utilizando uma cadeia única, chamada Chave de Medições reais do Utilizador (RUM). Pode obter uma chave RUM utilizando o portal Azure, uma API REST ou utilizando as interfaces PowerShell/CLI.

Para obter a chave RUM utilizando o portal Azure utilizando o seguinte procedimento:
1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se para obter uma avaliação gratuita durante um mês.
2. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. Na página de perfil do Gestor de Tráfego, clique em **Medições reais do Utilizador** em **Definições**.
4. Clique em **Criar** uma nova chave RUM.
        
   ![Gerar chave de medição real do utilizador](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Real User Measurements geração chave**

5. A página apresenta a Chave RUM que é gerada e um corte de código JavaScript que precisa de ser incorporado na sua página HTML.
 
   ![Código javascript para chave de medição real do utilizador](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: Chave de medição real do utilizador e javaScript de medição**
 
6. Clique no botão **Copiar** para copiar a Tecla RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Passo 2: Instrumente a sua aplicação com o pacote RUM do Mobile Center SDK

Se é novo no Visual Studio Mobile Center, visite o seu [website.](https://mobile.azure.com) Para obter instruções detalhadas sobre a integração do SDK, consulte [Getting Started with the Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Para utilizar medições reais do utilizador, complete o seguinte procedimento:

1.  Adicione o SDK ao projeto

    Durante a pré-visualização do Multibanco RUM SDK, é necessário fazer referência explícita ao repositório do pacote.

    No ficheiro **app/build.gradle** adicione as seguintes linhas:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    No ficheiro **app/build.gradle** adicione as seguintes linhas:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Inicie o SDK

    Abra a principal classe de atividadeda da sua aplicação e adicione as seguintes declarações de importação:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Procure a `onCreate` chamada no mesmo ficheiro e adicione o seguinte código:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre [as medições reais do utilizador](traffic-manager-rum-overview.md)
- Saiba como funciona o [Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre [o Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Inscreva-se](https://mobile.azure.com) no Mobile Center
- Saiba mais sobre os [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportados pelo Traffic Manager
- Saiba como [criar um perfil de Gestor](traffic-manager-create-profile.md) de Tráfego

