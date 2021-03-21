---
title: Gerir a utilização e os custos dos registos do Monitor Azure
description: Saiba como alterar o plano de preços e gerir a política de volume de dados e retenção para o seu espaço de trabalho Log Analytics no Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bwren
ms.openlocfilehash: 1ec8c65a1babe707175eb891592694dd0524711b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123603"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerir a utilização e os custos com Registos do Azure Monitor    

> [!NOTE]
> Este artigo descreve como compreender e controlar os seus custos para os Registos do Monitor Azure. Um artigo relacionado, [monitorização da utilização e custos estimados](../usage-estimated-costs.md) descreve como visualizar a utilização e os custos estimados em várias funcionalidades de monitorização do Azure para diferentes modelos de preços. Todos os preços e custos apresentados neste artigo são apenas para fins. 

Os Registos Azure Monitor são projetados para escalar e suportar a recolha, indexação e armazenamento de quantidades massivas de dados por dia a partir de qualquer fonte na sua empresa ou implantada em Azure.  Embora este possa ser um principal impulsionador para a sua organização, a eficiência de custos é, em última análise, o condutor subjacente. Para tal, é importante entender que o custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados recolhidos, também depende do plano selecionado, e quanto tempo escolheu para armazenar dados gerados a partir das suas fontes ligadas.  

Neste artigo analisamos como pode monitorizar proativamente o volume de dados ingeridos e o crescimento do armazenamento, e definimos limites para controlar os custos associados. 

## <a name="pricing-model"></a>Modelo preços

O preço padrão para Log Analytics é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para uma maior retenção de dados. O volume de dados é medido como o tamanho dos dados que serão armazenados em GB (10^9 bytes). Cada espaço de trabalho Log Analytics é cobrado como um serviço separado e contribui para a fatura da sua subscrição Azure. A quantidade de ingestão de dados pode ser considerável dependendo dos seguintes fatores: 

  - Número de soluções de gestão habilitados e sua configuração
  - Número de VMs monitorizados
  - Tipo de dados recolhidos de cada VM monitorizado 
  
Além do modelo Pay-As-You-Go, o Log Analytics tem níveis **de Reserva de Capacidade** que lhe permitem economizar até 25% em comparação com o preço Pay-As-You-Go. O preço da reserva de capacidade permite-lhe comprar uma reserva a partir de 100 GB/dia. Qualquer utilização acima do nível de reserva será faturada na tarifa Pay-As-You-Go. Os níveis de Reserva de Capacidade têm um período de compromisso de 31 dias. Durante o período de compromisso, pode mudar para um nível de Reserva de Capacidade de nível mais elevado (que reiniciará o período de compromisso de 31 dias), mas não pode voltar para Pay-As-You-Go ou para um nível de Reserva de Capacidade mais baixo até que o período de compromisso esteja terminado. A faturação dos níveis de Reserva de Capacidade é feita diariamente. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre o Log Analytics Pay-As-You-Go e o preço da Reserva de Capacidade. 

Em todos os níveis de preços, o tamanho dos dados de um evento é calculado a partir de uma representação de cadeia das propriedades que são armazenadas no Log Analytics para este evento, quer os dados sejam enviados de um agente ou adicionados durante o processo de ingestão. Isto inclui quaisquer [campos personalizados](custom-fields.md) que são adicionados à medida que os dados são recolhidos e depois armazenados no Log Analytics. Várias propriedades comuns a todos os tipos de dados, incluindo algumas [Propriedades Standard Do Log Analytics,](./log-standard-columns.md)estão excluídas no cálculo do tamanho do evento. Isto `_ResourceId` `_SubscriptionId` inclui, `_ItemId` , , e `_IsBillable` `_BilledSize` `Type` . Todas as outras propriedades armazenadas no Log Analytics estão incluídas no cálculo do tamanho do evento. Alguns tipos de dados estão totalmente isentos de taxas de ingestão de dados, por exemplo, os tipos AzureActivity, Heartbeat e Usage. Para determinar se um evento foi excluído da faturação para ingestão de dados, pode utilizar a `_IsBillable` propriedade como mostrado [abaixo.](#data-volume-for-specific-events) A utilização é reportada em GB (1.0E9 bytes). 

Além disso, note que algumas soluções, como [o Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) e a [Gestão de Configuração](https://azure.microsoft.com/pricing/details/automation/) têm os seus próprios modelos de preços. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics Clusters Dedicados

Log Analytics Clusters Dedicados são coleções de espaços de trabalho num único cluster Azure Data Explorer gerido para suportar cenários avançados como [chaves geridas pelo cliente.](customer-managed-keys.md)  Log Analytics Clusters Dedicados utilizam um modelo de preços de reserva de capacidade que deve ser configurado para pelo menos 1000 GB/dia. Este nível de capacidade tem um desconto de 25% em relação ao preço pay-as-you-go. Qualquer utilização acima do nível de reserva será faturada na tarifa Pay-As-You-Go. A Reserva de Capacidade do Cluster tem um período de compromisso de 31 dias após o aumento do nível de reserva. Durante o período de compromisso, o nível de reserva de capacidade não pode ser reduzido, mas pode ser aumentado a qualquer momento. Quando os espaços de trabalho estão associados a um cluster, a faturação de ingestão de dados para esses espaços de trabalho é feita ao nível do cluster usando o nível de reserva de capacidade configurado. Saiba mais sobre [a criação de um Log Analytics Clusters](customer-managed-keys.md#create-cluster) e [associar-lhe espaços de trabalho.](customer-managed-keys.md#link-workspace-to-cluster) A informação sobre preços da reserva de capacidade está disponível na página de preços do [Azure Monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

O nível de reserva de capacidade do cluster é configurado programáticamente com o Azure Resource Manager usando o `Capacity` parâmetro em `Sku` . O `Capacity` é especificado em unidades de GB e pode ter valores de 1000 GB/dia ou mais em incrementos de 100 GB/dia. Isto é detalhado na [chave gerida pelo cliente do Azure Monitor.](customer-managed-keys.md#create-cluster) Se o seu cluster necessitar de uma reserva superior a 2000 GB/dia contacte-nos em [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Existem dois modos de faturação para uso num cluster. Estes podem ser especificados pelo `billingType` parâmetro ao [configurar o seu cluster](customer-managed-keys.md#customer-managed-key-operations). Os dois modos são: 

1. **Cluster**: neste caso (que é o padrão), a faturação dos dados ingeridos é feita ao nível do cluster. As quantidades de dados ingeridas de cada espaço de trabalho associado a um cluster são agregadas para calcular a fatura diária do cluster. Note que as dotações por nódoa do [Azure Security Center](../../security-center/index.yml) são aplicadas ao nível do espaço de trabalho antes desta agregação de dados agregados em todos os espaços de trabalho do cluster. 

2. **Espaços de trabalho**: os custos de reserva de capacidade para o seu Cluster são atribuídos proporcionalmente aos espaços de trabalho no Cluster (após contabilização das dotações por nó do Centro de [Segurança Azure](../../security-center/index.yml) para cada espaço de trabalho.) Se o volume total de dados ingerido num espaço de trabalho por um dia for inferior à Reserva de Capacidade, então cada espaço de trabalho é faturado para os seus dados ingeridos à taxa efetiva de reserva de capacidade por GB, cobrando-lhes uma fração da Reserva de Capacidade, e a parte não utilizada da Reserva de Capacidade é faturada para o recurso do cluster. Se o volume total de dados ingerido num espaço de trabalho por um dia for superior à Reserva de Capacidade, então cada espaço de trabalho é faturado por uma fração da Reserva de Capacidade com base na sua fração dos dados ingeridos nesse dia, e cada espaço de trabalho para uma fração dos dados ingeridos acima da Reserva de Capacidade. Não há nada faturado para o recurso do cluster se o volume total de dados ingerido num espaço de trabalho por um dia for sobre a Reserva de Capacidade.

Nas opções de faturação de cluster, a retenção de dados é faturada no espaço de trabalho. Note que a faturação do cluster começa quando o cluster é criado, independentemente de os espaços de trabalho terem sido associados ao cluster. Além disso, note que os espaços de trabalho associados a um cluster já não têm um nível de preços.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerir o seu ambiente 

Se ainda não estiver a utilizar os Registos do Monitor Azure, pode utilizar a calculadora de preços do [Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização do Log Analytics. Comece por introduzir "Azure Monitor" na caixa de pesquisa e clique no azulejo Azure Monitor resultante. Percorra a página para O Monitor Azure e selecione Log Analytics a partir do dropdown do Tipo.  Aqui pode introduzir o número de VMs e o GB de dados que espera recolher de cada VM. Normalmente, 1 a 3 GB de mês de dados é ingerido a partir de um VM típico do Azure. Se já está a avaliar os Registos do Monitor Azure, pode utilizar as suas estatísticas de dados a partir do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorizados](#understanding-nodes-sending-data) e o [volume de dados que o seu espaço de trabalho está a ingerir.](#understanding-ingested-data-volume) 

## <a name="understand-your-usage-and-estimate-costs"></a>Compreenda o seu uso e calcule os custos

Se está a usar registos do Monitor Azure agora, é fácil entender quais os custos que provavelmente são baseados em padrões de utilização recentes. Para isso, utilize  **o Registo de Utilização e Custos Estimados** para rever e analisar a utilização dos dados. Isto mostra quantos dados são recolhidos por cada solução, quantos dados estão a ser retidos e uma estimativa dos seus custos com base na quantidade de dados ingeridos e qualquer retenção adicional para além do montante incluído.

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="Utilização e custos estimados":::

Para explorar mais detalhadamente os seus dados, clique no ícone no topo direito de qualquer um dos gráficos na página **Utilização e Custos Estimados.** Agora pode trabalhar com esta consulta para explorar mais detalhes do seu uso.  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="Vista de registos":::

Na página **Utilização e Custos Estimados** pode rever o volume de dados do mês. Isto inclui todos os dados faturados recebidos e retidos no seu espaço de trabalho Log Analytics.  
 
Os custos do Log Analytics são adicionados à sua conta Azure. Pode ver detalhes da sua conta Azure na secção de Faturação do portal Azure ou no [Portal de Faturação Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualização do uso do Log Analytics na sua conta Azure 

O Azure fornece uma grande funcionalidade útil no centro [Azure Cost Management + Billing.](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json) Por exemplo, a funcionalidade "Análise de Custos" permite-lhe visualizar os seus gastos para os recursos Azure. Em primeiro lugar, adicione um filtro por "Tipo de Recurso" (para microsoft.operationalinsights/espaço de trabalho para Log Analytics e microsoft.operationalinsights/cluster para Clusters De Log Analytics) permitir-lhe-á rastrear o seu gasto log Analytics. Em seguida, para "Grupo por" selecione "Categoria de Contador" ou "Medidor".  Note que outros serviços como o Azure Security Center e o Azure Sentinel também faturam o seu uso contra os recursos do espaço de trabalho Log Analytics. Para ver o mapeamento do nome de Serviço, pode selecionar a vista 'Tabela' em vez de um gráfico. 

Pode compreender melhor a utilização ao [transferir a utilização a partir do portal do Azure](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Na folha de cálculo transferida, pode ver a utilização por recurso do Azure (por exemplo, a área de trabalho do Log Analytics) por dia. Nesta folha de cálculo do Excel, a utilização dos seus espaços de trabalho Log Analytics pode ser encontrada através da primeira filtragem na coluna "Meter Category" para mostrar "Log Analytics", "Insight and Analytics" (usado por alguns dos níveis de preços do legado) e "Azure Monitor" (utilizado pelos níveis de preços da Reserva de Capacidade), e, em seguida, adicionar um filtro na coluna "Instance ID" que é "contém espaço de trabalho" ou "cluster" (este último inclui o Log Analytics Cluster). A utilização é mostrada na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Estão disponíveis mais detalhes para o ajudar a [entender a fatura do Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md). 

## <a name="changing-pricing-tier"></a>Alteração do nível de preços

Para alterar o nível de preços do Log Analytics do seu espaço de trabalho, 

1. No portal Azure, abra o **Uso e os custos estimados** do seu espaço de trabalho onde verá uma lista de cada um dos níveis de preços disponíveis para este espaço de trabalho.

2. Reveja os custos estimados para cada um dos níveis de preços. Esta estimativa baseia-se nos últimos 31 dias de utilização, pelo que esta estimativa de custos baseia-se nos últimos 31 dias sendo representativos do seu uso típico. No exemplo abaixo pode ver como, com base nos padrões de dados dos últimos 31 dias, este espaço de trabalho custaria menos no nível Pay-As-You-Go (#1) em comparação com o nível de Reserva de Capacidade de 100 GB/dia (#2).  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="Escalões de preço":::
    
3. Depois de rever os custos estimados com base nos últimos 31 dias de utilização, se decidir alterar o nível de preços, clique em **Select**.  

Também pode [definir o nível de preços através do Azure Resource Manager](../samples/resource-manager-workspace.md) utilizando o parâmetro `sku` `pricingTier` (no modelo Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Escalões de preço legados

As subscrições que tenham um espaço de trabalho log Analytics ou um recurso application insights nele antes de 2 de abril de 2018, ou estejam ligadas a um Acordo de Empresa que começou antes de 1 de fevereiro de 2019, continuarão a ter acesso a utilizar os níveis de preços do legado: **Free,** **Standalone (Per GB)** e **Per Node (OMS)**.  Os espaços de trabalho no nível de preços gratuitos terão a ingestão diária de dados limitada a 500 MB (exceto para tipos de dados de segurança recolhidos pelo [Azure Security Center)](../../security-center/index.yml)e a retenção de dados é limitada a 7 dias. O nível de preços gratuitos destina-se apenas a fins de avaliação. Os espaços de trabalho nos níveis de preços autónomos ou per nónsos têm uma retenção configurável entre 30 e 730 dias.

A utilização no nível de preços autónomos é faturada pelo volume de dados ingerido. É relatado no serviço **Log Analytics** e o contador chama-se "Dados Analisados". 

Os preços per nó de preços por VM (nó) monitorizados em uma hora de granularidade. Para cada nó monitorizado, o espaço de trabalho é atribuído a 500 MB de dados por dia que não são faturados. Esta alocação é calculada com granularidade horária e é agregada ao nível do espaço de trabalho todos os dias. Os dados ingeridos acima da alocação agregada de dados diários são faturados por GB como excesso de dados. Note que na sua conta, o serviço será **Insight e Analytics** para utilização do Log Analytics se o espaço de trabalho estiver no nível de preços per nó. A utilização é reportada em três metros:

1. Nó: trata-se de utilização para o número de nós monitorizados (VMs) em unidades de nó*meses.
2. Sobreavalagem de dados por nó: este é o número de GB de dados ingeridos acima da atribuição de dados agregados.
3. Dados incluídos por nó: esta é a quantidade de dados ingeridos que foi coberto pela alocação de dados agregados. Este contador também é usado quando o espaço de trabalho está em todos os níveis de preços para mostrar a quantidade de dados cobertos pelo Centro de Segurança Azure.

> [!TIP]
> Se o seu espaço de trabalho tiver acesso ao nível de preços **per nó,** mas está a pensar se isso custaria menos num nível Pay-As-You-Go, pode [usar a consulta abaixo](#evaluating-the-legacy-per-node-pricing-tier) para obter uma recomendação facilmente. 

Os espaços de trabalho criados antes de abril de 2016 também podem aceder aos níveis de preços **Standard** e **Premium** originais que têm retenção fixa de dados de 30 e 365 dias, respectivamente. Novos espaços de trabalho não podem ser criados nos níveis de preços **Standard** ou **Premium,** e se um espaço de trabalho for deslocado para fora destes níveis, não pode ser transferido para trás. Os contadores de ingestão de dados para estes níveis antigos são chamados de "Dados analisados".

Existem também alguns comportamentos entre o uso de níveis de Log Analytics legados e como o uso é cobrado para [o Azure Security Center](../../security-center/index.yml). 

1. Se o espaço de trabalho estiver no nível standard ou premium, o Azure Security Center será cobrado apenas para ingestão de dados do Log Analytics, e não por nó.
2. Se o espaço de trabalho estiver no nível de per nól, o Azure Security Center será faturado utilizando o [atual modelo de preços baseado no nó do Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/). 
3. Em outros níveis de preços (incluindo Reservas de Capacidade), se o Azure Security Center foi habilitado antes de 19 de junho de 2017, o Azure Security Center será cobrado apenas para ingestão de dados do Log Analytics. Caso contrário, o Azure Security Center será faturado utilizando o atual modelo de preços baseado no nó de segurança Azure.

Mais detalhes sobre as limitações do nível de preços estão disponíveis nos [limites de subscrição e serviço da Azure, quotas e restrições.](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)

Nenhum dos níveis de preços antigos tem preços regionais.  

> [!NOTE]
> Para utilizar os direitos que vêm da compra da Suite OMS E1, da Suite OMS E2 ou da Add-On OMS para o System Center, escolha o nível de preços do Log Analytics *Per Node.*

## <a name="log-analytics-and-security-center"></a>Log Analytics e Centro de Segurança

A faturação [do Azure Security Center](../../security-center/index.yml) está intimamente ligada à faturação do Log Analytics. O Security Center fornece uma alocação de 500 MB/nó/dia contra o subconjunto seguinte de tipos de [dados](/azure/azure-monitor/reference/tables/tables-category#security) de segurança (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) e os tipos de dados Update and UpdateSummary quando a solução de Gestão de Atualização não está a funcionar no espaço de trabalho ou na solução-alvo está ativada. Se o espaço de trabalho estiver no nível de preços per nóleiro legado, as alocações do Security Center e do Log Analytics são combinadas e aplicadas conjuntamente a todos os dados ingeridos.  

## <a name="change-the-data-retention-period"></a>Change the data retention period (Alterar o período de retenção de dados)

Os seguintes passos descrevem como configurar quanto tempo os dados de registo são mantidos no seu espaço de trabalho. A retenção de dados ao nível do espaço de trabalho pode ser configurada de 30 a 730 dias (2 anos) para todos os espaços de trabalho, a menos que estejam a usar o nível de preços gratuitos. A retenção para tipos individuais de dados pode ser definida até 4 dias. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre preços para uma retenção mais longa de dados.  Para reter dados superiores a 730 dias, considere a utilização da exportação de [dados do espaço de trabalho Log Analytics.](logs-data-export.md)

### <a name="workspace-level-default-retention"></a>Retenção padrão do nível do espaço de trabalho

Para definir a retenção padrão para o seu espaço de trabalho, 
 
1. No portal Azure, a partir do seu espaço de trabalho, selecione **Utilização e custos estimados** a partir do painel esquerdo.
2. Na página **Utilização e custos estimados**, clique em **Retenção de Dados**, na parte superior.
3. No painel, mova o controlo de deslize para aumentar ou diminuir o número de dias e clique em **OK**.  Se estiver no nível *livre,* não poderá modificar o período de retenção de dados e terá de atualizar para o nível pago para controlar esta definição.

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="Alterar a definição de retenção de dados do espaço de trabalho":::

Quando a retenção é reduzida, há um período de carência de vários dias antes de os dados mais antigos do que a nova definição de retenção ser removido. 

A página **de Retenção** de Dados permite configurações de retenção de 30, 31, 60, 90, 120, 180, 270, 365, 550 e 730 dias. Se for necessária outra definição, esta pode ser configurada utilizando [o Gestor de Recursos Azure](../samples/resource-manager-workspace.md) utilizando o `retentionInDays` parâmetro. Quando definir a retenção de dados para 30 dias, pode desencadear uma purga imediata de dados mais antigos utilizando o `immediatePurgeDataOn30Days` parâmetro (eliminando o período de carência de vários dias). Isto pode ser útil para cenários relacionados com a conformidade onde a remoção imediata de dados é imperativa. Esta funcionalidade de purga imediata só é exposta através do Azure Resource Manager. 

Os espaços de trabalho com retenção de 30 dias podem conservar dados durante 31 dias. Se for imperativo que os dados sejam conservados por apenas 30 dias, utilize o Gestor de Recursos Azure para definir a retenção para 30 dias e com o `immediatePurgeDataOn30Days` parâmetro.  

Dois tipos de dados `Usage` são `AzureActivity` retidos por um mínimo de 90 dias por defeito, e não há nenhuma taxa para esta retenção de 90 dias. Se a retenção do espaço de trabalho for aumentada acima dos 90 dias, a retenção destes tipos de dados também será aumentada.  Estes tipos de dados também estão isentos de encargos de ingestão de dados. 

Os tipos de dados dos recursos de Insights de Aplicação baseados no espaço de trabalho ( `AppAvailabilityResults` , , e `AppBrowserTimings` - também `AppDependencies` são `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` `AppTraces` mantidos por 90 dias por padrão, e não há nenhuma taxa para esta retenção de 90 dias. A sua retenção pode ser ajustada utilizando a funcionalidade de tipo de dados. 

Tenha em atenção que a [API de remoção](/rest/api/loganalytics/workspacepurge/purge) do Log Analytics não afeta a faturação de retenção e destina-se a ser utilizada em casos muito limitados. Para reduzir a sua conta de retenção, o período de retenção deve ser reduzido quer para o espaço de trabalho, quer para tipos de dados específicos. 

### <a name="retention-by-data-type"></a>Retenção por tipo de dados

Também é possível especificar diferentes definições de retenção para tipos de dados individuais de 4 a 730 dias (exceto espaços de trabalho no nível de preços gratuitos) que substituem a retenção padrão do nível de trabalho. Cada tipo de dados é um sub-recurso do espaço de trabalho. Por exemplo, a tabela SecurityEvent pode ser endereçada no [Gestor de Recursos da Azure](../../azure-resource-manager/management/overview.md) como:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Note que o tipo de dados (tabela) é sensível a casos.  Para obter as definições atuais de retenção por dados de um determinado tipo de dados (neste exemplo SecurityEvent), utilize:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> A retenção só é devolvida para um tipo de dados se a retenção tiver sido explicitamente definida para o mesmo.  Os tipos de dados que não tiveram a retenção explicitamente definida (e assim herdarão a retenção do espaço de trabalho) não devolverão nada desta chamada. 

Para obter as definições atuais de retenção por dados para todos os tipos de dados no seu espaço de trabalho que tiveram o seu conjunto de retenção por dados, apenas omite o tipo de dados específico, por exemplo:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Para definir a retenção de um determinado tipo de dados (neste exemplo SecurityEvent) para 730 dias, faça

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Os valores `retentionInDays` válidos são de 30 a 730.

Os `Usage` tipos e os tipos de `AzureActivity` dados não podem ser definidos com retenção personalizada. Assumirão o máximo de retenção padrão do espaço de trabalho ou 90 dias. 

Uma excelente ferramenta para ligar diretamente ao Azure Resource Manager para definir a retenção por tipo de dados é a ferramenta OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Saiba mais sobre a ARMclient a partir de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes.](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)  Aqui está um exemplo usando o ARMClient, definindo os dados do SecurityEvent para uma retenção de 730 dias:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> A definição de retenção em tipos de dados individuais pode ser usada para reduzir os seus custos de retenção de dados.  Para os dados recolhidos a partir de outubro de 2019 (quando esta funcionalidade foi lançada), a redução da retenção para alguns tipos de dados pode reduzir o custo de retenção ao longo do tempo.  Para os dados recolhidos anteriormente, definir uma retenção mais baixa para um tipo individual não afetará os seus custos de retenção.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gerencie o seu volume máximo de dados diários

Pode configurar uma tampa diária e limitar a ingestão diária para o seu espaço de trabalho, mas use o cuidado pois o seu objetivo não deve ser atingir o limite diário.  Caso contrário, perde dados para o resto do dia, o que pode afetar outros serviços e soluções Azure cuja funcionalidade pode depender da disponibilidade de dados atualizados no espaço de trabalho.  Como resultado, a capacidade de observar e receber alertas das condições de bom funcionamento dos recursos que suportam os serviços de TI é afetada.  A tampa diária destina-se a ser usada como uma forma de gerir um **aumento inesperado** do volume de dados a partir dos seus recursos geridos e permanecer dentro do seu limite, ou quando pretende limitar os encargos não planeados para o seu espaço de trabalho. Não é adequado definir um limite diário para que ele seja atingido, todos os dias, numa área de trabalho.

Cada espaço de trabalho tem a sua tampa diária aplicada numa hora diferente do dia. A hora de reset é mostrada na página **Daily Cap** (ver abaixo). Esta hora de reset não pode ser configurada. 

Logo após o limite diário, a recolha de tipos de dados faturados para para o resto do dia. A latência inerente à aplicação da tampa diária significa que a tampa não é aplicada precisamente ao nível da tampa diária especificada. Um banner de aviso aparece em toda a página para o espaço de trabalho de Log Analytics selecionado e um evento de operação é enviado para a tabela *de operação* na categoria **LogManagement.** A recolha de dados retoma após o tempo de reset definido no *limite diário ser fixado em*. Recomendamos a definição de uma regra de alerta com base neste evento de operação, configurada para notificar quando o limite de dados diário tiver sido atingido (ver [abaixo).](#alert-when-daily-cap-reached) 

> [!NOTE]
> A tampa diária não pode parar a recolha de dados como precisamente o nível de tampa especificado e esperam-se alguns dados em excesso, especialmente se o espaço de trabalho estiver a receber elevados volumes de dados. Veja [abaixo](#view-the-effect-of-the-daily-cap) uma consulta que seja útil no estudo do comportamento da tampa diária. 

> [!WARNING]
> A tampa diária não impede a recolha de tipos de dados WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus, Update and UpdateSummary, com exceção dos espaços de trabalho em que o Azure Security Center foi instalado antes de 19 de junho de 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar que limite de dados diários para definir

[Reveja o Registo de Utilização e os custos estimados](../usage-estimated-costs.md) para compreender a tendência de ingestão de dados e qual é a tampa diária de volume a definir. Deve ser considerado com cuidado, uma vez que não poderá monitorizar os seus recursos após o limite ser atingido. 

### <a name="set-the-daily-cap"></a>Definir o Daily Cap

Os passos seguintes descrevem como configurar um limite para gerir o volume de dados que o espaço de trabalho Log Analytics irá ingerir por dia.  

1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Na página **de Utilização e custos estimados** para o espaço de trabalho selecionado, clique em **Data Cap** a partir do topo da página. 
3. A tampa diária é **OFF** por padrão? clique **em ON** para o ativar e, em seguida, desa estale o limite de volume de dados em GB/dia.

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics configurar limite de dados":::
    
A tampa diária pode ser configurada via ARM definindo o parâmetro sob o `dailyQuotaGb` descrito nos Espaços de Trabalho - Criar ou `WorkspaceCapping` [Atualizar](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

### <a name="view-the-effect-of-the-daily-cap"></a>Ver o efeito do Daily Cap

Para ver o efeito da tampa diária, é importante ter em conta os tipos de dados de segurança não incluídos na tampa diária e a hora de reset para o seu espaço de trabalho. A hora diária de reset da tampa é visível na página **Daily Cap.**  A seguinte consulta pode ser usada para rastrear os volumes de dados sujeitos à Tampa Diária entre resets diários da tampa. Neste exemplo, a hora de reset do espaço de trabalho é às 14:00.  Terá de atualizar isto para o seu espaço de trabalho.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

(No tipo de dados de utilização, as unidades de `Quantity` estão em MB.)

### <a name="alert-when-daily-cap-reached"></a>Alerta quando a Daily Cap chegou

Embora apresentemos uma sugestão visual no portal Azure quando o limiar do limite de dados é cumprido, este comportamento não se alinha necessariamente com a forma como gere problemas operacionais que requerem atenção imediata.  Para receber uma notificação de alerta, pode criar uma nova regra de alerta no Azure Monitor.  Para saber mais, veja [como criar, ver e gerir alertas.](../alerts/alerts-metric.md)

Para começar, aqui estão as definições recomendadas para a consulta de alerta da `Operation` tabela utilizando a `_LogOperation` função. 

- Destino: Selecione o seu recurso Log Analytics
- Critérios: 
   - Nome do sinal: Pesquisa de registo personalizado
   - Consulta de pesquisa: `_LogOperation | where Operation == "Data Collection Status" | where Detail contains "OverQuota"`
   - Com base em: Número de resultados
   - Condição: Maior do que
   - Limiar: 0
   - Período: 5 (minutos)
   - Frequência: 5 (minutos)
- Nome da regra de alerta: Limite de dados diário atingido
- Gravidade: Aviso (Sev 1)

Uma vez definido o alerta e o limite é atingido, um alerta é acionado e executa a resposta definida no Grupo de Ação. Pode notificar a sua equipa através de mensagens de correio eletrónico e de texto, ou automatizar ações utilizando webhooks, livros de automação ou [integração com uma solução ITSM externa.](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts) 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Resolver o motivo pelo qual a utilização é superior ao esperado

A utilização superior deve-se a um ou a ambos os motivos abaixo:
- Mais nódes do que o esperado enviando dados para log analytics espaço de trabalho
- Mais dados do que o esperado enviados para o espaço de trabalho do Log Analytics (talvez devido ao início da utilização de uma nova solução ou de uma alteração de configuração para uma solução existente)

## <a name="understanding-nodes-sending-data"></a>Compreender os nosdes que enviam dados

Para entender o número de nós que reportam batimentos cardíacos do agente todos os dias no último mês, use

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
A conta de nó de nos dois que enviam dados nas últimas 24 horas utiliza a consulta: 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Para obter uma lista de nós que enviam quaisquer dados (e a quantidade de dados enviados por cada) pode ser utilizada a seguinte consulta:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Nódes faturados pelo nível de preços de Per Node

O [legado Per Node preços de preços de preços](#legacy-pricing-tiers) de nós com granularidade horária e também não conta nós apenas enviando um conjunto de tipos de dados de segurança. A sua contagem diária de nódes seria próxima da seguinte consulta:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

O número de unidades na sua conta está em unidades de node*meses que são representadas na `billableNodeMonthsPerDay` consulta. Se o espaço de trabalho tiver a solução de Gestão de Atualização instalada, adicione os tipos de dados Update e UpdateSummary à lista na seguinte cláusula na consulta acima. Finalmente, existe alguma complexidade adicional no algoritmo de faturação real quando é usado o alvo de solução que não está representado na consulta acima. 


> [!TIP]
> Utilize estas `find` consultas com moderação, uma vez que as verificações em todos os tipos de dados são [intensivas](../log-query/query-optimization.md#query-performance-pane) em recursos para executar. Se não necessitar de resultados **por computador,** consulte o tipo de dados de utilização (ver abaixo).

## <a name="understanding-ingested-data-volume"></a>Compreender o volume de dados ingerido

Na página **De Utilização e Custos Estimados,** o gráfico de ingestão de *dados por solução* mostra o volume total de dados enviados e quanto está a ser enviado por cada solução. Isto permite-lhe determinar tendências como se o uso global de dados (ou utilização por uma determinada solução) está a crescer, permanecendo estável ou diminuindo. 

### <a name="data-volume-for-specific-events"></a>Volume de dados para eventos específicos

Para ver a dimensão dos dados ingeridos para um determinado conjunto de eventos, pode consultar a tabela específica (neste `Event` exemplo) e, em seguida, restringir a consulta aos eventos de interesse (neste exemplo, iD 5145 ou 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Note que a cláusula `where _IsBillable = true` filtra tipos de dados de determinadas soluções para as quais não há carga de ingestão. [Saiba mais](./log-standard-columns.md#_isbillable) `_IsBillable` sobre.

### <a name="data-volume-by-solution"></a>Volume de dados por solução

A consulta utilizada para visualizar o volume de dados facturante por solução ao longo do último mês (excluindo o último dia parcial) é:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

A cláusula com `TimeGenerated` é apenas para garantir que a experiência de consulta no portal Azure olhará para trás para além do padrão 24 horas. Ao utilizar o tipo de dados de `StartTime` utilização, e representar os `EndTime` baldes de tempo para os quais os resultados são apresentados. 

### <a name="data-volume-by-type"></a>Volume de dados por tipo

Pode perfurar mais para ver as tendências de dados por tipo de dados:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

Ou ver uma mesa por solução e tipo para o último mês,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

O `Usage` tipo de dados não inclui informação a nível do computador. Para ver o **tamanho** dos dados ingeridos por computador, utilize a `_BilledSize` [propriedade,](./log-standard-columns.md#_billedsize)que fornece o tamanho dos bytes:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

A `_IsBillable` [propriedade](./log-standard-columns.md#_isbillable) especifica se os dados ingeridos incorrerão em encargos. 

Para ver a **contagem** de eventos faturados por computador, use 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> Utilize estas `find` consultas com moderação, uma vez que as verificações em todos os tipos de dados são [intensivas](../log-query/query-optimization.md#query-performance-pane) em recursos para executar. Se não necessitar de resultados **por computador,** então questione o tipo de dados de utilização.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recurso Azure, grupo de recursos ou subscrição

Para dados de nójados alojados no Azure, pode obter o **tamanho** dos dados ingeridos __por computador,__ utilize a [propriedade](./log-standard-columns.md#_resourceid)_ResourceId, que fornece todo o caminho para o recurso:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

Para dados de nójados hospedados no Azure, você pode obter o **tamanho** de dados ingeridos __por subscrição Azure,__ use a `_SubscriptionId` propriedade como:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| summarize BillableDataBytes = sum(BillableDataBytes) by _SubscriptionId | sort by BillableDataBytes nulls last
```

Para obter volume de dados por grupo de recursos, pode `_ResourceId` analisar:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Também pode analisar o `_ResourceId` mais completo, se necessário, usando

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Utilize estas `find` consultas com moderação, uma vez que as verificações em todos os tipos de dados são [intensivas](../log-query/query-optimization.md#query-performance-pane) em recursos para executar. Se não necessitar de resultados por subscrição, grupo de resouce ou nome de recurso, então consultar o tipo de dados de utilização.

> [!WARNING]
> Alguns dos campos do tipo de dados de utilização, ainda no esquema, foram depreciados e os seus valores deixarão de ser povoados. Estes são **Computador,** bem como campos relacionados com a ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla,** **BatchesCapped** e **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Consulta para tipos comuns de dados

Para aprofundar a fonte de dados para um determinado tipo de dados, aqui estão algumas consultas de exemplo úteis:

+ **Recursos de Insights de Aplicação baseados no espaço de trabalho**
  - saiba mais na [Gestão de utilização e custos para Insights de Aplicação](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
+ Solução de **Segurança**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solução de **Gestão de Registos**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de dados de **Desempenho**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de dados de **Evento**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de dados de **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de dados de **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>Sugestões para reduzir o volume de dados

Algumas sugestões para reduzir o volume de registos recolhidos incluem:

| Origem do volume de dados elevado | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Informações de Contentores         | [Configure a Informação do Contentor](../insights/container-insights-cost.md#controlling-ingestion-to-reduce-cost) para recolher apenas os dados necessários. |
| Eventos de segurança            | Selecione [eventos de segurança comuns ou mínimos](../../security-center/security-center-enable-data-collection.md#data-collection-tier) <br> Altere a política de auditoria de segurança para recolher apenas os eventos necessários. Em particular, reveja a necessidade de recolher eventos para <br> - [auditar a plataforma de filtragem](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [auditar o registo](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditar o sistema de ficheiros](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditar o objeto de kernel](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditar a manipulação de identificadores](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento amovível |
| Contadores de desempenho       | Altere a [configuração do contador de desempenho](../agents/data-sources-performance-counters.md) para: <br> - Reduzir a frequência da recolha <br> - Reduzir o número de contadores de desempenho |
| Registos de eventos                 | Altere a [configuração do registo de eventos](../agents/data-sources-windows-events.md) para: <br> - Reduzir o número de registos de eventos recolhidos <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* |
| Syslog                     | Altere a [configuração do syslog](../agents/data-sources-syslog.md) para: <br> - Reduzir o número de instalações recolhidas <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* e *Depuração* |
| AzureDiagnostics           | Alterar [a recolha de registos de recursos](../essentials/diagnostic-settings.md#create-in-azure-portal) para: <br> - Reduzir o número de registos de envio de recursos do Log Analytics <br> - Recolher apenas registos necessários |
| Dados de solução de computadores que não precisam da solução | Utilize [soluções direcionadas](../insights/solution-targeting.md) para recolher dados de grupos de computadores apenas necessários. |
| Application Insights | Opções de revisão para [https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume](managing Application Insights data volume) |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Utilize [o Set-AzSqlServerAudit](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit) para sintonizar as definições de auditoria. |
| Azure Sentinel | Reveja quaisquer [fontes de dados do Sentinel](https://docs.microsoft.com/azure/sentinel/connect-data-sources) que tenha ativado recentemente como fontes de volume de dados adicionais. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Receber os nós como faturado no nível de preços per nódoa

Para obter uma lista de computadores que serão faturados como nódos se o espaço de trabalho estiver no nível de preços de Per Node, procure nosmos que estejam a enviar **tipos de dados faturados** (alguns tipos de dados são gratuitos). Para isso, use a `_IsBillable` [propriedade](./log-standard-columns.md#_isbillable) e use o campo mais à esquerda do nome de domínio totalmente qualificado. Isto devolve a contagem de computadores com dados faturados por hora (que é a granularidade em que os nóns são contados e faturados):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Obtenção de contagens de nó de segurança e automação

Para ver o número de nós de segurança distintos, pode utilizar a consulta:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Para ver o número de nós de automatização distintos, utilize a consulta:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Avaliação do nível de preços por nó

A decisão de saber se os espaços de trabalho com acesso ao nível de preços **por nó** legado estão melhor fora desse nível ou num nível atual **de Pay-As-You-Go** ou **reserva de capacidade** é muitas vezes difícil para os clientes avaliarem.  Isto implica compreender a compensação entre o custo fixo por nó monitorizado no nível de preços per nódoa e a sua atribuição de dados incluída de 500 MB/nó/dia e o custo de apenas pagar os dados ingeridos no nível Pay-As-You-Go (Per GB). 

Para facilitar esta avaliação, a seguinte consulta pode ser usada para fazer uma recomendação para o nível de preços ideal com base nos padrões de utilização de um espaço de trabalho.  Esta consulta analisa os nós monitorizados e os dados ingeridos num espaço de trabalho nos últimos 7 dias, e para cada dia avalia qual o nível de preços que teria sido o ideal. Para utilizar a consulta, precisa de especificar

1. se o espaço de trabalho está a utilizar o Azure Security Center, definindo `workspaceHasSecurityCenter` `true` para, `false` 
2. atualizar os preços se tiver descontos específicos, e
3. especificar o número de dias para olhar para trás e analisar por definição `daysToEvaluate` . Isto é útil se a consulta estiver a demorar muito tempo a tentar olhar para 7 dias de dados. 

Aqui está a consulta de recomendação de nível de preços:

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/en-us/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Esta consulta não é uma réplica exata de como a utilização é calculada, mas funcionará para fornecer recomendações de nível de preços na maioria dos casos.  

> [!NOTE]
> Para utilizar os direitos que vêm da compra da Suite OMS E1, da Suite OMS E2 ou da Add-On OMS para o System Center, escolha o nível de preços do Log Analytics *Per Node.*

## <a name="create-an-alert-when-data-collection-is-high"></a>Crie um alerta quando a recolha de dados é alta

Esta secção descreve como criar um alerta o volume de dados nas últimas 24 horas excedeu uma quantidade especificada, utilizando [alertas de registo](../alerts/alerts-unified-log.md)do monitor Azure . 

Para alertar se o volume de dados faturado nas últimas 24 horas foi superior a 50 GB, siga estes passos: 

- **Definir condição de alerta** especifique a sua área de trabalho do Log Analytics como o destino de recursos.
- **Critérios de alerta** especifique o seguinte:
   - **Nome do Sinal** selecione **Pesquisa de registos personalizada**
   - **Consulta de pesquisa** para `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Se quiser um diferente 
   - A **Lógica de alerta** é **Baseada no** *número de resultados* e a **Condição** é *Maior do que* um **Limiar** de *0*
   - **Período** de tempo de *1440* minutos e **frequência de alerta** a cada *1440* minutos para ser executado uma vez por dia.
- **Definir detalhes do alerta** especifique o seguinte:
   - **Nome** para *volume de dados fatural superior a 50 GB em 24 horas*
   - A **gravidade** como *Aviso*

Especifique um existente ou crie um novo [Grupo de Ação](../alerts/action-groups.md), para que quando o alerta de registo corresponda aos critérios, seja notificado.

Quando receber um alerta, utilize os passos nas secções acima sobre como resolver problemas por que o uso é maior do que o esperado.

## <a name="data-transfer-charges-using-log-analytics"></a>Taxas de transferência de dados usando Log Analytics

O envio de dados para o Log Analytics pode incorrer em cargas de largura de banda de dados, no entanto, limita-se a Máquinas Virtuais onde um agente do Log Analytics está instalado e não se aplica quando se utiliza configurações de Diagnóstico ou com outros conectores que são incorporados no Azure Sentinel. Conforme descrito na página de preços da [largura de banda Azure, transferência de](https://azure.microsoft.com/pricing/details/bandwidth/)dados entre os serviços Azure localizados em duas regiões cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, esta taxa é muito pequena (poucas %) em comparação com os custos para a ingestão de dados do Log Analytics. Consequentemente, o controlo dos custos para o Log Analytics tem de se concentrar no seu [volume de dados ingerido.](#understanding-ingested-data-volume) 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Resolução de problemas por que o Log Analytics já não está a recolher dados

Se estiver no nível de preços gratuitos e tiver enviado mais de 500 MB de dados num dia, a recolha de dados para para o resto do dia. Atingir o limite diário é uma razão comum para que o Log Analytics deixe de recolher dados, ou os dados parecem estar em falta.  O Log Analytics cria um evento de tipo Operação quando a recolha de dados começa e para. Faça a seguinte consulta em busca de verificar se está a atingir o limite diário e os dados em falta: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a recolha de dados para, o OperationStatus apresenta **Aviso**. Quando a recolha de dados inicia, o OperationStatus apresenta **Com êxito**. A tabela a seguir descreve as razões pelas quais a recolha de dados para e uma ação sugerida para retomar a recolha de dados:  

|A coleção reason para| Solução| 
|-----------------------|---------|
|A tampa diária do seu espaço de trabalho foi alcançada|Aguarde que a recolha reinicie automaticamente ou aumente o limite diário de volume de dados descrito na gestão do volume máximo de dados diários. O tempo de reset da tampa diária é mostra-se na página **Daily Cap.** |
| O seu espaço de trabalho atingiu a [Taxa de Volume de Ingestão de Dados](../service-limits.md#log-analytics-workspaces) | O limite da taxa de volume de ingestão predefinido para os dados enviados dos recursos do Azure através das definições de diagnóstico é de aproximadamente 6 GB/min por área de trabalho. Este é um valor aproximado, pois o tamanho real pode variar entre os tipos de dados, dependendo do tamanho do registo e da taxa de compressão. Este limite não se aplica aos dados enviados a partir de agentes ou da API do Recoletor de Dados. Se enviar dados a uma taxa mais alta para uma única área de trabalho, alguns dados serão removidos e um evento será enviado para a tabela Operação na área de trabalho a cada 6 horas enquanto o limiar continuar a ser excedido. Se o volume de ingestão continuar a exceder o limite da taxa ou se estiver à espera de o atingir em breve, poderá pedir um aumento na área de trabalho ao enviar um e-mail para LAIngestionRate@microsoft.com ou ao abrir um pedido de suporte. O evento que deve procurar e que indica um limite da taxa de ingestão de dados pode ser encontrado pela consulta `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Limite diário do legado Nível de preços gratuitos atingido |Aguarde até ao dia seguinte para que a recolha reinicie automaticamente ou mude para um nível de preços pagos.|
|A subscrição da Azure encontra-se em estado suspenso devido a:<br> Julgamento gratuito terminou<br> Passe de Azure expirou<br> Limite de gastos mensais alcançado (por exemplo, numa subscrição da MSDN ou do Visual Studio)|Converter numa subscrição paga<br> Remover o limite, ou esperar até que o limite reinicie|

Para ser notificado quando a recolha de dados parar, utilize os passos descritos no Alerta *de limite de dados diários* para ser notificado quando a recolha de dados parar. Utilize os passos descritos na [criação de um grupo de ação](../alerts/action-groups.md) para configurar uma ação de e-mail, webhook ou runbook para a regra de alerta. 

## <a name="limits-summary"></a>Resumo dos limites

Existem alguns limites adicionais de Log Analytics, alguns dos quais dependem do nível de preços do Log Analytics. Estes estão documentados nos [limites de subscrição e serviço da Azure, quotas e constrangimentos.](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)


## <a name="next-steps"></a>Passos seguintes

- Consulte [as pesquisas de Registo em Registos monitores Azure](../logs/log-query-overview.md) para aprender a usar o idioma de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
- Utilize os passos descritos em [create a new log alert](../alerts/alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando um critério de pesquisa for cumprido.
- Utilize [soluções direcionadas](../insights/solution-targeting.md) para recolher dados de grupos de computadores apenas necessários.
- Para configurar uma política eficaz de recolha de eventos, reveja [a política de filtragem do Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Altere a [configuração do contador de desempenho](../agents/data-sources-performance-counters.md).
- Para modificar as definições de recolha de eventos, reveja [a configuração do registo de eventos](../agents/data-sources-windows-events.md).
- Para modificar as definições de recolha de syslog, reveja [a configuração do syslog](../agents/data-sources-syslog.md).
- Para modificar as definições de recolha de syslog, reveja [a configuração do syslog](../agents/data-sources-syslog.md).