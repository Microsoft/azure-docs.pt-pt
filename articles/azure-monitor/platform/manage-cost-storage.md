---
title: Gerir a utilização e os custos dos registos do Monitor Azure
description: Aprenda a alterar o plano de preços e gere o volume de dados e a política de retenção para o seu espaço de trabalho Log Analytics no Monitor Azure.
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
ms.date: 05/12/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: ea289dbdf22f76c8ea716acf87b0b1a2da6ef0f9
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196596"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerir o uso e os custos com registos do Monitor Azure

> [!NOTE]
> Este artigo descreve como compreender e controlar os seus custos para os Registos do Monitor Azure. Um artigo relacionado, [monitorização da utilização e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como visualizar o uso e os custos estimados em várias funcionalidades de monitorização do Azure para diferentes modelos de preços. Todos os preços e custos apresentados neste artigo são apenas para fins por exemplo. 

O Azure Monitor Logs foi concebido para escalar e apoiar a recolha, indexação e armazenamento de quantidades massivas de dados por dia a partir de qualquer fonte da sua empresa ou implantada no Azure.  Embora este possa ser um motor principal para a sua organização, a eficiência de custos é, em última análise, o condutor subjacente. Para tal, é importante entender que o custo de um espaço de trabalho log Analytics não se baseia apenas no volume de dados recolhidos, está também dependente do plano selecionado e do tempo que escolheu para armazenar dados gerados a partir das suas fontes conectadas.  

Neste artigo revemos como pode monitorizar proativamente o volume de dados ingerido e o crescimento do armazenamento, e definimos limites para controlar esses custos associados. 

## <a name="pricing-model"></a>Modelo preços

O preço padrão para log Analytics é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para uma maior retenção de dados. O volume de dados é medido como o tamanho dos dados que serão armazenados. Cada espaço de trabalho do Log Analytics é cobrado como um serviço separado e contribui para a fatura para a sua subscrição Azure. A quantidade de ingestão de dados pode ser considerável em função dos seguintes fatores: 

  - Número de soluções de gestão habilitadas e sua configuração
  - Número de VMs monitorizados
  - Tipo de dados recolhidos de cada VM monitorizado 
  
Além do modelo Pay-As-You-Go, o Log Analytics tem níveis de Reserva de **Capacidade** que lhe permitem economizar até 25% em comparação com o preço pay-As-You-Go. O preço da reserva de capacidade permite-lhe comprar uma reserva a partir de 100 GB/dia. Qualquer utilização acima do nível de reserva será faturada à taxa Pay-As-You-Go. Os níveis de Reserva de Capacidade têm um período de compromisso de 31 dias. Durante o período de compromisso, pode mudar para um nível mais elevado de nível de Reserva de Capacidade (que reiniciará o período de compromisso de 31 dias), mas não pode voltar para pay-As-You-Go ou para um nível de Reserva de Capacidade mais baixo até que o período de compromisso esteja terminado. A faturação dos níveis de Reserva de Capacidade é feita diariamente. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços de Pagamento de Log Analytics pay-As-You-Go e de Reserva de Capacidade. 

Em todos os níveis de preços, o volume de dados é calculado a partir de uma representação de cadeia dos dados, uma vez que está preparado para ser armazenado. Várias [propriedades comuns a todos os tipos](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) de dados não estão incluídas no cálculo do tamanho do evento, incluindo, e `_ResourceId` `_ItemId` `_IsBillable` `_BilledSize` .

Além disso, note que algumas soluções, como [o Azure Security Center,](https://azure.microsoft.com/pricing/details/security-center/) [o Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/) e a gestão de [Configuração](https://azure.microsoft.com/pricing/details/automation/) têm os seus próprios modelos de preços. 

### <a name="log-analytics-dedicated-clusters"></a>Clusters dedicados a análise de log analytics

Os Clusters Dedicados ao Log Analytics são coleções de espaços de trabalho num único cluster do Azure Data Explorer gerido para suportar cenários avançados como [chaves geridas pelo cliente.](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys)  Log Analytics Clusters dedicados suportam apenas um modelo de preços de reserva de capacidade a partir de 1000 GB/dia com um desconto de 25% em comparação com os preços pay-As-You-Go. Qualquer utilização acima do nível de reserva será faturada à taxa Pay-As-You-Go. A Reserva de Capacidade do Cluster tem um período de compromisso de 31 dias após o aumento do nível de reserva. Durante o período de compromisso, o nível de reserva de capacidade não pode ser reduzido, mas pode ser aumentado a qualquer momento. Saiba mais sobre a criação de [um Log Analytics Clusters](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) e [associando-lhe espaços de trabalho.](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource)  

O nível de reserva de capacidade do cluster é configurado através de programaticamente com o Gestor de Recursos Azure utilizando o `Capacity` parâmetro em `Sku` baixo . O `Capacity` é especificado em unidades de GB e pode ter valores de 1000 GB/dia ou mais em incrementos de 100 GB/dia. Isto é detalhado [aqui.](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) Se o seu cluster precisar de uma reserva superior a 2000 GB/dia contacte-nos em [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Como a faturação de dados ingeridos é feita ao nível do cluster, os espaços de trabalho associados a um cluster já não têm um nível de preços. As quantidades de dados ingeridas de cada espaço de trabalho associado a um cluster são agregadas para calcular a fatura diária do cluster. Note que as dotações por nó do [Azure Security Center](https://docs.microsoft.com/azure/security-center/) são aplicadas ao nível do espaço de trabalho antes desta agregação de dados agregados em todos os espaços de trabalho do cluster. A retenção de dados ainda é faturada ao nível do espaço de trabalho. Note que a faturação do cluster começa quando o cluster é criado, independentemente de os espaços de trabalho terem sido associados ao cluster. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerir o seu ambiente 

Se ainda não estiver a utilizar registos do Monitor Azure, pode utilizar a calculadora de [preços do Monitor Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização do Log Analytics. Comece por introduzir o "Monitor Azure" na caixa de pesquisa e clicando no azulejo do Monitor Azure resultante. Percorra a página para o Monitor Azure e selecione Log Analytics a partir do dropdown type.  Aqui pode introduzir o número de VMs e o GB de dados que espera recolher de cada VM. Normalmente, 1 a 3 GB de mês de dados é ingerido a partir de um VM azul típico. Se já está a avaliar os Registos do Monitor Do Azure, pode utilizar as suas estatísticas de dados a partir do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorizados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e o volume de dados que o seu espaço de [trabalho está a ingerir](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Compreenda os seus custos de utilização e estimativa

Se está a usar registos do Monitor Azure agora, é fácil entender quais os custos que provavelmente são baseados em padrões de utilização recentes. Para tal, utilize o Uso de **Log Analytics e custos estimados** para rever e analisar a utilização de dados. Isto mostra quanto supõe quanto é recolhido por cada solução, quanto supõe a retenção de dados e uma estimativa dos seus custos com base na quantidade de dados ingeridos e em qualquer retenção adicional para além do valor incluído.

![Utilização e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar os seus dados com mais detalhes, clique no ícone no direito superior de qualquer uma das tabelas na página **De utilização e Custos Estimados.** Agora pode trabalhar com esta consulta para explorar mais detalhes do seu uso.  

![Vista de registos](media/manage-cost-storage/logs.png)

A partir da página **De utilização e Custos Estimados** pode rever o volume de dados do mês. Isto inclui todos os dados recebidos e retidos no seu espaço de trabalho Log Analytics.  Clique em detalhes de **Utilização** a partir do topo da página, para ver o painel de utilização com informações sobre as tendências de volume de dados por fonte, computadores e oferta. Para visualizar e definir uma tampa diária ou para modificar o período de retenção, clique na gestão do **volume de dados.**
 
As taxas de Log Analytics são adicionadas à sua conta Azure. Pode ver detalhes da sua conta Azure no âmbito da secção de faturação do portal Azure ou no Portal de [Faturação Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualização do uso de Log Analytics na sua conta Azure 

O Azure fornece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Por exemplo, a funcionalidade "Análise de Custos" permite-lhe visualizar os seus gastos com recursos Azure. Em primeiro lugar, adicione um filtro por "Tipo de Recurso" (para microsoft.operationalinsights/workspace para Log Analytics e microsoft.operationalinsights/workspace para Log Analytics Clusters) permitir-lhe-á acompanhar os gastos do Log Analytics. Em seguida, para "Group by" selecione "Categoria Meter" ou "Meter".  Note que outros serviços como o Azure Security Center e o Azure Sentinel também faturam o seu uso contra os recursos do espaço de trabalho Log Analytics. Para ver o mapeamento para o nome de serviço, pode selecionar a vista tabela em vez de um gráfico. 

Mais compreensão do seu uso pode ser obtida [baixando o seu uso a partir do portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na folha de cálculo descarregada pode ver o uso por recurso Azure (por exemplo, log Analytics workspace) por dia. Nesta folha de cálculo do Excel, o uso dos seus espaços de trabalho Log Analytics pode ser encontrado através da primeira filtragem na coluna "Categoria Meter" para mostrar "Log Analytics", "Insights and Analytics" (usado por alguns dos níveis de preços legados) e "Monitor Azure" (utilizado pelos níveis de preços da Reserva de Capacidade), e depois adicionar um filtro na coluna "Hora de Identificação" que é "contém espaço de trabalho" ou "contém cluster" (este último para incluir o cluster de log analíticos). A utilização é mostrada na coluna "Quantidade Consumida" e a unidade para cada entrada é mostrada na coluna "Unidade de Medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender a sua conta Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Alteração do nível de preços

Para alterar o nível de preços do Log Analytics do seu espaço de trabalho, 

1. No portal Azure, abra **o Uso e os custos estimados** do seu espaço de trabalho onde verá uma lista de cada um dos níveis de preços disponíveis para este espaço de trabalho.

2. Reveja os custos estimados para cada um dos níveis de preços. Esta estimativa baseia-se nos últimos 31 dias de utilização, pelo que esta estimativa de custos depende dos últimos 31 dias sendo representativos do seu uso típico. No exemplo abaixo pode ver como, com base nos padrões de dados dos últimos 31 dias, este espaço de trabalho custaria menos no nível Pay-As-You-Go (#1) em comparação com o nível de Reserva de Capacidade de 100 GB/dia (#2).  

    ![Escalões de preço](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Depois de rever os custos estimados com base nos últimos 31 dias de utilização, se decidir alterar o nível de preços, clique em **Select**.  

Também pode [definir o nível](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) de preços através do Gestor de Recursos Azure utilizando o parâmetro (no modelo De gestor de recursos `sku` `pricingTier` Azure). 

## <a name="legacy-pricing-tiers"></a>Níveis de preços legados

As subscrições que tenham um recurso Log Analytics ou Application Insights nele antes de 2 de abril de 2018, ou estão ligadas a um Acordo de Empresa iniciado antes de 1 de fevereiro de 2019, continuarão a ter acesso à utilização dos níveis de preços legados: **Free,** **Standalone (Per GB)** e **Per Node (OMS)**.  Os espaços de trabalho no nível de preços gratuitos terão uma ingestão diária de dados limitada a 500 MB (exceto os tipos de dados de segurança recolhidos pelo Azure Security Center) e a retenção de dados é limitada a 7 dias. O nível de preços gratuitos destina-se apenas a fins de avaliação. Os espaços de trabalho nos níveis de preços Autónomos ou Por Nó têm retenção configurável pelo utilizador de 30 a 730 dias.

O nível de preços Per Nó por VM monitorizado (nó) numa hora de granularidade. Para cada nó monitorizado, o espaço de trabalho é atribuído 500 MB de dados por dia que não são faturados. Esta dotação é agregada a nível do espaço de trabalho. Os dados ingeridos acima da alocação diária de dados agregados são faturados por GB como excesso de dados. Note que na sua conta, o serviço será **Insight e Analytics** para o uso de Log Analytics se o espaço de trabalho estiver no nível de preços Per Nó. 

> [!TIP]
> Se o seu espaço de trabalho tiver acesso ao nível de preços **Per Node,** mas está a pensar se custaria menos num nível Pay-As-You-Go, pode [usar a consulta abaixo](#evaluating-the-legacy-per-node-pricing-tier) para obter facilmente uma recomendação. 

Os espaços de trabalho criados antes de abril de 2016 também podem aceder aos níveis de preços **Standard** e **Premium** originais que têm uma retenção fixa de dados de 30 e 365 dias, respectivamente. Não podem ser criados novos espaços de trabalho nos níveis de preços **Standard** ou **Premium,** e se um espaço de trabalho for deslocado para fora destes níveis, não poderá ser transferido para trás. 

Mais detalhes sobre as limitações do nível de preços estão disponíveis [aqui.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)

> [!NOTE]
> Para utilizar os direitos que provêm da compra da Suite OMS E1, Da Suite OMS E2 ou do Add-On OMS para system center, escolha o nível de preços log Analytics *Per Node.*

## <a name="change-the-data-retention-period"></a>Change the data retention period (Alterar o período de retenção de dados)

Os seguintes passos descrevem como configurar o tempo de conservação dos dados de registo no seu espaço de trabalho. A retenção de dados pode ser configurada de 30 a 730 dias (2 anos) para todos os espaços de trabalho, a menos que estejam a utilizar o nível de preços gratuitos. [Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços para uma maior retenção de dados. 

### <a name="default-retention"></a>Retenção por defeito

Para definir a retenção padrão para o seu espaço de trabalho, 
 
1. No portal Azure, a partir do seu espaço de trabalho, selecione **Utilização e custos estimados** do painel esquerdo.
2. Na página **Utilização e custos estimados**, clique em **Gestão de volumes de dados**, na parte superior.
3. No painel, mova o slider para aumentar ou diminuir o número de dias e, em seguida, clique **OK**.  Se estiver no nível *livre,* não poderá modificar o período de retenção de dados e terá de fazer upgrade para o nível pago para controlar esta definição.

    ![Alterar a definição de retenção de dados do espaço de trabalho](media/manage-cost-storage/manage-cost-change-retention-01.png)

Quando a retenção é reduzida, há um período de carência de vários dias antes de os dados mais antigos forem removidos. 
    
A retenção também pode ser definida através do Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) utilizando o `retentionInDays` parâmetro. Além disso, se definir a retenção de dados para 30 dias, pode desencadear uma purga imediata de dados mais antigos usando o parâmetro, o `immediatePurgeDataOn30Days` que pode ser útil para cenários relacionados com conformidade. Esta funcionalidade só é exposta através do Gestor de Recursos Azure. 


Dois tipos de dados `Usage` `AzureActivity` e.são retidos por 90 dias por padrão, e não há nenhum custo para esta retenção de 90 dias. Estes tipos de dados também estão isentos de taxas de ingestão de dados. 

Os tipos de dados dos recursos de Aplicação Insights baseados no espaço de trabalho , , , , `AppAvailabilityResults` `AppBrowserTimings` , e também `AppDependencies` são `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` `AppTraces` retidos por 90 dias por padrão, e não há qualquer custo para esta retenção de 90 dias. A sua retenção pode ser ajustada utilizando a retenção através da funcionalidade do tipo de dados. 


### <a name="retention-by-data-type"></a>Retenção por tipo de dados

Também é possível especificar diferentes definições de retenção para tipos de dados individuais de 30 a 730 dias (exceto para espaços de trabalho no nível de preços gratuitos legado). Cada tipo de dados é um subrecurso do espaço de trabalho. Por exemplo, a tabela SecurityEvent pode ser abordada no Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) como:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Note que o tipo de dados (tabela) é sensível ao caso.  Para obter as definições de retenção de tipo de dados de um determinado tipo de dados (neste exemplo SecurityEvent), use:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Para obter as definições de retenção de tipos de dados para todos os tipos de dados no seu espaço de trabalho, basta omitir o tipo de dados específico, por exemplo:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Para definir a retenção de um determinado tipo de dados (neste exemplo SecurityEvent) para 730 dias, fazer

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Valores válidos para `retentionInDays` são de 30 a 730.

Os `Usage` tipos e dados não podem ser `AzureActivity` definidos com retenção personalizada. Assumirão o máximo da retenção padrão do espaço de trabalho ou 90 dias. 

Uma excelente ferramenta para ligar diretamente ao Gestor de Recursos Azure para definir a retenção por tipo de dados é a ferramenta OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Saiba mais sobre armclient a partir de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes.](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)  Aqui está um exemplo usando armClient, definindo os dados do SecurityEvent para uma retenção de 730 dias:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> A definição de retenção em tipos de dados individuais pode ser utilizada para reduzir os seus custos de retenção de dados.  Para os dados recolhidos a partir de outubro de 2019 (quando esta funcionalidade foi lançada), a redução da retenção de alguns tipos de dados pode reduzir o seu custo de retenção ao longo do tempo.  Para os dados recolhidos anteriormente, a definição de uma menor retenção para um tipo individual não afetará os seus custos de retenção.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gerencie o seu volume máximo de dados diários

Pode configurar uma tampa diária e limitar a ingestão diária para o seu espaço de trabalho, mas use os cuidados, uma vez que o seu objetivo não deve ser atingir o limite diário.  Caso contrário, perde dados para o resto do dia, o que pode afetar outros serviços e soluções Azure cuja funcionalidade pode depender de dados atualizados disponíveis no espaço de trabalho.  Como resultado, a sua capacidade de observar e receber alertas quando as condições de saúde dos recursos de apoio aos serviços de TI são impactadas.  A tampa diária destina-se a ser usada como forma de gerir o aumento inesperado do volume de dados dos seus recursos geridos e permanecer dentro do seu limite, ou quando pretende limitar as tarifas não planeadas para o seu espaço de trabalho.  

Logo após o limite diário ser atingido, a recolha de tipos de dados faturados para durante o resto do dia. (A latência inerente à aplicação da tampa diária pode significar que a tampa não é aplicada precisamente como precisamente o nível de tampa diária especificado.) Um banner de aviso aparece na parte superior da página para o espaço de trabalho selecionado log Analytics e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement.** A recolha de dados retoma após o tempo de reset definido no *limite diário será fixado em*. Recomendamos a definição de uma regra de alerta com base neste evento de operação, configurado para notificar quando o limite diário de dados tiver sido atingido. 

> [!WARNING]
> A tampa diária não impede a recolha de dados do Azure Security Center, exceto para espaços de trabalho em que o Azure Security Center foi instalado antes de 19 de junho de 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite de dados diários a definir

Reveja o uso do [Log Analytics e os custos estimados](usage-estimated-costs.md) para entender a tendência de ingestão de dados e qual é a tampa de volume diária para definir. Deve ser considerado com cuidado, já que ganhou?não pode monitorizar os seus recursos após o limite ser atingido. 

### <a name="set-the-daily-cap"></a>Definir o boné diário

Os seguintes passos descrevem como configurar um limite para gerir o volume de dados que o espaço de trabalho do Log Analytics irá ingerir por dia.  

1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Na página **de Utilização e custos estimados** para o espaço de trabalho selecionado, clique na **gestão** do volume de dados a partir do topo da página. 
3. A tampa diária é **OFF** por defeito? clique **em ON** para o ativar e, em seguida, definir o limite de volume de dados em GB/dia.

    ![Log Analytics configurar limite de dados](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alerta quando o Daily Cap chegou

Embora apresentemos uma pista visual no portal Azure quando o seu limite de dados é cumprido, este comportamento não se alinha necessariamente com a forma como gere questões operacionais que requerem atenção imediata.  Para receber uma notificação de alerta, pode criar uma nova regra de alerta no Monitor Azure.  Para saber mais, veja [como criar, visualizar e gerir alertas.](alerts-metric.md)

Para começar, aqui estão as definições recomendadas para o alerta:

- Alvo: Selecione o seu recurso Log Analytics
- Critérios: 
   - Nome do sinal: Pesquisa de registo personalizado
   - Consulta de pesquisa: Operação [ Operação ] onde o Detalhe tem 'OverQuota'
   - Com base em: Número de resultados
   - Condição: Maior do que
   - Limiar: 0
   - Período: 5 (minutos)
   - Frequência: 5 (minutos)
- Nome da regra do alerta: Limite de dados diários atingido
- Gravidade: Aviso (Sev 1)

Uma vez definido o alerta e o limite atingido, é desencadeado um alerta e executa a resposta definida no Grupo de Ação. Pode notificar a sua equipa através de mensagens de e-mail e texto, ou automatizar ações utilizando webhooks, livros de execução automation ou [integrando-se com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Resolver o motivo pelo qual a utilização é superior ao esperado

A utilização superior deve-se a um ou a ambos os motivos abaixo:
- Mais nós do que o esperado envio de dados para log analytics espaço de trabalho
- Mais dados do que o esperado para o espaço de trabalho do Log Analytics (talvez devido a começar a usar uma nova solução ou uma mudança de configuração para uma solução existente)

## <a name="understanding-nodes-sending-data"></a>Compreender os nódosos que enviam dados

Para entender o número de nós que relatam batimentos cardíacos do agente todos os dias no último mês, use

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
A contagem de nódosos que enviam dados nas últimas 24 horas usa a consulta: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Para obter uma lista de nós enviando quaisquer dados (e a quantidade de dados enviados por cada um) a consulta de seguimento pode ser usada:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Utilize estas consultas com moderação, uma vez que as análises através de tipos de `union *` dados são [intensivas em recursos](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) para executar. Se não precisar de resultados **por computador,** consulte o tipo de dados de utilização (ver abaixo).

## <a name="understanding-ingested-data-volume"></a>Compreender o volume de dados ingerido

Na página **de Utilização e Custos Estimados,** o gráfico de *ingestão* de Dados por solução mostra o volume total de dados enviados e quanto está a ser enviado por cada solução. Isto permite determinar tendências como se o uso global de dados (ou o uso por uma determinada solução) está a crescer, a manter-se estável ou a diminuir. 

### <a name="data-volume-for-specific-events"></a>Volume de dados para eventos específicos

Para olhar para a dimensão dos dados ingeridos para um determinado conjunto de eventos, pode consultar a tabela específica (neste `Event` exemplo) e, em seguida, restringir a consulta aos eventos de interesse (neste caso de exemplo ID 5145 ou 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Note que a cláusula filtra os tipos de dados de determinadas soluções para as quais não existe qualquer taxa de `where IsBillable = true` ingestão. 

### <a name="data-volume-by-solution"></a>Volume de dados por solução

A consulta utilizada para visualizar o volume de dados faturado por solução ao longo do último mês (excluindo o último dia parcial) é:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

A cláusula `TimeGenerated` é apenas para garantir que a experiência de consulta no portal Azure olhará para trás para além das 24 horas padrão. Ao utilizar o tipo de dados de utilização `StartTime` e `EndTime` represente os baldes de tempo para os quais os resultados são apresentados. 

### <a name="data-volume-by-type"></a>Volume de dados por tipo

Pode perfurar mais para ver as tendências de dados por tipo de dados:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Ou ver uma tabela por solução e tipo para o último mês,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

O tipo de `Usage` dados não inclui informações a nível do computador. Para ver o **tamanho** dos dados ingeridos por computador, utilize a `_BilledSize` [propriedade,](log-standard-properties.md#_billedsize)que fornece o tamanho em bytes:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [propriedade](log-standard-properties.md#_isbillable) especifica se os dados ingeridos incorrerão em encargos. 

Para ver a **contagem** de eventos faturados ingeridos por computador, use 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

> [!TIP]
> Utilize estas consultas com moderação, uma vez que as análises através de tipos de `union  *` dados são [intensivas em recursos](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) para executar. Se não precisar de resultados **por computador,** então faça consulta no tipo de dados de utilização.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recurso Azure, grupo de recursos ou subscrição

Para obter dados de nós hospedados em Azure pode obter o **tamanho** dos dados ingeridos __por computador,__ utilize a [propriedade](log-standard-properties.md#_resourceid)_ResourceId, que fornece todo o caminho para o recurso:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para obter dados de nós hospedados em Azure pode obter o **tamanho** dos dados ingeridos __por subscrição azure,__ analise a `_ResourceId` propriedade como:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A mudança para mostrar á faturação do volume de dados ingerido pelo grupo de `subscriptionId` recursos `resourceGroup` Azure. 

> [!TIP]
> Utilize estas consultas com moderação, uma vez que as análises através de tipos de `union  *` dados são [intensivas em recursos](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) para executar. Se não precisar de resultados por subscrição, grupo de resouce ou nome de recurso, então questione o tipo de dados de utilização.

> [!WARNING]
> Alguns dos campos do tipo de dados de utilização, ainda que ainda no esquema, foram depreciados e os seus valores deixarão de ser povoados. Estes são **computadores,** bem como campos relacionados com ingestão **(TotalBatches,** **BatchesWithinSla,** **BatchesOutsideSla,** **BatchesCapped** e **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Consulta de tipos comuns de dados

Para aprofundar a fonte de dados para um determinado tipo de dados, aqui estão algumas consultas de exemplo úteis:

+ **Recursos de Insights de Aplicação baseados no espaço de trabalho**
  - aprender mais [aqui](https://docs.microsoft.com/azure/azure-monitor/app/pricing#data-volume-for-workspace-based-application-insights-resources)
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

### <a name="tips-for-reducing-data-volume"></a>Dicas para reduzir o volume de dados

Algumas sugestões para reduzir o volume de registos recolhidos incluem:

| Origem do volume de dados elevado | Como reduzir o volume de dados |
| -------------------------- | ------------------------- |
| Eventos de segurança            | Selecione [eventos de segurança comuns ou mínimos](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Altere a política de auditoria de segurança para recolher apenas os eventos necessários. Em particular, reveja a necessidade de recolher eventos para <br> - [auditar a plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditar o registo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditar o sistema de ficheiros](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditar o objeto de kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditar a manipulação de identificadores](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditoria de armazenamento amovível |
| Contadores de desempenho       | Altere a [configuração do contador de desempenho](data-sources-performance-counters.md) para: <br> - Reduzir a frequência da recolha <br> - Reduzir o número de contadores de desempenho |
| Registos de eventos                 | Altere a [configuração do registo de eventos](data-sources-windows-events.md) para: <br> - Reduzir o número de registos de eventos recolhidos <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* |
| Syslog                     | Altere a [configuração do syslog](data-sources-syslog.md) para: <br> - Reduzir o número de instalações recolhidas <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* e *Depuração* |
| AzureDiagnostics           | Alterar a coleção de registo de recursos para: <br> - Reduzir o número de registos de envio de recursos do Log Analytics <br> - Recolher apenas registos necessários |
| Dados de solução de computadores que não precisam da solução | Utilize [a solução direcionada](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Obter nódosos como faturado no nível de preços Per Nó

Para obter uma lista de computadores que serão faturados como nós se o espaço de trabalho estiver no legado per nó de preços, procure nós que estejam a enviar tipos de **dados faturados** (alguns tipos de dados são gratuitos). Para isso, utilize a `_IsBillable` [propriedade](log-standard-properties.md#_isbillable) e utilize o campo mais à esquerda do nome de domínio totalmente qualificado. Isto devolve a contagem de computadores com dados faturados por hora (que é a granularidade em que os nós são contados e faturados):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Obter o nó de segurança e automação conta

Se estiver no nível de preços "Per node (OMS)", então é cobrado com base no número de nós e soluções que utiliza, o número de nós insights e analíticos para os quais está a ser faturado será mostrado em tabela na página **De utilização e Custo Estimado.**  

Para ver o número de nós de Segurança distintos, pode usar a consulta:

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

Para ver o número de nós de Automação distintos, use a consulta:

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Avaliação do nível de preços do legado Por Nó

A decisão de saber se os espaços de trabalho com acesso ao nível de preços **per nó** do legado estão melhor nesse nível ou num nível atual de **Pay-As-You-Go** ou **De teta de Reserva** de Capacidade é muitas vezes difícil para os clientes avaliarem.  Isto implica compreender a compensação entre o custo fixo por nó monitorizado no nível de preços per nó e a sua atribuição de dados de 500 MB/nó/dia e o custo de apenas pagar os dados ingeridos no nível Pay-As-You-Go (Per GB). 

Para facilitar esta avaliação, a seguinte consulta pode ser usada para fazer uma recomendação para o nível de preços ideal com base nos padrões de utilização de um espaço de trabalho.  Esta consulta analisa os nós e dados monitorizados ingeridos num espaço de trabalho nos últimos 7 dias, e para cada dia avalia qual o nível de preços que teria sido ideal. Para usar a consulta, precisa especificar

1. se o espaço de trabalho está a utilizar o Azure Security Center, definindo `workspaceHasSecurityCenter` `true` `false` ou, 
2. atualizar os preços se tiver descontos específicos, e
3. especificar o número de dias para olhar para trás e analisar definindo `daysToEvaluate` . Isto é útil se a consulta estiver a demorar muito tempo a tentar olhar para 7 dias de dados. 

Aqui está a consulta de recomendação de nível de preços:

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union withsource = tt * 
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
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Esta consulta não é uma réplica exata de como o uso é calculado, mas funcionará para fornecer recomendações de nível de preços na maioria dos casos.  

> [!NOTE]
> Para utilizar os direitos que provêm da compra da Suite OMS E1, Da Suite OMS E2 ou do Add-On OMS para system center, escolha o nível de preços log Analytics *Per Node.*

## <a name="create-an-alert-when-data-collection-is-high"></a>Criar um alerta quando a recolha de dados é alta

Esta secção descreve como criar um alerta se:
- O volume de dados exceder uma determinada quantidade.
- Se previr que o volume de dados vai exceder uma determinada quantidade.

Os Alertas do Azure suportam [registar alertas](alerts-unified-log.md) que utilizam consultas de pesquisa. 

A consulta seguinte tem um resultado quando são recolhidos mais de 100 GB de dados nas últimas 24 horas:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

A consulta seguinte utiliza uma fórmula simples para prever quando é que vão ser enviados mais de 100 GB de dados num dia: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Para alertar relativamente a volumes de dados diferentes, altere o 100 nas consultas para o número de GB para o qual quer receber o alerta.

Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando a recolha de dados for superior ao esperado.

Quando criar o alerta para a primeira consulta – quando existem mais de 100 GB de dados em 24 horas –, defina:  

- **Definir condição de alerta** especifique a sua área de trabalho do Log Analytics como o destino de recursos.
- **Critérios de alerta** especifique o seguinte:
   - **Nome do Sinal** selecione **Pesquisa de registos personalizada**
   - A **consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - A **Lógica de alerta** é **Baseada no** *número de resultados* e a **Condição** é *Maior do que* um **Limiar** de *0*
   - **Período de tempo** de *1440* minutos e **Frequência de alertas** a cada *60* minutos, uma vez que os dados de utilização são atualizados apenas uma vez por hora.
- **Definir detalhes do alerta** especifique o seguinte:
   - O **nome** como *Volume de dados maior do que 100 GB em 24 horas*
   - A **gravidade** como *Aviso*

Especifique um existente ou crie um novo [Grupo de Ação](action-groups.md), para que quando o alerta de registo corresponda aos critérios, seja notificado.

Quando criar o alerta para a segunda consulta – quando se previr que vai haver mais de 100 GB de dados em 24 horas –, defina:

- **Definir condição de alerta** especifique a sua área de trabalho do Log Analytics como o destino de recursos.
- **Critérios de alerta** especifique o seguinte:
   - **Nome do Sinal** selecione **Pesquisa de registos personalizada**
   - A **consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - A **Lógica de alerta** é **Baseada no** *número de resultados* e a **Condição** é *Maior do que* um **Limiar** de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alertas** a cada *60* minutos, uma vez que os dados de utilização são atualizados apenas uma vez por hora.
- **Definir detalhes do alerta** especifique o seguinte:
   - O **nome** como *Previsto volume de dados maior do que 100 GB em 24 horas*
   - A **gravidade** como *Aviso*

Especifique um existente ou crie um novo [Grupo de Ação](action-groups.md), para que quando o alerta de registo corresponda aos critérios, seja notificado.

Quando receber um alerta, utilize os passos da secção seguinte para resolver o motivo pelo qual a utilização é superior ao esperado.

## <a name="data-transfer-charges-using-log-analytics"></a>Encargos de transferência de dados usando Log Analytics

O envio de dados para o Log Analytics pode incorrer em encargos de largura de banda de dados. Tal como descrito na página de preços da [Largura de Banda do Azure,](https://azure.microsoft.com/pricing/details/bandwidth/)a transferência de dados entre os serviços do Azure, localizadoem em duas regiões, cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, esta carga é muito pequena (poucos %) em comparação com os custos de ingestão de dados do Log Analytics. Consequentemente, controlar os custos do Log Analytics tem de se concentrar no volume de dados ingerido, e temos orientações para ajudar a compreender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Resolução de problemas porque é que o Log Analytics já não está a recolher dados

Se estiver no legado Free pricing tier e tiver enviado mais de 500 MB de dados num dia, a recolha de dados para para o resto do dia. Atingir o limite diário é uma razão comum para que o Log Analytics deixe de recolher dados, ou os dados parecem estar em falta.  O Log Analytics cria um evento de tipo Operação quando a recolha de dados começa e para. Faça a seguinte consulta em busca para verificar se está a atingir o limite diário e os dados em falta: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a recolha de dados termina, o Estado de Funcionamento é **Aviso**. Quando a recolha de dados começa, o Estado de Funcionamento é **bem sucedido**. O quadro seguinte descreve as razões pelas quais a recolha de dados para e uma ação sugerida para retomar a recolha de dados:  

|A recolha da razão para| Solução| 
|-----------------------|---------|
|Limite diário do legado Livre de preços ao nível atingido |Aguarde até ao dia seguinte para que a recolha reinicie automaticamente ou mude para um nível de preços pagos.|
|Tampa diária do seu espaço de trabalho foi alcançada|Aguarde que a recolha reinicie automaticamente ou aumente o limite de volume de dados diário descrito na gestão do volume máximo de dados diários. O tempo de reset diário da tampa é mostrado na página de gestão de **volume de dados.** |
|A subscrição do Azure encontra-se em estado suspenso devido a:<br> Julgamento gratuito terminou<br> Passe Azure expirado<br> Limite de gastos mensais atingido (por exemplo, numa subscrição da MSDN ou do Estúdio Visual)|Converter numa subscrição paga<br> Remover limite, ou esperar até que limite resets|

Para ser notificado quando a recolha de dados parar, utilize os passos descritos no alerta de tampa de *dados diária sê-lo* para ser notificado quando a recolha de dados parar. Utilize os passos descritos na [criação](action-groups.md) de um grupo de ação para configurar um e-mail, webhook ou ação de livro de execução para a regra de alerta. 

## <a name="limits-summary"></a>Resumo dos limites

Existem alguns limites adicionais de Log Analytics, alguns dos quais dependem do nível de preços do Log Analytics. Estes estão documentados [aqui.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)


## <a name="next-steps"></a>Passos seguintes

- Consulte [as pesquisas de Registo em Registos do Monitor Azure](../log-query/log-query-overview.md) para aprender a usar o idioma de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
- Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando um critério de pesquisa for cumprido.
- Utilize [a solução direcionada](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários.
- Para configurar uma política eficaz de recolha de eventos, reveja a política de [filtragem do Azure Security Center.](../../security-center/security-center-enable-data-collection.md)
- Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as definições da recolha de eventos, reveja a configuração do registo de [eventos.](data-sources-windows-events.md)
- Para modificar as definições de recolha de syslog, reveja a [configuração do slog](data-sources-syslog.md).