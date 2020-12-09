---
title: Tutorial - Construa uma aplicação web segura no Azure App Service / Rio Azure
description: Neste tutorial, aprende-se a construir uma aplicação web utilizando o Azure App Service, ativar a autenticação, ligar para o Azure Storage e ligar para o Microsoft Graph.
services: active-directory, app-service-web, storage, microsoft-graph
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 17931ee92072059d94cd950e9ce8ef6588f7e891
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905507"
---
# <a name="tutorial-enable-authentication-in-app-service-and-access-storage-and-microsoft-graph"></a>Tutorial: Ativar a autenticação no Serviço de Aplicações e aceder ao armazenamento e gráfico do Microsoft

Este tutorial descreve um cenário comum de aplicação no qual aprende a:

- [Configurar a autenticação para uma aplicação web](scenario-secure-app-authentication-app-service.md) e limitar o acesso aos utilizadores na sua organização. Veja A no diagrama.
- [Aceda de forma segura ao Azure Storage](scenario-secure-app-access-storage.md) para a aplicação web utilizando identidades geridas. Veja B no diagrama.
- Aceda aos dados no Microsoft Graph [para o utilizador inscrito](scenario-secure-app-access-microsoft-graph-as-user.md) ou para a [aplicação web](scenario-secure-app-access-microsoft-graph-as-app.md) utilizando identidades geridas. Ver C no diagrama.
- [Limpe os recursos](scenario-secure-app-clean-up-resources.md) que criou para este tutorial.

:::image type="content" source="./media/scenario-secure-app-overview/web-app.svg" alt-text="Diagrama que mostra cenários de aplicação na plataforma de identidade da Microsoft." border="false":::

Para começar, aprenda a ativar a autenticação para uma aplicação web.

> [!div class="nextstepaction"]
> [Configurar a autenticação para uma aplicação web](scenario-secure-app-authentication-app-service.md)
