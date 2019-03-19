---
title: Gerir a utilização e custos para o Azure Log Analytics | Documentos da Microsoft
description: Saiba como alterar o plano de preços e gerir a política de volume e retenção de dados para a área de trabalho do Log Analytics no Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: a1d8984b8c9d0859ff754e3d5bfb35bd98236b54
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098564"
---
# <a name="manage-usage-and-costs-for-log-analytics"></a>Gerir a utilização e custos para o Log Analytics

> [!NOTE]
> Este artigo descreve como controlar os custos do Log Analytics ao definir o período de retenção de dados.  Veja os artigos seguintes para obter informações relacionadas.
> - [Analisar a utilização de dados do Log Analytics](manage-cost-storage.md) descreve como analisar e alerta sobre a utilização de dados.
> - [Monitorizar a utilização e custos estimados](usage-estimated-costs.md) descreve como ver a utilização e custos estimados no Azure de várias funcionalidades para diferentes modelos de preços de monitorização. Também descreve como alterar o modelo de preços.

O log Analytics é criado para dimensionamento e suporte a recolher, indexação e armazenamento de grandes quantidades de dados por dia a partir de qualquer origem na sua empresa ou implementado no Azure.  Embora isso possa ser um controlador primário para a sua organização, relação custo-benefício é, por fim, o driver subjacente. Para esse fim, é importante compreender que o custo de uma área de trabalho do Log Analytics não está apenas com base no volume de dados recolhidos, também é dependente do plano selecionado e o tempo que optar por armazenar os dados gerados a partir de origens ligadas.  

Neste artigo, vamos rever como pode proativamente monitorizar crescimento de volume e o armazenamento de dados e definir limites para controlar os custos associados. 

O custo de dados pode ser considerável consoante os seguintes fatores: 

- Volume de dados gerado e ingeridos à área de trabalho 
    - Número de soluções de gestão ativada
    - Número de sistemas monitorados
    - Tipo de dados recolhidos a partir de cada recurso monitorizado 
- O período de tempo que optar por manter os seus dados 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Compreender a utilização e custo estimado de sua área de trabalho
Log Analytics torna isso fácil de entender o que são provável que os custos basear-se nos padrões de utilização recente.  Para tal, utilize **utilização do Log Analytics e os custos estimados** para rever e analisar a utilização de dados. Mostra a quantidade de dados é recolhido por cada solução, a quantidade de dados está a ser retido e uma estimativa dos seus custos com base na quantidade de dados ingeridos e qualquer retenção adicional que ultrapassem o montante incluído.

![Utilização e custos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar os seus dados em mais detalhes, clique no ícone na parte superior direita do qualquer um dos gráficos no **utilização e custos estimados** página. Agora pode trabalhar com esta consulta para explorar mais detalhes da sua utilização.  

![Ver registos](media/manage-cost-storage/logs.png)

Partir do **utilização e custos estimados** página pode rever o volume dos seus dados para o mês. Isto inclui todos os dados recebidos e mantidas na sua área de trabalho do Log Analytics.  Clique em **detalhes de utilização** da parte superior da página para ver o dashboard de utilização com informações sobre as tendências de volume de dados de origem, computadores e oferta. Para ver e definir um máximo diário ou para modificar o período de retenção, clique em **gestão de volumes de dados**.
 
Custos de análise de registo são adicionados à sua fatura do Azure. Pode ver detalhes do seu do Azure são faturadas na secção de faturação do portal do Azure ou no [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Extremidade diária
Pode configurar um máximo diário e limitar a ingestão diária da sua área de trabalho, mas usar cuidados de saúde, como o seu objetivo não deve ser atingir o limite diário.  Caso contrário, perde os dados para o resto do dia, que pode afetar outros serviços do Azure e soluções cuja funcionalidade pode depender de dados atualizados estarem disponíveis na área de trabalho.  Como resultado, a sua capacidade de observar e receber alertas quando as condições de estado de funcionamento de recursos que suportam serviços de TI são afetadas.  O limite diário destina-se para ser utilizado como uma forma de gerir o aumento inesperado no volume de dados a partir de recursos geridos e mantenha-se no seu limite, ou quando quiser simplesmente a limitar Cobranças não planeadas à sua área de trabalho.  

Quando for atingido o limite diário, a coleção de tipos de dados cobrar deixa para o resto do dia. É apresentada uma faixa de aviso na parte superior da página para a área de trabalho do Log Analytics selecionada e um evento de operação é enviado para o *operação* tabela sob **LogManagement** categoria. Recolha de dados retoma após a hora de reposição definidas em *limite diário terá o valor*. É recomendável definir uma regra de alerta com base em eventos esta operação, configurado para ser notificado quando for atingido o limite diário de dados. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificar o limite diário de dados para definir 
Revisão [utilização do Log Analytics e custos estimados](usage-estimated-costs.md) para compreender as tendências de ingestão de dados e o que é o limite de volume diário para definir. Deve ser considerado com cuidado, uma vez que não será possível monitorizar os seus recursos após ter sido atingido o limite. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gerir o volume de dados máximo diário 
Os passos seguintes descrevem como configurar um limite para gerir o volume de dados do Log Analytics será ingerir por dia.  

1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Sobre o **utilização e custos estimados** para a área de trabalho selecionada, clique em **gestão de volumes de dados** da parte superior da página. 
3. Limite diário é **OFF** por predefinição – clique em **ON** para ativá-la e, em seguida, defina o limite de volume de dados em GB/dia.<br><br> ![Configurar o limite de dados do log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alertar quando o limite diário atingido
Embora, apresentamos uma indicação visual no portal do Azure quando o limiar de limite de dados for cumprido, esse comportamento não necessariamente alinhados com como gerir os problemas operacionais que requerem atenção imediata.  Para receber uma notificação de alerta, pode criar uma nova regra de alerta no Azure Monitor.  Para obter mais informações, consulte [como criar, ver e gerir alertas](alerts-metric.md).      

Para começar, seguem-se as definições recomendadas para o alerta:

* Destino: Selecione o recurso do Log Analytics
* Critérios: 
   * Nome do sinal: Pesquisa de registos personalizado
   * Consulta de pesquisa: Operação | em que detalhes tem 'OverQuota'
   * Com base em: Número de resultados
   * Condição: Maior que
   * Limiar: 0
   * Período de: 5 (minutos)
   * Frequência de: 5 (minutos)
* Nome da regra de alerta: Limite diário de dados foi atingido
* Gravidade: Aviso (gravidade 1)

Assim que o alerta é definido e o limite for atingido, um alerta é acionado e executa a resposta definida no grupo de ação. Pode notificar a sua equipa através de e-mail e mensagens de texto, ou automatize ações através de webhooks, runbooks de automatização ou [integrar com uma solução ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Alterar o período de retenção de dados 
Os passos seguintes descrevem como configurar o registo quanto dados são mantidos na sua área de trabalho.
 
1. Na área de trabalho, selecione **Utilização e custos estimados**, no painel do lado esquerdo.
2. Na página **Utilização e custos estimados**, clique em **Gestão de volumes de dados**, na parte superior.
5. No painel, mova o controlo de deslize para aumentar ou diminuir o número de dias e, em seguida, clique em **OK**.  Se estiver a utilizar o *gratuita* escalão, não será capaz de modificar o período de retenção de dados e terá de atualizar para o escalão pago para poder controlar esta definição.<br><br> ![Alteração da definição de retenção de dados de área de trabalho](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Escalões de preços legado

Os clientes com um Enterprise Agreement assinado antes de 1 de Julho de 2018 ou que já criou uma área de trabalho do Log Analytics numa subscrição, ainda terá acesso para o *gratuito* plano. Se a sua subscrição não está vinculada a uma inscrição EA já existente, o *gratuito* escalão não está disponível quando criar uma área de trabalho numa nova subscrição depois de 2 de Abril de 2018.  Dados estão limitados a retenção de 7 dias para o *gratuito* escalão.  Para o legado *autónomo* ou *por nó* escalões, bem como o 2018 único escalão de preço atual, os dados recolhidos, está disponível nos últimos 31 dias. O *gratuito* escalão tem o limite de ingestão de diário de 500 MB e, se encontrar exceder consistentemente as quantidades permitidas volume, pode alterar sua área de trabalho para outro plano para recolher dados que ultrapassam este limite. 

> [!NOTE]
> Para utilizar a elegibilidade incluída na compra de OMS E1 Suite, OMS E2 Suite ou do suplemento OMS para o System Center, escolha o Log Analytics *por nó* escalão de preço.

## <a name="changing-pricing-tier"></a>Alterar o escalão de preço

Se a sua área de trabalho do Log Analytics tem acesso aos escalões de preços legados, para alternar entre os escalões de preços legados:

1. No portal do Azure, no painel de subscrições do Log Analytics, selecione uma área de trabalho.

2. No painel da área de trabalho, sob **gerais**, selecione **escalão de preço**.  

3. Sob **escalão de preço**, selecione um escalão de preço e, em seguida, clique em **selecione**.  
    ![Selecionado o plano de preços](media/manage-cost-storage/workspace-pricing-tier-info.png)

Se pretender mover a sua área de trabalho para o escalão de preço atual, terá [alterar o modelo de preços no Azure Monitor de monitorização da sua subscrição](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs#moving-to-the-new-pricing-model) que irá alterar o escalão de preço de todas as áreas de trabalho dessa subscrição.

> [!NOTE]
> Se a sua área de trabalho estiver ligada a uma conta de Automatização, antes poder selecionar o escalão de preço *Autónomo (Por GB)*, tem de eliminar quaisquer soluções de **Automatização e Controlo** e desassociar a conta de Automatização. No painel da área de trabalho, em **Geral**, clique em **Soluções** para ver e eliminar soluções. Para desassociar a Conta de automatização, clique no nome da Conta de automatização no painel **Escalão de preços**.

> [!NOTE]
> Pode saber mais sobre [definir o escalão de preço por meio de ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#create-a-log-analytics-workspace) e como garantir que a implementação de ARM será concluída com êxito, independentemente se a subscrição está no herdados ou no novo modelo de preços. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Por isso que o Log Analytics já não está a recolher dados de resolução de problemas
Se estiver no escalão de preço gratuito herdado e ter enviado a mais de 500 MB de dados num dia, deixa de recolha de dados para o resto do dia. Atingir o limite diário é um motivo comum que o Log Analytics interrompe a recolha de dados ou dados parecem estar em falta.  O log Analytics cria um evento do tipo operação quando a recolha de dados é iniciada e interrompida. Execute a seguinte consulta na pesquisa para verificar se estiver a atingir o limite diário e dados em falta: 

`Operation | where OperationCategory == 'Data Collection Status' `

Quando parar a recolha de dados, o OperationStatus indica um aviso. Quando inicia a recolha de dados, o OperationStatus é concluído com êxito. A tabela seguinte descreve as razões que pára de recolha de dados e uma ação sugerida para retomar a recolha de dados:  

|Deixa de coleção de razão| Solução| 
|-----------------------|---------|
|Foi atingido o limite diário de escalão de preço gratuito de legado |Aguarde até ao dia seguinte para a coleção reiniciar automaticamente ou alterar para um escalão de preço pago.|
|Foi atingido o limite diário da sua área de trabalho|Aguarde pela coleção reiniciar automaticamente ou aumentar o limite de volume de dados diário é descrito em Gerenciar o volume de dados máximo diário. O tempo de reposição de limite diário é mostra no **gestão de volumes de dados** página. |
|Subscrição do Azure está num estado suspenso devido a:<br> Versão de avaliação gratuita terminada<br> Passagem do Azure expirou<br> Atingido o limite de gastos mensal (por exemplo, numa assinatura do MSDN ou o Visual Studio)|Converter numa subscrição paga<br> Remover o limite, ou aguarde até que o limite é zerado|

Para ser notificado quando parar a recolha de dados, utilize os passos descritos em *criar limite de dados diário* alerta para ser notificado quando parar a recolha de dados e siga os passos utilizam os passos descritos em adicionam ações a regras de configurar um e-mail de alerta webhook ou runbook de ação para a regra de alerta. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Resolver o motivo pelo qual a utilização é superior ao esperado
A utilização superior deve-se a um ou a ambos os motivos abaixo:
- Estão a ser enviados para o Log Analytics mais dados do que o esperado
- Estão a ser enviados dados para o Log Analytics por mais nós do que o esperado

### <a name="data-volume"></a>Volume de dados 
Sobre o **utilização e custos estimados** página, o *ingestão de dados por solução* gráfico mostra o volume total de dados enviados e a quantidade está a ser enviado por cada solução. Isto permite-lhe determinar as tendências, como se a utilização de dados global (ou a utilização por uma solução específica) está a crescer, permanece estável ou diminui. É a consulta usada para gerar este

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Tenha em atenção que a cláusula "onde IsBillable = true" filtra tipos de dados de algumas soluções para o qual não é cobrado ingestão. 

Pode explorar mais a ver as tendências de dados para tipos de dados específicos, por exemplo, se quiser estudar os dados devido a registos do IIS:

`Usage| where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="nodes-sending-data"></a>Nós a enviar dados

Para compreender o número de computadores (nós) que fornecem dados por dia no mês passado, utilize

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

Para obter uma lista de computadores a enviar **faturados os tipos de dados** (alguns tipos de dados são gratuitos), aproveitar o [_IsBillable](log-standard-properties.md#_isbillable) propriedade:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

Utilize estes `union withsource = tt *` moderadamente, uma consulta como análises em todos os tipos de dados são dispendiosas. 

Isso pode ser estendido para devolver a contagem de computadores por hora, que estão a enviar faturados os tipos de dados:

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

Para ver os **tamanho** de eventos a cobrar ingeridos por computador, utilize o `_BilledSize` propriedade que fornece o tamanho em bytes:

`union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last `

Esta consulta substitui o método antigo de consulta isso com o tipo de dados de utilização. 

Para ver os **contagem** de eventos ingeridos por computador, utilize

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Para ver a contagem de eventos a cobrar ingeridos por computador, utilize 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Se quiser ver contagens para tipos de dados cobrar estão a enviar dados para um computador específico, utilize:

`union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last `

> [!NOTE]
> Alguns dos campos do tipo de dados de utilização, enquanto ainda está no esquema, foram preteridos e serão que seus valores já não são preenchidos. Estes são **computador** , bem como campos relacionados com a ingestão (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**,  **BatchesCapped** e **AverageProcessingTimeMs**.

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

### <a name="getting-node-counts"></a>Ao obter contagens de nó 

Se estiver a utilizar "Por nó (OMS)" escalão de preço, em seguida, é cobrado com base no número de nós e soluções utilizar, o número de informações e nós de análise que lhe está a ser cobrada serão apresentados na tabela no **utilização e custos estimados**página.  

Para ver o número de nós de segurança distintas, pode utilizar a consulta:

`union
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
| count`

Para ver o número de nós de automatização distintos, utilize a consulta:

` ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc`

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Criar um alerta quando a recolha de dados for superior ao esperado
Esta secção descreve como criar um alerta se:
- O volume de dados exceder uma determinada quantidade.
- Se previr que o volume de dados vai exceder uma determinada quantidade.

Os Alertas do Azure suportam [registar alertas](alerts-unified-log.md) que utilizam consultas de pesquisa. 

A consulta seguinte tem um resultado quando são recolhidos mais de 100 GB de dados nas últimas 24 horas:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

A consulta seguinte utiliza uma fórmula simples para prever quando é que vão ser enviados mais de 100 GB de dados num dia: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

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

## <a name="next-steps"></a>Passos Seguintes
* Veja [Pesquisas de registos no Log Analytics](../log-query/log-query-overview.md) para aprender a utilizar a linguagem de pesquisa. Pode utilizar as consultas de pesquisa para executar análises adicionais aos dados de utilização.
* Utilize os passos descritos em [create a new log alert](alerts-metric.md) (criar um novo alerta de registo) para ser notificado de quando um critério de pesquisa for cumprido.
* Utilize a [segmentação de soluções](../insights/solution-targeting.md) para recolher dados apenas de grupos de computadores necessários.
* Para configurar uma política de recolha de eventos em vigor, reveja [Centro de segurança do Azure política de filtragem](../../security-center/security-center-enable-data-collection.md).
* Altere a [configuração do contador de desempenho](data-sources-performance-counters.md).
* Para modificar as definições da recolha de eventos, veja [configuração do registo de eventos](data-sources-windows-events.md).
* Para modificar as definições da recolha do syslog, veja [configuração do syslog](data-sources-syslog.md).


