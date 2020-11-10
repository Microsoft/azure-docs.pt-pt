---
title: Tutorial - Limpar recursos / Rio Azure
description: Neste tutorial aprende-se a limpar os recursos Azure atribuídos enquanto cria a aplicação web.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ab91ea7aa6e621dabc5cac83fe818dbf175214b6
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428903"
---
# <a name="tutorial-clean-up-resources"></a>Tutorial: limpar recursos

Se completou todos os passos neste tutorial multi-partes, criou um serviço de aplicações, um plano de hospedagem de serviços de aplicações e uma conta de armazenamento num grupo de recursos.  Também criou um registo de aplicações no Azure AD.  Quando já não for necessário, elimine estes recursos e o registo de aplicações para que não continue a acumular encargos.  

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Apagar os recursos Azure criados ao seguir o tutorial

## <a name="delete-the-resource-group"></a>Eliminar o grupo de recursos
No [portal Azure](https://portal.azure.com), selecione **grupos** de Recursos do menu do portal e selecione o grupo de recursos que contém o seu serviço de aplicações e plano de serviço de aplicações.

**Selecione Eliminar o grupo de recursos** para eliminar o grupo de recursos e todos os recursos.

:::image type="content" alt-text="Eliminar grupo de recursos" source="./media/scenario-secure-app-clean-up-resources/delete-resource-group.png":::

Este comando pode demorar vários minutos a ser executado.

## <a name="delete-the-app-registration"></a>Excluir o registo da aplicação
A partir do menu do portal, selecione **Azure Ative Directory** , em seguida, **registos de Aplicações** , em seguida, a aplicação que criou.
:::image type="content" alt-text="Selecione o registo de aplicativos" source="./media/scenario-secure-app-clean-up-resources/select-app-registration.png":::

Na visão geral do registo da aplicação, selecione **Delete**.
:::image type="content" alt-text="Eliminar registo de aplicativos" source="./media/scenario-secure-app-clean-up-resources/delete-app-registration.png":::

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
>
> * Apagar os recursos Azure criados ao seguir o tutorial

Saiba como ligar uma [aplicação .NET Core](tutorial-dotnetcore-sqldb-app.md), [app Python,](tutorial-python-postgresql-app.md) [Java](tutorial-java-spring-cosmosdb.md)app ou [Node.js app](tutorial-nodejs-mongodb-app.md) a uma base de dados.