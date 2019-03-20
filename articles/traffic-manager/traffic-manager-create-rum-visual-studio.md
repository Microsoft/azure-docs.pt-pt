---
title: Medições de utilizador reais para Traffic Manager do Azure com o Visual Studio Mobile Center | Documentos da Microsoft
description: Configurar a sua aplicação móvel desenvolvida com o Visual Studio Mobile Center para enviar medições de utilizador reais para o Gestor de tráfego
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 1a5b883a8c9688d4545c0e98c00f78a2e982a611
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079723"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Como enviar medições de utilizador reais para o Gestor de tráfego com o Visual Studio Mobile Center

Pode definir a sua aplicação móvel desenvolvida com o Visual Studio Mobile Center para enviar medições de utilizador reais para o Gestor de tráfego ao seguir os passos:

>[!NOTE]
> Atualmente, o envio de medições de utilizador reais para o Gestor de tráfego apenas é suportada para Android.

Para configurar as medições de utilizador reais, terá de obter uma chave e instrumente a sua aplicação com o pacote de executar.

## <a name="step-1-obtain-a-key"></a>Passo 1: Obter uma chave
    
As medidas a tomar e enviados para o Gestor de tráfego a partir da sua aplicação de cliente são identificadas pelo serviço usando uma cadeia de caracteres exclusiva, chamada a chave de medições de utilizador Real (RUM). Pode obter uma chave de executar com o portal do Azure, uma API REST ou através do PowerShell / CLI interfaces.

Para obter a chave de rum do através do portal do Azure utilizando o seguinte procedimento:
1. Num browser, inicie sessão no portal do Azure. Se ainda não tiver uma conta, pode inscrever-se numa avaliação gratuita de um mês.
2. Na barra de pesquisa do portal, procure o nome de perfil do Gestor de tráfego que pretende modificar e, em seguida, clique no perfil do Gestor de tráfego nos resultados que apresentados.
3. Na página de perfil do Traffic Manager, clique em **medidas de utilizadores reais** sob **definições**.
4. Clique em **gerar chave** para criar uma nova chave de rum do.
        
   ![Gerar chave de medições de utilizador reais](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: Geração de chave de medições de utilizador real**

5. A página apresenta a chave de rum do que é gerado e um trecho de código JavaScript que tem de ser incorporados em sua página HTML.
 
   ![Código de JavaScript para a chave de medições de utilizador reais](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: Chave de medições de utilizador real e JavaScript de medida**
 
6. Clique nas **cópia** botão para copiar a chave de rum do. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Passo 2: Instrumente a sua aplicação com o pacote executar do SDK do Mobile Center

Se estiver familiarizado com o Visual Studio Mobile Center, visite seu [Web site](https://mobile.azure.com). Para obter instruções detalhadas sobre a integração do SDK, consulte [introdução ao Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Para utilizar medidas de utilizadores reais, execute o seguinte procedimento:

1.  Adicione o SDK ao projeto

    Durante a pré-visualização do ATM rum do SDK, precisa referenciar explicitamente o repositório do pacote.

    No seu **gradle** ficheiro, adicione as seguintes linhas:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    No seu **gradle** ficheiro, adicione as seguintes linhas:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Iniciar o SDK

    Abra a classe de atividade principal da sua aplicação e adicione as seguintes declarações de importação:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Procure o `onCreate` retorno de chamada no mesmo ficheiro e adicione o seguinte código:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Passos Seguintes
- Saiba mais sobre [medidas de utilizadores reais](traffic-manager-rum-overview.md)
- Saiba [como funciona o Gestor de tráfego](traffic-manager-overview.md)
- Saiba mais sobre [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Inscreva-se](https://mobile.azure.com) para Mobile Center
- Saiba mais sobre o [métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md) suportada pelo Gestor de tráfego
- Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-create-profile.md)

