---
title: Gerenciar o uso e os custos de logs de Azure Monitor | Microsoft Docs
description: Saiba como alterar o plano de preços e gerenciar o volume de dados e a política de retenção para seu espaço de trabalho do Log Analytics no Azure Monitor.
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
ms.date: 11/05/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 8c4169ccfb35b74b92ea4996cbc779bac35d6ccb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715858"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerenciar o uso e os custos com logs de Azure Monitor

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para logs de Azure Monitor. Um artigo relacionado, [monitorização da utilização e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como visualizar o uso e os custos estimados em várias funcionalidades de monitorização do Azure para diferentes modelos de preços.

> [!NOTE]
> Todos os preços e custos mostrados neste artigo são apenas para fins de exemplo. 

Os logs de Azure Monitor são projetados para dimensionar e dar suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia de qualquer fonte em sua empresa ou implantados no Azure.  Embora isso possa ser um controlador primário para a sua organização, relação custo-benefício é, por fim, o driver subjacente. Para esse fim, é importante entender que o custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados coletados, mas também depende do plano selecionado e por quanto tempo você optou por armazenar os dados gerados de suas fontes conectadas.  

Neste artigo, examinaremos como você pode monitorar proativamente o crescimento do armazenamento e do volume de dados ingeridos e definir limites para controlar esses custos associados. 

## <a name="pricing-model"></a>Modelo preços

O preço padrão para log Analytics é um modelo **Pay-As-You-Go** baseado no volume de dados ingerido e opcionalmente para uma maior retenção de dados. O volume de dados é medido como o tamanho dos dados que serão armazenados. Cada espaço de trabalho Log Analytics é cobrado como um serviço separado e contribui para a fatura de sua assinatura do Azure. A quantidade de ingestão de dados pode ser considerável dependendo dos seguintes fatores: 

  - Número de soluções de gerenciamento habilitadas e suas configurações (por exemplo, 
  - Número de VMs monitoradas
  - Tipo de dados coletados de cada VM monitorada 
  
Além do modelo Pay-As-You-Go, o Log Analytics tem níveis de Reserva de **Capacidade** que lhe permitem economizar até 25% em comparação com o preço pay-As-You-Go. O preço de reserva de capacidade permite que você compre uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa paga conforme o uso. As camadas de reserva de capacidade têm um período de compromisso de 31 dias. Durante o período de compromisso, você pode alterar para uma camada de reserva de capacidade de nível superior (que reiniciará o período de compromisso de 31 dias), mas não poderá voltar para o pré-pago ou para uma camada de reserva de capacidade mais baixa até que o período de compromisso seja Finaliza. 
[Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre os preços de Pagamento de Log Analytics pay-As-You-Go e de Reserva de Capacidade. 

Em todos os tipos de preço, o volume de dados é calculado a partir de uma representação de cadeia de caracteres dos dados conforme eles são preparados para serem armazenados. Várias [propriedades comuns a todos os tipos](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) de dados não estão incluídas no cálculo do tamanho do evento, incluindo `_ResourceId`, `_ItemId`, `_IsBillable` e `_BilledSize`.

Note ainda que Algumas soluções, como [o Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) e o [Azure Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)têm o seu próprio modelo de preços. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerenciar seu ambiente 

Se ainda não estiver a utilizar registos do Monitor Azure, pode utilizar a calculadora de [preços do Monitor Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo da utilização do Log Analytics. Comece inserindo "Azure Monitor" na caixa de pesquisa e clicando no bloco do Azure Monitor resultante. Role a página para baixo até Azure Monitor e selecione Log Analytics na lista suspensa tipo.  Aqui você pode inserir o número de VMs e os GB de dados que espera coletar de cada VM. Normalmente, 1 a 3 GB de dados de mês é ingerido de uma VM do Azure típica. Se já estiver avaliando os logs de Azure Monitor, você poderá usar suas estatísticas de dados do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitorizados](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e o volume de dados que o seu espaço de [trabalho está a ingerir](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e calcule os custos

Se você estiver usando logs de Azure Monitor agora, é fácil entender quais são os custos mais prováveis com base em padrões de uso recentes. Para tal, utilize o Uso de **Log Analytics e custos estimados** para rever e analisar a utilização de dados. Isso mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa dos custos com base na quantidade de dados ingeridos e qualquer retenção adicional além da quantidade incluída.

![Utilização e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar os seus dados com mais detalhes, clique no ícone no direito superior de qualquer uma das tabelas na página **De utilização e Custos Estimados.** Agora pode trabalhar com esta consulta para explorar mais detalhes da sua utilização.  

![Ver registos](media/manage-cost-storage/logs.png)

A partir da página **De utilização e Custos Estimados** pode rever o volume de dados do mês. Isto inclui todos os dados recebidos e mantidas na sua área de trabalho do Log Analytics.  Clique em detalhes de **Utilização** a partir do topo da página, para ver o painel de utilização com informações sobre as tendências de volume de dados por fonte, computadores e oferta. Para visualizar e definir uma tampa diária ou para modificar o período de retenção, clique na gestão do **volume de dados.**
 
Custos de análise de registo são adicionados à sua fatura do Azure. Pode ver detalhes da sua conta Azure no âmbito da secção de faturação do portal Azure ou no Portal de [Faturação Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Exibindo o uso de Log Analytics em sua fatura do Azure 

O Azure fornece uma grande quantidade de funcionalidades úteis no hub [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Por exemplo, a funcionalidade de "análise de custo" permite que você exiba seus gastos para os recursos do Azure. A adição de um filtro por tipo de recurso (para Microsoft. operationalinsights/Workspace para Log Analytics) permitirá que você acompanhe seus gastos.

Mais compreensão do seu uso pode ser obtida [baixando o seu uso a partir do portal Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso por recurso do Azure (por exemplo, Log Analytics espaço de trabalho) por dia. Nesta planilha do Excel, o uso de seus espaços de trabalho do Log Analytics pode ser encontrado primeiro filtrando a coluna "categoria do medidor" para mostrar "insights e análises" (usado por alguns dos tipos de preço herdados) e "Log Analytics" e, em seguida, adicionar um filtro na "instância do ID "coluna que é" contém espaço de trabalho ". O uso é mostrado na coluna "quantidade consumida" e a unidade de cada entrada é mostrada na coluna "unidade de medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender a sua conta Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Para alterar o tipo de preço Log Analytics do seu espaço de trabalho, 

1. No portal Azure, abra **o Uso e os custos estimados** do seu espaço de trabalho onde verá uma lista de cada um dos níveis de preços disponíveis para este espaço de trabalho.

2. Examine os custos estimados de cada tipo de preço. Essa estimativa se baseia nos últimos 31 dias de uso, portanto, essa estimativa de custo depende dos últimos 31 dias que representam o uso típico. No exemplo abaixo, você pode ver como, com base nos padrões de dados dos últimos 31 dias, esse espaço de trabalho custaria menos na camada pré-paga (#1) em comparação com a camada de reserva de capacidade de 100 GB/dia (#2).  

    ![Escalões de preço](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Depois de rever os custos estimados com base nos últimos 31 dias de utilização, se decidir alterar o nível de preços, clique em **Select**.  

Também pode [definir o nível de preços através](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) do Gestor de Recursos Azure utilizando o parâmetro `sku` (`pricingTier` no modelo Do Gestor de Recursos Azure). 

## <a name="legacy-pricing-tiers"></a>Tipos de preço herdados

As subscrições que tenham um recurso Log Analytics ou Application Insights nele antes de 2 de abril de 2018, ou estão ligadas a um Acordo de Empresa iniciado antes de 1 de fevereiro de 2019, continuarão a ter acesso à utilização dos níveis de preços legados: **Free,** **Standalone (Per GB)** e **Per Node (OMS)** .  Os espaços de trabalho no tipo de preço gratuito terão ingestão de dados diária limitada a 500 MB (exceto os tipos de dados de segurança coletados pela central de segurança do Azure) e a retenção de dados será limitada a 7 dias. O tipo de preço gratuito é destinado apenas para fins de avaliação. Os espaços de trabalho nos tipos de preço autônomos ou por nó têm retenção configurável do usuário de até 2 anos. 

Os espaços de trabalho criados antes de abril de 2016 também podem aceder aos níveis de preços **Standard** e **Premium** originais que têm uma retenção fixa de dados de 30 e 365 dias, respectivamente. Não podem ser criados novos espaços de trabalho nos níveis de preços **Standard** ou **Premium,** e se um espaço de trabalho for deslocado para fora destes níveis, não poderá ser transferido para trás. 

Mais detalhes sobre as limitações do nível de preços estão disponíveis [aqui.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)

> [!NOTE]
> Para utilizar os direitos que provêm da compra da Suite OMS E1, Da Suite OMS E2 ou do Add-On OMS para system center, escolha o nível de preços log Analytics *Per Node.*

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

Os passos seguintes descrevem como configurar o registo quanto dados são mantidos na sua área de trabalho.

### <a name="default-retention"></a>Retenção padrão

Para definir a retenção padrão para seu espaço de trabalho, 
 
1. No portal Azure, a partir do seu espaço de trabalho, selecione **Utilização e custos estimados** do painel esquerdo.
2. Na página **Utilização e custos estimados**, clique em **Gestão de volumes de dados**, na parte superior.
3. No painel, mova o slider para aumentar ou diminuir o número de dias e, em seguida, clique **OK**.  Se estiver no nível *livre,* não poderá modificar o período de retenção de dados e terá de fazer upgrade para o nível pago para controlar esta definição.

    ![Alterar a configuração de retenção de dados do espaço de trabalho](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A retenção também pode ser definida através do Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) utilizando o parâmetro `retentionInDays`. Além disso, se definir a retenção de dados para 30 dias, pode desencadear uma purga imediata de dados mais antigos usando o parâmetro `immediatePurgeDataOn30Days`, o que pode ser útil para cenários relacionados com conformidade. Essa funcionalidade só é exposta por meio de Azure Resource Manager. 

Dois tipos de dados, `Usage` e `AzureActivity`, são retidos por 90 dias por defeito, e não há nenhum custo para esta retenção de 90 dias. Esses tipos de dados também são gratuitos de encargos de ingestão de dados. 

### <a name="retention-by-data-type"></a>Retenção por tipo de dados

Também é possível especificar configurações de retenção diferentes para tipos de dados individuais. Cada tipo de dados é um subrecurso do espaço de trabalho. Por exemplo, a tabela SecurityEvent pode ser abordada no Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) como:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Observe que o tipo de dados (tabela) diferencia maiúsculas de minúsculas.  Para obter as configurações de retenção de tipo de dados atuais por um tipo de dados específico (neste exemplo SecurityEvent), use:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Para obter as configurações atuais de retenção de tipo de dados para todos os tipos de dados em seu espaço de trabalho, basta omitir o tipo de dados específico, por exemplo:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Para definir a retenção de um tipo de dados específico (neste exemplo, SecurityEvent) a 730 dias, faça

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Os tipos de dados `Usage` e `AzureActivity` não podem ser definidos com retenção personalizada. Eles terão o máximo de retenção de espaço de trabalho padrão ou de 90 dias. 

Uma excelente ferramenta para ligar diretamente ao Gestor de Recursos Azure para definir a retenção por tipo de dados é a ferramenta OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Saiba mais sobre armclient a partir de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes.](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)  Aqui está um exemplo usando armClient, definindo os dados do SecurityEvent para uma retenção de 730 dias:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> A configuração da retenção em tipos de dados individuais pode ser usada para reduzir os custos de retenção de dados.  Para os dados coletados a partir de outubro de 2019 (quando esse recurso foi lançado), a redução da retenção para alguns tipos de dados pode reduzir o custo de retenção ao longo do tempo.  Para os dados coletados anteriormente, a definição de uma retenção inferior para um tipo individual não afetará os custos de retenção.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gerenciar seu volume máximo de dados diário

Pode configurar um máximo diário e limitar a ingestão diária da sua área de trabalho, mas usar cuidados de saúde, como o seu objetivo não deve ser atingir o limite diário.  Caso contrário, perde os dados para o resto do dia, que pode afetar outros serviços do Azure e soluções cuja funcionalidade pode depender de dados atualizados estarem disponíveis na área de trabalho.  Como resultado, a sua capacidade de observar e receber alertas quando as condições de estado de funcionamento de recursos que suportam serviços de TI são afetadas.  O limite diário destina-se a ser usado como uma maneira de gerenciar o aumento inesperado no volume de dados de seus recursos gerenciados e permanecer dentro do limite ou quando você quiser limitar os encargos não planejados para seu espaço de trabalho.  

Quando for atingido o limite diário, a coleção de tipos de dados cobrar deixa para o resto do dia. Um banner de aviso aparece na parte superior da página para o espaço de trabalho selecionado log Analytics e um evento de operação é enviado para a tabela *Operação* na categoria **LogManagement.** A recolha de dados retoma após o tempo de reset definido no *limite diário será fixado em*. É recomendável definir uma regra de alerta com base em eventos esta operação, configurado para ser notificado quando for atingido o limite diário de dados. 

> [!NOTE]
> O limite diário não interrompe a coleta de dados da central de segurança do Azure, exceto para espaços de trabalho nos quais a central de segurança do Azure foi instalada antes de 19 de junho de 2017. 

> [!NOTE]
> A latência inerente à aplicação do limite diário pode significar que o limite não é aplicado exatamente ao nível de limite diário especificado. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados para definir

Reveja o uso do [Log Analytics e os custos estimados](usage-estimated-costs.md) para entender a tendência de ingestão de dados e qual é a tampa de volume diária para definir. Deve ser considerado com cuidado, uma vez que não será possível monitorizar os seus recursos após ter sido atingido o limite. 

### <a name="set-the-daily-cap"></a>Definir o limite diário

As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que Log Analytics espaço de trabalho será ingerido por dia.  

1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Na página **de Utilização e custos estimados** para o espaço de trabalho selecionado, clique na **gestão** do volume de dados a partir do topo da página. 
3. A tampa diária é **OFF** por padrão – clique **em ON** para o ativar e, em seguida, definir o limite de volume de dados em GB/dia.

    ![Log Analytics configurar o limite de dados](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alertar quando o limite diário for atingido

Embora, apresentamos uma indicação visual no portal do Azure quando o limiar de limite de dados for cumprido, esse comportamento não necessariamente alinhados com como gerir os problemas operacionais que requerem atenção imediata.  Para receber uma notificação de alerta, pode criar uma nova regra de alerta no Azure Monitor.  Para saber mais, veja [como criar, visualizar e gerir alertas.](alerts-metric.md)

Para começar, seguem-se as definições recomendadas para o alerta:

- Destino: Selecione o recurso do Log Analytics
- Critérios: 
   - Nome do sinal: pesquisa de registos personalizado
   - Consulta de pesquisa: operação | em que detalhes tem 'OverQuota'
   - Com base em: número de resultados
   - Condição: Maior do que
   - Limiar: 0
   - Período: 5 (minutos)
   - Frequência: 5 (minutos)
- Nome da regra de alerta: limite diário de dados foi atingido
- Gravidade: Aviso (gravidade 1)

Assim que o alerta é definido e o limite for atingido, um alerta é acionado e executa a resposta definida no grupo de ação. Pode notificar a sua equipa através de mensagens de e-mail e texto, ou automatizar ações utilizando webhooks, livros de execução automation ou [integrando-se com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Resolver o motivo pelo qual a utilização é superior ao esperado

A utilização superior deve-se a um ou a ambos os motivos abaixo:
- Mais nós do que o esperado enviando dados para Log Analytics espaço de trabalho
- Mais dados do que o esperado que está sendo enviado para Log Analytics espaço de trabalho

## <a name="understanding-nodes-sending-data"></a>Noções básicas sobre nós enviando dados

Para entender o número de computadores que relatam pulsações por dia no último mês, use

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Para obter uma lista de computadores que serão faturados como nós se o espaço de trabalho estiver no legado per nó de preços, procure nós que estejam a enviar tipos de **dados faturados** (alguns tipos de dados são gratuitos). Para isso, utilize a [propriedade](log-standard-properties.md#_isbillable) `_IsBillable` e utilize o campo mais à esquerda do nome de domínio totalmente qualificado. Isso retorna a lista de computadores com dados cobrados:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

A contagem de nós faturáveis vistos pode ser estimada como: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName)
```

> [!NOTE]
> Utilize estas consultas `union withsource = tt *` com moderação, uma vez que as análises através de tipos de dados são caras para executar. Essa consulta substitui a maneira antiga de consultar informações por computador com o tipo de dados de uso.  

Um cálculo mais preciso do que realmente será cobrado é obter a contagem de computadores por hora que estão enviando tipos de dados cobrados. (Para espaços de trabalho no tipo de preço herdado por nó, Log Analytics calcula o número de nós que precisam ser cobrados por hora.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Compreendendo o volume de dados ingeridos

Na página **de Utilização e Custos Estimados,** o gráfico de *ingestão* de Dados por solução mostra o volume total de dados enviados e quanto está a ser enviado por cada solução. Isto permite-lhe determinar as tendências, como se a utilização de dados global (ou a utilização por uma solução específica) está a crescer, permanece estável ou diminui. É a consulta usada para gerar este

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

Tenha em atenção que a cláusula "onde IsBillable = true" filtra tipos de dados de algumas soluções para o qual não é cobrado ingestão. 

Pode explorar mais a ver as tendências de dados para tipos de dados específicos, por exemplo, se quiser estudar os dados devido a registos do IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

Para ver o **tamanho** dos eventos faturados ingeridos por computador, utilize a [propriedade](log-standard-properties.md#_billedsize)`_BilledSize`, que fornece o tamanho em bytes:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A [propriedade](log-standard-properties.md#_isbillable) `_IsBillable` especifica se os dados ingeridos incorrerão em encargos.

Para ver a contagem de eventos **faturados** ingeridos por computador, use 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by eventCount nulls last
```

Se quiser ver contagens para tipos de dados cobrar estão a enviar dados para um computador específico, utilize:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume de dados por recurso do Azure, grupo de recursos ou assinatura

Para obter dados de nós hospedados em Azure pode obter o **tamanho** de eventos faturados ingeridos __por computador,__ utilize a [propriedade](log-standard-properties.md#_resourceid)_ResourceId, que fornece todo o caminho para o recurso:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para obter dados de nós hospedados em Azure pode obter o **tamanho** de eventos faturados ingeridos __por subscrição azure,__ analise a propriedade `_ResourceId` como:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A alteração `subscriptionId` para `resourceGroup` mostrará o volume de dados ingerido pelo grupo de recursos Azure. 


> [!NOTE]
> Alguns dos campos do tipo de dados de uso, enquanto ainda estão no esquema, foram preteridos e seus valores não serão mais populados. Estes são **computadores,** bem como campos relacionados com ingestão **(TotalBatches,** **BatchesWithinSla,** **BatchesOutsideSla,** **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Consultando tipos de dados comuns

Para saber mais sobre a origem de dados para um tipo de dados específico, aqui estão algumas consultas de exemplo úteis:

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
| Eventos de segurança            | Selecione [eventos de segurança comuns ou mínimos](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Altere a política de auditoria de segurança para recolher apenas os eventos necessários. Em particular, reveja a necessidade de recolher eventos para <br> - [auditar a plataforma de filtragem](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditar o registo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditar o sistema de ficheiros](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditar o objeto de kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditar a manipulação de identificadores](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> -Auditar o armazenamento amovível |
| Contadores de desempenho       | Altere a [configuração do contador de desempenho](data-sources-performance-counters.md) para: <br> - Reduzir a frequência da recolha <br> - Reduzir o número de contadores de desempenho |
| Registos de eventos                 | Altere a [configuração do registo de eventos](data-sources-windows-events.md) para: <br> - Reduzir o número de registos de eventos recolhidos <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* |
| Syslog                     | Altere a [configuração do syslog](data-sources-syslog.md) para: <br> - Reduzir o número de instalações recolhidas <br> - Recolher apenas níveis de eventos necessários. Por exemplo, não recolher eventos de nível *Informação* e *Depuração* |
| AzureDiagnostics           | Alterar a coleção de registo de recursos para: <br> - Reduzir o número de registos de envio de recursos do Log Analytics <br> - Recolher apenas registos necessários |
| Dados de solução de computadores que não precisam da solução | Utilize a [segmentação de soluções](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários. |

### <a name="getting-security-and-automation-node-counts"></a>Obtendo contagens de nós de segurança e automação

Se estiver no nível de preços "Per node (OMS)", então é cobrado com base no número de nós e soluções que utiliza, o número de nós insights e analíticos para os quais está a ser faturado será mostrado em tabela na página **De utilização e Custo Estimado.**  

Para ver o número de nós de segurança distintas, pode utilizar a consulta:

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

## <a name="create-an-alert-when-data-collection-is-high"></a>Criar um alerta quando a coleta de dados estiver alta

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
   - **A lógica** de alerta **baseia-se no** *número de resultados* e **a condição** é maior *do que* um **Limiar** de *0*
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
   - **A lógica** de alerta **baseia-se no** *número de resultados* e **a condição** é maior *do que* um **Limiar** de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alertas** a cada *60* minutos, uma vez que os dados de utilização são atualizados apenas uma vez por hora.
- **Definir detalhes do alerta** especifique o seguinte:
   - O **nome** como *Previsto volume de dados maior do que 100 GB em 24 horas*
   - A **gravidade** como *Aviso*

Especifique um existente ou crie um novo [Grupo de Ação](action-groups.md), para que quando o alerta de registo corresponda aos critérios, seja notificado.

Quando receber um alerta, utilize os passos da secção seguinte para resolver o motivo pelo qual a utilização é superior ao esperado.

## <a name="data-transfer-charges-using-log-analytics"></a>Cobranças de transferência de dados usando Log Analytics

Enviar dados para Log Analytics pode incorrer em encargos de largura de banda de dados. Tal como descrito na página de preços da [Largura de Banda do Azure,](https://azure.microsoft.com/pricing/details/bandwidth/)a transferência de dados entre os serviços do Azure, localizadoem em duas regiões, cobradas como transferência de dados de saída à taxa normal. A transferência de dados de entrada é gratuita. No entanto, esta carga é muito pequena (poucos %) em comparação com os custos de ingestão de dados do Log Analytics. Consequentemente, controlar os custos do Log Analytics tem de se concentrar no volume de dados ingerido, e temos orientações para ajudar a compreender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solução de problemas por que o Log Analytics não está mais coletando dados

Se você estiver no tipo de preço gratuito de legado e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida para o restante do dia. Atingir o limite diário é um motivo comum que o Log Analytics interrompe a recolha de dados ou dados parecem estar em falta.  O log Analytics cria um evento do tipo operação quando a recolha de dados é iniciada e interrompida. Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e os dados ausentes: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a recolha de dados termina, o Estado de Funcionamento é **Aviso**. Quando a recolha de dados começa, o Estado de Funcionamento é **bem sucedido**. A tabela seguinte descreve as razões que pára de recolha de dados e uma ação sugerida para retomar a recolha de dados:  

|Deixa de coleção de razão| Solução| 
|-----------------------|---------|
|Limite diário do tipo de preço gratuito de legado atingido |Aguarde até ao dia seguinte para a coleção reiniciar automaticamente ou alterar para um escalão de preço pago.|
|O limite diário do seu espaço de trabalho foi atingido|Aguarde até que a coleção seja reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em gerenciar o volume máximo de dados diário. O tempo de reset diário da tampa é mostrado na página de gestão de **volume de dados.** |
|Subscrição do Azure está num estado suspenso devido a:<br> Versão de avaliação gratuita terminada<br> Passagem do Azure expirou<br> Atingido o limite de gastos mensal (por exemplo, numa assinatura do MSDN ou o Visual Studio)|Converter numa subscrição paga<br> Remover o limite, ou aguarde até que o limite é zerado|

Para ser notificado quando a recolha de dados parar, utilize os passos descritos no alerta de tampa de *dados diária sê-lo* para ser notificado quando a recolha de dados parar. Utilize os passos descritos na [criação](action-groups.md) de um grupo de ação para configurar um e-mail, webhook ou ação de livro de execução para a regra de alerta. 

## <a name="limits-summary"></a>Resumo de limites

Há alguns limites de Log Analytics adicionais, alguns dos quais dependem do tipo de preço Log Analytics. Estes estão documentados [aqui.](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces)


## <a name="next-steps"></a>Passos Seguintes

- Consulte [as pesquisas de Registo em Registos do Monitor Azure](../log-query/log-query-overview.md) para aprender a usar o idioma de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
- Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando um critério de pesquisa for cumprido.
- Utilize a [segmentação de soluções](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários.
- Para configurar uma política eficaz de recolha de eventos, reveja a política de [filtragem do Azure Security Center.](../../security-center/security-center-enable-data-collection.md)
- Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as definições da recolha de eventos, veja [configuração do registo de eventos](data-sources-windows-events.md).
- Para modificar as definições da recolha do syslog, veja [configuração do syslog](data-sources-syslog.md).
