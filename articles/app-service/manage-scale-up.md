---
title: Escalar verticalmente recursos e capacidades
description: Saiba como escalar verticalmente um aplicativo no serviço Azure App. Obtenha mais CPU, memória, espaço em disco e recursos adicionais.
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.topic: article
ms.date: 08/19/2019
ms.custom: seodec18
ms.openlocfilehash: 37120377f87bee53f73fe4d3dd7ced59551a491f
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672315"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Escalar verticalmente um aplicativo no serviço Azure App

Este artigo mostra como dimensionar seu aplicativo no serviço Azure App. Há dois fluxos de trabalho para dimensionar, escalar verticalmente e escalar horizontalmente, e este artigo explica o fluxo de trabalho de escala vertical.

* [Escalar verticalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Obtenha mais CPU, memória, espaço em disco e recursos extras, como VMS (máquinas virtuais) dedicadas, domínios personalizados e certificados, slots de preparo, dimensionamento automático e muito mais. Você escala verticalmente alterando o tipo de preço do plano do serviço de aplicativo ao qual seu aplicativo pertence.
* [Escalar horizontalmente](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): Aumente o número de instâncias de VM que executam seu aplicativo.
  Você pode escalar horizontalmente até 30 instâncias, dependendo do tipo de preço. Os [ambientes do serviço de aplicativo](environment/intro.md) na camada **isolada** aumentam ainda mais sua contagem de expansão para instâncias de 100. Para obter mais informações sobre como escalar horizontalmente, consulte [dimensionar a contagem de instâncias manual ou automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md). Lá, você descobrirá como usar o dimensionamento automático, que é dimensionar automaticamente a contagem de instâncias com base em regras e agendamentos predefinidos.

As configurações de escala levam apenas alguns segundos para aplicar e afetar todos os aplicativos em seu [plano do serviço de aplicativo](../app-service/overview-hosting-plans.md).
Eles não exigem que você altere seu código ou reimplante seu aplicativo.

Para obter informações sobre os preços e os recursos de planos individuais do serviço de aplicativo, consulte [detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de mudar um plano do serviço de aplicativo da camada **gratuita** , você deve primeiro remover os [limites de gastos](https://azure.microsoft.com/pricing/spending-limits/) em vigor para sua assinatura do Azure. Para exibir ou alterar as opções de sua assinatura do serviço de aplicativo Microsoft Azure, consulte [Microsoft Azure assinaturas][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escalar verticalmente seu tipo de preço

> [!NOTE]
> Para escalar verticalmente para a camada **PremiumV2** , consulte [Configurar a camada PremiumV2 para o serviço de aplicativo](app-service-configure-premium-tier.md).
>

1. No browser, abra o [portal do Azure][portal].

1. Na página do aplicativo do serviço de aplicativo, no menu à esquerda, selecione **escalar verticalmente (plano do serviço de aplicativo)** .
   
3. Escolha sua camada e, em seguida, selecione **aplicar**. Selecione as diferentes categorias (por exemplo, **produção**) e **Veja também opções adicionais** para mostrar mais camadas.
   
    ![Navegue para escalar verticalmente seu aplicativo do Azure.][ChooseWHP]

    Quando a operação for concluída, você verá um pop-up de notificação com uma marca de seleção de êxito verde.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Dimensionar recursos relacionados
Se seu aplicativo depende de outros serviços, como o banco de dados SQL do Azure ou o armazenamento do Azure, você pode escalar verticalmente esses recursos separadamente. Esses recursos não são gerenciados pelo plano do serviço de aplicativo.

1. Na página **visão geral** do seu aplicativo, selecione o link **grupo de recursos** .
   
    ![Escalar verticalmente os recursos relacionados ao aplicativo do Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. Na parte **Resumo** da página **grupo de recursos** , selecione um recurso que você deseja dimensionar. A captura de tela a seguir mostra um recurso de banco de dados SQL.
   
    ![Navegue até a página do grupo de recursos para escalar verticalmente seu aplicativo do Azure](./media/web-sites-scale/ResourceGroup.png)

    Para escalar verticalmente o recurso relacionado, consulte a documentação para o tipo de recurso específico. Por exemplo, para escalar verticalmente um único banco de dados SQL, consulte [dimensionar recursos de banco de dados único no banco de dados SQL do Azure](../sql-database/sql-database-single-database-scale.md). Para escalar verticalmente um recurso de banco de dados do Azure para MySQL, consulte [dimensionar recursos do MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar tipos de preço

Para obter informações detalhadas, como tamanhos de VM para cada tipo de preço, consulte [detalhes de preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service).

Para obter uma tabela de limites de serviço, cotas e restrições e recursos com suporte em cada camada, consulte [limites do serviço de aplicativo](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Mais recursos

[Scale instance count manually or automatically](../monitoring-and-diagnostics/insights-how-to-scale.md) (Dimensionar a contagem de instâncias manual ou automaticamente)  
[Configurar camada de PremiumV2 para o serviço de aplicativo](app-service-configure-premium-tier.md)

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
