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
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: bc81f479305f39024b8d946e1ace3fc84ecb6253
ms.sourcegitcommit: 89b5e63945d0c325c1bf9e70ba3d9be6888da681
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57588861"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Controlar os custos associados a um laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como controlar o custo do seu laboratório. Ele mostra como ver a estimativa de custos trent para o mês de calendário atual para o laboratório. O artigo também mostra como ver o custo do mês até à data por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Ver a tendência de custo mensal estimado de laboratório 
Nesta secção, irá aprender a utilizar o **tendência de custo mensal estimado** gráfico para ver estimado custo até à data o calendário do mês atual e o custo previsto do mês final para o mês de calendário atual. Também irá aprender a gerir os custos de laboratório, definindo gastos metas e limiares que, quando alcançado, o acionador DevTest Labs para relatar os resultados para.

Para ver o gráfico de tendência de custo estimado mensal, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o seu laboratório.  
4. Selecione **Konfigurace a zásady** no menu da esquerda.  
4. Selecione **tendência de custo** no **controlo de custos** secção no menu da esquerda. Captura de ecrã seguinte mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

    O **custo estimado** valor é estimado custo até à data do mês de calendário atual. O **custo previsto** é o custo estimado para o mês de calendário atual todo, calculado com o custo do laboratório durante os últimos cinco dias.

    As quantidades de custo são arredondadas para o número inteiro seguinte. Por exemplo: 

    * 5.01 Arredonda por excesso até 6 
    * 5.50 Arredonda por excesso até 6
    * 5.99 Arredonda por excesso até 6

    Como ele declara acima do gráfico, os custos de vir por predefinição no gráfico estão *estimado* custa usando [pay as you go](https://azure.microsoft.com/offers/ms-azr-0003p/) oferecem taxas. Também pode definir seus próprios gastos destinos que são apresentados nos gráficos por [gerenciando os destinos de custo para o laboratório.](#managing-cost-targets-for-your-lab)

    Os seguintes custos estão *não* incluídas no cálculo de custo:

    * Subscrições de CSP e Dreamspark não são atualmente suportadas como o Azure DevTest Labs utiliza a [APIs de faturas do Azure](../billing/billing-usage-rate-card-overview.md) para calcular o laboratório de custos, que não suporta subscrições de CSP ou Dreamspark.
    * As taxas de oferta. Atualmente, não é possível utilizar as tarifas da oferta (mostradas na sua subscrição) que ter negociado com a Microsoft ou Microsoft parceiros. São utilizadas apenas as taxas de pay as you go.
    * Seus impostos
    * Slevy
    * A moeda de faturação. Atualmente, o custo do laboratório é apresentado apenas na moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gestão de destinos de custo para o laboratório
DevTest Labs permite que a gerenciar melhor os custos em seu laboratório, definindo um destino de gastos, em seguida, pode ver no gráfico de tendência de custo estimado mensal. DevTest Labs pode também enviar-lhe uma notificação quando os gastos de destino especificado ou o limiar for atingido. 

1. Sobre o **tendência de custo** página, selecione **destino gerir**.

    ![Gerir o botão de destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Sobre o **gerir destino** , especifique um destino de gastos e limiares. Também pode definir se cada limite selecionado é reportado no gráfico de tendência de custo ou por meio de uma notificação de webhook.

    ![Gerir o painel de destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período de tempo que deseja rastreados de destinos de custo.
      - **Mensal**: destinos de custo são controlados por mês.
      - **Foi corrigido**: destinos de custo são registados para o intervalo de datas que especificou nas datas de início e de fim. Normalmente, estes valores representam o tempo que o seu projeto é agendado para ser executada.
   - Especifique um **custo de destino**. Por exemplo, quanto planeja gastar neste laboratório, no período de tempo que definiu.
   - Selecione para ativar ou desativar qualquer limiar que pretende comunicado – em incrementos de 25% – até 125% do seu especificado **custo de destino**.
      - **Notificar**: Quando esse limiar for cumprido, será notificado por um URL de webhook especificado.
      - **Vykreslit v grafu**: Quando esse limiar for cumprido, os resultados são desenhados no gráfico de tendência de custo, pode ver, conforme descrito em [visualizar o gráfico de tendência de custo estimado mensal](#viewing-the-monthly-estimated-cost-trend-chart).
   - Se optar por **notificar** quando o limiar for cumprido, tem de especificar um URL do webhook. Na área de integrações de custo, selecione **clique aqui para adicionar uma integração**. Introduza um **URL de Webhook** no painel de notificação de configurar e, em seguida, selecione **OK**.

       ![Configurar o painel de notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

      - Se especificar **notificar**, tem de definir um URL do webhook.
      - Da mesma forma, se definir um URL do webhook, tem de definir **notificação** ao **no** no painel de limiar de custo.
      - Tem de criar um webhook antes de introduzi-la aqui.  

      Para obter mais informações sobre os webhooks, consulte [criar um webhook ou uma função de Azure API](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Vista de custos por recurso 
A funcionalidade de tendência de custo mensal no labs permite-lhe ver o quanto têm gasto no mês atual. Ela também mostra a projeção de gastos até ao fim do mês, com base nos seus gastos nos últimos sete dias. Para ajudar a compreender por que motivo o gastos no laboratório está a cumprir limiares no início, pode utilizar o **de custos por recurso** funcionalidade que lhe mostra o custo acumulado do mês **por recurso** numa tabela.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório pretendido.  
4. Selecione **Konfigurace a zásady** no menu da esquerda.
5. Selecione **de custos por recurso** no **controlo de custos** secção no menu da esquerda. Ver os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esta funcionalidade ajuda-o a identificar facilmente os recursos que custam mais, para que pode tomar medidas para reduzir os gastos de laboratório. Por exemplo, o custo de uma VM baseia-se no tamanho da VM. Quanto maior for o tamanho da VM, mais é o custo. Pode encontrar facilmente o tamanho de uma VM e o proprietário, para que pode se comunicar com o proprietário da VM para compreender por que motivo esse tamanho VM é necessária e se existe uma chance para reduzir o tamanho.

[Política de encerramento automático](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) ajuda-o a reduzir o custo por encerrar as VMs do laboratório num determinado momento do dia. No entanto, um utilizador de laboratório pode desativar a política de encerramento, o que aumenta o custo de execução da VM. Pode selecionar uma VM na tabela para ver se ele tem sido optado por-fora da política de encerramento automático. Se for esse o caso, pode se comunicar com o proprietário da VM para descobrir por que a VM tem sido tenha optado por ela-da política.
 
## <a name="next-steps"></a>Passos Seguintes
Aqui estão algumas coisas a experimentar a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) -Saiba como configurar as políticas de vários utilizadas para regular como seu laboratório e as respetivas VMs são usados. 
* [Criar uma imagem personalizada](devtest-lab-create-template.md) - quando cria uma VM, especifique uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um ficheiro VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) - DevTest Labs suporta a criação de VMs com base nas imagens do Azure Marketplace. Este artigo ilustra como especificar que, se houver, imagens do Azure Marketplace podem ser utilizadas durante a criação de VMs num laboratório.
* [Criar uma VM num laboratório](devtest-lab-add-vm.md) -ilustra como criar uma VM a partir de uma imagem base (qualquer um dos personalizado ou Marketplace) e como trabalhar com artefactos em sua VM.

