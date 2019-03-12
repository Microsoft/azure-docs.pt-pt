---
title: Criar uma aplicação Cordova nas Aplicações Móveis do Serviço de Aplicações do Azure | Microsoft Docs
description: Siga este tutorial para começar a utilizar back-ends de aplicações móveis do Azure para desenvolvimento do Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
tags: ''
keywords: cordova,javascript,móvel,cliente
ms.assetid: 0b08fc12-0a80-42d3-9cc1-9b3f8d3e3a3f
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: conceptual
ms.date: 07/07/2017
ms.author: crdun
ms.openlocfilehash: 7014d09bbb62e78c37a9496628e3509b6eaaa4ac
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768234"
---
# <a name="create-an-apache-cordova-app"></a>Criar uma aplicação Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

## <a name="overview"></a>Descrição geral
Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel Apache Cordova utilizando um back-end da aplicação móvel do Azure.  Pode criar tanto um novo back-end da aplicação móvel assim como uma simples aplicação Apache Cordova de uma *Lista de tarefas* que armazena dados da aplicação no Azure.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais do Apache Cordova referentes à utilização da funcionalidade Aplicações Móveis no App Service do Azure.

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa dos seguintes pré-requisitos:

* Um PC com o [Visual Studio Community 2017] ou posterior.
* [Visual Studio Tools para Apache Cordova].
* Uma [conta ativa do Azure](https://azure.microsoft.com/pricing/free-trial/).

Também pode ignorar o Visual Studio e utilizar diretamente a linha de comandos do Apache Cordova.  Com a linha de comandos é útil ao concluir o tutorial num computador Mac.  A compilação de aplicações cliente Apache Cordova utilizando a linha de comandos não é abrangida por este tutorial.

## <a name="create-an-azure-mobile-app-backend"></a>Criar um back-end da aplicação móvel do Azure
[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-1-Create-an-Azure-Mobile-App)

## <a name="configure-the-server-project"></a>Configurar o projeto de servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Transferir e executar a aplicação Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]

## <a name="next-steps"></a>Próximos Passos
Agora que concluiu este tutorial de início rápido, avance para um dos seguintes tutoriais:

* [Adicionar Dados Offline](app-service-mobile-cordova-get-started-offline-data.md) à sua aplicação do Apache Cordova.
* [Adicionar Autenticação](app-service-mobile-cordova-get-started-users.md) à aplicação Apache Cordova.
* [Adicionar Notificações Push](app-service-mobile-cordova-get-started-push.md) à aplicação Apache Cordova.

Saiba mais sobre os conceitos-chave com o App Service do Azure.

* [Dados Offline]
* [Autenticação]
* [Notificações Push]

Saiba como utilizar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- Images. -->

<!-- URLs -->
[Azure portal]: https://portal.azure.com/
[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Dados Offline]: app-service-mobile-offline-data-sync.md
[Autenticação]: app-service-mobile-auth.md
[Notificações Push]: ../notification-hubs/notification-hubs-push-notification-overview.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
