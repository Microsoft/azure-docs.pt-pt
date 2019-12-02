---
title: Adicionar notificações push à aplicação Xamarin.Android
description: Saiba como usar o serviço de Azure App e os hubs de notificação do Azure para enviar notificações por push para seu aplicativo Xamarin. Android.
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: c9dc4c825d65287f152522868a2b9e6a38ea70bb
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668735"
---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar notificações push à aplicação Xamarin.Android

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se quiser integrar serviços cloud na sua aplicação móvel, inscreva-se no [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral

Neste tutorial, você adicionará notificações por push ao projeto de [início rápido do Xamarin. Android](app-service-mobile-windows-store-dotnet-get-started.md) para que uma notificação por push seja enviada ao dispositivo toda vez que um registro for inserido.

Se você não usar o projeto baixado do servidor de início rápido, será necessário o pacote de extensão de notificação por push. Para obter mais informações, consulte o guia [trabalhar com o SDK do servidor de back-end do .net para aplicativos móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) .

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer a configuração:

* Uma conta do Google ativa. Você pode se inscrever para uma conta do Google em [accounts.google.com](https://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Google Cloud Messaging componente de cliente](https://components.xamarin.com/view/GCMClient/).

## <a name="configure-hub"></a>Configurar um hub de notificação

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Habilitar o firebase Cloud Messaging

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configurar o Azure para enviar solicitações por push

[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>Atualizar o projeto de servidor para enviar notificações por push

[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>Configurar o projeto do cliente para notificações por push

[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo

[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>Testar notificações por push em seu aplicativo

Você pode testar o aplicativo usando um dispositivo virtual no emulador. Há etapas de configuração adicionais necessárias ao executar em um emulador.

1. O dispositivo virtual deve ter APIs do Google definidas como o destino no Gerenciador de dispositivo virtual Android (AVD).

    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)

2. Adicione uma conta do Google ao dispositivo Android clicando em **aplicativos** > **configurações** > **adicionar conta**e, em seguida, siga os prompts.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)

3. Execute o aplicativo ToDoList como antes e insira um novo item de tarefas pendentes. Desta vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificação para exibir o texto completo da notificação.

    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: https://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: https://components.xamarin.com/view/azure-mobile-services/
