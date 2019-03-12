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
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: aaa7db14f3afe8eec000e14c7f5a9db032b87fe0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765939"
---
# <a name="create-a-xamarinios-app"></a>Criar uma aplicação Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Xamarin.iOS utilizando um back-end da aplicação móvel do Azure.  Pode criar tanto um novo back-end da aplicação móvel assim como uma simples aplicação Xamarin.iOS de uma *Lista de tarefas* que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Xamarin.iOS referentes à utilização da funcionalidade Mobile Apps no App Service do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Uma conta ativa do Azure. Se não tiver uma conta, inscreva-se para uma versão de avaliação do Azure e obtenha até 10 aplicações móveis gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Visual Studio com Xamarin. Para obter instruções, consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
* Um Mac com Xcode v7.0 ou posterior e Xamarin Studio Community instalado. Consulte [Configuração e instalação do Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) e [Configuração, instalação e verificações para utilizadores Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end da Aplicação Móvel do Azure
Siga estes passos para criar um back-end da Aplicação Móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor
Acabou de aprovisionar um back-end da Aplicação Móvel do Azure que pode ser utilizado pelas suas aplicações cliente móveis. Em seguida, transfira um projeto de servidor para um back-end simples de uma “lista de tarefas” e publique-o no Azure.

Siga os passos seguintes para configurar o projeto de servidor para utilizar o back-end Node.js ou .NET.

[!INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Transferir e executar a aplicação Xamarin.iOS
1. Abra o [Portal do Azure] numa janela do browser.
2. No painel de definições da Aplicação Móvel, clique em **Introdução** > **Xamarin.iOS**. No passo 3, clique em **Criar uma nova aplicação**, se a opção ainda não estiver selecionada.  Em seguida, clique no botão **Transferir**.

      Será transferida uma aplicação cliente que liga ao back-end móvel. Guarde o ficheiro de projeto comprimido no computador local e tome nota do local onde o guardou.
3. Extraia o projeto que transferiu e, em seguida, abra-o no Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]
4. Prima a tecla F5 para compilar o projeto e iniciar a aplicação no emulador do iPhone.
5. Na aplicação, digite um texto significativo, tal como *Saber mais sobre o Xamarin*, e clique no botão **+**.

    ![][10]

    Os dados do pedido são inseridos na tabela TodoItem. Os itens armazenados na tabela são devolvidos pelo back-end da aplicação móvel e os dados são apresentados na lista.

> [!NOTE]
> Pode rever o código que acede ao seu back-end da aplicação móvel para consultar e inserir dados no ficheiro QSTodoService.cs C#.
>
>

## <a name="next-steps"></a>Passos Seguintes
* [Adicionar Sincronização Offline à sua aplicação](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Adicionar autenticação à aplicação](app-service-mobile-xamarin-ios-get-started-users.md)
* [Adicionar notificações push à aplicação Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Como utilizar o cliente gerido para Mobile Apps do Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/
