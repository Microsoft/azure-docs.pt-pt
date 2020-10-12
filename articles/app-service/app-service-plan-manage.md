---
title: Gerir o plano de Serviço de Aplicações
description: Saiba como executar diferentes tarefas para gerir um plano de Serviço de Aplicações, como criar, mover, escalar e eliminar.
keywords: serviço de aplicações, serviço de aplicativos azure, escala, plano de serviço de aplicações, mudança, criar, gerir, gerir
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: bb0765a1d7934d60f787ed277dd3bd1f9bc1359b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962949"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerir um plano de Serviço de Aplicações em Azure

Um [plano de Serviço de Aplicações Azure](overview-hosting-plans.md) fornece os recursos que uma aplicação do Serviço de Aplicações precisa de ser executada. Este guia mostra como gerir um plano de Serviço de Aplicações.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se tiver um Ambiente de Serviço de Aplicações, consulte [criar um plano de Serviço de Aplicações num Ambiente de Serviço de Aplicações.](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Pode criar um plano de Serviço de Aplicações vazio ou criar um plano como parte da criação de aplicações.

1. No [portal Azure,](https://portal.azure.com)selecione **Criar um recurso**.

   ![Crie um recurso no portal Azure.][createResource] 

1. Selecione **New**  >  **Web App** ou outro tipo de aplicação de serviço de Aplicação.

   ![Crie uma aplicação no portal Azure.][createWebApp] 

2. Configurar a secção Detalhes de **Instância** antes de configurar o plano de Serviço de Aplicações. Configurações como **Sistemas Editoriais** e **Operativos** podem alterar os níveis de preços disponíveis para o seu plano de Serviço de Aplicações. **A região** determina onde o seu plano de Serviço de Aplicações é criado. 
   
3. Na secção **Plano de Serviço de Aplicações,** selecione um plano existente ou crie um plano selecionando Criar **novo**.

   ![Crie um plano de Serviço de Aplicações.][createASP] 

4. Ao criar um plano, pode selecionar o nível de preços do novo plano. No **Sku e tamanho,** selecione Alterar o **tamanho** para alterar o nível de preços. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover uma app para outro plano de Serviço de Aplicações

Pode mover uma aplicação para outro plano de Serviço de Aplicações, desde que o plano de origem e o plano-alvo estejam no _mesmo grupo de recursos e região geográfica._

> [!NOTE]
> O Azure implementa cada novo plano de Serviço de Aplicações numa unidade de implantação, chamada internamente de espaço web. Cada região pode ter muitos espaços web, mas a sua aplicação só pode mover-se entre planos que são criados no mesmo espaço web. Um Ambiente de Serviço de Aplicações é um espaço web isolado, pelo que as aplicações podem ser movidas entre planos no mesmo Ambiente de Serviço de Aplicações, mas não entre planos em diferentes Ambientes de Serviço de Aplicações.
>
> Não é possível especificar o espaço web que pretende ao criar um plano, mas é possível garantir que um plano é criado no mesmo espaço web que um plano existente. Em resumo, todos os planos criados com o mesmo grupo de recursos e combinação de região são implantados no mesmo espaço web. Por exemplo, se criou um plano no grupo de recursos A e na região B, então qualquer plano que posteriormente crie no grupo de recursos A e na região B é implantado no mesmo espaço web. Note que os planos não podem mover webspaces depois de criados, por isso não pode mover um plano para "o mesmo espaço web" como outro plano, transferindo-o para outro grupo de recursos.
> 

1. No [portal Azure,](https://portal.azure.com)procure e selecione **os serviços de Aplicações** e selecione a aplicação que pretende mover.

2. A partir do menu esquerdo, selecione **o plano de Serviço de Aplicações de Alteração.**

3. No plano de serviço de **aplicações,** selecione um plano existente para mover a app para. A queda mostra apenas planos que estão no mesmo grupo de recursos e região geográfica que o atual plano de Serviço de Aplicações. Se tal plano não existir, permite criar um plano por defeito. Também pode criar um novo plano manualmente selecionando **Criar novo**.

4. Se criar um plano, pode selecionar o nível de preços do novo plano. No **Preço Tier**, selecione o nível existente para alterá-lo. 
   
   > [!IMPORTANT]
   > Se estiver a mover uma aplicação de um plano de nível superior para um plano de nível inferior, como de **D1** para **F1,** a aplicação pode perder certas capacidades no plano alvo. Por exemplo, se a sua aplicação utilizar certificados TLS/SSL, poderá ver esta mensagem de erro:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Quando terminar, selecione **OK**.
   
   ![Seletor de plano de serviço de aplicações.][change] 

## <a name="move-an-app-to-a-different-region"></a>Mover uma app para uma região diferente

A região em que a sua aplicação é a região do plano do Serviço de Aplicações em que se encontra. No entanto, não é possível alterar a região de um plano de Serviço de Aplicações. Se quiser executar a sua aplicação numa região diferente, uma alternativa é a clonagem de aplicações. A clonagem faz uma cópia da sua aplicação num novo plano de Serviço de Aplicações em qualquer região.

Pode encontrar **a App Clone** na secção **Ferramentas** de Desenvolvimento do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Pode ler sobre eles na [clonagem da App Service App Azure.](app-service-web-app-cloning.md)

## <a name="scale-an-app-service-plan"></a>Escalar um plano de Serviço de Aplicações

Para aumentar o nível de preços de um plano de aplicações, consulte [Scale up uma aplicação em Azure](manage-scale-up.md).

Para aumentar a contagem de instâncias de uma aplicação, consulte [a contagem de instâncias de escala manual ou automática](../azure-monitor/platform/autoscale-get-started.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminar um plano de Serviço de Aplicações

Para evitar custos inesperados, quando elimina a última aplicação num plano de Serviço de Aplicações, o Serviço de Aplicações também elimina o plano por padrão. Se optar por manter o plano, deve alterar o plano para **Free** Tier para que não seja cobrado.

> [!IMPORTANT]
> Os planos do Serviço de Aplicações que não têm aplicações associadas a eles ainda incorrem em encargos porque continuam a reservar as instâncias VM configuradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aumentar verticalmente uma aplicação no Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png