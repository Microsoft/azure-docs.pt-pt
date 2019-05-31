---
title: Criar uma aplicação iOS nas Aplicações Móveis do Serviço de Aplicações do Azure | Microsoft Docs
description: Siga este tutorial para começar a utilizar back-ends de aplicações móveis do Azure para desenvolvimento iOS em Objective-C ou Swift
services: app-service\mobile
documentationcenter: ios
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6461a899-9340-42dd-b118-ffc5ba00e846
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: crdun
ms.openlocfilehash: 60190e0f8441d52b3d753e1dc79c67f480434dbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240256"
---
# <a name="create-an-ios-app"></a>Criar uma aplicação iOS

[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

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
