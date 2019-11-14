---
title: Medidas de Usuário Reais com o Visual Studio Mobile Center-Gerenciador de tráfego do Azure
description: Configurar seu aplicativo móvel desenvolvido usando o Visual Studio Mobile Center para enviar Medidas de Usuário Reais para o Gerenciador de tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 734049a45eca2688b2ad309ee3245bbb7bf152de
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74040310"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Como enviar Medidas de Usuário Reais para o Gerenciador de tráfego com o Visual Studio Mobile Center

Você pode configurar seu aplicativo móvel desenvolvido usando o Visual Studio Mobile Center para enviar Medidas de Usuário Reais ao Gerenciador de tráfego seguindo as etapas:

>[!NOTE]
> Atualmente, só há suporte para o envio de Medidas de Usuário Reais ao Gerenciador de tráfego no Android.

Para configurar Medidas de Usuário Reais, você precisa obter uma chave e instrumentar seu aplicativo com o pacote RUM.

## <a name="step-1-obtain-a-key"></a>Etapa 1: obter uma chave
    
As medidas que você usa e envia para o Gerenciador de tráfego do seu aplicativo cliente são identificadas pelo serviço usando uma cadeia de caracteres exclusiva, chamada de chave de Medidas de Usuário Reais (RUM). Você pode obter uma chave RUM usando o portal do Azure, uma API REST ou usando as interfaces do PowerShell/CLI.

Para obter a chave RUM usando portal do Azure usando o procedimento a seguir:
1. Em um navegador, entre no portal do Azure. Se você ainda não tiver uma conta, poderá se inscrever para uma avaliação gratuita de um mês.
2. Na barra de pesquisa do portal, pesquise o nome do perfil do Gerenciador de tráfego que você deseja modificar e, em seguida, clique no perfil do Gerenciador de tráfego nos resultados exibidos.
3. Na página perfil do Gerenciador de tráfego, clique em **medidas de usuário reais** em **configurações**.
4. Clique em **gerar chave** para criar uma nova chave rum.
        
   ![Gerar chave de Medidas de Usuário Reais](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: geração de chave de Medidas de Usuário Reais**

5. A página exibe a chave RUM que é gerada e um trecho de código JavaScript que precisa ser inserido em sua página HTML.
 
   ![Código JavaScript para chave de Medidas de Usuário Reais](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: Medidas de Usuário Reais o JavaScript de medida e de chave**
 
6. Clique no botão **copiar** para copiar a chave rum. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Etapa 2: instrumentar seu aplicativo com o pacote RUM do SDK do Mobile Center

Se você for novo no Visual Studio Mobile Center, visite seu [site](https://mobile.azure.com). Para obter instruções detalhadas sobre a integração do SDK, consulte [introdução com o SDK do Android](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Para usar Medidas de Usuário Reais, conclua o seguinte procedimento:

1.  Adicionar o SDK ao projeto

    Durante a versão prévia do SDK do RUM do ATM, você precisa referenciar explicitamente o repositório do pacote.

    No arquivo **app/Build. gradle** , adicione as seguintes linhas:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    No arquivo **app/Build. gradle** , adicione as seguintes linhas:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Iniciar o SDK

    Abra a classe de atividade principal do aplicativo e adicione as seguintes instruções de importação:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Procure o `onCreate` retorno de chamada no mesmo arquivo e adicione o seguinte código:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre o [medidas de usuário reais](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gerenciador de tráfego](traffic-manager-overview.md)
- Saiba mais sobre o [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Inscrever-se](https://mobile.azure.com) no Mobile Center
- Saiba mais sobre os [métodos de roteamento de tráfego](traffic-manager-routing-methods.md) com suporte pelo Gerenciador de tráfego
- Saiba como [criar um perfil do Gerenciador de tráfego](traffic-manager-create-profile.md)

