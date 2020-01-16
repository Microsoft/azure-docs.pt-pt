---
title: Exibir a tendência de custo estimado mensal do laboratório em Azure DevTest Labs | Microsoft Docs
description: Saiba mais sobre o gráfico de tendência de custo estimado mensal Azure DevTest Labs.
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
ms.openlocfilehash: 247327d001342fd0e2943ae7cf010e648761cdaa
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976351"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Acompanhe os custos associados a um laboratório no Azure DevTest Labs
Este artigo fornece informações sobre como controlar o custo do seu laboratório. Ele mostra como exibir o Trent de custo estimado para o mês do calendário atual do laboratório. O artigo também mostra como exibir o custo acumulado no mês por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Exibir a tendência de custo estimado mensal do laboratório 
Nesta seção, você aprenderá a usar o gráfico de **tendência de custo estimado mensal** para exibir o custo estimado do mês do calendário atual e o custo do fim do mês projetado para o mês do calendário atual. Você também aprende a gerenciar os custos de laboratório definindo metas de gastos e limites que, quando atingido, disparam os laboratórios de DevTest para relatar os resultados para você.

Para exibir o gráfico de tendência de custo estimado mensal, siga estas etapas: 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione seu laboratório.  
4. Selecione **configuração e políticas** no menu à esquerda.  
4. Selecione **tendência de custo** na seção **controle de custo** no menu à esquerda. A captura de tela a seguir mostra um exemplo de um gráfico de custo. 
   
    ![Gráfico de custo](./media/devtest-lab-configure-cost-management/graph.png)

    O valor de **custo estimado** é o custo estimado do mês do calendário atual. O **custo projetado** é o custo estimado para todo o mês do calendário atual, calculado usando o custo do laboratório dos cinco dias anteriores.

    Os valores de custo são arredondados para o próximo número inteiro. Por exemplo: 

   * 5, 1 arredonda até 6 
   * 5,50 arredonda até 6
   * 5,99 arredonda até 6

     Como ele diz acima do gráfico, os custos que você vê por padrão no gráfico são custos *estimados* usando tarifas [de oferta pagas conforme o](https://azure.microsoft.com/offers/ms-azr-0003p/) uso. Você também pode definir seus próprios destinos de gastos que são exibidos nos gráficos [Gerenciando as metas de custo para seu laboratório.](#managing-cost-targets-for-your-lab)

     Os seguintes custos *não* estão incluídos no cálculo de custo:

   * Atualmente, não há suporte para assinaturas do CSP e do DreamSpark, pois Azure DevTest Labs usa as [APIs de cobrança do Azure](../cost-management-billing/manage/usage-rate-card-overview.md) para calcular o custo do laboratório, que não oferece suporte a assinaturas CSP ou DreamSpark.
   * Suas tarifas de oferta. No momento, você não pode usar as taxas de oferta (mostradas em sua assinatura) que você negocia com a Microsoft ou com parceiros da Microsoft. Somente as tarifas pagas conforme o uso são usadas.
   * Seus impostos
   * Seus descontos
   * Sua moeda de cobrança. Atualmente, o custo do laboratório é exibido apenas na moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerenciando metas de custo para seu laboratório
O DevTest Labs permite que você gerencie melhor os custos em seu laboratório definindo um alvo de gastos que você pode exibir no gráfico de tendência de custo estimado mensalmente. O DevTest Labs também pode enviar uma notificação quando os gastos ou o limite de destino especificado forem atingidos. 

1. Na página **tendência de custo** , selecione **gerenciar destino**.

    ![Botão gerenciar destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na página **gerenciar destino** , especifique um alvo e limites de gastos. Você também pode definir se cada limite selecionado é relatado no gráfico de tendência de custo ou por meio de uma notificação de webhook.

    ![Painel gerenciar destino](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período de tempo durante o qual você deseja que os destinos de custo sejam acompanhados.
      - **Mensalmente**: os destinos de custo são acompanhados por mês.
      - **Corrigido**: os destinos de custo são rastreados para o intervalo de datas especificado nas datas de início e término. Normalmente, esses valores representam por quanto tempo seu projeto está agendado para ser executado.
   - Especifique um **custo de destino**. Por exemplo, quanto você planeja gastar neste laboratório no período de tempo definido.
   - Selecione para habilitar ou desabilitar qualquer limite que você deseja reportar – em incrementos de 25% – até 125% do **custo de destino**especificado.
      - **Notificar**: quando esse limite for atingido, você será notificado por uma URL do webhook que você especificar.
      - **Plotar no gráfico**: quando esse limite é atingido, os resultados são plotados no grafo de tendência de custo que você pode exibir, conforme descrito em exibindo o gráfico de tendência de custo estimado mensal.
   - Se você optar por **notificar** quando o limite for atingido, deverá especificar uma URL de webhook. Na área integrações de custo, selecione **clique aqui para adicionar uma integração**. Insira uma **URL de webhook** no painel configurar notificação e, em seguida, selecione **OK**.

       ![Configurar o painel de notificação](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Se você especificar **notificar**, deverá definir uma URL de webhook.
     - Da mesma forma, se você definir uma URL de webhook, deverá definir **notificação** como **ativado** no painel de limite de custo.
     - Você deve criar um webhook antes de inseri-lo aqui.  

       Para obter mais informações sobre WebHooks, consulte [criar um webhook ou uma função de API do Azure](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Exibir custo por recurso 
O recurso de tendência de custo mensal em laboratórios permite que você veja quanto você gastou no mês do calendário atual. Ele também mostra a projeção dos gastos até o fim do mês, com base em seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório estão atendendo aos limites no início, você pode usar o recurso de **custo por recurso** que mostra o custo acumulado do mês **por recurso** em uma tabela.

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços**e, em seguida, selecione **DevTest Labs** na lista.
3. Na lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **configuração e políticas** no menu à esquerda.
5. Selecione **custo por recurso** na seção **controle de custo** no menu à esquerda. Você verá os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esse recurso ajuda a identificar facilmente os recursos que custam mais para que você possa tomar medidas para reduzir os gastos com o laboratório. Por exemplo, o custo de uma VM é baseado no tamanho da VM. Quanto maior o tamanho da VM, mais é o custo. Você pode encontrar facilmente o tamanho de uma VM e o proprietário, para que você possa se comunicar com o proprietário da VM para entender por que esse tamanho de VM é necessário e se há uma chance de diminuir o tamanho.

A [política de desligamento automático](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) ajuda a reduzir o custo desligando VMs de laboratório em um determinado momento do dia. No entanto, um usuário de laboratório pode recusar a política de desligamento, o que aumenta o custo da execução da VM. Você pode selecionar uma VM na tabela para ver se ela foi recusada da política de desligamento automático. Se esse for o caso, você poderá se comunicar com o proprietário da VM para descobrir por que a VM foi recusada da política.
 
## <a name="next-steps"></a>Passos seguintes
Aqui estão algumas coisas a serem experimentadas a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) – saiba como definir as várias políticas usadas para controlar como seu laboratório e suas VMs são usados. 
* [Criar imagem personalizada](devtest-lab-create-template.md) -ao criar uma VM, você especifica uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada de um arquivo VHD.
* [Configurar imagens do Marketplace](devtest-lab-configure-marketplace-images.md) -o DevTest Labs dá suporte à criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais imagens do Azure Marketplace podem ser usadas ao criar VMs em um laboratório.
* [Criar uma VM em um laboratório](devtest-lab-add-vm.md) – ilustra como criar uma VM de uma imagem base (personalizada ou do Marketplace) e como trabalhar com artefatos em sua VM.

