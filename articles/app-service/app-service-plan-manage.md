---
title: Gerenciar plano do serviço de aplicativo
description: Saiba como executar tarefas diferentes para gerenciar um plano do serviço de aplicativo, como criar, mover, dimensionar e excluir.
keywords: serviço de aplicativo, serviço de aplicativo do Azure, escala, plano do serviço de aplicativo, alterar, criar, gerenciar, gerenciamento
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: 68cabbd00dd0b738590109cc39d8df82f5b7362d
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681188"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerenciar um plano do serviço de aplicativo no Azure

Um [plano de serviço Azure app](overview-hosting-plans.md) fornece os recursos que um aplicativo do serviço de aplicativo precisa executar. Este guia mostra como gerenciar um plano do serviço de aplicativo.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se você tiver um Ambiente do Serviço de Aplicativo, consulte [criar um plano do serviço de aplicativo em um ambiente do serviço de aplicativo](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Você pode criar um plano do serviço de aplicativo vazio ou pode criar um plano como parte da criação do aplicativo.

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso**.

   ![Crie um recurso no portal do Azure.][createResource] 

1. Selecione **novo** > **aplicativo Web** ou outro tipo de aplicativo do serviço de aplicativo.

   ![Crie um aplicativo no portal do Azure.][createWebApp] 

2. Configure a seção **detalhes da instância** antes de configurar o plano do serviço de aplicativo. As configurações, como **publicar** e **sistemas operacionais** , podem alterar os tipos de preço disponíveis para seu plano do serviço de aplicativo. A **região** determina onde o plano do serviço de aplicativo é criado. 
   
3. Na seção **plano do serviço de aplicativo** , selecione um plano existente ou crie um plano selecionando **criar novo**.

   ![Crie um plano do serviço de aplicativo.][createASP] 

4. Ao criar um plano, você pode selecionar o tipo de preço do novo plano. Em **SKU e tamanho**, selecione **alterar tamanho** para alterar o tipo de preço. 

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover um aplicativo para outro plano do serviço de aplicativo

Você pode mover um aplicativo para outro plano do serviço de aplicativo, desde que o plano de origem e o plano de destino estejam no _mesmo grupo de recursos e região geográfica_.

> [!NOTE]
> O Azure implanta cada novo plano do serviço de aplicativo em uma unidade de implantação, internamente chamado de espaço Web. Cada região pode ter muitos espaços Web, mas seu aplicativo só pode mover entre planos criados no mesmo espaço Web. Um Ambiente do Serviço de Aplicativo é um espaço Web isolado, portanto, os aplicativos podem ser movidos entre planos na mesma Ambiente do Serviço de Aplicativo, mas não entre planos em diferentes ambientes de serviço de aplicativo.
>
> Não é possível especificar o espaço Web que você deseja ao criar um plano, mas é possível garantir que um plano seja criado no mesmo espaço da Web de um plano existente. Em resumo, todos os planos criados com a mesma combinação de grupo de recursos e região são implantados no mesmo espaço Web. Por exemplo, se você criou um plano no grupo de recursos A e na região B, qualquer plano que você criar posteriormente no grupo de recursos A e a região B será implantado no mesmo espaço Web. Observe que os planos não podem mover espaços do webspace depois de criados, portanto, você não pode mover um plano para "o mesmo espaço Web" como outro plano, movendo-o para outro grupo de recursos.
> 

1. Na [portal do Azure](https://portal.azure.com), procure e selecione serviços de **aplicativos** e selecione o aplicativo que você deseja mover.

2. No menu à esquerda, selecione **Alterar plano do serviço de aplicativo**.

3. Na lista suspensa **plano do serviço de aplicativo** , selecione um plano existente para o qual mover o aplicativo. A lista suspensa mostra apenas os planos que estão no mesmo grupo de recursos e região geográfica que o plano do serviço de aplicativo atual. Se esse plano não existir, ele permitirá que você crie um plano por padrão. Você também pode criar um novo plano manualmente selecionando **criar novo**.

4. Se você criar um plano, poderá selecionar o tipo de preço do novo plano. Em **tipo de preço**, selecione a camada existente para alterá-la. 
   
   > [!IMPORTANT]
   > Se você estiver movendo um aplicativo de um plano de camadas superiores para um plano de nível inferior, como de **D1** a **F1**, o aplicativo poderá perder certos recursos no plano de destino. Por exemplo, se seu aplicativo usar certificados SSL, você poderá ver esta mensagem de erro:
   >
   > `Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

5. Quando terminar, selecione **OK**.
   
   ![Seletor de plano do serviço de aplicativo.][change] 

## <a name="move-an-app-to-a-different-region"></a>Mover um aplicativo para uma região diferente

A região em que seu aplicativo é executado é a região do plano do serviço de aplicativo em que ele está. No entanto, você não pode alterar a região de um plano do serviço de aplicativo. Se você quiser executar seu aplicativo em uma região diferente, uma alternativa será a clonagem de aplicativo. A clonagem faz uma cópia do seu aplicativo em um plano do serviço de aplicativo novo ou existente em qualquer região.

Você pode encontrar **clonar aplicativo** na seção **ferramentas de desenvolvimento** do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Você pode ler sobre eles em [Azure app clonagem de aplicativo de serviço](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano do serviço de aplicativo

Para escalar verticalmente o tipo de preço do plano do serviço de aplicativo, consulte [escalar verticalmente um aplicativo no Azure](manage-scale-up.md).

Para escalar horizontalmente a contagem de instâncias de um aplicativo, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Excluir um plano do serviço de aplicativo

Para evitar encargos inesperados, quando você exclui o último aplicativo em um plano do serviço de aplicativo, o serviço de aplicativo também exclui o plano por padrão. Se você optar por manter o plano em vez disso, deverá alterar o plano para a camada **gratuita** para que não seja cobrado.

> [!IMPORTANT]
> Os planos do serviço de aplicativo que não têm aplicativos associados a eles ainda incorrem em encargos porque continuam a reservar as instâncias de VM configuradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Aumentar verticalmente uma aplicação no Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[createResource]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-a-resource.png
