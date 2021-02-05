---
title: Plano de gestão de custos para o Serviço de Aplicações
description: Saiba como planear e gerir os custos do Azure App Service utilizando a análise de custos no portal Azure.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 3df08705859678525526f8fef198826f58249d8b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573369"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>Planear e gerir os custos do Azure App Service

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Este artigo descreve como planeia e gere os custos do Azure App Service. Em primeiro lugar, utiliza a calculadora de preços Azure para ajudar a planear os custos do Serviço de Aplicações antes de adicionar quaisquer recursos para o serviço para estimar custos. Em seguida, ao adicionar recursos Azure, reveja os custos estimados. Depois de ter começado a utilizar os recursos do Serviço de Aplicações, utilize funcionalidades [de Gestão de Custos](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para definir orçamentos e monitorizar os custos. Também pode rever os custos previstos e identificar tendências de gastos para identificar áreas onde você pode querer agir. Os custos do Azure App Service são apenas uma parte dos custos mensais na sua conta Azure. Embora este artigo explique como planear e gerir custos para o Serviço de Aplicações, você está cobrado para todos os serviços e recursos Azure usados na sua subscrição Azure, incluindo os serviços de terceiros.

## <a name="relevant-costs-for-app-service"></a>Custos relevantes para o Serviço de Aplicações

O Serviço de Aplicações funciona em infraestrutura Azure que acumula custos. É importante entender que infraestruturas adicionais podem acumular custos. Tem de gerir esse custo quando faz alterações nos recursos implantados.

### <a name="costs-that-accrue-with-azure-app-service"></a>Custos que acumulam com o Azure App Service

Dependendo da funcionalidade que utilizar no Serviço de Aplicações, podem ser criados os seguintes recursos de acumulação de custos:

- **Plano de Serviço de Aplicações**  É necessário acolher uma aplicação do Serviço de Aplicações.
- **Nível isolado**  É necessária uma [Rede Virtual](../virtual-network/index.yml) para um ambiente de Serviço de Aplicações.
- **Backup**  É necessária [uma conta de Armazenamento](../storage/index.yml) para fazer cópias de segurança.
- **Registos de diagnóstico**  Pode selecionar a [conta de Armazenamento](../storage/index.yml) como opção de registo registado ou integrar-se com o [Azure Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md).
- **Certificados de Serviço de Aplicações**  Os certificados que comprar em Azure devem ser mantidos no [Cofre da Chave Azure](../key-vault/index.yml).

Outros recursos de custos para o Serviço de Aplicações são (ver [preços do Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/) para mais detalhes):

- [Domínios do Serviço de Aplicações](manage-custom-dns-buy-domain.md)  A sua subscrição é cobrada para o registo de domínio anualmente, se permitir a renovação automática.
- [Certificados de Serviço de Aplicações](configure-ssl-certificate.md#import-an-app-service-certificate)  Uma vez cobrada no momento da compra. Se tiver vários subdomínios para garantir, pode reduzir o custo comprando um certificado wildcard em vez de vários certificados padrão.
- [Encadernações de certificados baseadas em IP](configure-ssl-bindings.md#create-binding)  A encadernação está configurada num certificado ao nível da aplicação. Os custos são acrescidos por cada encadernação. Para o nível **Standard** e acima, a primeira ligação baseada em IP não é cobrada.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Custos que podem acumular após a supressão de recursos

Quando elimina todas as aplicações num plano de Serviço de Aplicações, o plano continua a acumular encargos com base no seu nível de preços configurado e no número de casos. Para evitar cargas indesejadas, elimine o plano ou reduza-o para nível **Livre.**

Depois de eliminar os recursos do Azure App Service, os recursos dos serviços Azure relacionados podem continuar a existir. Continuam a acumular custos até os eliminares. Por exemplo:

- A Rede Virtual que criou para um plano de serviço de aplicações de nível **isolado**
- Contas de armazenamento que criou para armazenar backups ou registos de diagnóstico
- Key Vault que criou para armazenar certificados de Serviço de Aplicações
- Log Analítico espaços de nome que criou para enviar registos de diagnóstico
- [Reservas de caso ou carimbo](#azure-reservations) para o Serviço de Aplicações que ainda não expirou

### <a name="using-monetary-credit-with-azure-app-service"></a>Utilização de Crédito Monetário com Serviço de Aplicações Azure

Você pode pagar os custos do Azure App Service com o seu crédito Azure Prepayment (anteriormente chamado de compromisso monetário). No entanto, não pode usar o crédito Azure Prepayment para pagar os custos de produtos e serviços de terceiros, incluindo os do Azure Marketplace.

## <a name="estimate-costs"></a>Cálculo de custos

Uma maneira fácil de estimar e otimizar previamente o custo do seu Serviço de Aplicações é utilizando a [calculadora de preços Azure.](https://azure.microsoft.com/pricing/calculator/)

Para utilizar a calculadora de preços, clique em Serviço de **Aplicações** no separador **Produtos.** Em seguida, desça para trabalhar com a calculadora. A imagem a seguir é um exemplo e não reflete os preços atuais.

![Exemplo que mostra o custo estimado na calculadora de preços do Azure](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>Rever os custos estimados no portal do Azure

Quando cria uma aplicação de Serviço de Aplicações ou um plano de Serviço de Aplicações, pode ver os custos estimados.

Para criar uma app e ver o preço estimado:

1. Na página de criar, desloque-se até ao **plano de Serviço de Aplicações** e clique em Criar **novo**.
1. Especifique um nome e clique **em OK**.
1. Ao lado **do Sku e do tamanho,** clique no **tamanho da mudança**.
1. Reveja o preço estimado indicado no resumo. A imagem a seguir é um exemplo e não reflete os preços atuais.

    ![Rever o custo estimado para cada nível de preços no portal](media/overview-manage-costs/pricing-estimates.png)

Se a sua subscrição Azure tiver um limite de gastos, o Azure impede-o de gastar acima do seu valor de crédito. À medida que cria e utiliza recursos Azure, os seus créditos são usados. Quando se atinge o seu limite de crédito, os recursos que implementou são desativados durante o resto desse período de faturação. Não podes alterar o teu limite de crédito, mas podes removê-lo. Para obter mais informações sobre os limites de gastos, consulte [o limite de gastos do Azure.](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="optimize-costs"></a>Otimizar custos

A um nível básico, as aplicações do Serviço de Aplicações são cobradas pelo plano de Serviço de Aplicações que as acolhe. Os custos associados à sua implementação do Serviço de Aplicações dependem de alguns fatores principais:

- **Nível de preços**  Também conhecido como O SKU do plano de Serviço de Aplicações. Os níveis mais altos fornecem mais núcleos de CPU, memória, armazenamento ou funcionalidades, ou combinações dos mesmos.
- **Os**  níveis dedicados (Básico e Superior) podem ser dimensionados, e cada instância escalonada aumenta os custos.
- **Taxa de selo**  No nível isolado, uma taxa fixa é acumulada no seu ambiente de Serviço de Aplicações, independentemente de quantas aplicações ou instâncias de trabalhador são hospedadas.

Um plano de Serviço de Aplicações pode acolher mais do que uma aplicação. Dependendo da sua implementação, poderá poupar custos com mais aplicações num plano de Serviço de Aplicações (ou seja, hospedar as suas apps em menos planos de Serviço de Aplicações).

Para mais detalhes, consulte a [visão geral do plano do Serviço de Aplicações](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Cargas de trabalho não produção

Para testar o Serviço de Aplicações ou a sua solução enquanto acumula um custo baixo ou mínimo, pode começar por utilizar os dois níveis de preços de nível de entrada, **Gratuito** e **Partilhado,** que são hospedados em instâncias partilhadas. Para testar a sua aplicação em casos dedicados com melhor desempenho, pode fazer upgrade para o tier **Basic,** que suporta aplicações Windows e Linux. 

> [!NOTE]
> Preços de **Azure Dev/Test**  Para testar cargas de trabalho de pré-produção que requerem níveis mais elevados (todos os níveis exceto **isolados**), os assinantes do Estúdio Visual também podem tirar partido do [Preço Azure Dev/Test,](https://azure.microsoft.com/pricing/dev-test/)que oferece descontos significativos.
>
> Tanto o nível **Free** and **Shared,** como os descontos de preços Azure Dev/Test, não têm um SLA apoiado financeiramente.

### <a name="production-workloads"></a>Cargas de trabalho de produção

As cargas de trabalho de produção vêm com a recomendação do nível de preços **standard** dedicado ou superior. Embora o preço suba para níveis mais altos, também lhe dá mais memória e armazenamento e hardware de maior desempenho, dando-lhe maior densidade de aplicações por instância de computação. Isso traduz-se na contagem de exemplos mais baixa para o mesmo número de apps e, portanto, menor custo. De facto, o **Premium V3** (o nível não **isolado** mais elevado) é a forma mais rentável de servir a sua app em escala. Para adicionar às poupanças, você pode obter descontos profundos em [reservas Premium V3](#azure-reservations).

> [!NOTE]
> **Premium V3** suporta recipientes Windows e linux. 

Uma vez que escolha o nível de preços que deseja, deve minimizar as instâncias ociosas. Numa implementação de escala, você pode desperdiçar dinheiro em casos de computação subutilizada. Deve [configurar autoscaling](../azure-monitor/platform/autoscale-get-started.md), disponível no nível **Standard** e acima. Ao criar horários de escala, bem como regras de escala baseadas em métricas, você só paga pelos casos que realmente precisa em qualquer momento.

### <a name="azure-reservations"></a>Reservas do Azure

Se planeia utilizar um número mínimo conhecido de instâncias computacional durante um ano ou mais, deve aproveitar o nível **Premium V3** e reduzir drasticamente o custo da instância, reservando esses casos em incrementos de 1 ano ou 3 anos. A poupança mensal de custos pode chegar aos 55% por exemplo. São possíveis dois tipos de reservas:

- **Windows (ou plataforma agnóstica)**  Pode aplicar-se a instâncias Windows ou Linux na sua subscrição.
- **Linux específico**  Aplica-se apenas a casos Linux na sua subscrição.

O preço reservado aplica-se às instâncias aplicáveis na sua subscrição, até ao número de casos que reserva. Os casos reservados são um assunto de faturação e não estão ligados a casos específicos de computação. Se tiver menos casos do que reserva em qualquer ponto durante o período de reserva, ainda paga pelas instâncias reservadas. Se executar mais casos do que reserva em qualquer ponto durante o período de reserva, paga o custo acumulado normal para as instâncias adicionais.

O nível **isolado** (ambiente de Serviço de Aplicações) também suporta reservas de 1 ano e 3 anos a preços reduzidos. Para mais informações, consulte [como os descontos de reserva se aplicam ao Azure App Service](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Monitorizar os custos

Ao utilizar os recursos Azure com o Serviço de Aplicações, incorre em custos. Os custos da unidade de utilização do recurso azul variam em intervalos de tempo (segundos, minutos, horas e dias). Assim que a utilização do Serviço de Aplicações começa, os custos são incorridos e você pode ver os custos na [análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Quando utiliza a análise de custos, vê os custos do Serviço de Aplicações em gráficos e tabelas para diferentes intervalos de tempo. Alguns exemplos são de dia, mês e mês anterior, e ano. Vê-se também os custos contra os orçamentos e os custos previstos. Mudar para vistas mais longas ao longo do tempo pode ajudá-lo a identificar tendências de gastos. E vê onde pode ter ocorrido gastos excessivos. Se criou orçamentos, também pode ver facilmente onde são ultrapassados.
    
Para ver os custos do Serviço de Aplicações na análise de custos:

1. Inicie sessão no Portal do Azure.
2. Abra o âmbito no portal Azure e selecione **análise de custos** no menu. Por exemplo, vá a **Subscrições,** selecione uma subscrição da lista e, em seguida, selecione  **análise de custos** no menu. Selecione **Scope** para mudar para um âmbito diferente na análise de custos.
3. Por padrão, o custo dos serviços é mostrado na primeira tabela de donuts. Selecione a área no gráfico marcado serviço de aplicações.

Os custos mensais reais são mostrados quando inicialmente abre a análise de custos. Aqui está um exemplo mostrando todos os custos mensais de uso.

![Exemplo mostrando custos acumulados para uma subscrição](media/overview-manage-costs/all-costs.png)

Para reduzir os custos para um único serviço, como o App Service, **selecione adicionar filtro** e, em seguida, selecione o nome de **Serviço**. Em seguida, selecione **App Service**.

Aqui está um exemplo mostrando custos apenas para o Serviço de Aplicações.

![Exemplo mostrando custos acumulados para o ServiceName](media/overview-manage-costs/service-specific-costs.png)

No exemplo anterior, vê-se o custo atual do serviço. Os custos das regiões Azure (localizações) e do Serviço de Aplicações por grupo de recursos também são mostrados. A partir daqui, você pode explorar os custos por conta própria.

## <a name="create-budgets"></a>Criar orçamentos

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

Pode criar [orçamentos](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para gerir custos e criar [alertas](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) que notifiquem automaticamente as partes interessadas das anomalias nos gastos e do risco em fazer gastos excessivos. Os alertas são baseados nos gastos em comparação com os orçamentos e os limites de custos. Orçamentos e alertas são criados para subscrições e grupos de recursos Azure, por isso são úteis como parte de uma estratégia global de monitorização de custos. 

Os orçamentos podem ser criados com filtros para recursos ou serviços específicos em Azure se quiser mais granularidade presente na sua monitorização. Os filtros ajudam a garantir que não criam acidentalmente novos recursos que lhe custam dinheiro extra. Para obter mais informações sobre as opções de filtro disponíveis quando criar um orçamento, consulte [opções de Grupo e filtro](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exportar dados de custos

Também pode [exportar os seus dados de custos](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) para uma conta de armazenamento. Isto é útil quando você precisa ou outros para fazer mais análise de dados para custos. Por exemplo, uma equipa de finanças pode analisar os dados usando o Excel ou o Power BI. Pode exportar os seus custos num horário diário, semanal ou mensal e definir um intervalo de datas personalizado. Exportar dados de custos é a forma recomendada de recuperar conjuntos de dados de custos.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre como os preços funcionam com o Azure Storage. Consulte [os preços do Serviço de Aplicações.](https://azure.microsoft.com/pricing/details/app-service/)
- Saiba [como otimizar o seu investimento em nuvem com a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba mais sobre gestão de custos com [a análise de custos.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Saiba como [evitar custos inesperados.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Faça o curso de aprendizagem guiada [de Gestão](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) de Custos.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->