---
title: Criar uma aplicação iOS nas Aplicações Móveis do Serviço de Aplicações do Azure | Microsoft Docs
description: Siga este tutorial para começar a utilizar back-ends de aplicações móveis do Azure para desenvolvimento iOS em Objective-C ou Swift
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9140de50e23447f53fd1fa204a2a67c324672397
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449148"
---
# <a name="create-an-ios-app"></a>Criar uma aplicação iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started) hoje mesmo.
>

## <a name="overview"></a>Descrição geral

Este tutorial mostra como adicionar [Aplicações Móveis do Serviço de Aplicações do Azure](app-service-mobile-value-prop.md), um serviço de back-end na cloud, a uma aplicação iOS. O primeiro passo é criar um novo back-end móvel no Azure. Em seguida, transfira uma aplicação de exemplo iOS da *Lista de tarefas* que armazena dados no Azure.

Para concluir este tutorial, precisa de um Mac e de [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end da aplicação móvel do Azure

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação de base de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-ios-app"></a>Executar a aplicação iOS

[!INCLUDE [app-service-mobile-ios-run-app](../../includes/app-service-mobile-ios-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
