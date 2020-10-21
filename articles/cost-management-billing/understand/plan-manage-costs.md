---
title: Planear a gestão dos custos do Azure
description: Saiba como planear a gestão dos custos do Azure e a utilizar as funcionalidades de monitorização e gestão da sua conta do Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: 50180984423b7c6d2921234a3502495016c4df5c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150104"
---
# <a name="plan-to-manage-azure-costs"></a>Planear a gestão dos custos do Azure

Este artigo mostra como começar a planear a gestão dos custos do Azure. Quando se inscreve no Azure, há várias coisas que pode fazer para ter uma ideia melhor dos seus gastos:

- Obtenha custos estimados antes de adicionar serviços com a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/), a folha de preços do Azure ou ao adicionar serviços no portal do Azure.
- Monitorize os custos com [orçamentos](../costs/tutorial-acm-create-budgets.md), [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) e a [análise de custos](../costs/quick-acm-cost-analysis.md).
- Reveja os custos na sua fatura comparando-os com os [ficheiros de utilização detalhada](../manage/download-azure-invoice-daily-usage-date.md).
- Integre os dados da faturação e dos custos com o seu próprio sistema de relatórios com a utilização de APIs de [faturação](/rest/api/billing/) e [consumo](/rest/api/consumption/).
- Utilize funcionalidades e ferramentas adicionais para clientes do Contrato Enterprise (EA), do Fornecedor de Soluções Cloud (CSP) e do Azure Sponsorship.
- Tire partido de [alguns dos serviços mais populares do Azure gratuitamente por 12 meses](../manage/create-free-services.md) disponíveis com a [conta gratuita do Azure](https://azure.microsoft.com/free/). Juntamente com as recomendações listadas abaixo, veja [Evitar que seja faturado pela sua conta gratuita](../manage/avoid-charges-free-account.md).

Se precisar de cancelar a sua subscrição do Azure, veja [Cancelar a sua subscrição do Azure](../manage/cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

Utilize uma das seguintes ferramentas para calcular o custo da utilização de um serviço do Azure:
- Calculadora de preços do Azure
- Folha de preços do Azure
- Portal do Azure

As imagens nas secções a seguir mostram os preços do exemplo em dólares americanos. Os preços mencionados são meramente para fins de exemplo. Não se destinam a sugerir custos reais. 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar o custo online com a calculadora de preços

Veja a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter um custo mensal estimado do serviço que pretende adicionar. Pode alterar a moeda para obter a estimativa na moeda local.

![Captura de ecrã do menu da calculadora de preços](./media/plan-manage-costs/pricing-calc.png)

Pode visualizar o custo estimado para qualquer serviço original do Azure. Por exemplo, na captura de ecrã abaixo, estima-se que uma Máquina Virtual (VM) do Windows A1 custe 66,96 $ por mês em horas de computação, se for deixada a executar o tempo todo:

![Captura de ecrã da calculadora de preços que mostra um custo estimado da VM Windows A1 por mês](./media/plan-manage-costs/pricing-calc-vm.png)

Os preços mencionados são meramente para fins de exemplo. Não se destinam a sugerir custos reais.

Para obter mais informações sobre preços, veja as [Perguntas mais frequentes sobre preços](https://azure.microsoft.com/pricing/faq/). Se quiser comunicar com um vendedor do Azure, ligue para o número de telefone mostrado na parte superior da página de perguntas mais frequentes.

### <a name="review-prices"></a>Rever os preços

Se tiver acesso ao Azure através de um Contrato Enterprise (EA) ou um Contrato de Cliente da Microsoft (MCA), poderá ver e transferir a folha de preços para a sua conta do Azure. A folha de preços é um ficheiro do Excel que contém preços para todos os serviços do Azure. Para obter mais informações, veja [Ver e transferir os seus preços do Azure](../manage/ea-pricing.md).

Relativamente a outros tipos de subscrições, pode obter preços de retalho padrão com a [API Azure Retail Prices](/rest/api/cost-management/retail-prices/azure-retail-prices).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

Pode ver o custo estimado por mês ao adicionar um serviço no portal do Azure. Por exemplo, ao escolher o tamanho da VM do Windows, verá o custo mensal estimado para as horas de computação:

![Exemplo: uma VM do Windows A1 a mostrar o custo estimado por mês](./media/plan-manage-costs/vm-size-cost.png)

Os preços mencionados são meramente para fins de exemplo. Não se destinam a sugerir custos reais.

## <a name="monitor-costs-when-using-azure-services"></a>Monitorizar custos ao utilizar os serviços do Azure
Pode monitorizar os custos com as seguintes ferramentas:

- Orçamento e alertas de custo
- Análise de custos

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Controlar custos com orçamentos e alertas de custo

Crie [orçamentos](../costs/tutorial-acm-create-budgets.md) para gerir custos e criar [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) que notifiquem automaticamente a si e às partes interessadas das anomalias nos gastos e dos gastos excessivos invulgares.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Explorar e analisar os custos com a análise de custos

Depois de os serviços do Azure estarem em execução, verifique os custos regularmente para controlar os seus gastos no Azure. Pode utilizar a análise de custos para compreender qual é a origem dos custos da sua utilização do Azure.

Aceda à [página de Gestão de Custos + Faturação no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

Clique em **Análise de custos** no lado esquerdo do ecrã para ver o custo atual dividido por várias tabelas dinâmicas, como serviço, localização e subscrição. Depois de adicionar um serviço ou fazer uma compra, espere 24 horas para que os dados sejam apresentados. Por predefinição, a análise de custos mostra o custo para o âmbito onde está. Por exemplo, na captura de ecrã abaixo, é apresentado o custo da conta de faturação da Contoso. Utilize o atalho Âmbito para alternar para um âmbito diferente na análise de custos. Para obter mais informações sobre âmbitos, veja [Compreender e trabalhar com âmbitos](../costs/understand-work-scopes.md#scopes)

![Captura de ecrã da análise de custos no portal do Azure](./media/plan-manage-costs/cost-analysis.png)

Pode filtrar por várias propriedades, como, por exemplo, os rótulos, o tipo de recurso e o intervalo de tempo. Clique em **Adicionar filtro** para adicionar o filtro para uma propriedade e selecione os valores a serem filtrados. Selecione **Exportar** para exportar a vista para um ficheiro de valores separados por vírgulas (. csv).

Além disso, pode clicar nos rótulos do gráfico para ver o histórico de gastos diários para esse rótulo. Por exemplo, na captura de ecrã abaixo, a seleção de uma máquina virtual apresenta o custo diário da execução das suas VMs.

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="Captura de ecrã da vista do histórico de gastos no portal do Azure" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>Otimizar e reduzir os custos

Se não estiver familiarizado com os princípios da gestão de custos, leia [Como otimizar o investimento em cloud com o Azure Cost Management](../costs/cost-mgt-best-practices.md).

No portal do Azure, também pode otimizar e reduzir os custos do Azure com o encerramento automático para VMs e as recomendações do Assistente.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere as funcionalidades de redução de custos como o encerramento automático para as VMs

Dependendo do seu cenário, pode configurar o encerramento automático para as VMs no portal do Azure. Para obter mais informações, veja [Encerramento automático para VMs com o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de ecrã da opção de encerramento automático no portal](./media/plan-manage-costs/auto-shutdown.png)

Encerramento automático não é o mesmo que quando se encerra dentro da VM com opções de energia. O encerramento automático para e desaloja as VMs para evitar despesas adicionais de utilização. Para obter mais informações, veja as perguntas mais frequentes sobre preços para [VMs de Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs de Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) sobre estados das VMs.

Para obter mais funcionalidades de redução de custos para os ambientes de desenvolvimento e teste, veja [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Ative e reveja as recomendações do Assistente do Azure

O [Assistente do Azure](../../advisor/advisor-overview.md) ajuda a reduzir custos ao identificar os recursos com baixa utilização. Procure o **Assistente** no portal do Azure:

![Captura de ecrã do botão do Assistente do Azure no portal do Azure](./media/plan-manage-costs/advisor-button.png)

Selecione **Custo** no lado esquerdo. Irá ver recomendações acionáveis no separador **Custo**:

![Captura de ecrã do exemplo de recomendação de custos do Assistente](./media/plan-manage-costs/advisor-action.png)

Os preços mencionados são meramente para fins de exemplo. Não se destinam a sugerir custos reais.

Reveja o tutorial [Otimizar custos a partir de recomendações](../costs/tutorial-acm-opt-recommendations.md) para um tutorial guiado sobre recomendações do Assistente para redução de custos.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integrar com APIs de faturação e consumo

Utilize as APIs de [faturação](/rest/api/billing/) e [consumo](/rest/api/consumption/) do Azure para obter, através de programação, os dados de faturação e de custos. Utilize a API RateCard e a API de Utilização em conjunto para obter a utilização faturada. Para obter mais informações, veja [Gain insights into your Microsoft Azure resource consumption](../manage/usage-rate-card-overview.md) (Obter informações sobre o consumo de recursos do Microsoft Azure).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Recursos adicionais e casos especiais

### <a name="ea-csp-and-sponsorship-customers"></a>Clientes de EA, CSP e Sponsorship
Contacte o seu gestor de conta ou parceiro do Azure para começar.

| Oferta | Recursos |
|-------------------------------|-----------------------------------------------------------------------------------|
| Contrato Enterprise (EA) | [Portal de EA](https://ea.azure.com/), [documentos de ajuda](https://ea.azure.com/helpdocs) e [relatório do Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Fornecedor de Soluções Cloud (CSP) | Contacte o seu fornecedor |
| Azure Sponsorship | [Portal do Sponsorship](https://www.microsoftazuresponsorships.com/) |

Se estiver a gerir o departamento de TI de uma grande organização, é recomendável ler [Estrutura do Azure Enterprise](/azure/architecture/cloud-adoption-guide/subscription-governance) e o [documento técnico Enterprise IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (transferência de .pdf, apenas em inglês).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Vistas de custos do Contrato Enterprise no portal do Azure

As vistas de custos do Enterprise estão atualmente em Pré-visualização Pública. Itens a ter em atenção:

- Os custos da subscrição baseiam-se na utilização e não incluem valores pré-pagos, utilizações excedidas, quantidades incluídas, ajustes e impostos. As despesas reais são calculadas no nível da Inscrição.
- Os montantes mostrados no portal do Azure podem ser diferentes dos que estão no portal do Enterprise. As atualizações no portal do Enterprise podem levar alguns minutos até que as alterações sejam mostradas no portal do Azure.
- Se não estiver a ver os custos, tal pode ser por um dos seguintes motivos:
    - Não tem permissões no nível de subscrição. Para ver as vistas de custos do Enterprise, tem de ser Leitor de Faturação, Leitor, Contribuidor ou Proprietário ao nível da subscrição.
    - É Proprietário de Conta e o seu Administrador de Inscrição desativou a configuração "AO vista de despesas".  Entre em contacto com o Administrador de Inscrição para obter acesso aos custos.
    - É Administrador de Departamento e o seu Administrador de Inscrição desativou a configuração **DA vista de despesas**.  Entre em contacto com o Administrador de Inscrição para obter acesso.
    - Comprou o Azure através de um parceiro de canal e o parceiro não emite informações com os preços.  
- Se atualizar as configurações relativas aos custos, no acesso no portal do Enterprise, haverá um atraso de alguns minutos até que as alterações sejam mostradas no portal do Azure.
- O limite de gastos e as diretrizes das faturas não se aplicam às subscrições do EA.

### <a name="check-your-subscription-and-access"></a>Verificar a sua subscrição e o acesso

Para visualizar os custos, precisa de acesso de nível de conta ou de subscrição para as informações de custos ou faturação. O acesso varia consoante o tipo de conta de faturação. Para saber mais sobre as contas de faturação e verificar o tipo da sua conta de faturação, veja [Ver as contas de faturação no portal do Azure](../manage/view-all-accounts.md).

Se tiver acesso ao Azure através de uma conta de faturação do Programa do Serviço Online da Microsoft (MOSP), veja [Gerir o acesso às informações de faturação do Azure](../manage/manage-billing-access.md).

Se tiver acesso ao Azure através de uma conta de faturação do Contrato Enterprise (EA), veja [Compreender as funções administrativas do Contrato Enterprise do Azure](../manage/understand-ea-roles.md).

Se tiver acesso ao Azure através de uma conta de faturação do Contrato de Cliente da Microsoft (MCA), veja [Compreender as funções administrativas do Contrato Enterprise do Azure](../manage/understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Pedir um crédito de Contrato de Nível de Serviço para um incidente de serviço

O Contrato de Nível de Serviço (SLA) descreve o compromisso da Microsoft quanto ao período de disponibilidade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure apresentam um problema que afeta o tempo de atividade ou a conectividade, geralmente conhecido como *interrupção*. Se não alcançarmos e mantivermos os níveis de serviço para cada serviço, conforme descrito no SLA, poderá estar elegível para um crédito em relação a uma parte dos seus honorários de serviço mensais.

Para pedir um crédito:

Inicie sessão no [portal do Azure](https://portal.azure.com/). Se tiver várias contas, confirme que utiliza aquela que foi afetada pelo tempo de inatividade do Azure. Em seguida, crie um pedido de suporte novo.

Em **Tipo de problema**, selecione **Faturação** e, em **Tipo do problema**, selecione **Pedido de Reembolso**.

Adicione detalhes para especificar que está a pedir um crédito de SLA, mencione a data/hora/fuso horário, bem como os serviços afetados (VMs, sites, etc.).

Por fim, verifique os detalhes de contacto e selecione **Criar** para submeter o seu pedido.

Para alguns serviços, existem pré-requisitos para que o SLA seja aplicado. Por exemplo, as máquinas virtuais têm de ter duas ou mais instâncias implementadas no mesmo Conjunto de Disponibilidade.

Para obter mais informações, veja [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) e a documentação [Resumo do SLA para os serviços do Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como utilizar [limites de gastos](../manage/spending-limit.md) para evitar os gastos em excesso.
- Comece a [analisar os seus custos do Azure](../costs/quick-acm-cost-analysis.md).