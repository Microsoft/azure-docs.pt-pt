---
title: Criar uma aplicação Cordova nas Aplicações Móveis do Serviço de Aplicações do Azure | Microsoft Docs
description: Siga este tutorial para começar a utilizar back-ends de aplicações móveis do Azure para desenvolvimento do Apache Cordova
services: app-service\mobile
documentationcenter: javascript
author: elamalani
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
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 8a1634cfa7b701401927859a9fbd3fb203b61a77
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445655"
---
# <a name="create-an-apache-cordova-app"></a>Criar uma aplicação Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

> [!NOTE]
> Visual Studio App Center está a investir em serviços de novo e integrados essenciais para o desenvolvimento de aplicações móveis. Os desenvolvedores podem usar **crie**, **teste** e **distribuir** serviços para configurar os pipelines de integração e entrega contínuas. Assim que a aplicação é implementada, os programadores podem monitorizar o estado e a utilização da sua aplicação com o **Analytics** e **diagnóstico** serviços e interaja com os utilizadores que utilizam o **Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados de aplicações na cloud. Confira [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started) hoje mesmo.
>

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

## <a name="create-a-database-connection-and-configure-the-client-and-server-project"></a>Criar uma ligação de base de dados e configurar o projeto de cliente e servidor
[!INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-apache-cordova-app"></a>Transferir e executar a aplicação Apache Cordova
[!INCLUDE [app-service-mobile-cordova-run-app](../../includes/app-service-mobile-cordova-run-app.md)]
<!-- URLs -->
[Portal do azure]: https://portal.azure.com/

[Visual Studio Community 2017]: https://www.visualstudio.com/
[Visual Studio Tools para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx