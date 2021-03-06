---
title: Tutorial - Limpar recursos | Rio Azure
description: Neste tutorial, aprende-se a limpar os recursos Azure atribuídos enquanto cria a aplicação web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: cfb42b82943f03c3633ff69662ab841bf587ede4
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221862"
---
# <a name="tutorial-clean-up-resources"></a>Tutorial: Limpar recursos

Se completou todos os passos neste tutorial multipart, criou um serviço de aplicações, um plano de hospedagem de serviços de aplicações e uma conta de armazenamento num grupo de recursos. Também criou um registo de aplicações no Azure Ative Directory. Quando já não for necessário, elimine estes recursos e o registo de aplicações para que não continue a acumular encargos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Elimine os recursos Azure criados enquanto segue o tutorial.

## <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos

No [portal Azure](https://portal.azure.com), selecione **grupos** de Recursos do menu do portal e selecione o grupo de recursos que contém o seu serviço de aplicações e plano de serviço de aplicações.

**Selecione Eliminar o grupo de recursos** para eliminar o grupo de recursos e todos os recursos.

:::image type="content" alt-text="Screenshot que mostra a eliminação do grupo de recursos." source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Este comando pode levar vários minutos a correr.

## <a name="delete-the-app-registration"></a>Excluir o registo da aplicação

A partir do menu do portal, selecione as inscrições da **App Azure Ative**  >  **Directory**. Em seguida, selecione a aplicação que criou.
:::image type="content" alt-text="Screenshot que mostra a seleção do registo de aplicações." source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Na visão geral do registo da aplicação, selecione **Delete**.
:::image type="content" alt-text="Screenshot que mostra a eliminação do registo da aplicação." source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Elimine os recursos Azure criados enquanto segue o tutorial.

Saiba como ligar uma [aplicação .NET Core](tutorial-dotnetcore-sqldb-app.md), [app Python,](tutorial-python-postgresql-app.md) [Java](tutorial-java-spring-cosmosdb.md)app ou [Node.js app](tutorial-nodejs-mongodb-app.md) a uma base de dados.