---
title: Ver a tendência de custo mensal estimado laboratório no Azure DevTest Labs | Documentos da Microsoft
description: Saiba mais sobre o gráfico de tendência de custo estimado mensal do Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: d3a02a850059dc96e4e4ba306fed7cded2f7dd7d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454192"
---
# <a name="view-the-monthly-estimated-lab-cost-trend-in-azure-devtest-labs"></a>Ver a tendência de custo mensal estimado laboratório no Azure DevTest Labs
A funcionalidade de gestão de custos do DevTest Labs ajuda-o a controlar o custo do seu laboratório. Este artigo ilustra como utilizar o **tendência de custo mensal estimado** gráfico para ver estimado custo até à data o calendário do mês atual e o custo previsto do mês final para o mês de calendário atual. Este artigo também mostra como gerenciar melhor os custos de laboratório, definindo gastos metas e limiares que, quando alcançado, o acionador DevTest Labs para relatar os resultados para.

## <a name="viewing-the-monthly-estimated-cost-trend-chart"></a>Visualizar o gráfico de tendência de custo estimado mensal
Para ver o gráfico de tendência de custo estimado mensal, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Se necessário, selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista. (Seu laboratório já pode ser mostrado no Dashboard sob **todos os recursos**).
1. Na lista de laboratórios, selecione o laboratório pretendido.  
1. O laboratório **descrição geral** área, selecione **Konfigurace a zásady**.   
1. À esquerda sob **custo de controlo**, selecione **tendência de custo**.

   Captura de ecrã seguinte mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

O **custo estimado** valor é estimado custo até à data do mês de calendário atual. O **custo previsto** é o custo estimado para o mês de calendário atual todo, calculado com o custo do laboratório durante os últimos cinco dias.

As quantidades de custo são arredondadas para o número inteiro seguinte. Por exemplo: 

* 5.01 Arredonda por excesso até 6 
* 5.50 Arredonda por excesso até 6
* 5.99 Arredonda por excesso até 6

Como ele declara acima do gráfico, os custos de vir por predefinição no gráfico estão *estimado* custa usando [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) oferecem taxas. Também pode definir seus próprios gastos destinos que são apresentados nos gráficos por [gerenciando os destinos de custo para o laboratório.](#managing-cost-targets-for-your-lab)

Além disso, seguem-se *não* incluídas no cálculo de custo:

* Subscrições de CSP e Dreamspark não são atualmente suportadas como o Azure DevTest Labs utiliza a [APIs de faturas do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o laboratório de custos, que não suporta subscrições de CSP ou Dreamspark.
* As taxas de oferta. Atualmente, não é possível utilizar as tarifas da oferta (mostradas na sua subscrição) que ter negociado com a Microsoft ou Microsoft parceiros. São utilizadas apenas as taxas de pay as you go.
* Seus impostos
* Slevy
* A moeda de faturação. Atualmente, o custo do laboratório é apresentado apenas na moeda USD.

## <a name="managing-cost-targets-for-your-lab"></a>Gestão de destinos de custo para o laboratório
DevTest Labs permite que a gerenciar melhor os custos em seu laboratório, definindo um destino de gastos, em seguida, pode ver no gráfico de tendência de custo estimado mensal. DevTest Labs pode também enviar-lhe uma notificação quando os gastos de destino especificado ou o limiar for atingido. 

1. No seu laboratório **descrição geral** painel, selecione **Konfigurace a zásady**.
1. À esquerda sob **custo de controlo**, selecione **tendência de custo**.

    ![Gerir o botão de destino](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. Na **tendência de custo** painel, selecione **destino gerir**.

    ![Gerir o botão de destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. No painel de destino de gerir, especifique o destino pretendido de gastos e a limiares. Também pode definir se cada limite selecionado é reportado no gráfico de tendência de custo ou por meio de uma notificação de webhook.

    ![Gerir o painel de destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período de tempo que deseja rastreados de destinos de custo.
      - **Mensal**: destinos de custo são controlados por mês.
      - **Foi corrigido**: destinos de custo são registados para o intervalo de data que especificar na data de início e campos de data de fim. Normalmente, isso pode corresponder com o tempo que o seu projeto é agendado para ser executada.
   - Especifique um **custo de destino**. Por exemplo, pode ser quanto planeja gastar neste laboratório, no período de tempo que definiu.
   - Selecione para ativar ou desativar qualquer limiar que pretende comunicado – em incrementos de 25% – até 125% do seu especificado **custo de destino**.
      - **Notificar**: Quando esse limiar for cumprido, será notificado por um URL de webhook especificado.
      - **Vykreslit v grafu**: Quando esse limiar for cumprido, os resultados são desenhados no gráfico de tendência de custo, pode ver, conforme descrito em [visualizar o gráfico de tendência de custo estimado mensal](#viewing-the-monthly-estimated-cost-trend-chart).
   - Se optar por **notificar** quando o limiar for cumprido, tem de especificar um URL do webhook. Na área de integrações de custo, selecione **clique aqui para adicionar uma integração**.

      Introduza um URL do Webhook no painel de notificação de configurar e, em seguida, selecione **OK**.

       ![Configurar o painel de notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Se especificar **notificar**, tem de definir um URL do webhook.
      - Da mesma forma, se definir um URL do webhook, tem de definir **notificação** ao **no** no painel de limiar de custo.
      - Tem de criar um webhook antes de introduzi-la aqui.  

      Para obter mais informações sobre os webhooks, consulte [criar um webhook ou uma função de Azure API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 
 

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionados
* [Mais duas etapas para manter o custo controlado no DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Por que os limiares de custo?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## <a name="next-steps"></a>Passos Seguintes
Aqui estão algumas coisas a experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) -Saiba como configurar as políticas de vários utilizadas para regular como seu laboratório e as respetivas VMs são usados. 
* [Criar uma imagem personalizada](devtest-lab-create-template.md) - quando cria uma VM, especifique uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um ficheiro VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) - DevTest Labs suporta a criação de VMs com base nas imagens do Azure Marketplace. Este artigo ilustra como especificar que, se houver, imagens do Azure Marketplace podem ser utilizadas durante a criação de VMs num laboratório.
* [Criar uma VM num laboratório](devtest-lab-add-vm.md) -ilustra como criar uma VM a partir de uma imagem base (qualquer um dos personalizado ou Marketplace) e como trabalhar com artefactos em sua VM.

