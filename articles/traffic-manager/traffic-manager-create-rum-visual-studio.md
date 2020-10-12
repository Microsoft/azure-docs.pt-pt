---
title: Medições reais do utilizador com Visual Studio Mobile Center - Azure Traffic Manager
description: Configurar a sua aplicação móvel desenvolvida utilizando o Visual Studio Mobile Center para enviar medições reais do utilizador para o Gestor de Tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: devx-track-js
ms.openlocfilehash: c49672ad38ac8cf80214d03870b5876d741e76ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259871"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Como enviar medições reais do utilizador para gerente de tráfego com o Visual Studio Mobile Center

Pode configurar a sua aplicação móvel desenvolvida utilizando o Visual Studio Mobile Center para enviar medições reais do utilizador para o Gestor de Tráfego seguindo os passos:

>[!NOTE]
> Atualmente, o envio de Medições reais do utilizador para o gestor de tráfego é apenas suportado para Android.

Para configurar as Medições Reais do Utilizador, é necessário obter uma chave e instrumento a sua aplicação com o pacote RUM.

## <a name="step-1-obtain-a-key"></a>Passo 1: Obter uma chave
    
As medições que efetua e envia para o Gestor de Tráfego da sua aplicação de cliente são identificadas pelo serviço utilizando uma cadeia única, chamada Chave de Medidas reais do Utilizador (RUM). Pode obter uma chave RUM utilizando o portal Azure, uma API REST ou utilizando as interfaces PowerShell/CLI.

Para obter a chave RUM utilizando o portal Azure utilizando o seguinte procedimento:
1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se para obter uma avaliação gratuita durante um mês.
2. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que pretende modificar e, em seguida, clique no perfil do Gestor de Tráfego nos resultados apresentados.
3. Na página de perfil do Gestor de Tráfego, clique em **"Real User Measurements"** em **Definições**.
4. Clique **em Gerar Tecla** para criar uma nova chave RUM.
        
   ![Gerar chave de medições reais do utilizador](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração-chave das medidas do utilizador real**

5. A página apresenta a chave RUM que é gerada e um corte de código JavaScript que precisa de ser incorporado na sua página HTML.
 
   ![Código Javascript para chave de medições reais do utilizador](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: Chave de medição do utilizador real e medição JavaScript**
 
6. Clique no botão **Copiar** para copiar a chave RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Passo 2: Instrumento a sua app com o pacote RUM do Mobile Center SDK

Se é novo no Visual Studio Mobile Center, visite o seu [site.](https://mobile.azure.com) Para obter instruções detalhadas sobre a integração do [SDK, consulte "Começar com o Android SDK".](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android)

Para utilizar as Medições Reais do Utilizador, complete o seguinte procedimento:

1.  Adicione o SDK ao projeto

    Durante a pré-visualização do ATM RUM SDK, você precisa referenciar explicitamente o repositório de pacotes.

    No seu ficheiro **app/build.gradle** adicione as seguintes linhas:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    No seu ficheiro **app/build.gradle** adicione as seguintes linhas:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Inicie o SDK

    Abra a principal classe de atividade da sua aplicação e adicione as seguintes declarações de importação:

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
- Saiba mais sobre [as Medições Reais do Utilizador](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gestor de Tráfego](traffic-manager-overview.md)
- Saiba mais sobre [o Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Inscreva-se no](https://mobile.azure.com) Mobile Center
- Saiba mais sobre os [métodos de encaminhamento de tráfego suportados](traffic-manager-routing-methods.md) pelo Traffic Manager
- Saiba como [criar um perfil de Gestor de Tráfego](traffic-manager-create-profile.md)

