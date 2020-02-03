---
title: Exibir a tendência de custo estimado mensal do laboratório em Azure DevTest Labs
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721732"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Acompanhe os custos associados a um laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como controlar o custo do seu laboratório. Ele mostra como exibir o Trent de custo estimado para o mês do calendário atual do laboratório. O artigo também mostra como exibir o custo acumulado no mês por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Exibir a tendência de custo estimado mensal do laboratório 
Nesta secção, aprende-se a utilizar o gráfico mensal de tendência de **custos estimados** para ver o custo estimado do mês civil em curso e o custo previsto para o mês civil em curso. Você também aprende a gerenciar os custos de laboratório definindo metas de gastos e limites que, quando atingido, disparam os laboratórios de DevTest para relatar os resultados para você.

Para exibir o gráfico de tendência de custo estimado mensal, siga estas etapas: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione seu laboratório.  
4. Selecione **Configuração e políticas** no menu esquerdo.  
4. Selecione **a tendência Custo** na secção de rastreio de **custos** no menu esquerdo. A captura de tela a seguir mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

    O valor estimado do **custo** é o custo estimado do mês civil em curso. O **custo projetado** é o custo estimado para todo o mês civil atual, calculado usando o custo do laboratório para os cinco dias anteriores.

    Os valores de custo são arredondados para o próximo número inteiro. Por exemplo: 

   * 5, 1 arredonda até 6 
   * 5,50 arredonda até 6
   * 5,99 arredonda até 6

     Como se afirma acima do gráfico, os custos que vê por padrão na tabela são custos *estimados* usando as tarifas de oferta [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Também pode definir os seus próprios alvos de gastos que são exibidos nas tabelas, gerindo os alvos de [custos para o seu laboratório.](#managing-cost-targets-for-your-lab)

     Os seguintes custos *não* estão incluídos no cálculo dos custos:

   * As subscrições do CSP e do Dreamspark não são atualmente suportadas, uma vez que o Azure DevTest Labs utiliza as APIs de [faturação azure](../cost-management-billing/manage/usage-rate-card-overview.md) para calcular o custo do laboratório, que não suporta subscrições de CSP ou Dreamspark.
   * Suas tarifas de oferta. No momento, você não pode usar as taxas de oferta (mostradas em sua assinatura) que você negocia com a Microsoft ou com parceiros da Microsoft. Somente as tarifas pagas conforme o uso são usadas.
   * Seus impostos
   * Seus descontos
   * Sua moeda de cobrança. Atualmente, o custo do laboratório é exibido apenas na moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerenciando metas de custo para seu laboratório
O DevTest Labs permite que você gerencie melhor os custos em seu laboratório definindo um alvo de gastos que você pode exibir no gráfico de tendência de custo estimado mensalmente. O DevTest Labs também pode enviar uma notificação quando os gastos ou o limite de destino especificado forem atingidos. 

1. Na página de **tendência Custo,** selecione **Gerir o alvo**.

    ![Botão gerenciar destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na **página-alvo de Gestão,** especifique um alvo de gastos e limiares. Você também pode definir se cada limite selecionado é relatado no gráfico de tendência de custo ou por meio de uma notificação de webhook.

    ![Painel gerenciar destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período de tempo durante o qual você deseja que os destinos de custo sejam acompanhados.
      - **Mensal :** as metas de custos são rastreadas por mês.
      - **Fixo**: os objetivos de custo são rastreados para o intervalo de data que especifica nas datas de início e fim. Normalmente, esses valores representam por quanto tempo seu projeto está agendado para ser executado.
   - Especifique um **custo-alvo**. Por exemplo, quanto você planeja gastar neste laboratório no período de tempo definido.
   - Selecione para ativar ou desativar qualquer limiar que pretenda reportado – em incrementos de 25% – até 125% do custo do **Target**especificado .
      - **Notifique**: Quando este limiar for atingido, é notificado por um URL de webhook que especifica.
      - **Enredo na tabela**: Quando este limiar é cumprido, os resultados são traçados no gráfico de tendência de custos que você pode ver, como descrito no gráfico de visualização do gráfico mensal de tendência de custos estimado.
   - Se optar por **notificar** quando o limiar for atingido, deve especificar um URL de webhook. Na área de integrações de Custos, selecione **Clique aqui para adicionar uma integração**. Introduza um **URL webhook** no painel de notificação configure e, em seguida, selecione **OK**.

       ![Configurar o painel de notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se especificar **notificar,** deve definir um URL de webhook.
     - Da mesma forma, se definir um URL webhook, deve definir **a Notificação** para **Ligar** no painel de limiar de custo.
     - Você deve criar um webhook antes de inseri-lo aqui.  

       Para mais informações sobre webhooks, consulte [Criar um webhook ou função API Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Exibir custo por recurso 
O recurso de tendência de custo mensal em laboratórios permite que você veja quanto você gastou no mês do calendário atual. Ele também mostra a projeção dos gastos até o fim do mês, com base em seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório estão a cumprir os limiares mais cedo, você pode usar o **custo por recurso** que mostra o custo mensal por **recurso** em uma tabela.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços**e, em seguida, selecione **DevTest Labs** da lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **Configuração e políticas** no menu esquerdo.
5. Selecione **Custo por recurso** na secção de rastreio **custo** no menu esquerdo. Você verá os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esse recurso ajuda a identificar facilmente os recursos que custam mais para que você possa tomar medidas para reduzir os gastos com o laboratório. Por exemplo, o custo de uma VM é baseado no tamanho da VM. Quanto maior o tamanho da VM, mais é o custo. Você pode encontrar facilmente o tamanho de uma VM e o proprietário, para que você possa se comunicar com o proprietário da VM para entender por que esse tamanho de VM é necessário e se há uma chance de diminuir o tamanho.

[A política de encerramento automático ajuda-o](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) a reduzir os custos, desligando os VMs de laboratório numa determinada hora do dia. No entanto, um usuário de laboratório pode recusar a política de desligamento, o que aumenta o custo da execução da VM. Você pode selecionar uma VM na tabela para ver se ela foi recusada da política de desligamento automático. Se esse for o caso, você poderá se comunicar com o proprietário da VM para descobrir por que a VM foi recusada da política.
 
## <a name="next-steps"></a>Passos Seguintes
Aqui estão algumas coisas a serem experimentadas a seguir:

* [Defina as políticas](devtest-lab-set-lab-policy.md) de laboratório - Aprenda a definir as várias políticas usadas para governar a forma como o seu laboratório e os seus VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) - Quando criar um VM, especifice uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configure imagens](devtest-lab-configure-marketplace-images.md) do Marketplace - DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens do Azure Marketplace podem ser usadas ao criar VMs em um laboratório.
* [Crie um VM num laboratório](devtest-lab-add-vm.md) - Ilustra como criar um VM a partir de uma imagem base (personalizada ou marketplace), e como trabalhar com artefactos no seu VM.

