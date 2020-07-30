---
title: Evitar e analisar custos inesperados com o Azure Cost Management e a faturação
description: Saiba como evitar custos inesperados na sua fatura do Azure e aprenda a utilizar as funcionalidades de gestão e monitorização dos custos da sua conta do Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 1b63b0316b8314e4c5107ddd3f1d22c1d59cbb08
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281808"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Evitar custos inesperados com a faturação do Azure e a gestão de custos

Quando se inscreve no Azure, há várias coisas que pode fazer para ter uma ideia melhor dos seus gastos:

- Obtenha custos estimados antes de adicionar serviços com a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/), a folha de preços do Azure ou ao adicionar serviços no portal do Azure.
- Monitorize os custos com [orçamentos](../costs/tutorial-acm-create-budgets.md), [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) e a [análise de custos](../costs/quick-acm-cost-analysis.md).
- Reveja os custos na sua fatura comparando-os com os [ficheiros de utilização detalhada](download-azure-invoice-daily-usage-date.md).
- Integre os dados da faturação e dos custos com o seu próprio sistema de relatórios com a utilização de APIs de [faturação](https://docs.microsoft.com/rest/api/billing/) e [consumo](https://docs.microsoft.com/rest/api/consumption/).
- Utilize funcionalidades e ferramentas adicionais para clientes do Contrato Enterprise (EA), do Fornecedor de Soluções Cloud (CSP) e do Azure Sponsorship.
- Tire partido de [alguns dos serviços mais populares do Azure gratuitamente por 12 meses](create-free-services.md) disponíveis com a [conta gratuita do Azure](https://azure.microsoft.com/free/). Juntamente com as recomendações listadas abaixo, veja [Evitar que seja faturado pela sua conta gratuita](avoid-charges-free-account.md).

Se precisar de cancelar a sua subscrição do Azure, veja [Cancelar a sua subscrição do Azure](cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Obter custos estimados antes de adicionar serviços do Azure

Utilize uma das seguintes ferramentas para calcular o custo da utilização de um serviço do Azure:
- Calculadora de preços do Azure
- Folha de preços do Azure
- Portal do Azure

As imagens nas secções a seguir mostram os preços do exemplo em dólares americanos.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Estimar o custo online com a calculadora de preços

Veja a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para obter um custo mensal estimado do serviço que pretende adicionar. Pode alterar a moeda para obter a estimativa na moeda local.

![Captura de ecrã do menu da calculadora de preços](./media/getting-started/pricing-calc.png)

Pode visualizar o custo estimado para qualquer serviço original do Azure. Por exemplo, na captura de ecrã abaixo, estima-se que uma Máquina Virtual (VM) do Windows A1 custe 66,96 $ por mês em horas de computação, se for deixada a executar o tempo todo:

![Captura de ecrã da calculadora de preços que mostra um custo estimado da VM Windows A1 por mês](./media/getting-started/pricing-calcvm.png)

Para obter mais informações sobre preços, veja as [Perguntas mais frequentes sobre preços](https://azure.microsoft.com/pricing/faq/). Se quiser comunicar com um vendedor do Azure, ligue para o número de telefone mostrado na parte superior da página de perguntas mais frequentes.

### <a name="view-and-download-azure-price-sheet"></a>Visualizar e transferir a folha de preços do Azure

Se tiver acesso ao Azure através de um Contrato Enterprise (EA) ou um Contrato de Cliente da Microsoft (MCA), poderá ver e transferir a folha de preços para a sua conta do Azure. A folha de preços é um ficheiro do Excel que contém preços para todos os serviços do Azure. Para obter mais informações, veja [Ver e transferir os seus preços do Azure](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

Pode ver o custo estimado por mês ao adicionar um serviço no portal do Azure. Por exemplo, ao escolher o tamanho da VM do Windows, verá o custo mensal estimado para as horas de computação:

![Exemplo: uma VM do Windows A1 a mostrar o custo estimado por mês](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorizar custos ao utilizar os serviços do Azure
Pode monitorizar os custos com as seguintes ferramentas:

- Orçamento e alertas de custo
- Análise de custos

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Controlar custos com orçamentos e alertas de custo

Crie [orçamentos](../costs/tutorial-acm-create-budgets.md) para gerir custos e criar [alertas](../costs/cost-mgt-alerts-monitor-usage-spending.md) que notifiquem automaticamente a si e às partes interessadas das anomalias nos gastos e dos gastos excessivos invulgares.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Explorar e analisar os custos com a análise de custos

Depois de os serviços do Azure estarem em execução, verifique os custos regularmente para controlar os seus gastos no Azure. Pode utilizar a análise de custos para compreender qual é a origem dos custos da sua utilização do Azure.

1. Aceda à [página de Gestão de Custos + Faturação no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Clique em **Análise de custos** no lado esquerdo do ecrã para ver o custo atual dividido por várias tabelas dinâmicas, como serviço, localização e subscrição. Depois de adicionar um serviço ou fazer uma compra, espere 24 horas para que os dados sejam apresentados. Por predefinição, a análise de custos mostra o custo para o âmbito onde está. Por exemplo, na captura de ecrã abaixo, é apresentado o custo da conta de faturação da Contoso. Utilize o atalho Âmbito para alternar para um âmbito diferente na análise de custos. Para obter mais informações sobre âmbitos, veja [Compreender e trabalhar com âmbitos](../costs/understand-work-scopes.md#scopes)

    ![Captura de ecrã da análise de custos no portal do Azure](./media/getting-started/cost-analysis.png)

4. Pode filtrar por várias propriedades, como, por exemplo, os rótulos, o tipo de recurso e o intervalo de tempo. Clique em **Adicionar filtro** para adicionar o filtro para uma propriedade e selecione os valores a serem filtrados. Selecione **Exportar** para exportar a vista para um ficheiro de valores separados por vírgulas (. csv).

5. Além disso, pode clicar nos rótulos do gráfico para ver o histórico de gastos diários para esse rótulo. Por exemplo: Na captura de ecrã abaixo, clicar em máquinas virtuais apresenta o custo diário da execução das suas VMs.

    ![Captura de ecrã da vista do histórico de gastos no portal do Azure](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Otimizar e reduzir os custos
Se não estiver familiarizado com os princípios da gestão de custos, leia [Como otimizar o investimento em cloud com o Azure Cost Management](../costs/cost-mgt-best-practices.md).

No portal do Azure, também pode otimizar e reduzir os custos do Azure com o encerramento automático para VMs e as recomendações do Assistente.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Considere as funcionalidades de redução de custos como o encerramento automático para as VMs

Dependendo do seu cenário, pode configurar o encerramento automático para as VMs no portal do Azure. Para obter mais informações, veja [Encerramento automático para VMs com o Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Captura de ecrã da opção de encerramento automático no portal](./media/getting-started/auto-shutdown.png)

Encerramento automático não é o mesmo que quando se encerra dentro da VM com opções de energia. O encerramento automático para e desaloja as VMs para evitar despesas adicionais de utilização. Para obter mais informações, veja as perguntas mais frequentes sobre preços para [VMs de Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [VMs de Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) sobre estados das VMs.

Para obter mais funcionalidades de redução de custos para os ambientes de desenvolvimento e teste, veja [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Ative e reveja as recomendações do Assistente do Azure

O [Assistente do Azure](../../advisor/advisor-overview.md) ajuda a reduzir custos ao identificar os recursos com baixa utilização. Procure o **Assistente** no portal do Azure:

![Captura de ecrã do botão do Assistente do Azure no portal do Azure](./media/getting-started/advisor-button.png)

Selecione **Custo** no lado esquerdo. Irá ver recomendações acionáveis no separador **Custo**:

![Captura de ecrã do exemplo de recomendação de custos do Assistente](./media/getting-started/advisor-action.png)

Reveja o tutorial [Otimizar custos a partir de recomendações](../costs/tutorial-acm-opt-recommendations.md) para um tutorial guiado sobre recomendações do Assistente para redução de custos.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integrar com APIs de faturação e consumo

Utilize as APIs de [faturação](https://docs.microsoft.com/rest/api/billing/) e [consumo](https://docs.microsoft.com/rest/api/consumption/) do Azure para obter, através de programação, os dados de faturação e de custos. Utilize a API RateCard e a API de Utilização em conjunto para obter a utilização faturada. Para obter mais informações, veja [Gain insights into your Microsoft Azure resource consumption](usage-rate-card-overview.md) (Obter informações sobre o consumo de recursos do Microsoft Azure).

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

Para visualizar os custos, precisa de acesso de nível de conta ou de subscrição para as informações de custos ou faturação. O acesso varia consoante o tipo de conta de faturação. Para saber mais sobre as contas de faturação e verificar o tipo da sua conta de faturação, veja [Ver as contas de faturação no portal do Azure](view-all-accounts.md).

Se tiver acesso ao Azure através de uma conta de faturação do Programa do Serviço Online da Microsoft (MOSP), veja [Gerir o acesso às informações de faturação do Azure](manage-billing-access.md).

Se tiver acesso ao Azure através de uma conta de faturação do Contrato Enterprise (EA), veja [Compreender as funções administrativas do Contrato Enterprise do Azure](understand-ea-roles.md).

Se tiver acesso ao Azure através de uma conta de faturação do Contrato de Cliente da Microsoft (MCA), veja [Compreender as funções administrativas do Contrato Enterprise do Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Pedir um crédito de Contrato de Nível de Serviço para um incidente de serviço

O Contrato de Nível de Serviço (SLA) descreve o compromisso da Microsoft quanto ao período de disponibilidade e à conectividade. Um incidente de serviço é relatado quando os serviços do Azure apresentam um problema que afeta o tempo de atividade ou a conectividade, geralmente conhecido como *interrupção*. Se não alcançarmos e mantivermos os níveis de serviço para cada serviço, conforme descrito no SLA, poderá estar elegível para um crédito em relação a uma parte dos seus honorários de serviço mensais.

Para pedir um crédito:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/). Se tiver várias contas, confirme que utiliza aquela que foi afetada pelo tempo de inatividade do Azure.
2. Crie um pedido de suporte novo.
3. Em **Tipo de questão**, selecione **Faturação**.
4. Em **Tipo de problema**, selecione **Pedido de Reembolso**.
5. Adicione detalhes para especificar que está a pedir um crédito de SLA, mencione a data/hora/fuso horário, bem como os serviços afetados (VMs, sites, etc.).
6. Verifique os detalhes de contacto e selecione **Criar** para enviar o seu pedido.

Os limites de SLA variam de acordo com o serviço. Por exemplo, o Escalão SQL Web tem um SLA de 99,9%, as VMs têm um SLA de 99,95% e o Escalão SQL Standard tem um SLA de 99,99%.

Para alguns serviços, existem pré-requisitos para que o SLA seja aplicado. Por exemplo, as máquinas virtuais têm de ter duas ou mais instâncias implementadas no mesmo Conjunto de Disponibilidade.

Para obter mais informações, veja [Contratos de Nível de Serviço](https://azure.microsoft.com/support/legal/sla/) e a documentação [Resumo do SLA para os serviços do Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="analyze-unexpected-charges"></a>Analisar custos inesperados

É provável que a infraestrutura de recursos de cloud que criou para a sua organização seja complexa. Muitos tipos de recursos do Azure têm diferentes tipos de custos. Os recursos podem ser propriedade de muitas equipas diversas da organização e podem ter tipos de modelos de faturação diferentes. Para compreender melhor os custos, utilize uma ou mais das estratégias nas secções abaixo para começar a sua análise.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Rever a fatura e identificar o recurso responsável pelo custo

A forma como compra os serviços do Azure ajuda a determinar a metodologia e as ferramentas à sua disposição para identificar o recurso associado a um custo. Para determinar a metodologia que lhe é aplicável, comece por [confirmar o seu tipo de oferta do Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Depois, identifique a categoria de cliente na lista de [ofertas do Azure suportadas](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Os artigos seguintes disponibilizam passos detalhados que explicam como rever a sua fatura consoante o seu tipo de cliente. Cada artigo inclui instruções para transferir um ficheiro CSV que contém detalhes de utilização e custo relativos a um determinado período de faturação.

- [Processo de revisão da fatura Pay As You Go](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Processo de revisão da fatura Contrato Enterprise](../understand/review-enterprise-agreement-bill.md)
- [Processo de revisão do Contrato de Cliente Microsoft](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Processo de revisão do Contrato de Parceiros da Microsoft](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

A fatura do Azure agrega os custos do mês por _medidor_. Os medidores são utilizados para monitorizar a utilização de um recurso ao longo do tempo e para calcular a sua fatura. Quando cria um recurso do Azure individual, como uma máquina virtual, são criadas uma ou mais instâncias de medidor para esse recurso.

Filtre o ficheiro CSV de utilização de acordo com _MeterName_, conforme mostrado na fatura que pretende analisar para ver todos os itens de linha que se aplicam ao medidor. O _InstanceID_ do item de linha corresponde ao recurso do Azure real que gerou o custo.

Depois de identificar o recurso em causa, pode utilizar a Análise de custos no Azure Cost Management para fazer mais análises aos custos relacionados com o mesmo. Para saber mais sobre como utilizar a Análise de custos, veja [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md).

### <a name="review-invoiced-charges-in-cost-analysis"></a>Consultar as cobranças faturadas na Análise de custos

Para ver os detalhes da sua fatura no portal do Azure, navegue para a Análise de custos para consultar o âmbito associado à fatura que está a analisar. Selecione a vista **Detalhes da fatura**. Os detalhes da fatura mostram-lhe as cobranças conforme apresentadas na fatura.

[![Exemplo a mostrar os detalhes da fatura](./media/getting-started/invoice-details.png)](./media/getting-started/invoice-details.png#lightbox)

Quando vê os detalhes da fatura, pode identificar o serviço que apresenta custos inesperados e determinar que recursos estão diretamente associados ao recurso na Análise de custos. Por exemplo, se quiser analisar as cobranças associadas ao serviço Máquinas Virtuais, navegue para a vista **Custos acumulados**. Em seguida, defina a granularidade para **Diário** e filtre as cobranças **Nome do serviço: Máquinas virtuais** e agrupe as cobranças por **Recurso**.

[![Exemplo a mostrar os custos acumulados para as máquinas virtuais](./media/getting-started/virtual-machines.png)](./media/getting-started/virtual-machines.png#lightbox)


### <a name="identify-spikes-in-cost-over-time"></a>Identificar picos no custo ao longo do tempo

Por vezes, poderá não saber que custos recentes resultaram em alterações aos custos que lhe foram cobrados. Para compreender o que mudou, pode utilizar a Análise de custos para [ver uma discriminação diária ou mensal dos custos ao longo do tempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Depois de criar a vista, agrupe os custos por **Serviço** ou por **Recurso** para identificar as alterações. Também pode mudar a vista para um gráfico de **Linhas** para visualizar melhor os dados.

![Exemplo que mostra os custos ao longo do tempo na análise de custos](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Determinar os preços do recurso e compreender o modelo de faturação do mesmo

Um mesmo recurso pode acumular custos em vários produtos e serviços do Azure. Veja a página de [preços do Azure por produto](https://azure.microsoft.com/pricing/#product-pricing) para saber mais sobre os preços de cada serviço do Azure. Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para monitorizar a utilização da mesma. Cada medidor pode ter preços diferentes.

- Horas de Computação
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferência de Dados de Saída
- Disco Gerido Standard
- Operações de Discos Geridos Standard
- E/S Padrão – Disco
- E/S Padrão – Leitura de Blobs de Blocos
- E/S Padrão – Escrita de Blobs de Blocos
- E/S Padrão – Eliminação de Blobs de Blocos

Uma vez criada a VM, cada medidor começa a emitir registos de utilização. A utilização e o preço do medidor são monitorizados no sistema de medição do Azure. Pode ver os medidores que foram utilizados para calcular a faturação no ficheiro CSV de utilização.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Encontrar as pessoas responsáveis pelo recurso e contactá-las

Muitas vezes, a equipa responsável por um determinado recurso estará a par das alterações feitas a um recurso. É importante contactá-la quando tenta determinar o porquê de os custos poderem aparecer. Por exemplo, a equipa responsável pode ter criado recentemente o recurso, atualizado o SKU do mesmo (e, consequentemente, alterado a respetiva taxa) ou aumentado a carga no recurso devido a alterações ao código. Continue a ler as próximas secções para obter mais técnicas para saber quem detém um recurso.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Analisar os registos de auditoria do recurso

Se tiver permissões para ver um recurso, deverá poder aceder aos registos de auditoria do mesmo. Reveja os registos para saber que utilizador é responsável pelas alterações mais recentes a um recurso. Para saber mais, veja [Ver e obter eventos de registo de atividades do Azure](../../azure-monitor/platform/activity-log-view.md).

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analisar as permissões do utilizador no âmbito principal do recurso

Geralmente, as pessoas com acesso de escrita a uma subscrição ou a um grupo de recursos têm informações sobre os recursos que foram criados. Deverão conseguir explicar o objetivo de um recurso ou indicar-lhe a pessoa que o saiba. Para identificar as pessoas com permissões para um âmbito de Subscrição, veja [Ver atribuições de funções](../../role-based-access-control/check-access.md#view-role-assignments). Pode utilizar um processo semelhante para grupos de recursos.

### <a name="get-help-to-identify-charges"></a>Obter ajuda para identificar alterações

Se tiver seguido as estratégias anteriores e ainda não percebe por que motivo recebeu um custo, ou se precisar de ajuda com outros problemas de faturação, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes
- Saiba mais sobre como utilizar [limites de gastos](spending-limit.md) para evitar os gastos em excesso.
- Comece a [analisar os seus custos do Azure](../costs/quick-acm-cost-analysis.md).
