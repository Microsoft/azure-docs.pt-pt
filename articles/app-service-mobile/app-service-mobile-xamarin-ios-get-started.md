---
title: Introdução às Aplicações Móveis do Serviço de Aplicações do Azure para aplicações Xamarin.iOS | Microsoft Docs
description: Siga este tutorial para começar a utilizar Mobile Apps para o desenvolvimento do Xamarin.iOS
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 14428794-52ad-4b51-956c-deb296cafa34
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: crdun
ms.openlocfilehash: 559050cbc575fce5bdb5b32ec266e1cc3d09b2d5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242712"
---
# <a name="create-a-xamarinios-app"></a>Criar uma aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Xamarin.iOS utilizando um back-end da aplicação móvel do Azure.  Pode criar tanto um novo back-end da aplicação móvel assim como uma simples aplicação Xamarin.iOS de uma *Lista de tarefas* que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Xamarin.iOS referentes à utilização da funcionalidade Mobile Apps no App Service do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma conta, inscreva-se para uma versão de avaliação do Azure e obtenha até 10 aplicações móveis gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio para Mac. Consulte [configuração e instalação do Visual Studio para Mac](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)
* Um Mac com Xcode 9.0 ou posterior.
  
## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end da Aplicação Móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação de base de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-xamarinios-app"></a>Executar a aplicação xamarin. IOS
1. Abra o projeto xamarin. IOS.

2. Vá para o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Sobre o `Overview` painel, procure o URL que é o ponto final público para a sua aplicação móvel. Exemplo - sitename para meu nome de aplicação "test123" será https://test123.azurewebsites.net.

3. Abra o ficheiro `QSTodoService.cs` nesta pasta - xamarin.iOS/ZUMOAPPNAME. O nome da aplicação é `ZUMOAPPNAME`.

4. Na `QSTodoService` classe, substitua `ZUMOAPPURL` variável com o ponto final público acima.

    `const string applicationURL = @"ZUMOAPPURL";`

    torna-se
    
    `const string applicationURL = @"https://test123.azurewebsites.net";`
    
5. Prima a tecla F5 para implementar e executar a aplicação no emulador do iPhone.

6. Na aplicação, digite um texto significativo, tal como *concluir o tutorial* e, em seguida, clique o botão +.

    ![][10]

    Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

   > [!NOTE]
   > Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados (presente no ficheiro ToDoActivity.cs c#).
   
<!-- Images. -->
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png