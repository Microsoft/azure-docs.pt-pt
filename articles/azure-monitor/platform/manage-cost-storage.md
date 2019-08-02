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
ms.date: 07/29/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 5e325f7766e7b0d9764949eb3fbf9753d65db8b3
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619390"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gerenciar o uso e os custos com logs de Azure Monitor

> [!NOTE]
> Este artigo descreve como controlar seus custos em Azure Monitor definindo o período de retenção de dados para seu espaço de trabalho Log Analytics.  Consulte o artigo a seguir para obter informações relacionadas.
> - [Monitorizar a utilização e custos estimados](usage-estimated-costs.md) descreve como ver a utilização e custos estimados no Azure de várias funcionalidades para diferentes modelos de preços de monitorização. Também descreve como alterar o modelo de preços.

Os logs de Azure Monitor são projetados para dimensionar e dar suporte à coleta, indexação e armazenamento de grandes quantidades de dados por dia de qualquer fonte em sua empresa ou implantados no Azure.  Embora isso possa ser um controlador primário para a sua organização, relação custo-benefício é, por fim, o driver subjacente. Para esse fim, é importante entender que o custo de um espaço de trabalho Log Analytics não se baseia apenas no volume de dados coletados, mas também depende do plano selecionado e por quanto tempo você optou por armazenar os dados gerados de suas fontes conectadas.  

Neste artigo, vamos rever como pode proativamente monitorizar crescimento de volume e o armazenamento de dados e definir limites para controlar os custos associados. 

O custo de dados pode ser considerável consoante os seguintes fatores: 

- Volume de dados gerados e ingeridos no espaço de trabalho 
    - Número de soluções de gerenciamento habilitadas
    - Número de sistemas monitorados
    - Tipo de dados coletados de cada recurso monitorado 
- O período de tempo que você decide para manter seus dados 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Entenda o uso e o custo estimado do seu espaço de trabalho

Os logs de Azure Monitor facilitam a compreensão do que os custos são prováveis com base em padrões de uso recentes. Para fazer isso, use **log Analytics uso e custos estimados** para revisar e analisar o uso de dados. Mostra a quantidade de dados é recolhido por cada solução, a quantidade de dados está a ser retido e uma estimativa dos seus custos com base na quantidade de dados ingeridos e qualquer retenção adicional que ultrapassem o montante incluído.

![Utilização e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar os seus dados em mais detalhes, clique no ícone na parte superior direita do qualquer um dos gráficos no **utilização e custos estimados** página. Agora pode trabalhar com esta consulta para explorar mais detalhes da sua utilização.  

![Ver registos](media/manage-cost-storage/logs.png)

Na página **uso e custos estimados** , você pode examinar o volume de dados do mês. Isto inclui todos os dados recebidos e mantidas na sua área de trabalho do Log Analytics.  Clique em **detalhes de uso** na parte superior da página para exibir o painel uso com informações sobre as tendências de volume de dados por fonte, computadores e oferta. Para ver e definir um máximo diário ou para modificar o período de retenção, clique em **gestão de volumes de dados**.
 
Custos de análise de registo são adicionados à sua fatura do Azure. Pode ver detalhes do seu do Azure são faturadas na secção de faturação do portal do Azure ou no [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Extremidade diária

Pode configurar um máximo diário e limitar a ingestão diária da sua área de trabalho, mas usar cuidados de saúde, como o seu objetivo não deve ser atingir o limite diário.  Caso contrário, perde os dados para o resto do dia, que pode afetar outros serviços do Azure e soluções cuja funcionalidade pode depender de dados atualizados estarem disponíveis na área de trabalho.  Como resultado, a sua capacidade de observar e receber alertas quando as condições de estado de funcionamento de recursos que suportam serviços de TI são afetadas.  O limite diário destina-se a ser usado como uma maneira de gerenciar o aumento inesperado no volume de dados de seus recursos gerenciados e permanecer dentro do limite ou quando você quiser limitar os encargos não planejados para seu espaço de trabalho.  

Quando for atingido o limite diário, a coleção de tipos de dados cobrar deixa para o resto do dia. É apresentada uma faixa de aviso na parte superior da página para a área de trabalho do Log Analytics selecionada e um evento de operação é enviado para o *operação* tabela sob **LogManagement** categoria. Recolha de dados retoma após a hora de reposição definidas em *limite diário terá o valor*. É recomendável definir uma regra de alerta com base em eventos esta operação, configurado para ser notificado quando for atingido o limite diário de dados. 

> [!NOTE]
> O limite diário não interrompe a coleta de dados da central de segurança do Azure.

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados para definir

Revisão [utilização do Log Analytics e custos estimados](usage-estimated-costs.md) para compreender as tendências de ingestão de dados e o que é o limite de volume diário para definir. Deve ser considerado com cuidado, uma vez que não será possível monitorizar os seus recursos após ter sido atingido o limite. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gerir o volume de dados máximo diário

As etapas a seguir descrevem como configurar um limite para gerenciar o volume de dados que Log Analytics espaço de trabalho será ingerido por dia.  

1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Sobre o **utilização e custos estimados** para a área de trabalho selecionada, clique em **gestão de volumes de dados** da parte superior da página. 
3. Limite diário é **OFF** por predefinição – clique em **ON** para ativá-la e, em seguida, defina o limite de volume de dados em GB/dia.

    ![Log Analytics configurar o limite de dados](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alertar quando o limite diário for atingido

Embora, apresentamos uma indicação visual no portal do Azure quando o limiar de limite de dados for cumprido, esse comportamento não necessariamente alinhados com como gerir os problemas operacionais que requerem atenção imediata.  Para receber uma notificação de alerta, pode criar uma nova regra de alerta no Azure Monitor.  Para saber mais, consulte [como criar, exibir e gerenciar alertas](alerts-metric.md).

Para começar, seguem-se as definições recomendadas para o alerta:

- Alvo: Selecione seu recurso de Log Analytics
- Critérios: 
   - Nome do sinal: Pesquisa de logs personalizada
   - Consulta de pesquisa: Operação | onde o detalhe tem ' overquota '
   - Com base em: Número de resultados
   - Condição: Maior que
   - Limiar: 0
   - Período 5 (minutos)
   - Frequência 5 (minutos)
- Nome da regra de alerta: Limite diário de dados atingido
- Gravidade: Aviso (Sev 1)

Assim que o alerta é definido e o limite for atingido, um alerta é acionado e executa a resposta definida no grupo de ação. Pode notificar a sua equipa através de e-mail e mensagens de texto, ou automatize ações através de webhooks, runbooks de automatização ou [integrar com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados

Os passos seguintes descrevem como configurar o registo quanto dados são mantidos na sua área de trabalho.
 
1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Na página **Utilização e custos estimados**, clique em **Gestão de volumes de dados**, na parte superior.
3. No painel, mova o controlo de deslize para aumentar ou diminuir o número de dias e, em seguida, clique em **OK**.  Se estiver a utilizar o *gratuita* escalão, não será capaz de modificar o período de retenção de dados e terá de atualizar para o escalão pago para poder controlar esta definição.

    ![Alterar a configuração de retenção de dados do espaço de trabalho](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
A retenção também pode ser [definida por meio](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) do ARM `dataRetention` usando o parâmetro. Além disso, se você definir a retenção de dados para 30 dias, poderá disparar uma limpeza imediata de dados mais antigos `immediatePurgeDataOn30Days` usando o parâmetro, o que pode ser útil para cenários relacionados à conformidade. Essa funcionalidade só é exposta por meio do ARM. 

## <a name="legacy-pricing-tiers"></a>Tipos de preço herdados

As assinaturas que tinham um espaço de trabalho Log Analytics ou Application Insights recurso nele antes de 2 de abril de 2018 ou estão vinculadas a uma Enterprise Agreement iniciada antes de 1º de fevereiro de 2019, continuarão a ter acesso para usar os tipos de preço herdados: **Gratuito**, **autônomo (por GB)** e **por nó (OMS)** .  Os espaços de trabalho no tipo de preço gratuito terão ingestão de dados diária limitada a 500 MB (exceto os tipos de dados de segurança coletados pela central de segurança do Azure) e a retenção de dados será limitada a 7 dias. O tipo de preço gratuito é destinado apenas para fins de avaliação. Os espaços de trabalho nos tipos de preço autônomos ou por nó têm retenção configurável do usuário de até 2 anos. 

Os espaços de trabalho criados antes de abril de 2016 também têm acesso aos tipos de preço **padrão** original e **Premium** que têm retenção de dados fixa de 30 a 365 dias, respectivamente. Novos espaços de trabalho não podem ser criados nos tipos de preço **Standard** ou **Premium** e, se um espaço de trabalho for movido para fora dessas camadas, ele não poderá ser movido de volta. 

Mais detalhes sobre as limitações do tipo de preço estão disponíveis [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Para usar os direitos que vêm da compra do OMS E1 Suite, do OMS E2 Suite ou do complemento do OMS para o System Center, escolha o Log Analytics tipo de preço *por nó* .


## <a name="changing-pricing-tier"></a>Alterando o tipo de preço

Se seu espaço de trabalho Log Analytics tiver acesso a tipos de preço herdados, para alterar entre os tipos de preço herdados:

1. No portal do Azure, no painel de subscrições do Log Analytics, selecione uma área de trabalho.

2. No painel da área de trabalho, sob **gerais**, selecione **escalão de preço**.  

3. Sob **escalão de preço**, selecione um escalão de preço e, em seguida, clique em **selecione**.  
    ![Selecionado o plano de preços](media/manage-cost-storage/workspace-pricing-tier-info.png)

Você também pode [definir o tipo de preço por meio](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) do `ServiceTier` ARM usando o parâmetro. 

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

Para obter uma lista de computadores que serão cobrados como nós se o espaço de trabalho estiver no tipo de preço herdado por nó, procure os nós que estão enviando **tipos de dados cobrados** (alguns tipos de dados são gratuitos). Para fazer isso, use a `_IsBillable` [Propriedade](log-standard-properties.md#_isbillable) e use o campo mais à esquerda do nome de domínio totalmente qualificado. Isso retorna a lista de computadores com dados cobrados:

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
> Use essas `union withsource = tt *` consultas com moderação, pois as verificações entre os tipos de dados são caras de serem executadas. Essa consulta substitui a maneira antiga de consultar informações por computador com o tipo de dados de uso.  

Um cálculo mais preciso do que realmente será cobrado é obter a contagem de computadores por hora que estão enviando tipos de dados cobrados. (Para espaços de trabalho no tipo de preço herdado por nó, Log Analytics calcula o número de nós que precisam ser cobrados por hora.) 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Compreendendo o volume de dados ingeridos

Sobre o **utilização e custos estimados** página, o *ingestão de dados por solução* gráfico mostra o volume total de dados enviados e a quantidade está a ser enviado por cada solução. Isto permite-lhe determinar as tendências, como se a utilização de dados global (ou a utilização por uma solução específica) está a crescer, permanece estável ou diminui. É a consulta usada para gerar este

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Tenha em atenção que a cláusula "onde IsBillable = true" filtra tipos de dados de algumas soluções para o qual não é cobrado ingestão. 

Pode explorar mais a ver as tendências de dados para tipos de dados específicos, por exemplo, se quiser estudar os dados devido a registos do IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Volume de dados por computador

Para ver o **tamanho** dos eventos faturáveis ingeridos por computador, use `_BilledSize` a [Propriedade](log-standard-properties.md#_billedsize), que fornece o tamanho em bytes:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

A `_IsBillable` [Propriedade](log-standard-properties.md#_isbillable) especifica se os dados ingeridos incorrerão em encargos.

Para ver a contagem de eventos **faturáveis** ingeridos por computador, use 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
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

Para dados de nós hospedados no Azure, você pode obter o **tamanho** dos eventos faturáveis ingeridos __por assinatura do Azure__, analisar a `_ResourceId` Propriedade como:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

A `subscriptionId` alteração `resourceGroup` para mostrará o volume de dados ingeridos Faturável pelo grupo de recursos do Azure. 


> [!NOTE]
> Alguns dos campos do tipo de dados de uso, enquanto ainda estão no esquema, foram preteridos e seus valores não serão mais populados. Estes são **computador** , bem como campos relacionados com a ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** e **AverageProcessingTimeMs**.

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

Se estiver a utilizar "Por nó (OMS)" escalão de preço, em seguida, é cobrado com base no número de nós e soluções utilizar, o número de informações e nós de análise que lhe está a ser cobrada serão apresentados na tabela no **utilização e custos estimados**página.  

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
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

A consulta seguinte utiliza uma fórmula simples para prever quando é que vão ser enviados mais de 100 GB de dados num dia: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

Para alertar relativamente a volumes de dados diferentes, altere o 100 nas consultas para o número de GB para o qual quer receber o alerta.

Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando a recolha de dados for superior ao esperado.

Quando criar o alerta para a primeira consulta – quando existem mais de 100 GB de dados em 24 horas –, defina:  

- **Definir condição de alerta** especifique a sua área de trabalho do Log Analytics como o destino de recursos.
- **Critérios de alerta** especifique o seguinte:
   - **Nome do Sinal** selecione **Pesquisa de registos personalizada**
   - A **consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
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
   - A **consulta de pesquisa** como `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - A **Lógica de alerta** é **Baseada no** *número de resultados* e a **Condição** é *Maior do que* um **Limiar** de *0*
   - **Período de tempo** de *180* minutos e **Frequência de alertas** a cada *60* minutos, uma vez que os dados de utilização são atualizados apenas uma vez por hora.
- **Definir detalhes do alerta** especifique o seguinte:
   - O **nome** como *Previsto volume de dados maior do que 100 GB em 24 horas*
   - A **gravidade** como *Aviso*

Especifique um existente ou crie um novo [Grupo de Ação](action-groups.md), para que quando o alerta de registo corresponda aos critérios, seja notificado.

Quando receber um alerta, utilize os passos da secção seguinte para resolver o motivo pelo qual a utilização é superior ao esperado.

## <a name="limits-summary"></a>Resumo de limites

Há alguns limites de Log Analytics adicionais, alguns dos quais dependem do tipo de preço Log Analytics. Eles estão documentados [aqui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Passos Seguintes

- Consulte [pesquisas de log em logs de Azure monitor](../log-query/log-query-overview.md) para saber como usar o idioma de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
- Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando um critério de pesquisa for cumprido.
- Utilize a [segmentação de soluções](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários.
- Para configurar uma política de coleta de eventos em vigor, examine [política de filtragem da central de segurança do Azure](../../security-center/security-center-enable-data-collection.md).
- Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
- Para modificar as definições da recolha de eventos, veja [configuração do registo de eventos](data-sources-windows-events.md).
- Para modificar as definições da recolha do syslog, veja [configuração do syslog](data-sources-syslog.md).