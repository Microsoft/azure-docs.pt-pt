---
title: Solução de análise de rede do Azure no Azure Monitor | Microsoft Docs
description: Você pode usar a solução de análise de rede do Azure em Azure Monitor para examinar os logs do grupo de segurança de rede do Azure e Aplicativo Azure logs de gateway.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 5cce4ccd3acd9df896f6c28bd010a92ed4ec1a7a
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893319"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Soluções de monitoramento de rede do Azure no Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O Azure Monitor oferece as seguintes soluções para monitorar suas redes:
* Monitor de Desempenho de Rede (NPM) a
    * Monitorar a integridade da sua rede
* Análise de Aplicativo Azure gateway a ser revisada
    * Aplicativo Azure logs de gateway
    * Métricas de gateway de Aplicativo Azure
* Soluções para monitorar e auditar a atividade de rede em sua rede de nuvem
    * [Análise de Tráfego](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Análise do Grupo de Segurança de Rede do Azure

## <a name="network-performance-monitor-npm"></a>Monitor de Desempenho de Rede (NPM)

A solução de gerenciamento de [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) é uma solução de monitoramento de rede que monitora a integridade, a disponibilidade e a acessibilidade das redes.  Ele é usado para monitorar a conectividade entre:

* Nuvem pública e local
* Data centers e locais de usuários (filiais)
* Sub-redes que hospedam várias camadas de um aplicativo de várias camadas.

Para obter mais informações, consulte [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Análise de Aplicativo Azure gateway e grupo de segurança de rede
Para usar as soluções:
1. Adicione a solução de gerenciamento a Azure Monitor e
2. Habilite o diagnóstico para direcionar o diagnóstico para um espaço de trabalho Log Analytics no Azure Monitor. Não é necessário gravar os logs no armazenamento de BLOBs do Azure.

Você pode habilitar o diagnóstico e a solução correspondente para um ou ambos os grupos de segurança de rede e de gateway de aplicativo.

Se você não habilitar o log de recursos de diagnóstico para um tipo de recurso específico, mas instalar a solução, as folhas do painel para esse recurso ficarão em branco e exibirão uma mensagem de erro.

> [!NOTE]
> Em janeiro de 2017, o modo com suporte de envio de logs de gateways de aplicativo e grupos de segurança de rede para um espaço de trabalho Log Analytics alterado. Se você vir a solução **análise de rede do Azure (preterida)** , consulte [migrando da solução de análise de rede antiga](#migrating-from-the-old-networking-analytics-solution) para obter as etapas que você precisa seguir.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Examinar detalhes da coleta de dados de rede do Azure
A análise de gateway Aplicativo Azure e as soluções de gerenciamento de análise de grupo de segurança de rede coletam logs de diagnóstico diretamente de gateways de Aplicativo Azure e grupos de segurança de rede. Não é necessário gravar os logs no armazenamento de BLOBs do Azure e nenhum agente é necessário para a coleta de dados.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para Aplicativo Azure análise de gateway e a análise do grupo de segurança de rede.

| Plataforma | Agente direto | Agente de Operations Manager do Systems Center | Azure | Operations Manager necessário? | Operations Manager dados do agente enviados por meio do grupo de gerenciamento | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Quando registrado |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Aplicativo Azure solução de análise de gateway no Azure Monitor

![Símbolo de Análise do Gateway de Aplicativo do Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

Os seguintes logs têm suporte para gateways de aplicativo:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

As métricas a seguir têm suporte para gateways de aplicativo: novamente


* taxa de transferência de 5 minutos

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Use as instruções a seguir para instalar e configurar a solução de análise de gateway Aplicativo Azure:

1. Habilite a solução de análise de gateway Aplicativo Azure do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) ou usando o processo descrito em [adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Habilite o log de diagnóstico para os [gateways de aplicativo](../../application-gateway/application-gateway-diagnostics.md) que você deseja monitorar.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Habilitar o diagnóstico de Aplicativo Azure gateway no portal

1. Na portal do Azure, navegue até o recurso de gateway de aplicativo a ser monitorado.
2. Selecione *logs de diagnóstico* para abrir a página a seguir.

   ![imagem do recurso de gateway de Aplicativo Azure](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Clique em *Ativar diagnóstico* para abrir a página a seguir.

   ![imagem do recurso de gateway de Aplicativo Azure](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Para ativar o diagnóstico, clique *em ativado* em *status*.
5. Clique na caixa de seleção *Enviar para log Analytics*.
6. Selecione um espaço de trabalho Log Analytics existente ou crie um espaço de trabalho.
7. Clique na caixa de seleção em **log** para cada um dos tipos de log a serem coletados.
8. Clique em *salvar* para habilitar o log de diagnósticos para Azure monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Habilitar o diagnóstico de rede do Azure usando o PowerShell

O script do PowerShell a seguir fornece um exemplo de como habilitar o log de recursos para gateways de aplicativo.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Usar Aplicativo Azure análise de gateway
![imagem do bloco análise do gateway de Aplicativo Azure](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Depois de clicar no bloco **análise de gateway de aplicativo Azure** na visão geral, você pode exibir resumos de seus logs e, em seguida, analisar os detalhes para as seguintes categorias:

* Logs de acesso do gateway de aplicativo
  * Erros de cliente e servidor para logs de acesso do gateway de aplicativo
  * Solicitações por hora para cada gateway de aplicativo
  * Solicitações com falha por hora para cada gateway de aplicativo
  * Erros por agente do usuário para gateways de aplicativo
* Desempenho do gateway de aplicativo
  * Integridade do host para o gateway de aplicativo
  * Máximo e 95 º percentil para solicitações com falha do gateway de aplicativo

![imagem do painel de análise do gateway Aplicativo Azure](media/azure-networking-analytics/log-analytics-appgateway01.png)

![imagem do painel de análise do gateway Aplicativo Azure](media/azure-networking-analytics/log-analytics-appgateway02.png)

No painel **análise de Gateway aplicativo Azure** , examine as informações resumidas em uma das folhas e clique em uma para exibir informações detalhadas na página pesquisa de logs.

Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e seu histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Solução de análise de grupo de segurança de rede do Azure no Azure Monitor

![Símbolo de Análise do Grupo de Segurança de Rede do Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> A solução de análise do grupo de segurança de rede está mudando para o suporte da Comunidade, pois sua funcionalidade foi substituída por [análise de tráfego](../../network-watcher/traffic-analytics.md).
> - A solução agora está disponível nos [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) e, em breve, não estará mais disponível no Azure Marketplace.
> - Para clientes existentes que já adicionaram a solução ao espaço de trabalho, ele continuará a funcionar sem alterações.
> - A Microsoft continuará a dar suporte ao envio de logs de recursos NSG para seu espaço de trabalho usando as configurações de diagnóstico.

Os seguintes logs têm suporte para grupos de segurança de rede:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Use as instruções a seguir para instalar e configurar a solução de análise de rede do Azure:

1. Habilite a solução de análise do grupo de segurança de rede do Azure no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) ou usando o processo descrito em [adicionar soluções de Azure monitor do Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Habilite o log de diagnóstico para os recursos do [grupo de segurança de rede](../../virtual-network/virtual-network-nsg-manage-log.md) que você deseja monitorar.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Habilitar o diagnóstico do grupo de segurança de rede do Azure no portal

1. Na portal do Azure, navegue até o recurso de grupo de segurança de rede a ser monitorado
2. Selecione *logs de diagnóstico* para abrir a página a seguir

   ![imagem do recurso de grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Clique em *Ativar diagnóstico* para abrir a página a seguir

   ![imagem do recurso de grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Para ativar o diagnóstico, clique *em ativar* em *status*
5. Clique na caixa de seleção *Enviar para log Analytics*
6. Selecione um espaço de trabalho Log Analytics existente ou crie um espaço de trabalho
7. Clique na caixa de seleção em **log** para cada um dos tipos de log a serem coletados
8. Clique em *salvar* para habilitar o log de diagnósticos para log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Habilitar o diagnóstico de rede do Azure usando o PowerShell

O script do PowerShell a seguir fornece um exemplo de como habilitar o log de recursos para grupos de segurança de rede
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Usar a análise do grupo de segurança de rede do Azure
Depois de clicar no bloco **análise de grupo de segurança de rede do Azure** na visão geral, você pode exibir resumos de seus logs e, em seguida, analisar os detalhes para as seguintes categorias:

* Fluxos bloqueados do grupo de segurança de rede
  * Regras do grupo de segurança de rede com fluxos bloqueados
  * Endereços MAC com fluxos bloqueados
* Fluxos permitidos do grupo de segurança de rede
  * Regras do grupo de segurança de rede com fluxos permitidos
  * Endereços MAC com fluxos permitidos

![imagem do painel de análise do grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg01.png)

![imagem do painel de análise do grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg02.png)

No painel **análise do grupo de segurança de rede do Azure** , examine as informações resumidas em uma das folhas e clique em uma para exibir informações detalhadas na página pesquisa de logs.

Em qualquer uma das páginas de pesquisa de log, você pode exibir os resultados por tempo, resultados detalhados e seu histórico de pesquisa de log. Você também pode filtrar por facetas para restringir os resultados.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrando da antiga solução de análise de rede
Em janeiro de 2017, o modo com suporte de envio de logs de gateways de Aplicativo Azure e grupos de segurança de rede do Azure para um espaço de trabalho Log Analytics alterado. Essas alterações oferecem as seguintes vantagens:
+ Os logs são gravados diretamente em Azure Monitor sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir do momento em que os logs são gerados para serem disponibilizados no Azure Monitor
+ Menos etapas de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para usar as soluções atualizadas:

1. [Configurar o diagnóstico para ser enviado diretamente a Azure Monitor de gateways de Aplicativo Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configurar o diagnóstico para ser enviado diretamente a Azure Monitor de grupos de segurança de rede do Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Habilite o *análise do gateway de aplicativo do Azure* e a solução de *análise do grupo de segurança de rede do Azure* usando o processo descrito em [adicionar soluções de Azure monitor do Galeria de soluções](solutions.md)
3. Atualizar quaisquer consultas, painéis ou alertas salvos para usar o novo tipo de dados
   + O tipo é AzureDiagnostics. Você pode usar o ResourceType para filtrar os logs de rede do Azure.

     | Em vez de: | Utilizá |
     | --- | --- |
     | NetworkApplicationgateways &#124; Where OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124; em que ResourceType = = "APPLICATIONGATEWAYS" e OperationName = = "ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; Where OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124; em que ResourceType = = "APPLICATIONGATEWAYS" e OperationName = = "ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + Para qualquer campo que tenha um sufixo de \_s, \_d ou \_g no nome, altere o primeiro caractere para minúscula
   + Para qualquer campo que tenha um sufixo de \_o em nome, os dados são divididos em campos individuais com base nos nomes de campo aninhados.
4. Remova a solução de *análise de rede do Azure (preterida)* .
   + Se você estiver usando o PowerShell, use `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Os dados coletados antes da alteração não são visíveis na nova solução. Você pode continuar a consultar esses dados usando o tipo e os nomes de campo antigos.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Use [consultas de log em Azure monitor](../log-query/log-query-overview.md) para exibir dados detalhados do diagnóstico do Azure.
