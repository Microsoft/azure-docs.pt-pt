---
title: Gerir o plano do serviço de aplicações - Azure | Documentos da Microsoft
description: Saiba como executar tarefas diferentes para gerir um plano do serviço de aplicações.
keywords: serviço de aplicações, serviço de aplicações do azure, dimensionável, plano de serviço de aplicações, alterar, criar, gerir, gestão
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
ms.date: 11/09/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 936abe80a66c1dbe99e7d8a255fe8995a2df0803
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852283"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Gerir um plano do serviço de aplicações no Azure

Uma [plano do serviço de aplicações do Azure](overview-hosting-plans.md) fornece os recursos que tem de executar uma aplicação de serviço de aplicações. Este guia mostra como gerir um plano do serviço de aplicações.

## <a name="create-an-app-service-plan"></a>Crie um plano do Serviço de Aplicações

> [!TIP]
> Se tiver um ambiente de serviço de aplicações, veja [criar um plano do serviço de aplicações num ambiente de serviço de aplicações](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Pode criar um plano do serviço de aplicações vazio ou pode criar um plano como parte da criação de aplicações.

1. Na [portal do Azure](https://portal.azure.com), selecione **New** > **Web + móvel**e, em seguida, selecione **aplicação Web** ou outro tipo de aplicação de serviço de aplicações.

2. Selecione um plano de serviço de aplicações existente ou crie um plano para a nova aplicação.

   ![Crie uma aplicação no portal do Azure.][createWebApp]

   Para criar um plano:

   a. Selecione **[+] Criar um novo**.

      ![Crie um plano de serviço de aplicações.][createASP] 

   b. Para **plano do App Service**, introduza o nome do plano.

   c. Para **localização**, selecione uma localização adequada.

   d. Para **escalão de preço**, selecione um escalão de preço adequado para o serviço. Selecione **ver tudo** para ver mais opções, de preços, tal como **gratuito** e **partilhado**. Depois de selecionar o escalão de preço, clique nas **selecione** botão.

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Mover uma aplicação para outro plano do serviço de aplicações

Pode mover uma aplicação para outro plano do serviço de aplicações, enquanto o plano de origem e o plano de destino estão na _mesmo grupo de recursos e região geográfica_.

> [!NOTE]
> Azure implementa a cada novo plano do serviço de aplicações numa unidade de implementação, chamada internamente um espaço Web. Cada região pode ter espaços Web muitos, mas a aplicação só pode mover entre planos criados com o mesmo espaço de Web. Um ambiente de serviço de aplicações é um espaço Web isolado, para que as aplicações podem ser movidas entre planos no mesmo ambiente de serviço de aplicações, mas não entre planos em diferentes ambientes de serviço de aplicações.
>
> Não é possível especificar o espaço Web que pretende quando criar um plano, mas é possível garantir que é criado um plano do espaço Web mesmo como um plano existente. Em resumo, todos os planos criados com o mesmo grupo de recursos e a combinação de região são implementadas para o mesmo espaço de Web. Por exemplo, se tiver criado um plano no grupo de recursos e região B, qualquer plano de que criar, em seguida, no grupo de recursos e região B é implementado para o mesmo espaço de Web. Tenha em atenção que os planos não é possível mover espaços Web depois de ocorrer, pelo que não é possível mover um plano em "o mesmo espaço Web" como plano de outro, movendo-os para outro grupo de recursos.
> 

1. Na [portal do Azure](https://portal.azure.com), navegar para a aplicação que pretende mover.

1. No menu, procure o **plano do serviço de aplicações** secção.

1. Selecione **plano do serviço de aplicações de alteração** para abrir o **plano do App Service** Seletor.

   ![Seletor de plano de serviço de aplicações.][change] 

1. Na **plano do App Service** Seletor, selecione um existente planear mover esta aplicação em.   

O **plano do serviço de aplicações selecione** página mostra apenas os planos que estão no mesmo grupo de recursos e região geográfica como plano de serviço de aplicações da aplicação atual.

Cada plano tem seu próprio para o escalão de preço. Por exemplo, mover um site a partir um **gratuito** escalão para um **padrão** escalão permite que todas as aplicações atribuídas ao mesmo a usar os recursos e recursos do **padrão** escalão. No entanto, a mover uma aplicação de um plano superior em camadas para um plano em camadas inferiores significa que já não tem acesso a determinados recursos. Se a sua aplicação utiliza uma funcionalidade que não está disponível no plano de destino, obterá um erro que mostra a qual o recurso está em utilização que não está disponível. 

Por exemplo, se uma das suas aplicações utiliza certificados SSL, poderá ver esta mensagem de erro:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

Neste caso, antes de prosseguir a aplicação para o plano de destino, precisa:
- Aumentar verticalmente o escalão de preço do plano de destino no **básica** ou superior.
- Remova todas as ligações de SSL para a sua aplicação.

## <a name="move-an-app-to-a-different-region"></a>Mover uma aplicação para uma região diferente

A região em que a aplicação for executada é a região do plano do serviço de aplicações está a ser. No entanto, não é possível alterar a região de um plano de serviço de aplicações. Se quiser executar a sua aplicação numa região diferente, uma alternativa é a aplicação de clonagem. A clonagem faz uma cópia da sua aplicação num plano do serviço de aplicações novo ou existente em qualquer região.

Pode encontrar **clonar aplicação** no **ferramentas de desenvolvimento** secção do menu.

> [!IMPORTANT]
> A clonagem tem algumas limitações. Pode ler sobre os mesmos em [clonagem de aplicação do serviço de aplicações do Azure](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano do serviço de aplicações

Para aumentar verticalmente o serviço de aplicações do plano do escalão de preço, consulte [aumentar verticalmente uma aplicação no Azure](web-sites-scale.md).

Para aumentar o número de instâncias de uma aplicação, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminar um plano do serviço de aplicações

Para evitar cobranças inesperadas, ao eliminar a última aplicação num plano do serviço de aplicações, serviço de aplicações também elimina o plano por predefinição. Se optar por manter o plano em vez disso, deve alterar o plano para **gratuito** camada para que não será cobrado.

> [!IMPORTANT]
> Planos de serviço de aplicações que não tem aplicações associadas a eles continuam a incorrer em custos porque eles continuam a reservar instâncias de VM configuradas.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Aumentar verticalmente uma aplicação no Azure](web-sites-scale.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
