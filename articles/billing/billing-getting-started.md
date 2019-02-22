---
title: Evitar custos inesperados, gerir a faturação no Azure | Documentos da Microsoft
description: Saiba como evitar encargos inesperados na sua fatura do Azure. Utilize as funcionalidades de gestão e controlo de custos para uma subscrição do Microsoft Azure.
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2018
ms.author: banders
ms.openlocfilehash: 0d1d4358789cce4d393143e8a243ee9a3e06bf36
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652404"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitar custos inesperados com a faturação do Azure e a gestão de custos

Quando se inscreve para o Azure, existem várias coisas que pode fazer para ter uma idéia melhor de seus gastos. O [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode fornecer uma estimativa dos custos antes de criar um recurso do Azure. O [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornece-lhe a análise detalhada do custo atual e a previsão para a sua subscrição. Se pretende agrupar e compreender os custos para diferentes projetos ou equipas, observe [identificação de recursos](../azure-resource-manager/resource-group-using-tags.md). Se sua organização tiver um sistema de relatórios que preferir usar, veja a [APIs de faturação](billing-usage-rate-card-overview.md).

- Se a sua subscrição for um Enterprise Agreement (EA), a pré-visualização pública para ver os custos no portal do Azure está disponível. Se a sua subscrição através do fornecedor de soluções Cloud (CSP) ou Azure Sponsorship, em seguida, alguns dos recursos seguintes podem não se aplicar a. Ver [recursos adicionais para o EA, o CSP e o patrocínio](#other-offers) para obter mais informações.

- Se a sua subscrição é uma versão de avaliação gratuita [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), o Azure no Open (AIO) ou ao BizSpark, a sua subscrição é automaticamente desativada quando todos os seus créditos. Saiba mais sobre [limites de gastos](#spending-limit) para evitar que a sua subscrição foi desativada inesperadamente.

- Se o ter iniciado sessão no [conta gratuita do Azure](https://azure.microsoft.com/free/), [pode usar alguns dos serviços mais populares do Azure gratuitamente durante 12 meses](billing-create-free-services-included-free-account.md). Juntamente com as recomendações apresentadas abaixo, veja [evitar ser cobrado gratuitamente conta](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obtenha custos estimados antes de adicionar serviços do Azure

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar custos online usando a Calculadora de preços

Veja a [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter um custo mensal estimado do serviço que está interessado. Pode adicionar qualquer parte primeiro recurso do Azure para obter um estimativa de custo.

![Captura de ecrã do menu de calculadora de preços](./media/billing-getting-started/pricing-calc.png)

Por exemplo, uma Máquina Virtual de Windows A1 (VM) estima-se custos 66.96 USD/mês em horas de computação se o deixar em execução o tempo todo:

![Captura de ecrã da Calculadora de preços que mostra que uma VM do Windows A1 estima-se custos 66.96 USD por mês](./media/billing-getting-started/pricing-calcVM.png)

Para obter mais informações sobre os preços, consulte esta [FAQ](https://azure.microsoft.com/pricing/faq/). Ou, se quiser falar com um vendedor do Azure, contacte o 1-800-867-1389.

### <a name="review-the-estimated-cost-in-the-azure-portal"></a>Reveja o custo estimado no portal do Azure

Normalmente, quando adicionar um serviço no portal do Azure, existe uma vista que mostra-lhe um custo estimado semelhante por mês. Por exemplo, ao escolher o tamanho da sua VM do Windows, consulte o custo mensal estimado as horas de computação:

![Exemplo: uma VM do Windows A1 estima-se custos 66.96 USD por mês](./media/billing-getting-started/vm-size-cost.PNG)

### <a name="spending-limit"></a> Verifique se tem um limite de gastos

Se tiver uma subscrição que utilize os créditos, em seguida, o limite de gastos está ativado para por padrão. Dessa forma, quando gastar os créditos, seu cartão de crédito não será cobrado. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

No entanto, se tiver atingido o limite de gastos, desativar os serviços. Isso significa que as VMs forem desalocadas. Para evitar períodos de indisponibilidade de serviço, tem de desativar o limite de gastos. Qualquer utilização excessiva é cobrada em seu cartão de crédito no ficheiro. 

Para ver se já tem de limite de gastos no, vá para o [subscrições ver no Centro de contas](https://account.windowsazure.com/Subscriptions). É apresentada uma faixa se o limite de gastos no:

![Captura de ecrã que mostra um aviso sobre gastos limite que está a ser no Centro de contas](./media/billing-getting-started/spending-limit-banner.PNG)

Clique na faixa e siga as instruções para remover o limite de gastos. Se não introduzir informações de cartão de crédito quando se inscreveu, insira-o para remover o limite de gastos. Para obter mais informações, consulte [limite de gastos do Azure – como ele funciona e como ativar ou removê-lo](https://azure.microsoft.com/pricing/spending-limits/).

Pode utilizar o [Cloudyn](https://www.cloudyn.com/) serviço para criar alertas que o notificam automaticamente os participantes de anomalias de gastos e riscos de ultrapassar o limite. Pode criar alertas através de relatórios que os alertas de suporte com base no orçamento e limiares de custo. Para obter mais informações sobre como utilizar o Cloudyn, consulte [Tutorial: Rever a utilização e custos](../cost-management/tutorial-review-usage.md).

Este exemplo utiliza a **custo Efetivo ao longo do tempo** relatório para enviar uma notificação quando seus gastos na VM do Azure está prestes a seu orçamento total. Neste cenário, tem um orçamento total de US $20.000 e pretender receber uma notificação quando os custos estão prestes a atingir metade do seu orçamento, US $9,000 e um alerta adicional quando os custos de atingir US $10.000.

1. No menu na parte superior do portal do Cloudyn, selecione **custos** > **análise de custo** > **custo Efetivo ao longo do tempo**. 
2. Defina **Grupos** como **Serviços** e defina **Filtrar por serviço** como **Azure/VM**. 
3. No canto superior direito do relatório, selecione **ações** e, em seguida, selecione **agendar relatório**.
4. Para enviar si próprio um e-mail do relatório no intervalo agendado, selecione o **agendamento** separador a **guardar ou agendar esta** caixa de diálogo do relatório. Lembre-se de selecionar **Enviar por e-mail**. Todas as marcas de agrupamento e filtragem que uso são incluídos no relatório enviado por e-mail. 
5. Selecione o **limiar** separador e, em seguida, selecione **custo Efetivo vs. Limiar**. 
   1. Na **alerta vermelho** caixa limiar introduza 10000. 
   2. Na **alerta amarelo** caixa limiar introduza 9000. 
   3. Na **número de alertas consecutivos** , introduza o número de alertas consecutivos para receber. Quando receber o número total de alertas que especificou, não são enviados alertas adicionais. 
6. Selecione **Guardar**.

    ![Exemplo que mostra alertas amarelas e vermelhas, com base nos limites de gastos](./media/billing-getting-started/schedule-alert01.png)

Também pode escolher o **percentagem de custos vs. Orçamento** métrica de limiar para criar alertas. Isto permite-lhe especificar os limiares como percentagens de seu orçamento em vez de valores de moeda.

## <a name="ways-to-monitor-your-costs-when-using-azure-services"></a>Formas de monitorizar os seus custos ao utilizar os serviços do Azure

### <a name="tags"></a> Adicionar etiquetas aos seus recursos para agrupar os dados de faturas

Pode utilizar etiquetas para dados de faturação de grupo para os serviços suportados. Por exemplo, se executar várias VMs para as equipes diferentes, pode utilizar etiquetas para categorizar os custos pelo centro de custos (RH, Finanças, de marketing) ou o ambiente (teste de pré-produção, de produção,). 

![Captura de ecrã que mostra como configurar etiquetas no portal](./media/billing-getting-started/tags.PNG)

As etiquetas aparecem em todo o custo de diferente modos de exibição de relatórios. Por exemplo, eles são visíveis no seu [vista de análise de custos](#costs) imediato e. csv de utilização em pormenor após o primeiro período de faturação.

Para obter mais informações, consulte [utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a> Verifique o portal para análise detalhada do custo e gravar a taxa de regularmente

Depois de obter seus serviços em execução, regularmente verificar quanto eles estão a causar custos. Pode ver os gastos atuais e gravar taxa no portal do Azure.

1. Visite o [subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

2. Caso seja suportado para a sua subscrição, veja a análise detalhada do custo e gravar a taxa.

    ![Captura de ecrã da taxa de grave e divisão no portal do Azure](./media/billing-getting-started/burn-rate.PNG)

3. Clique em **análise de custo** na lista à esquerda para ver a análise detalhada do custo por recurso. Aguarde 24 horas depois de adicionar um serviço para os dados preencher.

    ![Captura de ecrã da vista de análise de custo no portal do Azure](./media/billing-getting-started/cost-analysis.PNG)

4. Pode filtrar por propriedades diferentes, como [etiquetas](#tags), tipo de recurso, o grupo de recursos e o período de tempo. Clique em **aplicar** para confirmar os filtros e **transferir** se pretender exportar o modo de exibição para um ficheiro de valores de comma-separated (. csv).

5. Além disso, pode clicar num recurso para ver seu diariamente gastar o histórico e quanto o recurso tem um custo por dia.

    ![Captura de ecrã da vista de histórico de gastos no portal do Azure](./media/billing-getting-started/costhistory.PNG)

Recomendamos que verifique os custos que vir com as estimativas que viu quando tiver selecionado os serviços. Se os custos estão totalmente diferentes em relação às estimativas, verifique novamente o plano de preços que selecionou para os seus recursos.

### <a name="consider-enabling-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere ativar funcionalidades de custos, como o encerramento automático para VMs

Dependendo do seu cenário, pode configurar o encerramento automático para as VMs no portal do Azure. Para obter mais informações, consulte [encerramento automático para VMs com o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de ecrã da opção de encerramento automático no portal](./media/billing-getting-started/auto-shutdown.PNG)

Encerramento automático não é igual a quando desligar dentro da VM com as opções de energia. Encerramento automático para e desaloca a suas VMs para parar os custos de utilização adicionais. Para obter mais informações, veja a FAQ de preços [VMs do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows VMs](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) sobre Estados VM.

Para obter mais recursos de custos para os seus ambientes de desenvolvimento e teste, confira [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-check-out-azure-advisor-recommendations"></a>Ativar e veja as recomendações do Assistente do Azure

[O Assistente do Azure](../advisor/advisor-overview.md) é uma funcionalidade que o ajuda a reduzir os custos ao identificar recursos com baixa utilização. Visite o assistente no portal do Azure:

![Botão de captura de ecrã do Assistente do Azure no portal do Azure](./media/billing-getting-started/advisor-button.PNG)

Em seguida, pode obter recomendações acionáveis no **custo** separador no dashboard do Advisor:

![Exemplo de recomendação de custo de captura de ecrã do Advisor](./media/billing-getting-started/advisor-action.PNG)

Para obter mais informações, consulte [recomendações de custos do Advisor](../advisor/advisor-cost-recommendations.md).

## <a name="reviewing-costs-at-the-end-of-your-billing-cycle"></a>Rever os custos ao final do seu ciclo de faturação

Após o fim do seu ciclo de faturação, a fatura irá tornar-se disponível. Também pode [transferir anteriores faturas e ficheiros de utilização em pormenor](billing-download-azure-invoice-daily-usage-date.md) para se certificar de que foram bem cobrados corretamente. Para obter mais informações sobre como comparar a sua utilização diária com a sua fatura, consulte [compreender a sua fatura do Microsoft Azure](billing-understand-your-bill.md).

### <a name="billing-api"></a>API de Faturação

Utilize a nossa API de faturação para obter programaticamente os dados de utilização. Utilize a API de RateCard e a API de utilização em conjunto para obter a sua utilização faturada. Para obter mais informações, consulte [obter informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md).

## <a name="other-offers"></a> Recursos adicionais e casos especiais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes com contrato EA, o CSP e o patrocínio
Fale com o seu Gestor de conta ou o parceiro do Azure para começar a utilizar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrato Enterprise (EA) | [Portal EA](https://ea.azure.com/), [ajudar docs](https://ea.azure.com/helpdocs), e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fornecedor de Soluções Cloud (CSP) | Comunicar com o seu fornecedor |
| Azure Sponsorship | [Portal de patrocínio](https://www.microsoftazuresponsorships.com/) |

Se estiver a gerir IT para uma grande organização, recomendamos que leia [estrutura empresarial do Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) e o [enterprise TI white paper](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (. pdf download, apenas em inglês).

#### <a name="EA"></a> Modos de exibição no portal do Azure de custos do contrato Enterprise da pré-visualização 

Vistas de custo de Enterprise estão atualmente em pré-visualização pública. Itens a ter em conta:

- Os custos de subscrição são com base na utilização e não incluem valores pré-pagos, utilizações excedidas, quantidades incluídas, ajustes e impostos. Os custos reais são calculados ao nível da inscrição.
- Montantes mostrados no portal do Azure podem ser diferentes do que está no portal da empresa. As atualizações no portal da empresa poderão demorar alguns minutos antes das alterações são mostradas no portal do Azure.
- Se não forem os custos, poderá ser de um dos seguintes motivos:
    - Não tem permissões ao nível da subscrição. Para ver as vistas de custo de empresa, tem de ser um leitor de faturação, leitor, Contribuidor ou proprietário ao nível da subscrição.
    - É um proprietário de conta e o administrador de inscrição desativou as "AO modo de exibição cobranças" definição.  Contacte o administrador de inscrição para obter acesso aos custos. 
    - É administrador de departamento e o administrador de inscrição desativou as "DA vista cobranças" definição.  Contacte o administrador de inscrição para obter acesso.
    - Comprou o Azure através de um parceiro de canal e o parceiro não libertar informações de preços.  
- Se atualizar definições relacionadas com os custos de acesso no portal da empresa, existe um atraso de alguns minutos antes das alterações são mostradas no portal do Azure.
- Limite de gastos e orientações de nota fiscal não se aplicam às subscrições do EA.

### <a name="check-your-subscription-and-access"></a>Verifique a sua subscrição e acesso

Para ver os custos, tem de ter [acesso no nível de subscrições para informações de faturação](billing-manage-access.md). Apenas o administrador de conta pode aceder a [Centro de contas](https://account.azure.com/Subscriptions), alterar as informações de faturas e gerir as subscrições. O administrador de conta é a pessoa que correu o processo de inscrição. Para obter mais informações, consulte [adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou os serviços](billing-add-change-azure-subscription-administrator.md).

Para ver se é o administrador de conta, aceda à [subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Ver a lista de subscrições que tem acesso. Procure em **a minha função**. Se disser *administrador de conta*, em seguida, está ok. Se disser algo como *proprietário*, em seguida, não tiver todos os privilégios.

![Captura de ecrã da sua função na vista de subscrições no portal do Azure](./media/billing-getting-started/sub-blade-view.PNG)

Se não for o administrador de conta, alguém provavelmente lhe forneceu acesso parcial usando [controlo de acesso baseado em função de diretório do Azure Active Directory](../role-based-access-control/role-assignments-portal.md) (RBAC). Para gerir subscrições e faturação informações, de alterações [encontrar o administrador de conta](billing-subscription-transfer.md#whoisaa). Peça ao administrador de conta para realizar as tarefas ou [transferir a subscrição para si](billing-subscription-transfer.md).

Se o seu administrador de conta não se encontra com a sua organização e tem de gerir a faturação, [contacte-nos](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver alguma dúvida ou precisar de ajuda, [criar um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
