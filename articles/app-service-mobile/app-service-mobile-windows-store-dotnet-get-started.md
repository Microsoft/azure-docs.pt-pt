---
title: Criar um Windows plataforma Universal (UWP) que utiliza aplicações móveis do Azure | Documentos da Microsoft
description: Siga este tutorial para começar a utilizar back-ends da aplicação móvel do Azure para o desenvolvimento da aplicação Plataforma Universal do Windows (UWP) em C#, Visual Basic ou JavaScript.
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 47124296-2908-4d92-85e0-05c4aa6db916
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: be579b631fd910c56f2c360d6aace5b8d35c22e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66236005"
---
# <a name="create-a-windows-app-with-an-azure-backend"></a>Criar uma aplicação do Windows com um back-end do Azure

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação Plataforma Universal do Windows (UWP). Para obter mais informações, consulte [O que são Mobile Apps](app-service-mobile-value-prop.md). As capturas de ecrã seguintes mostram a aplicação completa:

![Aplicação de ambiente de trabalho completa](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-completed-desktop.png)

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Mobile Apps para aplicações UWP.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa do seguinte:

* Uma conta ativa do Azure. Se não tiver uma conta, pode inscrever-se para uma versão de avaliação do Azure e obter até 10 Mobile Apps gratuitas, que pode continuar a utilizar mesmo após o final do período de avaliação. Para obter mais detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Windows 10.
* Visual Studio Community 2017.
* Familiaridade com o desenvolvimento de aplicações do UWP. Visite o [documentação da UWP](https://docs.microsoft.com/windows/uwp/) para saber como [mãos à obra](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) para criar aplicações do UWP.

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end da Aplicação Móvel do Azure

Siga estes passos para criar um novo back-end da Aplicação Móvel.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação de base de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-client-project"></a>Execute o projeto de cliente

1. Abra o projeto do UWP.

2. Vá para o [portal do Azure](https://portal.azure.com/) e navegue para a aplicação móvel que criou. Sobre o `Overview` painel, procure o URL que é o ponto final público para a sua aplicação móvel. Exemplo - sitename para meu nome de aplicação "test123" será https://test123.azurewebsites.net.

3. Abra o ficheiro `App.xaml.cs` nesta pasta - windows-uwp-cs/ZUMOAPPNAME /. O nome da aplicação é `ZUMOAPPNAME`.

4. Na `App` classe, substitua `ZUMOAPPURL` parâmetro com o ponto final público acima.

    `public static MobileServiceClient MobileService = new MobileServiceClient("ZUMOAPPURL");`

    torna-se
    
    `public static MobileServiceClient MobileService = new MobileServiceClient("https://test123.azurewebsites.net");`

5. Prima a tecla F5 para implementar e executar a aplicação.

6. Na aplicação, digite um texto significativo, tal como *Concluir o tutorial*, na caixa de texto **Inserir um Item da Lista a Fazer** e, em seguida, clique no botão **Guardar**.

    ![Ambiente de trabalho completo do início rápido do Windows](./media/app-service-mobile-windows-store-dotnet-get-started/mobile-quickstart-startup.png)

    Esta ação permite enviar um pedido POST para o novo back-end da aplicação móvel que está alojado no Azure.
