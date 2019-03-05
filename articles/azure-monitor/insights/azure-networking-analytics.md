---
title: Solução de análise de rede do Azure no Log Analytics | Documentos da Microsoft
description: Pode utilizar a solução de análise de redes do Azure no Log Analytics para rever os registos do grupo de segurança de rede do Azure e os registos do Gateway de aplicação do Azure.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: ewinner
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: richrund
ms.openlocfilehash: 5412fd2357a6848dae0cd5230546ff13cd1a6054
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316749"
---
# <a name="azure-networking-monitoring-solutions-in-log-analytics"></a>Soluções do Log Analytics de monitorização da rede do Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O log Analytics oferece as seguintes soluções para monitorização de suas redes:
* Monitor de desempenho de rede (NPM) para
    * Monitorizar o estado de funcionamento da sua rede
* Análise de Gateway de aplicação do Azure para rever
    * Registos do Gateway de aplicação do Azure
    * Métricas de Gateway de aplicação do Azure
* Soluções para monitorizar e auditar a atividade na sua rede de nuvem de rede
    * [Análise de tráfego](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Análise do Grupo de Segurança de Rede do Azure

## <a name="network-performance-monitor-npm"></a>Monitor de desempenho de rede (NPM)

O [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) solução de gestão é uma solução, que monitoriza o estado de funcionamento, a disponibilidade e a acessibilidade de redes de monitorização de rede.  É utilizado para monitorizar a conectividade entre:

* A cloud pública e no local
* Centros de dados e locais do usuário (filiais)
* Sub-redes alojar várias camadas de um aplicativo de várias camadas.

Para obter mais informações, consulte [Monitor de desempenho de rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Análise de Gateway de aplicação e o grupo de segurança de rede do Azure
Para utilizar as soluções:
1. Adicionar a solução de gestão para o Log Analytics, e
2. Ative diagnósticos direcionar os diagnósticos para uma área de trabalho do Log Analytics. Não é necessário escrever os registos para o armazenamento de Blobs do Azure.

Pode ativar o diagnóstico e a solução correspondente para um ou ambos dos grupos de segurança de rede e de Gateway de aplicação.

Se não ative o registo de diagnóstico para um tipo de recurso específico, mas instala a solução, os painéis de dashboard para esse recurso em branco e apresentam uma mensagem de erro.

> [!NOTE]
> Em Janeiro de 2017, alterar a forma como suportada de enviar registos de Gateways de aplicação e os grupos de segurança de rede para o Log Analytics. Se vir a **Azure Networking Analytics (preterido)** solução, consulte [migrar da solução de análise de rede antiga](#migrating-from-the-old-networking-analytics-solution) para obter os passos que tem de seguir.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Reveja os detalhes de recolha de dados de redes do Azure
A análise de Gateway de aplicação do Azure e as soluções de gestão do grupo de segurança de rede analytics recolhe registos de diagnóstico diretamente a partir de Gateways de aplicação do Azure e grupos de segurança de rede. Não é necessário escrever os registos para o armazenamento de Blobs do Azure e sem agente é obrigatório para a recolha de dados.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para análise de Gateway de aplicação do Azure e a análise do grupo de segurança de rede.

| Plataforma | Agente direto | Agente do Systems Center Operations Manager | Azure | Gestor de operações necessárias? | Dados de agente do Operations Manager enviados por grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |Quando tiver sessão iniciada |


## <a name="azure-application-gateway-analytics-solution-in-log-analytics"></a>Solução de análise do Gateway de aplicação do Azure no Log Analytics

![Símbolo de análise do Gateway de aplicação do Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

Os seguintes registos são suportados para Gateways de aplicação:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

As métricas seguintes são suportadas para Gateways de aplicação: novamente


* débito de 5 minutos

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução de análise do Gateway de aplicação do Azure:

1. Ativar a solução de análise de Gateway de aplicação do Azure partir [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) ou utilizando o processo descrito [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Ativar diagnósticos de registo para o [Gateways de aplicação](../../application-gateway/application-gateway-diagnostics.md) que pretende monitorizar.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Ativar o diagnóstico do Gateway de aplicação do Azure no portal

1. No portal do Azure, navegue para o recurso de Gateway de aplicação para monitorizar
2. Selecione *registos de diagnóstico* para abrir a página seguinte

   ![imagem de recurso de Gateway de aplicação do Azure](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Clique em *ativar os diagnósticos* para abrir a página seguinte

   ![imagem de recurso de Gateway de aplicação do Azure](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Para ativar os diagnósticos, clique em *nos* sob *Estado*
5. Clique a caixa de verificação *enviar para o Log Analytics*
6. Selecione uma área de trabalho do Log Analytics existente ou crie uma área de trabalho
7. Clique na caixa de verificação sob **Log** para cada um dos tipos de registo a recolher
8. Clique em *guardar* para ativar o registo de diagnóstico para o Log Analytics

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar o diagnóstico de rede do Azure com o PowerShell

O seguinte script do PowerShell fornece um exemplo de como ativar o diagnóstico de registo para gateways de aplicação.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Utilizar a análise de Gateway de aplicação do Azure
![imagem de Gateway de aplicação do Azure analytics mosaico](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Depois de clicar no **analytics de Gateway de aplicação Azure** mosaico descrição de geral, pode ver resumos dos seus registos e, em seguida, faça uma busca detalhes para as seguintes categorias:

* Registos de acesso de Gateway de aplicação
  * Erros de cliente e servidor para aceder aos registos de Gateway de aplicação
  * Pedidos por hora para cada Gateway de aplicação
  * Falha de pedidos por hora para cada Gateway de aplicação
  * Erros por agente de utilizador para Gateways de aplicação
* Desempenho do Gateway de aplicação
  * Estado de funcionamento do anfitrião para o Gateway de aplicação
  * Máximo e 95 º percentil para o Gateway de aplicação de pedidos falhados

![imagem do dashboard de análise do Gateway de aplicação do Azure](media/azure-networking-analytics/log-analytics-appgateway01.png)

![imagem do dashboard de análise do Gateway de aplicação do Azure](media/azure-networking-analytics/log-analytics-appgateway02.png)

Sobre o **analytics de Gateway de aplicação do Azure** dashboard, reveja as informações de resumo em um dos painéis de e, em seguida, clique num para ver informações detalhadas sobre a página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e seu histórico de pesquisa de registos. Também pode filtrar por facetas para refinar os resultados.


## <a name="azure-network-security-group-analytics-solution-in-log-analytics"></a>Solução de análise do grupo de segurança de rede do Azure no Log Analytics

![Símbolo de análise do grupo de segurança de rede do Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> A solução de análise do grupo de segurança de rede está a mudar para o suporte da Comunidade, uma vez que a sua funcionalidade foi substituída pela [a análise de tráfego](../../network-watcher/traffic-analytics.md).
> - A solução agora está disponível no [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) e em breve já não estarão disponíveis no Azure Marketplace.
> - Para os clientes existentes que já adicionado a solução à sua área de trabalho, ele continuará a funcionar sem alterações.
> - A Microsoft continuará a ter suportar a enviar registos de diagnóstico de NSG para a área de trabalho com as definições de diagnóstico.

Os seguintes registos são suportados para grupos de segurança de rede:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução de análise de redes do Azure:

1. Ativar a solução de análise do grupo de segurança de rede do Azure partir [do Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) ou utilizando o processo descrito [adicionar soluções Log Analytics da Galeria de soluções](../../azure-monitor/insights/solutions.md).
2. Ativar diagnósticos de registo para o [grupo de segurança de rede](../../virtual-network/virtual-network-nsg-manage-log.md) recursos que pretende monitorizar.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Ativar diagnósticos de grupo de segurança de rede do Azure no portal

1. No portal do Azure, navegue para o recurso do grupo de segurança de rede para monitorizar
2. Selecione *registos de diagnóstico* para abrir a página seguinte

   ![imagem de recurso do grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Clique em *ativar os diagnósticos* para abrir a página seguinte

   ![imagem de recurso do grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Para ativar os diagnósticos, clique em *nos* sob *Estado*
5. Clique a caixa de verificação *enviar para o Log Analytics*
6. Selecione uma área de trabalho do Log Analytics existente ou crie uma área de trabalho
7. Clique na caixa de verificação sob **Log** para cada um dos tipos de registo a recolher
8. Clique em *guardar* para ativar o registo de diagnóstico para o Log Analytics

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar o diagnóstico de rede do Azure com o PowerShell

O seguinte script do PowerShell fornece um exemplo de como ativar o diagnóstico de registo para grupos de segurança de rede
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Análise do grupo de segurança de rede do uso do Azure
Depois de clicar no **analytics de grupo de segurança de rede do Azure** mosaico descrição de geral, pode ver resumos dos seus registos e, em seguida, faça uma busca detalhes para as seguintes categorias:

* Fluxos bloqueados do grupo de segurança de rede
  * Regras de grupo de segurança de rede com fluxos bloqueados
  * Endereços MAC com fluxos bloqueados
* Grupo de segurança de rede fluxos permitido
  * Regras de grupo de segurança de rede com fluxos permitidos
  * Endereços MAC com fluxos permitidos

![imagem do dashboard de análise do grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg01.png)

![imagem do dashboard de análise do grupo de segurança de rede do Azure](media/azure-networking-analytics/log-analytics-nsg02.png)

Sobre o **analytics de grupo de segurança de rede do Azure** dashboard, reveja as informações de resumo em um dos painéis de e, em seguida, clique num para ver informações detalhadas sobre a página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode ver os resultados por tempo, os resultados detalhados e seu histórico de pesquisa de registos. Também pode filtrar por facetas para refinar os resultados.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrar a partir da solução de análise de rede antiga
Em Janeiro de 2017, alterar a forma como suportada de enviar registos a partir de Gateways de aplicação do Azure e grupos de segurança de rede do Azure para o Log Analytics. Estas alterações fornecem as seguintes vantagens:
+ Os registos são escritos diretamente ao Log Analytics sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir da hora de quando os registos são gerados para eles estejam disponíveis no Log Analytics
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnóstico do Azure

Para utilizar as soluções atualizadas:

1. [Configurar diagnósticos para serem enviados diretamente para o Log Analytics a partir de Gateways de aplicação do Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configurar diagnóstico sejam enviadas diretamente para o Log Analytics do Azure grupos de segurança de rede](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Ativar a *análise de Gateway de aplicação do Azure* e o *análise de grupo de segurança de rede do Azure* solução utilizando o processo descrito em [adicionar soluções Log Analytics dos Galeria de soluções](../../azure-monitor/insights/solutions.md)
3. Atualizar quaisquer consultas guardadas, dashboards ou alertas para usar o novo tipo de dados
  + Tipo é AzureDiagnostics. Pode usar o ResourceType para filtrar registos de rede do Azure.

    | Em vez de: | Utilização: |
    | --- | --- |
    | NetworkApplicationgateways &#124; onde OperationName = = "ApplicationGatewayAccess" | AzureDiagnostics &#124; where ResourceType="APPLICATIONGATEWAYS" and OperationName=="ApplicationGatewayAccess" |
    | NetworkApplicationgateways &#124; onde OperationName = = "ApplicationGatewayPerformance" | AzureDiagnostics &#124; where ResourceType=="APPLICATIONGATEWAYS" and OperationName=ApplicationGatewayPerformance |
    | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + Para qualquer campo que tem um sufixo de \_s, \_1!d, ou \_g o nome, altere o primeiro caráter em minúsculas
   + Para qualquer campo que tem um sufixo de \_o nome, os dados é dividido em campos individuais com base nos nomes de campos aninhados.
4. Remover os *Azure Networking Analytics (preterido)* solução.
  + Se estiver a utilizar o PowerShell, utilize `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Os dados recolhidos antes da alteração não é visível na solução de novo. Pode continuar a consultar estes dados com o tipo de antigo e nomes de campos.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos Seguintes
* Uso [pesquisas de registos no Log Analytics](../../azure-monitor/log-query/log-query-overview.md) ver dados de diagnóstico do Azure de detalhados.
