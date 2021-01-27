---
title: Veja a tendência mensal estimada de custos de laboratório na Azure DevTest Labs
description: Este artigo fornece informações sobre como rastrear o custo do seu laboratório (tabela mensal estimada de tendência de custos) em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6a2a9bef9e54ef7deda123aad34cf0c576fd158f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98892342"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Custos de pista associados a um laboratório em Azure DevTest Labs
Este artigo fornece informações sobre como rastrear o custo do seu laboratório. Mostra como ver a tendência de custos estimada para o mês civil em curso para o laboratório. O artigo também mostra como visualizar o custo mensal por recurso no laboratório.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Veja a tendência mensal estimada de custos do laboratório 
Nesta secção, aprende-se a utilizar o gráfico **mensal de tendência de custos estimados** para visualizar o custo estimado do mês civil em vigor e o custo de fim de mês projetado para o mês civil em curso. Também aprende a gerir os custos do laboratório, definindo metas de gastos e limiares que, quando alcançados, desencadeiam a DevTest Labs para lhe reportar os resultados.

Para ver o gráfico mensal de tendências de custos estimados, siga estes passos: 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o seu laboratório.  
4. Selecione **Configuração e políticas** no menu à esquerda.  
4. Selecione **a tendência custo** na secção de rastreio de **custos** no menu esquerdo. A imagem que se segue mostra um exemplo de um gráfico de custos. 
   
    ![Gráfico de custos](./media/devtest-lab-configure-cost-management/graph.png)

    O valor **estimado do custo** é o custo estimado do mês em curso. O **custo projetado** é o custo estimado para todo o mês civil em curso, calculado usando o custo do laboratório para os cinco dias anteriores.

    Os custos são arredondados para o próximo número inteiro. Por exemplo: 

   * 5.01 rounds até 6 
   * 5.50 rounds até 6
   * 5.99 arredondas até 6

     Como diz acima do gráfico, os custos que vê por padrão no gráfico são custos *estimados* usando as tarifas de oferta [Pay-As-You-Go.](https://azure.microsoft.com/offers/ms-azr-0003p/) Também pode definir os seus próprios alvos de gastos que são apresentados nas [tabelas, gerindo os alvos de custos do seu laboratório.](#managing-cost-targets-for-your-lab)

     Os seguintes custos *não* estão incluídos no cálculo dos custos:

   * As subscrições da CSP e da Dreamspark não são atualmente suportadas, uma vez que a Azure DevTest Labs utiliza as APIs de faturação Azure para calcular o custo do laboratório, que não suporta subscrições de CSP ou Dreamspark.
   * As suas ofertas. Atualmente, não é possível utilizar as tarifas de oferta (mostradas na sua subscrição) que negociou com parceiros da Microsoft ou da Microsoft. Apenas são utilizadas tarifas Pay-As-You-Go.
   * Seus impostos
   * Os seus descontos
   * Sua moeda de faturação. Atualmente, o custo do laboratório é apresentado apenas em moeda USD.

### <a name="managing-cost-targets-for-your-lab"></a>Gerir alvos de custos para o seu laboratório
A DevTest Labs permite-lhe gerir melhor os custos do seu laboratório, definindo um alvo de gastos que pode ver na tabela mensal de tendências de custos estimados. A DevTest Labs também pode enviar-lhe uma notificação quando o limite ou o limiar de destino especificado for atingido. 

1. Na página de **tendência Custos,** **selecione Gerir o alvo**.

    ![Gerir o botão alvo](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na **página-alvo** do Manage, especifique um alvo de gastos e limiares. Também pode definir se cada limiar selecionado é reportado no gráfico de tendência de custos ou através de uma notificação webhook.

    ![Gerir o painel de alvos](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Selecione um período de tempo durante o qual deseja que os alvos de custos sejam rastreados.
      - **Mensalmente:** as metas de custos são rastreadas por mês.
      - **Fixo:** os objetivos de custos são rastreados para o intervalo de datas que especifica nas datas de início e fim. Normalmente, estes valores representam o tempo que o seu projeto está programado para ser executado.
   - Especificar um **custo-alvo.** Por exemplo, quanto planeia gastar neste laboratório no período de tempo que definiu.
   - Selecione para ativar ou desativar qualquer limiar que pretenda reportado – em incrementos de 25% – até 125% do custo do **Target** especificado.
      - **Notifique:** Quando este limiar for atingido, é notificado por um URL webhook que especifica.
      - **Gráfico na tabela**: Quando este limiar é cumprido, os resultados são traçados no gráfico de tendência de custos que pode ver, como descrito na tabela de Tendências de Custos Estimadas Mensais.
   - Se optar por **notificar** quando o limiar for atingido, deve especificar um URL webhook. Na área de integrações de Custos, **selecione Clique aqui para adicionar uma integração**. Introduza um **URL webhook** no painel de notificação configurar e, em seguida, selecione **OK**.

       ![Painel de notificação de configuração](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Se especificar **Notificação,** tem de definir um URL webhook.
     - Da mesma forma, se definir um URL webhook, deve definir **a Notificação** para **On no** painel de limiar de custo.
     - Tem de criar um webhook antes de o introduzir aqui.  

       Para obter mais informações sobre webhooks, consulte [Criar um webhook ou a API Azure Function](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Ver custo por recurso 
A funcionalidade de tendência mensal de custos em laboratório permite-lhe ver quanto gastou no mês civil em curso. Mostra também a projeção dos gastos até ao final do mês, com base nos seus gastos nos últimos sete dias. Para ajudá-lo a entender por que os gastos no laboratório estão a cumprir os limiares desde o início, você pode usar o **custo por** recurso que mostra o custo mensal **por recurso** em uma tabela.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Selecione **Todos os Serviços** e, em seguida, selecione **DevTest Labs** da lista.
3. Da lista de laboratórios, selecione o laboratório desejado.  
4. Selecione **Configuração e políticas** no menu à esquerda.
5. Selecione **Custo por recurso** na secção de rastreio de **custos** no menu esquerdo. Vê os custos associados a cada recurso associado a um laboratório. 

    ![Custo por recurso](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Esta funcionalidade ajuda-o a identificar facilmente os recursos que mais custam para que possa tomar medidas para reduzir os gastos do laboratório. Por exemplo, o custo de um VM baseia-se no tamanho do VM. Quanto maior for o tamanho do VM, mais é o custo. Você pode facilmente encontrar o tamanho de um VM e do proprietário, para que você possa falar com o proprietário VM para entender por que esse tamanho VM é necessário e se há uma chance de baixar o tamanho.

[A política de paragem automática ajuda-o](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) a reduzir o custo desligando os VMs de laboratório numa determinada hora do dia. No entanto, um utilizador de laboratório pode optar por não fazer a política de encerramento, o que aumenta o custo de funcionamento do VM. Pode selecionar um VM na tabela para ver se foi excluído da política de paragem automática. Se for esse o caso, pode falar com o proprietário da VM para descobrir porque é que o VM foi excluído da apólice.
 
## <a name="next-steps"></a>Próximos passos
Aqui estão algumas coisas a seguir:

* [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) - Aprenda a definir as várias políticas usadas para governar a forma como o seu laboratório e os seus VMs são usados. 
* [Crie uma imagem personalizada](devtest-lab-create-template.md) - Quando criar um VM, especifique uma base, que pode ser uma imagem personalizada ou uma imagem do Marketplace. Este artigo ilustra como criar uma imagem personalizada a partir de um ficheiro VHD.
* [Configure imagens marketplace](devtest-lab-configure-marketplace-images.md) - A DevTest Labs suporta a criação de VMs com base em imagens do Azure Marketplace. Este artigo ilustra como especificar quais, se houver, imagens do Azure Marketplace podem ser usadas ao criar VMs em laboratório.
* [Criar um VM em laboratório](devtest-lab-add-vm.md) - Ilustra como criar um VM a partir de uma imagem base (personalizada ou marketplace), e como trabalhar com artefactos no seu VM.
