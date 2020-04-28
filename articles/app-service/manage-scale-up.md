---
title: Aumentar as funcionalidades e capacidades
description: Aprenda a escalar uma aplicação no Azure App Service. Obtenha mais CPU, memória, espaço em disco e funcionalidades extra.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: dfd9297e80836978b8a185df3fc4659676383802
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75659904"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Aumentar uma aplicação no Azure App Service

Este artigo mostra-lhe como escalar a sua app no Azure App Service. Existem dois fluxos de trabalho para escala, escala e escala, e este artigo explica a escala de fluxo de trabalho.

* [Escala para cima](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais CPU, memória, espaço em disco e funcionalidades extra como máquinas virtuais dedicadas (VMs), domínios e certificados personalizados, slots de encenação, autoscalcificação e muito mais. Escala-se alterando o nível de preços do plano de Serviço de Aplicações a que a sua aplicação pertence.
* [Escala para fora](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumente o número de casos vm que executam a sua aplicação.
  Pode aumentar até 30 instâncias, dependendo do seu nível de preços. [O App Service Environments](environment/intro.md) em nível **isolado** aumenta ainda mais a sua contagem de escala para 100 instâncias. Para obter mais informações sobre o escalonamento, consulte a contagem de [exemplos de escala manualmente ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Lá, você descobre como usar autoscalcificação, que é escalar a contagem de instâncias automaticamente com base em regras e horários pré-definidos.

As definições de escala demoram apenas alguns segundos a aplicar e a afetar todas as aplicações do seu plano de [Serviço de Aplicações](../app-service/overview-hosting-plans.md).
Não exigem que altere o seu código ou reimplemente a sua aplicação.

Para obter informações sobre os preços e funcionalidades dos planos individuais do App Service, consulte os detalhes dos preços do serviço de [aplicações.](https://azure.microsoft.com/pricing/details/web-sites/)  

> [!NOTE]
> Antes de mudar um plano de Serviço de Aplicações **do** free tier, primeiro deve remover os limites de [gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para a sua subscrição Azure. Para visualizar ou alterar opções para a subscrição do Microsoft Azure App Service, consulte as [subscrições do Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Aumentar o seu nível de preços

> [!NOTE]
> Para escalar até ao nível **PremiumV2,** consulte [o nível Configure PremiumV2 para o Serviço](app-service-configure-premium-tier.md)de Aplicações .
>

1. No browser, abra o [portal do Azure][portal].

1. Na página da sua aplicação App Service, a partir do menu esquerdo, selecione Scale Up (plano de serviço de **aplicações)**.
   
3. Escolha o seu nível e, em seguida, selecione **Aplicar**. Selecione as diferentes categorias (por exemplo, **Produção)** e consulte também **opções adicionais** para mostrar mais níveis.
   
    ![Navegue para escalar a sua aplicação Azure.][ChooseWHP]

    Quando a operação estiver concluída, vê-se um pop-up de notificação com uma marca de verificação de sucesso verde.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Recursos relacionados com escala
Se a sua aplicação depender de outros serviços, como o Azure SQL Database ou o Azure Storage, pode aumentar esses recursos separadamente. Estes recursos não são geridos pelo plano de Serviço de Aplicações.

1. Na página **'Visão Geral'** da sua aplicação, selecione o link do **grupo Recurso.**
   
    ![Aumentar os recursos relacionados da sua app Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na parte **resumo** da página do **grupo Recursos,** selecione um recurso que pretende escalar. A imagem que se segue mostra um recurso de base de dados SQL.
   
    ![Navegue para a página do grupo de recursos para aumentar a sua app Azure](./media/web-sites-scale/ResourceGroup.png)

    Para aumentar o recurso relacionado, consulte a documentação para o tipo de recurso específico. Por exemplo, para aumentar uma única base de dados SQL, consulte os recursos de base de dados individuais da Escala na Base de [Dados Azure SQL](../sql-database/sql-database-single-database-scale.md). Para escalar uma Base de Dados Azure para o recurso MySQL, consulte [os recursos Scale MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar níveis de preços

Para obter informações detalhadas, tais como tamanhos vm para cada nível de preços, consulte detalhes de preços do serviço de [aplicações](https://azure.microsoft.com/pricing/details/app-service).

Para uma tabela de limites de serviço, quotas e restrições, e funcionalidades suportadas em cada nível, consulte os limites do Serviço de [Aplicações.](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)

<a name="Next Steps"></a>

## <a name="more-resources"></a>Mais recursos

[Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Dimensionar a contagem de instâncias manual ou automaticamente)  
[Configure o nível PremiumV2 para o Serviço de Aplicações](app-service-configure-premium-tier.md)

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
