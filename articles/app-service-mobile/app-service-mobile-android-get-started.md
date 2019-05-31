---
title: Criar uma aplicação Android nas Aplicações Móveis do Serviço de Aplicações do Azure | Microsoft Docs
description: Siga este tutorial para começar a utilizar back-ends de aplicações móveis do Azure para desenvolvimento do Android
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34a9
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: crdun
ms.openlocfilehash: 72fd043321aefe74e95b34ec24c7be2ffd409439
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240312"
---
# <a name="create-an-android-app"></a>Criar uma aplicação Android
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Android utilizando um back-end da aplicação móvel do Azure.  Poderá criar tanto um novo back-end da aplicação móvel como uma simples aplicação Android de uma *Lista de tarefas* que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais Android referentes à utilização da funcionalidade Aplicações Móveis no App Service do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa do seguinte:

* [Ferramentas de Programador Android](https://developer.android.com/sdk/index.html), que inclui o ambiente de desenvolvimento integrado do Android Studio e a plataforma Android mais recente.
* SDK Android móvel do Azure.
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-new-azure-mobile-app-backend"></a>Criar um novo back-end da aplicação móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação de base de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="run-the-android-app"></a>Executar a aplicação Android
[!INCLUDE [app-service-mobile-android-run-app](../../includes/app-service-mobile-android-run-app.md)]

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
