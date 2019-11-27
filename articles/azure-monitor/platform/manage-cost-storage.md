---
title: Gerenciar o uso e os custos de logs de Azure Monitor | Microsoft Docs
description: Saiba como alterar o plano de preços e gerenciar o volume de dados e a política de retenção para seu espaço de trabalho do Log Analytics no Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 1fbd69a095e171ec2fb386bed34b196f723e7496
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278940"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerenciar o uso e os custos com logs de Azure Monitor

> [!NOTE]
> Este artigo descreve como entender e controlar seus custos para logs de Azure Monitor. Um artigo relacionado, [monitoramento de uso e custos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descreve como exibir o uso e os custos estimados em vários recursos de monitoramento do Azure para diferentes modelos de preços.

> [!NOTE]
> Todos os preços e custos mostrados neste artigo são apenas para fins de exemplo. 

Os logs de Azure Monitor são projetados para dimensionar e dar suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia de qualquer fonte em sua empresa ou implantados no Azure.  Embora isso possa ser um controlador primário para a sua organização, relação custo-benefício é, por fim, o driver subjacente. Para esse fim, é importante entender que o custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados coletados, mas também depende do plano selecionado e por quanto tempo você optou por armazenar os dados gerados de suas fontes conectadas.  

Neste artigo, examinaremos como você pode monitorar proativamente o crescimento do armazenamento e do volume de dados ingeridos e definir limites para controlar esses custos associados. 

## <a name="pricing-model"></a>Modelo preços

O preço padrão para Log Analytics é um modelo **pago conforme o uso** com base no volume de dados ingerido e, opcionalmente, para a retenção de dados mais longa. O volume de dados é medido como o tamanho dos dados que serão armazenados. Cada espaço de trabalho Log Analytics é cobrado como um serviço separado e contribui para a fatura de sua assinatura do Azure. A quantidade de ingestão de dados pode ser considerável dependendo dos seguintes fatores: 

  - Número de soluções de gerenciamento habilitadas e suas configurações (por exemplo, 
  - Número de VMs monitoradas
  - Tipo de dados coletados de cada VM monitorada 
  
Além do modelo pago conforme o uso, Log Analytics tem camadas de **reserva de capacidade** que permitem que você economize até 25% em comparação com o preço pago conforme o uso do. O preço de reserva de capacidade permite que você compre uma reserva a partir de 100 GB/dia. Qualquer uso acima do nível de reserva será cobrado com a taxa paga conforme o uso. As camadas de reserva de capacidade têm um período de compromisso de 31 dias. Durante o período de compromisso, você pode alterar para uma camada de reserva de capacidade de nível superior (que reiniciará o período de compromisso de 31 dias), mas não poderá voltar para o pré-pago ou para uma camada de reserva de capacidade mais baixa até que o período de compromisso seja Finaliza. 
[Saiba mais](https://azure.microsoft.com/pricing/details/monitor/) sobre o log Analytics preços pagos conforme o uso e de reserva de capacidade. 

Além disso, observe que algumas soluções, como a [central de segurança do Azure](https://azure.microsoft.com/pricing/details/security-center/) e o [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), têm seu próprio modelo de preços. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimando os custos para gerenciar seu ambiente 

Se você ainda não estiver usando logs de Azure Monitor, poderá usar a [calculadora de preços de Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para estimar o custo do uso de log Analytics. Comece inserindo "Azure Monitor" na caixa de pesquisa e clicando no bloco do Azure Monitor resultante. Role a página para baixo até Azure Monitor e selecione Log Analytics na lista suspensa tipo.  Aqui você pode inserir o número de VMs e os GB de dados que espera coletar de cada VM. Normalmente, 1 a 3 GB de dados de mês é ingerido de uma VM do Azure típica. Se já estiver avaliando os logs de Azure Monitor, você poderá usar suas estatísticas de dados do seu próprio ambiente. Veja abaixo como determinar o [número de VMs monitoradas](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e o [volume de dados que seu espaço de trabalho está ingerindo](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Entenda seu uso e calcule os custos

Se você estiver usando logs de Azure Monitor agora, é fácil entender quais são os custos mais prováveis com base em padrões de uso recentes. Para fazer isso, use **log Analytics uso e custos estimados** para revisar e analisar o uso de dados. Isso mostra quantos dados são coletados por cada solução, quantos dados estão sendo retidos e uma estimativa dos custos com base na quantidade de dados ingeridos e qualquer retenção adicional além da quantidade incluída.

![Utilização e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar seus dados com mais detalhes, clique no ícone na parte superior direita de qualquer um dos gráficos na página **uso e custos estimados** . Agora pode trabalhar com esta consulta para explorar mais detalhes da sua utilização.  

![Ver registos](media/manage-cost-storage/logs.png)

Na página **uso e custos estimados** , você pode examinar o volume de dados do mês. Isto inclui todos os dados recebidos e mantidas na sua área de trabalho do Log Analytics.  Clique em **detalhes de uso** na parte superior da página para exibir o painel uso com informações sobre as tendências de volume de dados por fonte, computadores e oferta. Para exibir e definir um limite diário ou para modificar o período de retenção, clique em **Gerenciamento de volume de dados**.
 
Custos de análise de registo são adicionados à sua fatura do Azure. Você pode ver os detalhes da sua fatura do Azure na seção de cobrança da portal do Azure ou na [portal de cobrança do Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Exibindo o uso de Log Analytics em sua fatura do Azure 

O Azure fornece uma grande funcionalidade útil no gerenciamento de [custos do Azure +](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub de cobrança. Por exemplo, a funcionalidade de "análise de custo" permite que você exiba seus gastos para os recursos do Azure. A adição de um filtro por tipo de recurso (para Microsoft. operationalinsights/Workspace para Log Analytics) permitirá que você acompanhe seus gastos.

Mais informações sobre seu uso podem ser obtidas [baixando seu uso do portal do Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Na planilha baixada, você pode ver o uso por recurso do Azure (por exemplo, Log Analytics espaço de trabalho) por dia. Nesta planilha do Excel, o uso de seus espaços de trabalho do Log Analytics pode ser encontrado primeiro filtrando a coluna "categoria do medidor" para mostrar "insights e análises" (usado por alguns dos tipos de preço herdados) e "Log Analytics" e, em seguida, adicionar um filtro na "instância do ID "coluna que é" contém espaço de trabalho ". O uso é mostrado na coluna "quantidade consumida" e a unidade de cada entrada é mostrada na coluna "unidade de medida".  Mais detalhes estão disponíveis para ajudá-lo a [entender sua fatura de Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Para alterar o tipo de preço Log Analytics do seu espaço de trabalho, 

1. No portal do Azure, abra **uso e custos estimados** em seu espaço de trabalho, em que você verá uma lista de cada tipo de preço disponível para esse espaço de trabalho.

2. Examine os custos estimados de cada tipo de preço. Essa estimativa se baseia nos últimos 31 dias de uso, portanto, essa estimativa de custo depende dos últimos 31 dias que representam o uso típico. No exemplo abaixo, você pode ver como, com base nos padrões de dados dos últimos 31 dias, esse espaço de trabalho custaria menos na camada pré-paga (#1) em comparação com a camada de reserva de capacidade de 100 GB/dia (#2).  

    ![Escalões de preço](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Depois de examinar os custos estimados com base nos últimos 31 dias de uso, se você decidir alterar o tipo de preço, clique em **selecionar**.  

Você também pode [definir o tipo de preço por meio de Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando o parâmetro `sku` (`pricingTier` no modelo Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Tipos de preço herdados

As assinaturas que tinham um espaço de trabalho Log Analytics ou Application Insights recurso nele antes de 2 de abril de 2018 ou estão vinculadas a uma Enterprise Agreement iniciada antes de 1º de fevereiro de 2019, continuarão a ter acesso para usar os tipos de preço herdados: **gratuito**, **Autônomo (por GB)** e **por nó (OMS)** .  Os espaços de trabalho no tipo de preço gratuito terão ingestão de dados diária limitada a 500 MB (exceto os tipos de dados de segurança coletados pela central de segurança do Azure) e a retenção de dados será limitada a 7 dias. O tipo de preço gratuito é destinado apenas para fins de avaliação. Os espaços de trabalho nos tipos de preço autônomos ou por nó têm retenção configurável do usuário de até 2 anos. 

Os espaços de trabalho criados antes de abril de 2016 também podem acessar os tipos de preço Standard e **Premium** **padrão** que têm retenção de dados fixa de 30 a 365 dias, respectivamente. Novos espaços de trabalho não podem ser criados nos tipos de preço **Standard** ou **Premium** e, se um espaço de trabalho for movido para fora dessas camadas, ele não poderá ser movido de volta. 

Mais detalhes sobre as limitações do tipo de preço estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Para usar os direitos que vêm da compra do OMS E1 Suite, do OMS E2 Suite ou do complemento do OMS para o System Center, escolha o Log Analytics tipo de preço *por nó* .

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

Os passos seguintes descrevem como configurar o registo quanto dados são mantidos na sua área de trabalho.

### <a name="default-retention"></a>Retenção padrão

Para definir a retenção padrão para seu espaço de trabalho, 
 
1. No portal do Azure, no espaço de trabalho, selecione **uso e custos estimados** no painel esquerdo.
2. Na página **Utilização e custos estimados**, clique em **Gestão de volumes de dados**, na parte superior.
3. No painel, mova o controle deslizante para aumentar ou diminuir o número de dias e clique em **OK**.  Se você estiver na camada *gratuita* , não poderá modificar o período de retenção de dados e precisará atualizar para a camada paga a fim de controlar essa configuração.

    ![Alterar a configuração de retenção de dados do espaço de trabalho](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A retenção também pode ser [definida por meio de Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando o parâmetro `retentionInDays`. Além disso, se você definir a retenção de dados para 30 dias, poderá disparar uma limpeza imediata de dados mais antigos usando o parâmetro `immediatePurgeDataOn30Days`, que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade só é exposta por meio de Azure Resource Manager. 

Dois tipos de dados--`Usage` e `AzureActivity`--são retidos por 90 dias por padrão, e não há nenhum custo para essa retenção de 90 dias. Esses tipos de dados também são gratuitos de encargos de ingestão de dados. 

### <a name="retention-by-data-type"></a>Retenção por tipo de dados

Também é possível especificar configurações de retenção diferentes para tipos de dados individuais. Cada tipo de dados é um subrecurso do espaço de trabalho. Por exemplo, a tabela SecurityEvent pode ser endereçada em [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) como:

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

Uma ótima ferramenta para se conectar diretamente ao Azure Resource Manager definir a retenção por tipo de dados é a ferramenta OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Saiba mais sobre o ARMclient de artigos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Aqui está um exbordo usando ARMClient, definindo os dados de SecurityEvent para uma retenção de 730 dias:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> A configuração da retenção em tipos de dados individuais pode ser usada para reduzir os custos de retenção de dados.  Para os dados coletados a partir de outubro de 2019 (quando esse recurso foi lançado), a redução da retenção para alguns tipos de dados pode reduzir o custo de retenção ao longo do tempo.  Para os dados coletados anteriormente, a definição de uma retenção inferior para um tipo individual não afetará os custos de retenção.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gerenciar seu volume máximo de dados diário

Pode configurar um máximo diário e limitar a ingestão diária da sua área de trabalho, mas usar cuidados de saúde, como o seu objetivo não deve ser atingir o limite diário.  Caso contrário, perde os dados para o resto do dia, que pode afetar outros serviços do Azure e soluções cuja funcionalidade pode depender de dados atualizados estarem disponíveis na área de trabalho.  Como resultado, a sua capacidade de observar e receber alertas quando as condições de estado de funcionamento de recursos que suportam serviços de TI são afetadas.  O limite diário destina-se a ser usado como uma maneira de gerenciar o aumento inesperado no volume de dados de seus recursos gerenciados e permanecer dentro do limite ou quando você quiser limitar os encargos não planejados para seu espaço de trabalho.  

Quando for atingido o limite diário, a coleção de tipos de dados cobrar deixa para o resto do dia. Uma faixa de aviso aparece na parte superior da página do espaço de trabalho Log Analytics selecionado e um evento de operação é enviado para a tabela de *operação* em **LogManagement** categoria. A coleta de dados será retomada depois que o tempo de redefinição definido em *limite diário será definido em*. É recomendável definir uma regra de alerta com base em eventos esta operação, configurado para ser notificado quando for atingido o limite diário de dados. 

> [!NOTE]
> O limite diário não interrompe a coleta de dados da central de segurança do Azure, exceto para espaços de trabalho nos quais a central de segurança do Azure foi instalada antes de 19 de junho de 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados para definir

Examine [log Analytics uso e custos estimados](usage-estimated-costs.md) para entender a tendência de ingestão de dados e qual é o limite de volume diário a ser definido. Deve ser considerado com cuidado, uma vez que não será possível monitorizar os seus recursos após ter sido atingido o limite. 

### <a name="set-the-daily-cap"></a>Definir o limite diário

As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que Log Analytics espaço de trabalho será ingerido por dia.  

1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Na página **uso e custos estimados** para o espaço de trabalho selecionado, clique em **Gerenciamento de volume de dados** na parte superior da página. 
3. O limite diário está **desativado** por padrão – Clique **em ativado** para habilitá-lo e defina o limite de volume de dados em GB/dia.

    ![Log Analytics configurar o limite de dados](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alertar quando o limite diário for atingido

Embora, apresentamos uma indicação visual no portal do Azure quando o limiar de limite de dados for cumprido, esse comportamento não necessariamente alinhados com como gerir os problemas operacionais que requerem atenção imediata.  Para receber uma notificação de alerta, pode criar uma nova regra de alerta no Azure Monitor.  Para saber mais, consulte [como criar, exibir e gerenciar alertas](alerts-metric.md).

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

Assim que o alerta é definido e o limite for atingido, um alerta é acionado e executa a resposta definida no grupo de ação. Ele pode notificar sua equipe por email e mensagens de texto, ou automatizar ações usando WebHooks, runbooks de automação ou [integração com uma solução de ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

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

Para obter uma lista de computadores que serão cobrados como nós se o espaço de trabalho estiver no tipo de preço herdado por nó, procure os nós que estão enviando **tipos de dados cobrados** (alguns tipos de dados são gratuitos). Para fazer isso, use a [propriedade](log-standard-properties.md#_isbillable) `_IsBillable` e use o campo mais à esquerda do nome de domínio totalmente qualificado. Isso retorna a lista de computadores com dados cobrados:

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
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Use essas consultas `union withsource = tt *` com moderação, pois as verificações entre os tipos de dados são caras de serem executadas. Essa consulta substitui a maneira antiga de consultar informações por computador com o tipo de dados de uso.  

Um cálculo mais preciso do que realmente será cobrado é obter a contagem de computadores por hora que estão enviando tipos de dados cobrados. (Para espaços de trabalho no tipo de preço herdado por nó, Log Analytics calcula o número de nós que precisam ser cobrados por hora.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Compreendendo o volume de dados ingeridos

Na página **uso e custos estimados** , o gráfico *ingestão de dados por solução* mostra o volume total de dados enviados e a quantidade de envio por cada solução. Isto permite-lhe determinar as tendências, como se a utilização de dados global (ou a utilização por uma solução específica) está a crescer, permanece estável ou diminui. É a consulta usada para gerar este

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

Para ver o **tamanho** dos eventos faturáveis ingeridos por computador, use a [Propriedade](log-standard-properties.md#_billedsize)`_BilledSize`, que fornece o tamanho em bytes:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A [propriedade](log-standard-properties.md#_isbillable) `_IsBillable` especifica se os dados ingeridos incorrerão em encargos.

Para ver a contagem de eventos **faturáveis** ingeridos por computador, use 

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

Para dados de nós hospedados no Azure, você pode obter o **tamanho** dos eventos faturáveis ingeridos __por computador__, usar a [Propriedade](log-standard-properties.md#_resourceid)_ResourceId, que fornece o caminho completo para o recurso:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para dados de nós hospedados no Azure, você pode obter o **tamanho** dos eventos faturáveis ingeridos __por assinatura do Azure__, analisar a propriedade `_ResourceId` como:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Alterar `subscriptionId` para `resourceGroup` mostrará o volume de dados ingerido Faturável pelo grupo de recursos do Azure. 


> [!NOTE]
> Alguns dos campos do tipo de dados de uso, enquanto ainda estão no esquema, foram preteridos e seus valores não serão mais populados. Esses são **computadores** , bem como campos relacionados à ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

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

Se você estiver no tipo de preço "por nó (OMS)", será cobrado com base no número de nós e soluções que você usa, o número de percepções e nós de análise para os quais você está sendo cobrado será mostrado na tabela na página **uso e custo estimado** .  

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

## <a name="data-transfer-charges-using-log-analytics"></a>Cobranças de transferência de dados usando Log Analytics

Enviar dados para Log Analytics pode incorrer em encargos de largura de banda de dados. Conforme descrito na [página de preços de largura de banda do Azure](https://azure.microsoft.com/pricing/details/bandwidth/), a transferência de dados entre os serviços do Azure localizados em duas regiões cobradas como transferência de dados de saída na taxa normal. A transferência de dados de entrada é gratuita. No entanto, esse encargo é muito pequeno (alguns%) em comparação com os custos de ingestão de dados Log Analytics. Consequentemente, controlar os custos de Log Analytics precisa se concentrar no volume de dados ingerido e temos diretrizes para ajudar a entender isso [aqui](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solução de problemas por que o Log Analytics não está mais coletando dados

Se você estiver no tipo de preço gratuito de legado e tiver enviado mais de 500 MB de dados em um dia, a coleta de dados será interrompida para o restante do dia. Atingir o limite diário é um motivo comum que o Log Analytics interrompe a recolha de dados ou dados parecem estar em falta.  O log Analytics cria um evento do tipo operação quando a recolha de dados é iniciada e interrompida. Execute a seguinte consulta na pesquisa para verificar se você está atingindo o limite diário e os dados ausentes: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando a coleta de dados é interrompida, o OperationStatus é **aviso**. Quando a coleta de dados é iniciada, o OperationStatus é **bem-sucedido**. A tabela seguinte descreve as razões que pára de recolha de dados e uma ação sugerida para retomar a recolha de dados:  

|Deixa de coleção de razão| Solução| 
|-----------------------|---------|
|Limite diário do tipo de preço gratuito de legado atingido |Aguarde até ao dia seguinte para a coleção reiniciar automaticamente ou alterar para um escalão de preço pago.|
|O limite diário do seu espaço de trabalho foi atingido|Aguarde até que a coleção seja reiniciada automaticamente ou aumente o limite diário de volume de dados descrito em gerenciar o volume máximo de dados diário. A hora de redefinição de limite diário é mostrada na página de **Gerenciamento de volume de dados** . |
|Subscrição do Azure está num estado suspenso devido a:<br> Versão de avaliação gratuita terminada<br> Passagem do Azure expirou<br> Atingido o limite de gastos mensal (por exemplo, numa assinatura do MSDN ou o Visual Studio)|Converter numa subscrição paga<br> Remover o limite, ou aguarde até que o limite é zerado|

Para ser notificado quando a coleta de dados for interrompida, use as etapas descritas em criar alerta de *limite de dados diários* para ser notificado quando a coleta de dados parar. Use as etapas descritas em [criar um grupo de ações](action-groups.md) para configurar uma ação de email, webhook ou runbook para a regra de alerta. 

## <a name="limits-summary"></a>Resumo de limites

Há alguns limites de Log Analytics adicionais, alguns dos quais dependem do tipo de preço Log Analytics. Eles estão documentados [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Passos seguintes

- Consulte [pesquisas de log em logs de Azure monitor](../log-query/log-query-overview.md) para saber como usar o idioma de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
- Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando um critério de pesquisa for cumprido.
- Utilize a [segmentação de soluções](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários.
- Para configurar uma política de coleta de eventos em vigor, examine [política de filtragem da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md).
- Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as definições da recolha de eventos, veja [configuração do registo de eventos](data-sources-windows-events.md).
- Para modificar as definições da recolha do syslog, veja [configuração do syslog](data-sources-syslog.md).
