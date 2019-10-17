---
title: Introdução às Aplicações Móveis do Serviço de Aplicações do Azure para aplicações Xamarin.iOS | Microsoft Docs
description: Siga este tutorial para começar a utilizar Mobile Apps para o desenvolvimento do Xamarin.iOS
services: app-service\mobile
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 14fb925896fc94def59b753711eb74f155b2e139
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388364"
---
# <a name="create-a-xamarinios-app"></a>Criar uma aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> O Visual Studio App Center suporta serviços de ponto a ponto e integrados, fundamentais para o desenvolvimento de aplicações móveis. Os programadores podem utilizar os serviços de **Compilação**, **Teste** e **Distribuição** para configurar o pipeline de Integração e Entrega Contínuas. Após a implementação da aplicação, os programadores podem monitorizar o estado e a utilização da aplicação através dos serviços de **Análise** e de **Diagnóstico** e interagir com os utilizadores através do serviço **Push**. Os programadores também podem tirar partido da **Autenticação** para autenticar os utilizadores e do serviço de **Dados** para manter e sincronizar os dados da aplicação na cloud.
>
> Se você estiver procurando integrar os serviços de nuvem em seu aplicativo móvel, Inscreva-se com o [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) hoje mesmo.

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Xamarin.iOS utilizando um back-end da aplicação móvel do Azure.  Pode criar tanto um novo back-end da aplicação móvel assim como uma simples aplicação Xamarin.iOS de uma *Lista de tarefas* que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Xamarin.iOS referentes à utilização da funcionalidade Mobile Apps no App Service do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma conta, inscreva-se para uma versão de avaliação do Azure e obtenha até 10 aplicações móveis gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio para Mac. Consulte [instalação e instalação para Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Um Mac com o Xcode 9,0 ou posterior.
  
## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end da Aplicação Móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma conexão de banco de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Executar o aplicativo Xamarin. iOS
1. Abra o projeto Xamarin. iOS.

2. Vá para a [portal do Azure](https://portal.azure.com/) e navegue até o aplicativo móvel que você criou. Na folha `Overview`, procure a URL que é o ponto de extremidade público para seu aplicativo móvel. Exemplo – o sitename para o nome do meu aplicativo "test123" será https://test123.azurewebsites.net.

3. Abra o arquivo `QSTodoService.cs` nesta pasta-xamarin. iOS/ZUMOAPPNAME. O nome do aplicativo é `ZUMOAPPNAME`.

4. Na classe `QSTodoService`, substitua a variável `ZUMOAPPURL` pelo ponto de extremidade público acima.

    `const string applicationURL = @"ZUMOAPPURL";`

    ficará
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Pressione a tecla F5 para implantar e executar o aplicativo em um emulador do iPhone.

6. No aplicativo, digite um texto significativo, como *concluir o tutorial* e, em seguida, clique no botão +.

    ![][10]

    Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

   > [!NOTE]
   > Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados (presente no ficheiro ToDoActivity.cs c#).
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png