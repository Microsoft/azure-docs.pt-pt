---
title: Aumentar verticalmente os recursos e capacidades - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como aumentar verticalmente uma aplicação no serviço de aplicações do Azure para adicionar capacidade e funcionalidades.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: mollybos
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 98d3d1f6fc0f2f30196f360811808579dfbab312
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832155"
---
# <a name="scale-up-an-app-in-azure"></a>Aumentar verticalmente uma aplicação no Azure

> [!NOTE]
> A nova **PremiumV2** escalão dá-lhe CPUs mais rápidas, armazenamento SSD e duplica o rácio de memória/núcleo dos escalões de preços existentes. Com a vantagem de desempenho, que pode poupar dinheiro ao executar as suas aplicações em menos instâncias. Dimensionar até **PremiumV2** escalão, consulte [escalão PremiumV2 configurar serviço de aplicações](app-service-configure-premium-tier.md).
>

Este artigo mostra-lhe como dimensionar a sua aplicação no serviço de aplicações do Azure. Existem dois fluxos de trabalho para uma escala de dimensionamento, segurança e aumentar horizontalmente e este artigo explica o fluxo de trabalho de expansão.

* [Aumentar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais CPU, memória, espaço em disco e funcionalidades adicionais, como máquinas de virtuais (VMs) dedicadas, domínios e certificados personalizados, ranhuras de teste, dimensionamento automático e muito mais. Aumentar verticalmente ao alterar o escalão de preço do plano do serviço de aplicações que qual pertence a sua aplicação.
* [Aumentar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumente o número de instâncias de VMS que executam a aplicação.
  Pode aumentar horizontalmente até 20 instâncias, consoante o escalão de preço. [Ambientes do App Service](environment/intro.md) no **Isolated** camada de ainda mais aumenta sua contagem de escalamento horizontal a 100 instâncias. Para obter mais informações sobre como ampliar, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Aí, encontrará informações sobre como utilizar o dimensionamento automático, que é Dimensionar automaticamente com base em regras predefinidas e agendas de contagem de instâncias.

As definições de dimensionamento levam apenas alguns segundos para aplicar e afetam todas as aplicações no seu [plano do App Service](../app-service/overview-hosting-plans.md).
Eles não exigem que altere o código ou voltar a implementar a sua aplicação.

Para obter informações sobre preços e funcionalidades dos planos do serviço de aplicações individuais, consulte [detalhes de preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de alternar um plano do serviço de aplicações do **gratuito** escalão, tem primeiro de remover o [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) no local para a sua subscrição do Azure. Para ver ou alterar as opções para a sua subscrição do serviço de aplicações do Microsoft Azure, veja [as assinaturas do Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Aumentar verticalmente o escalão de preço
1. No browser, abra a [portal do Azure][portal].
2. Na página da aplicação de serviço de aplicações, clique em **todas as definições**e, em seguida, clique em **aumentar verticalmente**.
   
    ![Navegue para aumentar verticalmente a sua aplicação do Azure.][ChooseWHP]
3. Escolha o escalão e, em seguida, clique em **aplicar**.
   
    O **notificações** separador irá flash um verde **êxito** depois de concluída a operação.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Dimensionar os recursos relacionados
Se a sua aplicação depende de outros serviços, como a base de dados do Azure SQL ou o armazenamento do Azure, pode dimensionar a estes recursos em separado. Estes recursos não são geridos pelo plano de serviço de aplicações.

1. Na **Essentials**, clique nas **grupo de recursos** ligação.
   
    ![Aumentar verticalmente recursos relacionados da sua aplicação do Azure](./media/web-sites-scale/RGEssentialsLink.png)
2. Na **resumo** faz parte da **grupo de recursos** página, clique num recurso que pretende dimensionar. Captura de ecrã seguinte mostra um recurso de base de dados SQL e um recurso de armazenamento do Azure.
   
    ![Navegue até à página de grupo de recursos para aumentar verticalmente a sua aplicação do Azure](./media/web-sites-scale/ResourceGroup.png)
3. Para um recurso de base de dados SQL, clique em **configurações** > **escalão de preço** para dimensionar o escalão de preço.
   
    ![Aumentar verticalmente o back-end de base de dados SQL para a sua aplicação do Azure](./media/web-sites-scale/ScaleDatabase.png)
   
    Também pode ativar [georreplicação](../sql-database/sql-database-geo-replication-overview.md) para a sua instância de base de dados SQL.
   
    Para um recurso de armazenamento do Azure, clique em **configurações** > **configuração** para aumentar verticalmente as opções de armazenamento.
   
    ![Aumentar verticalmente a conta de armazenamento do Azure utilizada pela sua aplicação do Azure](./media/web-sites-scale/ScaleStorage.png)

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Compare os escalões de preços
Para obter informações detalhadas, como tamanhos de VM para cada escalão de preço, consulte [detalhes de preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/web-sites/).
Para uma tabela de limites do serviço, quotas e restrições e as funcionalidades suportadas em cada escalão, consulte [limites do serviço de aplicações](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="next-steps"></a>Passos Seguintes
* Para obter informações sobre preços, suporte e SLA, visite as seguintes ligações:
  
    [Detalhes dos preços das transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/)
  
    [Planos de suporte do Azure da Microsoft](https://azure.microsoft.com/support/plans/)
  
    [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/)
  
    [Detalhes dos preços da base de dados SQL](https://azure.microsoft.com/pricing/details/sql-database/)
  
    [Máquina virtual e tamanhos do serviço Cloud do Microsoft Azure][vmsizes]
  
* Para obter informações sobre o App Service do Azure melhores práticas, incluindo a criação de uma arquitetura resiliente e dimensionável, consulte [práticas recomendadas: Aplicações Web do serviço de aplicações do Azure](https://azure.microsoft.com/blog/best-practices-windows-azure-websites-waws/).
* Para os vídeos sobre o dimensionamento de aplicações do serviço de aplicações, consulte os seguintes recursos:
  
  * [Quando dimensionar Web sites do Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
  * [Web sites do Azure, da CPU de dimensionamento automático ou agendadas - com Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
  * [Escala de Web sites como o Azure - com Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
