---
title: Gerir o plano de serviço de aplicações
description: Aprenda a executar diferentes tarefas para gerir um plano de Serviço de Aplicações, tais como criar, mover, escalar e eliminar.
keywords: serviço de aplicações, serviço de aplicações azure, escala, plano de serviço de aplicações, alteração, criação, gestão, gestão
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d40f5db65ce9ca90ae978bac4491bdebccc2a328
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811708"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerir um plano de serviço de aplicações em Azure

Um [plano azure App Service](overview-hosting-plans.md) fornece os recursos que uma aplicação do App Service precisa de executar. Este guia mostra como gerir um plano de Serviço de Aplicações.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se tiver um Ambiente de Serviço de Aplicações, consulte Criar um plano de serviço de aplicações num ambiente de serviço de [aplicações.](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Pode criar um plano de Serviço de Aplicações vazio, ou pode criar um plano como parte da criação de apps.

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.

   ![Crie um recurso no portal Azure.][createResource] 

1. Selecione **New** > **Web App** ou outro tipo de app de serviço de aplicação.

   ![Crie uma aplicação no portal Azure.][createWebApp] 

2. Configure a secção detalhes da **instância** antes de configurar o plano de serviço de aplicações. Definições como **Publicar** e **Sistemas Operativos** podem alterar os níveis de preços disponíveis para o seu plano de Serviço de Aplicações. **A região** determina onde é criado o seu plano de Serviço de Aplicações. 
   
3. Na secção Plano de Serviço de **Aplicações,** selecione um plano existente ou crie um plano selecionando **criar novos**.

   ![Crie um plano de Serviço de Aplicações.][createASP] 

4. Ao criar um plano, pode selecionar o nível de preços do novo plano. Em **Sku e tamanho**, selecione **Alterar** tamanho para alterar o nível de preços. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mova uma aplicação para outro plano de Serviço de Aplicações

Pode mover uma aplicação para outro plano de Serviço de Aplicações, desde que o plano de origem e o plano-alvo estejam no mesmo grupo de _recursos e região geográfica._

> [!NOTE]
> O Azure implementa cada novo plano de Serviço de Aplicações numa unidade de implantação, chamada internamente de webspace. Cada região pode ter muitos webspaces, mas a sua aplicação só pode mover-se entre planos que são criados no mesmo espaço web. Um App Service Environment é um webspace isolado, pelo que as aplicações podem ser movidas entre planos no mesmo App Service Environment, mas não entre planos em diferentes Ambientes de Serviço sinuoso.
>
> Não é possível especificar o espaço web que pretende ao criar um plano, mas é possível garantir que um plano seja criado no mesmo espaço web que um plano existente. Em resumo, todos os planos criados com o mesmo grupo de recursos e a combinação da região são implantados no mesmo espaço web. Por exemplo, se criou um plano no grupo de recursos A e na região B, então qualquer plano que crie posteriormente no grupo de recursos A e na região B é implantado no mesmo espaço web. Note que os planos não podem mover webspaces depois de criados, por isso não pode mover um plano para "o mesmo espaço web" que outro plano, transferindo-o para outro grupo de recursos.
> 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **serviços de App** e selecione a app que pretende mover.

2. A partir do menu esquerdo, selecione plano de serviço de **aplicações de mudança**.

3. No plano de dropdown do **App Service,** selecione um plano existente para mover a app para. A queda mostra apenas planos que se encontram no mesmo grupo de recursos e região geográfica que o atual plano de Serviço de Aplicações. Se não existir tal plano, permite-lhe criar um plano por defeito. Também pode criar um novo plano manualmente selecionando **criar novo**.

4. Se criar um plano, pode selecionar o nível de preços do novo plano. No **Nível de Preços,** selecione o nível existente para alterá-lo. 
   
   > [!IMPORTANT]
   > Se estiver a mover uma aplicação de um plano de nível superior para um plano de nível inferior, como de **D1** para **F1,** a aplicação pode perder certas capacidades no plano alvo. Por exemplo, se a sua aplicação utilizar certificados TLS/SSL, poderá ver esta mensagem de erro:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Quando terminar, selecione **OK**.
   
   ![Seletor de plano de serviço de aplicações.][change] 

## <a name="move-an-app-to-a-different-region"></a>Mova uma aplicação para uma região diferente

A região em que a sua aplicação funciona é a região do plano de Serviço de Aplicações em que se encontra. No entanto, não é possível alterar a região de um plano de Serviço sinuoso. Se quiser executar a sua aplicação numa região diferente, uma alternativa é a clonagem de aplicações. A clonagem faz uma cópia da sua aplicação num novo ou existente plano de Serviço de Aplicações em qualquer região.

Pode encontrar **a App Clone** na secção **Ferramentas** de Desenvolvimento do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Pode ler sobre eles na clonagem da App De Serviço de [Aplicações Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Escala rés-da-aplicação

Para aumentar o nível de preços de um plano de app service, consulte [scale up uma app em Azure](manage-scale-up.md).

Para escalonar a contagem de exemplos de uma aplicação, consulte a contagem de [exemplos de escala manual mente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminar um plano de serviço de aplicações

Para evitar encargos inesperados, ao eliminar a última aplicação num plano de Serviço de Aplicações, o App Service também elimina o plano por padrão. Se optar por manter o plano, deve mudar o plano para **free** tier para não ser cobrado.

> [!IMPORTANT]
> Os planos do Serviço de Aplicações que não têm aplicações associadas ainda incorrem em encargos porque continuam a reservar as instâncias de VM configuradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aumentar verticalmente uma aplicação no Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
