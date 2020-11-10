---
title: Tutorial - construa uma aplicação web segura no Azure App Service / Rio Azure
description: Neste tutorial aprende-se a construir uma aplicação web utilizando o Azure App Service, ativar a autenticação, ligar para o armazenamento do Azure e ligar para o Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: fcddf99c3a4c53fe25db1ed653983e8ddac0edb7
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428930"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutorial: permitir a autenticação no Serviço de Aplicações e armazenamento de acesso e Gráfico microsoft

Este tutorial descreve um cenário comum de aplicação, aprendendo a:

- [(A) Configurar a autenticação para uma aplicação web](scenario-secure-app-authentication-app-service.md) e limitar o acesso aos utilizadores na sua organização.
- [(B) Aceda de forma segura ao armazenamento do Azure](scenario-secure-app-access-storage.md) em nome da aplicação web utilizando identidades geridas.
- (C) Aceder aos dados no Microsoft Graph [em nome do utilizador inscrito](scenario-secure-app-access-microsoft-graph-as-user.md) ou em nome da [aplicação web](scenario-secure-app-access-microsoft-graph-as-app.md) utilizando identidades geridas.
- [Limpe os recursos](scenario-secure-app-clean-up-resources.md) que criou para este tutorial.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Cenários de aplicação na plataforma de identidade da Microsoft" border="false":::

Para começar, aprenda a ativar a autenticação para uma aplicação web.

> [!div class="nextstepaction"]
> [Configurar a autenticação para uma aplicação web](scenario-secure-app-authentication-app-service.md)
