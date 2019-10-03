---
title: Evitar custos inesperados e gerir faturação no Azure
description: Saiba como evitar despesas inesperadas na sua fatura do Azure. Utilize as funcionalidades de gestão e controlo de custos para uma subscrição do Azure.
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: d6c287d5ead0095a4f7bb5ad754212b134f7103c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719809"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitar custos inesperados com a faturação do Azure e a gestão de custos

Quando se inscreve no Azure, há várias coisas que pode fazer para ter uma ideia melhor dos seus gastos:

- A [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) pode fornecer uma estimativa dos custos antes de criar um recurso do Azure. 

- O [portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) fornece a discriminação de custos atuais e a previsão para a sua subscrição. 

- Se quiser agrupar e compreender os custos para diferentes projetos ou equipas, veja a [marcação de recursos](../azure-resource-manager/resource-group-using-tags.md). Se a sua organização tiver um sistema de relatórios que prefira utilizar, veja as [APIs de faturação](billing-usage-rate-card-overview.md).

- Se a sua subscrição foi criada num Contrato Enterprise (EA), pode ver os custos no portal do Azure. Se a sua subscrição foi através de um Fornecedor de Soluções na Cloud (CSP) ou do Azure Sponsorship, algumas das funcionalidades podem não se aplicar a si. Para obter mais informações, veja [Recursos adicionais para EA, CSP e Sponsorship](#other-offers).

- Se a sua subscrição for uma oferta de avaliação gratuita, o [Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), o Azure no Open (AIO) ou o BizSpark, a sua subscrição será desativada automaticamente quando forem utilizados todos os créditos. Saiba mais sobre os [limites de gastos](#spending-limit) para evitar que a sua subscrição seja desativada inesperadamente.

- Se se inscreveu para uma [conta gratuita do Azure](https://azure.microsoft.com/free/), [poderá utilizar alguns dos serviços mais populares do Azure gratuitamente por 12 meses](billing-create-free-services-included-free-account.md). Juntamente com as recomendações listadas abaixo, veja [Evitar que seja faturado pela sua conta gratuita](billing-avoid-charges-free-account.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

Veja algumas informações adicionais sobre como estimar os custos com as seguintes ferramentas:
- Calculadora de preços do Azure
- Portal do Azure
- Limite de gastos

As imagens nas secções a seguir mostram os preços do exemplo em dólares americanos.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar o custo online com a calculadora de preços

Veja a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter um custo mensal estimado do serviço no qual está interessado. Pode adicionar qualquer recurso do Azure de terceiros para obter um custo estimado. Na calculadora de preços, pode alterar o tipo de moeda.

![Captura de ecrã do menu da calculadora de preços](./media/billing-getting-started/pricing-calc.png)

Por exemplo, na calculadora de preços, estima-se que uma Máquina Virtual (VM) do Windows A1 custe um determinado montante/mês em horas de computação, se for deixada a executar o tempo todo:

![Captura de ecrã da calculadora de preços que mostra um custo estimado da VM Windows A1 por mês](./media/billing-getting-started/pricing-calcvm.png)

Para obter mais informações sobre preços, veja as [Perguntas mais frequentes sobre preços](https://azure.microsoft.com/pricing/faq/). Se quiser comunicar com um vendedor do Azure, ligue para o número de telefone mostrado na parte superior da página de perguntas mais frequentes.

### <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

Normalmente, quando adiciona um serviço no portal do Azure, há uma vista que mostra um custo estimado por mês na moeda de faturação. Por exemplo, ao escolher o tamanho da VM do Windows, verá o custo mensal estimado para as horas de computação:

![Exemplo: uma VM do Windows A1 a mostrar o custo estimado por mês](./media/billing-getting-started/vm-size-cost.png)

### <a name="spending-limit"></a> Verifique se tem um limite de gastos ativado

Se tiver uma subscrição que utiliza créditos, o limite de gastos está ativado para si, por predefinição. Desta forma, quando gasta todos os créditos, o seu cartão de crédito não é cobrado. Consulte a [lista completa de ofertas do Azure e a disponibilidade do limite de gastos](https://azure.microsoft.com/support/legal/offer-details/).

No entanto, quando atinge o limite de gastos, os serviços são desativados. A desativação significa que as VMs são desalojadas. Para evitar tempo de inatividade de serviço, tem de desativar o limite de gastos. Qualquer utilização excedida é cobrada no seu cartão de crédito registado.

Para ver se tem um limite de gastos ativado, aceda à [Vista de subscrições no Centro de Contas](https://account.windowsazure.com/Subscriptions). Será apresentada uma faixa se o limite de gastos estiver ativado, semelhante à seguinte:

![Captura de ecrã que mostra um aviso sobre o limite de gastos estar ativado no Centro de Contas](./media/billing-getting-started/spending-limit-banner.png)

Clique na faixa e siga as instruções para remover o limite de gastos. Se não introduziu as informações do cartão de crédito quando se inscreveu, é necessário introduzi-las para remover o limite de gastos. Para obter mais informações, veja [Limite de gastos do Azure - como funciona e como ativar ou removê-lo](https://azure.microsoft.com/pricing/spending-limits/).

## <a name="use-budgets-and-cost-alerts"></a>Utilizar orçamentos e alertas de custo

Pode criar [orçamentos](../cost-management/tutorial-acm-create-budgets.md) para gerir custos e criar [alertas](../cost-management/cost-mgt-alerts-monitor-usage-spending.md) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorizar custos ao utilizar os serviços do Azure
Pode monitorizar os custos com as seguintes ferramentas:

- Etiquetas
- Discriminação de custos e o burn rate
- Análise de custos

### <a name="tags"></a> Adicionar marcas a recursos para agrupar dados de faturação

Pode utilizar marcas para agrupar os dados de faturação para os serviços suportados. Por exemplo, se executar várias VMs para equipas diferentes, poderá utilizar marcas para categorizar os custos por centro de custo (por exemplo: RH, marketing, finanças, etc.) ou ambiente (por exemplo: produção, pré-produção, teste).

![Captura de ecrã que mostra a configuração das marcas no portal](./media/billing-getting-started/tags.png)

As marcas aparecem em diferentes vistas dos relatórios de custos. Por exemplo, são visíveis na sua [vista de análise de custos](#costs) de imediato e no ficheiro CSV de utilização detalhada após o primeiro período de faturação.

Para obter mais informações, veja [Utilizar etiquetas para organizar os recursos do Azure](../azure-resource-manager/resource-group-using-tags.md).

### <a name="costs"></a>Monitorizar discriminação de custos e burn rate

Depois de os serviços do Azure estarem em execução, verifique os custos regularmente. Pode ver os gastos atuais e o burn rate no portal do Azure.

1. Visite as [Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione uma subscrição.

2. Se for suportado para a sua subscrição, verá a discriminação de custos e o burn rate.

    ![Captura de ecrã do burn rate e da discriminação no portal do Azure](./media/billing-getting-started/burn-rate.PNG)

3. Clique em [Análise de custos](../cost-management/quick-acm-cost-analysis.md) na lista à esquerda para ver a discriminação dos custos por recurso. Depois de adicionar um serviço, espere 24 horas para que os dados sejam apresentados.

    ![Captura de ecrã da análise de custos no portal do Azure](./media/billing-getting-started/cost-analysis.png)

4. Pode filtrar por propriedades diferentes, como [marcas](#tags), tipo de recurso, grupo de recursos e período de tempo. Clique em **Aplicar** para confirmar os filtros e **Transferir** se pretender exportar a vista para um ficheiro de valores separados por vírgulas (. csv).

5. Além disso, pode clicar num recurso para ver o histórico dos gastos diários e quanto custa o recurso por cada dia.

    ![Captura de ecrã da vista do histórico de gastos no portal do Azure](./media/billing-getting-started/costhistory.png)

Compare os custos que vê com as estimativas que viu quando selecionou os serviços. Se os custos forem significativamente diferentes das estimativas, verifique o plano de preços que selecionou para os seus recursos.

## <a name="optimize-and-reduce-costs"></a>Otimizar e reduzir os custos
Se não estiver familiarizado com os princípios da gestão de custos, leia [Como otimizar o investimento em cloud com o Azure Cost Management](../cost-management/cost-mgt-best-practices.md).

No portal do Azure, também pode otimizar e reduzir os custos do Azure com o encerramento automático para VMs e as recomendações do Assistente.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere as funcionalidades de redução de custos como o encerramento automático para as VMs

Dependendo do seu cenário, pode configurar o encerramento automático para as VMs no portal do Azure. Para obter mais informações, veja [Encerramento automático para VMs com o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de ecrã da opção de encerramento automático no portal](./media/billing-getting-started/auto-shutdown.png)

Encerramento automático não é o mesmo que quando se encerra dentro da VM com opções de energia. O encerramento automático para e desaloja as VMs para evitar despesas adicionais de utilização. Para obter mais informações, veja as perguntas mais frequentes sobre preços para [VMs de Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs de Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) sobre estados das VMs.

Para obter mais funcionalidades de redução de custos para os ambientes de desenvolvimento e teste, veja [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Ative e reveja as recomendações do Assistente do Azure

O [Assistente do Azure](../advisor/advisor-overview.md) ajuda a reduzir custos ao identificar os recursos com baixa utilização. Visite o Assistente no portal do Azure:

![Captura de ecrã do botão do Assistente do Azure no portal do Azure](./media/billing-getting-started/advisor-button.png)

Pode obter recomendações acionáveis no separador **Custo** no painel do Assistente:

![Captura de ecrã do exemplo de recomendação de custos do Assistente](./media/billing-getting-started/advisor-action.png)

Reveja o tutorial [Otimizar custos a partir de recomendações](../cost-management/tutorial-acm-opt-recommendations.md) para um tutorial guiado sobre recomendações do Assistente para redução de custos.

## <a name="review-costs-against-your-latest-invoice"></a>Rever os custos em relação à fatura mais recente

No final do ciclo de faturação, estará disponível a fatura mais recente. Também pode [transferir faturas anteriores e ficheiros de utilização detalhada](billing-download-azure-invoice-daily-usage-date.md), para ter a certeza de que os custos estão corretos. Para obter mais informações sobre como comparar a utilização diária com a fatura, veja [Understand your bill for Microsoft Azure](billing-understand-your-bill.md) (Compreender a fatura do Microsoft Azure).

### <a name="billing-api"></a>API de Faturação

Utilize a API de faturação do Azure para obter dados de utilização programaticamente. Utilize a API RateCard e a API de Utilização em conjunto para obter a utilização faturada. Para obter mais informações, veja [Gain insights into your Microsoft Azure resource consumption](billing-usage-rate-card-overview.md) (Obter informações sobre o consumo de recursos do Microsoft Azure).

## <a name="other-offers"></a> Recursos adicionais e casos especiais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes de EA, CSP e Sponsorship
Contacte o seu gestor de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrato Enterprise (EA) | [Portal de EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fornecedor de Soluções Cloud (CSP) | Contacte o seu fornecedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver a gerir o departamento de TI de uma grande organização, é recomendável ler [Estrutura do Azure Enterprise](/azure/architecture/cloud-adoption-guide/subscription-governance) e o [documento técnico Enterprise IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (transferência de .pdf, apenas em inglês).

#### <a name="EA"></a> Vistas de custos do Contrato Enterprise no portal do Azure

As vistas de custos do Enterprise estão atualmente em Pré-visualização Pública. Itens a ter em atenção:

- Os custos da subscrição baseiam-se na utilização e não incluem valores pré-pagos, utilizações excedidas, quantidades incluídas, ajustes e impostos. As despesas reais são calculadas no nível da Inscrição.
- Os montantes mostrados no portal do Azure podem ser diferentes dos que estão no portal do Enterprise. As atualizações no portal do Enterprise podem levar alguns minutos até que as alterações sejam mostradas no portal do Azure.
- Se não estiver a ver os custos, tal pode ser por um dos seguintes motivos:
    - Não tem permissões no nível de subscrição. Para ver as vistas de custos do Enterprise, tem de ser Leitor de Faturação, Leitor, Contribuidor ou Proprietário ao nível da subscrição.
    - É Proprietário de Conta e o seu Administrador de Inscrição desativou a configuração "AO vista de despesas".  Entre em contacto com o Administrador de Inscrição para obter acesso aos custos.
    - É Administrador de Departamento e o seu Administrador de Inscrição desativou a configuração **DA vista de despesas**.  Entre em contacto com o Administrador de Inscrição para obter acesso.
    - Comprou o Azure através de um parceiro de canal e o parceiro não emite informações com os preços.  
- Se atualizar as configurações relativas ao acesso aos custos no portal do Enterprise, haverá um atraso de alguns minutos até que as alterações sejam mostradas no portal do Azure.
- O limite de gastos e as diretrizes das faturas não se aplicam às subscrições do EA.

### <a name="check-your-subscription-and-access"></a>Verificar a sua subscrição e o acesso

Para ver os custos, os utilizadores devem ter [acesso ao nível de subscrição das informações de faturação](billing-manage-access.md). Apenas o administrador da conta pode aceder ao [Centro de Contas](https://account.azure.com/Subscriptions), alterar as informações de faturação e gerir subscrições. O Administrador da Conta é a pessoa que passou pelo processo de inscrição. Para obter mais informações, veja [Adicionar ou alterar funções de administrador do Azure que gerem a subscrição ou os serviços](billing-add-change-azure-subscription-administrator.md).

Para ver se é o Administrador da conta, aceda a [Subscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Veja a lista de subscrições e localize **A minha função**. Se a função for *Administrador de conta*, terá privilégios totais. Se disser algo mais, como *Proprietário*, não tem privilégios totais.

![Captura de ecrã da sua função na vista de Subscrições no portal do Azure](./media/billing-getting-started/sub-blade-view.PNG)

Para gerir as subscrições e alterar as informações de faturação, [localize o Administrador da Conta](billing-subscription-transfer.md#whoisaa). Peça ao Administrador da Conta para concluir as tarefas ou [transferir a subscrição para si](billing-subscription-transfer.md).

Se o Administrador da conta não estiver mais na organização e precisar de gerir a faturação, [entre em contacto connosco](https://go.microsoft.com/fwlink/?linkid=2083458).


### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Pedir um crédito de Contrato de Nível de Serviço para um incidente de serviço

O Contrato de Nível de Serviço (SLA) descreve o compromisso da Microsoft quanto ao período de disponibilidade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure apresentam um problema que afeta o tempo de atividade ou a conectividade, geralmente conhecido como *interrupção*. Se não alcançarmos e mantivermos os níveis de serviço para cada serviço, conforme descrito no SLA, poderá estar elegível para um crédito em relação a uma parte dos seus honorários de serviço mensais.

Para pedir um crédito:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Se tiver várias contas, confirme que utiliza aquela que foi afetada pelo tempo de inatividade do Azure. 
2. Crie um pedido de suporte novo.
3. Em **Tipo de questão**, selecione **Faturação**.
4. Em **Tipo de problema**, selecione **Pedido de Reembolso**.
5. Adicionar detalhes para especificar que está a pedir um crédito de SLA, mencione a data/hora/fuso horário, bem como os serviços afetados (VMs, sites, etc.)
6. Verifique os detalhes de contacto e selecione **Criar** para enviar o seu pedido.

Os limites de SLA variam de acordo com o serviço. Por exemplo, o Escalão SQL Web tem um SLA de 99,9%, as VMs têm um SLA de 99,95% e o Escalão SQL Standard tem um SLA de 99,99%.

Para alguns serviços, existem pré-requisitos para que o SLA seja aplicado. Por exemplo, as máquinas virtuais têm de ter duas ou mais instâncias implementadas no mesmo Conjunto de Disponibilidade.

Para obter mais informações, veja [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) e a documentação [Resumo do SLA para os serviços do Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Contacte-nos.

Se tiver dúvidas ou precisar de ajuda, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como utilizar [limites de gastos](billing-spending-limit.md) para evitar os gastos em excesso.
- Comece a [analisar os seus custos do Azure](../cost-management/quick-acm-cost-analysis.md).
