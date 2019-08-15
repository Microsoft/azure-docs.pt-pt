---
title: Gerenciar plano do serviço de aplicativo-Azure | Microsoft Docs
description: Saiba como executar tarefas diferentes para gerenciar um plano do serviço de aplicativo.
keywords: serviço de aplicativo, serviço de aplicativo do Azure, escala, plano do serviço de aplicativo, alterar, criar, gerenciar, gerenciamento
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c749f090cbb0aeb036a53a01e6cd516617c3186d
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68953883"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerenciar um plano do serviço de aplicativo no Azure

Um [plano de serviço Azure app](overview-hosting-plans.md) fornece os recursos que um aplicativo do serviço de aplicativo precisa executar. Este guia mostra como gerenciar um plano do serviço de aplicativo.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se você tiver um Ambiente do Serviço de Aplicativo, consulte [criar um plano do serviço de aplicativo em um ambiente do serviço de aplicativo](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Você pode criar um plano do serviço de aplicativo vazio ou pode criar um plano como parte da criação do aplicativo.

1. Na [portal do Azure](https://portal.azure.com), selecione **novo** > **Web + móvel**e, em seguida, selecione **aplicativo Web** ou outro tipo de aplicativo do serviço de aplicativo.

2. Selecione um plano do serviço de aplicativo existente ou crie um plano para o novo aplicativo.

   ![Crie um aplicativo no portal do Azure.][createWebApp]

   Para criar um plano:

   a. Selecione **[+] criar novo**.

      ![Crie um plano do serviço de aplicativo.][createASP] 

   b. Para o **plano do serviço de aplicativo**, insira o nome do plano.

   c. Para **local**, selecione um local apropriado.

   d. Para **tipo de preço**, selecione um tipo de preço apropriado para o serviço. Selecione **Exibir tudo** para exibir mais opções de preços, como **gratuito** e **compartilhado**. Depois de selecionar o tipo de preço, clique no botão **selecionar** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover um aplicativo para outro plano do serviço de aplicativo

Você pode mover um aplicativo para outro plano do serviço de aplicativo, desde que o plano de origem e o plano de destino estejam no _mesmo grupo de recursos e região geográfica_.

> [!NOTE]
> O Azure implanta cada novo plano do serviço de aplicativo em uma unidade de implantação, internamente chamado de espaço Web. Cada região pode ter muitos espaços Web, mas seu aplicativo só pode mover entre planos criados no mesmo espaço Web. Um Ambiente do Serviço de Aplicativo é um espaço Web isolado, portanto, os aplicativos podem ser movidos entre planos na mesma Ambiente do Serviço de Aplicativo, mas não entre planos em diferentes ambientes de serviço de aplicativo.
>
> Não é possível especificar o espaço Web que você deseja ao criar um plano, mas é possível garantir que um plano seja criado no mesmo espaço da Web de um plano existente. Em resumo, todos os planos criados com a mesma combinação de grupo de recursos e região são implantados no mesmo espaço Web. Por exemplo, se você criou um plano no grupo de recursos A e na região B, qualquer plano que você criar posteriormente no grupo de recursos A e a região B será implantado no mesmo espaço Web. Observe que os planos não podem mover espaços do webspace depois de criados, portanto, você não pode mover um plano para "o mesmo espaço Web" como outro plano, movendo-o para outro grupo de recursos.
> 

1. Na [portal do Azure](https://portal.azure.com), navegue até o aplicativo que você deseja mover.

1. No menu, procure a seção **plano do serviço de aplicativo** .

1. Selecione **Alterar plano do serviço de aplicativo** para abrir o seletor do **plano do serviço de aplicativo** .

   ![Seletor de plano do serviço de aplicativo.][change] 

1. No seletor do **plano do serviço de aplicativo** , selecione um plano existente para mover esse aplicativo.   

A página **selecionar plano do serviço de aplicativo** mostra apenas os planos que estão no mesmo grupo de recursos e região geográfica que o plano do serviço de aplicativo do aplicativo atual.

Cada plano tem seu próprio tipo de preço. Por exemplo, mover um site de uma camada **gratuita** para uma camada **Standard** permite que todos os aplicativos atribuídos a ele usem os recursos e recursos da camada **Standard** . No entanto, mover um aplicativo de um plano de camada superior para um plano de camada inferior significa que você não tem mais acesso a determinados recursos. Se seu aplicativo usar um recurso que não está disponível no plano de destino, você receberá um erro que mostra qual recurso está em uso que não está disponível. 

Por exemplo, se um de seus aplicativos usar certificados SSL, você poderá ver esta mensagem de erro:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Nesse caso, antes de poder mover o aplicativo para o plano de destino, você precisa:
- Escalar verticalmente o tipo de preço do plano de destino para **básico** ou superior.
- Remova todas as conexões SSL para seu aplicativo.

## <a name="move-an-app-to-a-different-region"></a>Mover um aplicativo para uma região diferente

A região em que seu aplicativo é executado é a região do plano do serviço de aplicativo em que ele está. No entanto, você não pode alterar a região de um plano do serviço de aplicativo. Se você quiser executar seu aplicativo em uma região diferente, uma alternativa será a clonagem de aplicativo. A clonagem faz uma cópia do seu aplicativo em um plano do serviço de aplicativo novo ou existente em qualquer região.

Você pode encontrar **clonar aplicativo** na seção **ferramentas de desenvolvimento** do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Você pode ler sobre eles em [Azure app clonagem de aplicativo de serviço](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano do serviço de aplicativo

Para escalar verticalmente o tipo de preço do plano do serviço de aplicativo, consulte [escalar verticalmente um aplicativo no Azure](web-sites-scale.md).

Para escalar horizontalmente a contagem de instâncias de um aplicativo, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Excluir um plano do serviço de aplicativo

Para evitar encargos inesperados, quando você exclui o último aplicativo em um plano do serviço de aplicativo, o serviço de aplicativo também exclui o plano por padrão. Se você optar por manter o plano em vez disso, deverá alterar o plano para a camada **gratuita** para que não seja cobrado.

> [!IMPORTANT]
> Os planos do serviço de aplicativo que não têm aplicativos associados a eles ainda incorrem em encargos porque continuam a reservar as instâncias de VM configuradas.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Escalar verticalmente um aplicativo no Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
