---
title: Criar um Plataforma Universal do Windows (UWP) que usa os aplicativos móveis do Azure | Microsoft Docs
description: Siga este tutorial para começar a utilizar back-ends da aplicação móvel do Azure para o desenvolvimento da aplicação Plataforma Universal do Windows (UWP) em C#, Visual Basic ou JavaScript.
services: app-service\mobile
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3ddd5ef197740315add2960971c978addb7fe62a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388552"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Criar um aplicativo do Windows com um back-end do Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Plataforma Universal do Windows (UWP). Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md). As capturas de ecrã seguintes mostram a aplicação completa:

![Aplicação de ambiente de trabalho completa](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações UWP.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Familiaridade com o desenvolvimento de aplicativos UWP. Visite a [documentação do UWP](https://docs.microsoft.com/windows/uwp/) para saber como [obter uma configuração](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para criar aplicativos UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end da Aplicação Móvel do Azure

Siga estes passos para criar um novo back-end da Aplicação Móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Executar o projeto do cliente

1. Abra o projeto UWP.

2. Vá para a [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na folha `Overview`, procure a URL que é o ponto de extremidade público para seu aplicativo móvel. Exemplo – o sitename para o nome do meu aplicativo "test123" será https://test123.azurewebsites.net.

3. Abra o arquivo `App.xaml.cs` nesta pasta-Windows-UWP-cs/ZUMOAPPNAME/. O nome do aplicativo é `ZUMOAPPNAME`.

4. Na classe `App`, substitua o parâmetro `ZUMOAPPURL` pelo ponto de extremidade público acima.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    ficará
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Pressione a tecla F5 para implantar e executar o aplicativo.

6. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, na caixa de texto **Inserir um Item da Lista a Fazer** e, em seguida, clique no botão **Guardar**.

    ![Ambiente de trabalho completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel que está alojado no Azure.
