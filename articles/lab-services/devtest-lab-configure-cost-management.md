---
title: Veja a tendência mensal de custos de laboratório estimados em Azure DevTest Labs
description: Este artigo fornece informações sobre como rastrear o custo do seu laboratório (gráfico de tendência de custos estimado mensalmente) em Azure DevTest Labs.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2e4fe55fac5edf73e16df05bd38cc2712a94377d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721732"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Custos de pista associados a um laboratório em Azure DevTest Labs
Este artigo fornece informações sobre como rastrear o custo do seu laboratório. Mostra como ver o custo estimado para o mês civil atual para o laboratório. O artigo também mostra como ver o custo mensal por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Ver a tendência mensal de custos estimados do laboratório 
Nesta secção, aprende-se a utilizar o gráfico mensal de tendência de **custos estimados** para ver o custo estimado do mês civil em curso e o custo previsto para o mês civil em curso. Também aprendes a gerir os custos do laboratório, definindo metas de gastos e limiares que, quando alcançados, desencadeiam a DevTest Labs para lhe reportar os resultados.

Para ver o gráfico mensal de tendência de custos estimados, siga estes passos: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o seu laboratório.  
4. Selecione **Configuração e políticas** no menu esquerdo.  
4. Selecione **a tendência Custo** na secção de rastreio de **custos** no menu esquerdo. A imagem que se segue mostra um exemplo de um gráfico de custos. 
   
    ![Gráfico de custos](./media/devtest-lab-configure-cost-management/graph.png)

    O valor estimado do **custo** é o custo estimado do mês civil em curso. O **custo projetado** é o custo estimado para todo o mês civil atual, calculado usando o custo do laboratório para os cinco dias anteriores.

    Os valores de custo são arredondados para o próximo número completo. Por exemplo: 

   * 5.01 rondas até 6 
   * 5.50 rounds até 6
   * 5.99 rounds até 6

     Como se afirma acima do gráfico, os custos que vê por padrão na tabela são custos *estimados* usando as tarifas de oferta [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Também pode definir os seus próprios alvos de gastos que são exibidos nas tabelas, gerindo os alvos de [custos para o seu laboratório.](#managing-cost-targets-for-your-lab)

     Os seguintes custos *não* estão incluídos no cálculo dos custos:

   * As subscrições do CSP e do Dreamspark não são atualmente suportadas, uma vez que o Azure DevTest Labs utiliza as APIs de [faturação azure](../cost-management-billing/manage/usage-rate-card-overview.md) para calcular o custo do laboratório, que não suporta subscrições de CSP ou Dreamspark.
   * As suas ofertas. Atualmente, não pode utilizar as tarifas de oferta (mostradas sob a sua subscrição) que negociou com os parceiros da Microsoft ou microsoft. Apenas são utilizadas taxas pay-as-you-go.
   * Seus impostos
   * Os seus descontos
   * Sua moeda de faturação. Atualmente, o custo do laboratório é apresentado apenas em moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerir alvos de custos para o seu laboratório
A DevTest Labs permite-lhe gerir melhor os custos do seu laboratório, estabelecendo um objetivo de gastos que pode então ver na tabela mensal de tendências de custos estimados. A DevTest Labs também pode enviar-lhe uma notificação quando o limite ou o limiar especificado do alvo for atingido. 

1. Na página de **tendência Custo,** selecione **Gerir o alvo**.

    ![Gerir o botão alvo](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na **página-alvo de Gestão,** especifique um alvo de gastos e limiares. Também pode definir se cada limiar selecionado é reportado no gráfico de tendência de custos ou através de uma notificação webhook.

    ![Gerir o painel alvo](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período de tempo durante o qual pretende que os alvos de custos sejam rastreados.
      - **Mensal :** as metas de custos são rastreadas por mês.
      - **Fixo**: os objetivos de custo são rastreados para o intervalo de data que especifica nas datas de início e fim. Normalmente, estes valores representam o tempo que o seu projeto está programado para ser executado.
   - Especifique um **custo-alvo**. Por exemplo, quanto planeia gastar neste laboratório no período de tempo que definiu.
   - Selecione para ativar ou desativar qualquer limiar que pretenda reportado – em incrementos de 25% – até 125% do custo do **Target**especificado .
      - **Notifique**: Quando este limiar for atingido, é notificado por um URL de webhook que especifica.
      - **Enredo na tabela**: Quando este limiar é cumprido, os resultados são traçados no gráfico de tendência de custos que você pode ver, como descrito no gráfico de visualização do gráfico mensal de tendência de custos estimado.
   - Se optar por **notificar** quando o limiar for atingido, deve especificar um URL de webhook. Na área de integrações de Custos, selecione **Clique aqui para adicionar uma integração**. Introduza um **URL webhook** no painel de notificação configure e, em seguida, selecione **OK**.

       ![Configure painel de notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se especificar **notificar,** deve definir um URL de webhook.
     - Da mesma forma, se definir um URL webhook, deve definir **a Notificação** para **Ligar** no painel de limiar de custo.
     - Tem de criar um gancho antes de entrar aqui.  

       Para mais informações sobre webhooks, consulte [Criar um webhook ou função API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Ver custo por recurso 
A funcionalidade mensal de tendência de custos em laboratórios permite-lhe ver quanto gastou no mês civil em curso. Mostra também a projeção dos gastos até ao final do mês, com base nos seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório estão a cumprir os limiares mais cedo, você pode usar o **custo por recurso** que mostra o custo mensal por **recurso** em uma tabela.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **Configuração e políticas** no menu esquerdo.
5. Selecione **Custo por recurso** na secção de rastreio **custo** no menu esquerdo. Vê-se os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esta funcionalidade ajuda-o a identificar facilmente os recursos que mais custam para que possa tomar medidas para reduzir os gastos do laboratório. Por exemplo, o custo de um VM baseia-se no tamanho do VM. Quanto maior for o tamanho do VM, mais é o custo. Você pode facilmente encontrar o tamanho de um VM e do proprietário, para que você possa falar com o proprietário vM para entender por que tal tamanho VM é necessário e se há uma chance de baixar o tamanho.

[A política de encerramento automático ajuda-o](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) a reduzir os custos, desligando os VMs de laboratório numa determinada hora do dia. No entanto, um utilizador de laboratório pode optar por não aceitar a política de encerramento, o que aumenta o custo de funcionamento do VM. Pode selecionar um VM na tabela para ver se foi optado pela política de encerramento automático. Se for esse o caso, pode falar com o dono da VM para descobrir porque é que o VM foi excluído da apólice.
 
## <a name="next-steps"></a>Passos seguintes
Aqui estão algumas coisas para tentar a seguir:

* [Defina as políticas](devtest-lab-set-lab-policy.md) de laboratório - Aprenda a definir as várias políticas usadas para governar a forma como o seu laboratório e os seus VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) - Quando criar um VM, especifice uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada a partir de um ficheiro VHD.
* [Configure imagens](devtest-lab-configure-marketplace-images.md) do Marketplace - DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas na criação de VMs num laboratório.
* [Crie um VM num laboratório](devtest-lab-add-vm.md) - Ilustra como criar um VM a partir de uma imagem base (personalizada ou marketplace), e como trabalhar com artefactos no seu VM.

