---
title: Dimensione as características e capacidades
description: Saiba como escalar uma aplicação no Azure App Service. Obtenha mais CPU, memória, espaço em disco e funcionalidades extra.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: eb0ce5cfadbab247299cd3c22bf6ee618bf19731
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582486"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Dimensione uma aplicação no Azure App Service

Este artigo mostra-lhe como escalar a sua aplicação no Azure App Service. Existem dois fluxos de trabalho para dimensionamento, escala e escala, e este artigo explica a escala do fluxo de trabalho.

* [Escala](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais CPU, memória, espaço em disco e funcionalidades extra como máquinas virtuais dedicadas (VMs), domínios e certificados personalizados, slots de encenação, autoscaling e muito mais. Aumenta-se alterando o nível de preços do plano de Serviço de Aplicações a que a sua aplicação pertence.
* [Escala para fora](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumente o número de instâncias VM que executam a sua aplicação.
  Você pode escalar até 30 instâncias, dependendo do seu nível de preços. [Ambientes de Serviço de Aplicações](environment/intro.md) em nível **isolado** aumentam ainda mais a sua contagem de escala para 100 instâncias. Para obter mais informações sobre a escala, consulte [a contagem de instâncias de escala manual ou automática](../azure-monitor/autoscale/autoscale-get-started.md). Lá, você descobre como usar autoscaling, que é escalar a contagem de instâncias automaticamente com base em regras e horários predefinidos.

As definições de escala demoram apenas alguns segundos a aplicar e a afetar todas as aplicações do seu [plano de Serviço de Aplicações.](../app-service/overview-hosting-plans.md)
Não exigem que altere o seu código ou reimplante a sua aplicação.

Para obter informações sobre os preços e funcionalidades dos planos individuais do Serviço de Aplicações, consulte detalhes [de preços do serviço de aplicações.](https://azure.microsoft.com/pricing/details/web-sites/)  

> [!NOTE]
> Antes de mudar um plano de Serviço de Aplicações do nível **Free,** tem primeiro de remover os [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para a sua subscrição Azure. Para visualizar ou alterar opções para a subscrição do Microsoft Azure App Service, consulte as [subscrições do Microsoft Azure.][azuresubscriptions]
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Dimensione o seu nível de preços

> [!NOTE]
> Para escalar até ao nível **PremiumV3,** consulte [o nível Configure PremiumV3 para o Serviço de Aplicações.](app-service-configure-premium-tier.md)
>

1. No browser, abra o [portal do Azure][portal].

1. Na sua página de aplicações do Serviço de Aplicações, a partir do menu esquerdo, selecione **Scale Up (plano de Serviço de Aplicações)**.
   
3. Escolha o seu nível e, em seguida, **selecione Aplicar**. Selecione as diferentes categorias (por exemplo, **Produção)** e consulte **também opções adicionais** para mostrar mais níveis.
   
    ![Navegue para aumentar a sua aplicação Azure.][ChooseWHP]

    Quando a operação estiver concluída, vê um pop-up de notificação com uma marca verde de verificação de sucesso.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Recursos relacionados à escala
Se a sua aplicação depender de outros serviços, como a Base de Dados Azure SQL ou o Azure Storage, pode aumentar estes recursos separadamente. Estes recursos não são geridos pelo plano do Serviço de Aplicações.

1. Na página **'Vista Geral'** para a sua aplicação, selecione o link **do grupo Recursos.**
   
    ![Dimensione os recursos relacionados da sua app Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na parte **sumária** da página do **grupo Recursos,** selecione um recurso que pretende escalar. A imagem que se segue mostra um recurso SQL Database.
   
    ![Navegue para a página do grupo de recursos para escalar a sua app Azure](./media/web-sites-scale/ResourceGroup.png)

    Para aumentar o recurso relacionado, consulte a documentação para o tipo de recurso específico. Por exemplo, para escalar uma única Base de Dados SQL, consulte [os recursos de base de dados únicos de escala na Base de Dados Azure SQL](../azure-sql/database/single-database-scale.md). Para aumentar uma Base de Dados Azure para o recurso MySQL, consulte [os recursos de Escala MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar níveis de preços

Para obter informações detalhadas, tais como tamanhos VM para cada nível de preços, consulte detalhes [de preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service).

Para uma tabela de limites de serviço, quotas e constrangimentos, e funcionalidades suportadas em cada nível, consulte [os limites do Serviço de Aplicações](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Mais recursos

[Scale instance count manually or automatically](../azure-monitor/autoscale/autoscale-get-started.md) (Dimensionar a contagem de instâncias manual ou automaticamente)  
[Configure o nível PremiumV3 para o Serviço de Aplicações](app-service-configure-premium-tier.md)

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