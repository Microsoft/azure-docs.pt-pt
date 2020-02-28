---
title: Solução Azure Networking Analytics no Azure Monitor Microsoft Docs
description: Pode utilizar a solução Azure Networking Analytics no Azure Monitor para rever os registos do grupo de segurança da rede Azure e os registos do Portal de Aplicações Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 1045f86db5e1a9ed1979a266937974045e401e27
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77667097"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Soluções de monitorização de rede Azure no Monitor Azure

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O Azure Monitor oferece as seguintes soluções para monitorizar as suas redes:
* Monitor de Desempenho da Rede (NPM) para
    * Monitorize a saúde da sua rede
* Análise de Gateway de aplicação Azure para revisão
    * Registos de gateway de aplicação Azure
    * Métricas de Gateway de Aplicação Azure
* Soluções para monitorizar e auditar atividade da rede na sua rede de nuvem
    * [Análise de Tráfego](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Monitor de Desempenho da Rede (NPM)

A solução de gestão do Monitor de Desempenho da [Rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview) é uma solução de monitorização da rede, que monitoriza a saúde, disponibilidade e alcance das redes.  É utilizado para monitorizar a conectividade entre:

* Nuvem pública e no local
* Centros de dados e locais de utilizadores (sucursais)
* Subredes que acolhem vários níveis de uma aplicação de várias camadas.

Para mais informações, consulte o Monitor de [Desempenho da Rede](https://docs.microsoft.com/azure/networking/network-monitoring-overview).

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Análise de Gateway de Aplicações Azure e Grupo de Segurança de Rede
Para utilizar as soluções:
1. Adicione a solução de gestão ao Monitor Azure, e
2. Ative diagnósticos para direcionar os diagnósticos para um espaço de trabalho de Log Analytics no Monitor Azure. Não é necessário escrever os registos para o armazenamento da Blob Azure.

Pode ativar diagnósticos e a solução correspondente para um ou ambos os Gateway de aplicação e grupos de segurança em rede.

Se não ativar o registo de recursos de diagnóstico para um determinado tipo de recurso, mas instale a solução, as lâminas do painel de instrumentos para esse recurso ficam em branco e exibem uma mensagem de erro.

> [!NOTE]
> Em janeiro de 2017, a forma suportada de enviar registos de Gateways de Aplicações e Grupos de Segurança de Rede para um espaço de trabalho log Analytics mudou. Se vir a solução **Azure Networking Analytics (depreciada),** consulte a [migração da antiga solução Networking Analytics](#migrating-from-the-old-networking-analytics-solution) para os passos que precisa seguir.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Rever detalhes da recolha de dados em rede Azure
A análise do Portal de Aplicações Azure e as soluções de gestão de análise do Network Security Group recolhem registos de diagnóstico diretamente dos Gateways de Aplicações Azure e dos Grupos de Segurança da Rede. Não é necessário escrever os registos para o armazenamento da Blob Azure e nenhum agente é necessário para a recolha de dados.

A tabela que se segue mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para a análise do Portal de Aplicação Azure e para a análise do Grupo de Segurança da Rede.

| Plataforma | Agente direto | Agente gestor de operações do Centro de Sistemas | Azure | Gestor de Operações necessário? | Dados do agente de operações enviados através de grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |quando registado |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Solução de análise Azure Application Gateway no Monitor Azure

![Símbolo de Análise de Gateway de Aplicação Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

Os seguintes registos são suportados para Gateways de Aplicação:

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

As seguintes métricas são suportadas para Gateways de Aplicação:novamente


* Entrada de 5 minutos

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução de análise do Gateway de Aplicação Azure:

1. Ative a solução de análise de Gateway de Aplicação Azure do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) ou utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md).
2. Ative o registo de diagnósticos para as Gateways de [Aplicação](../../application-gateway/application-gateway-diagnostics.md) que pretende monitorizar.

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Ativar diagnósticos de gateway de aplicação Azure no portal

1. No portal Azure, navegue até ao recurso Application Gateway para monitorizar.
2. Selecione *registos* de Diagnóstico para abrir a página seguinte.

   ![imagem do recurso Gateway de aplicação Azure](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. Clique *em ligar os diagnósticos* para abrir a página seguinte.

   ![imagem do recurso Gateway de aplicação Azure](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. Para ligar os diagnósticos, clique *em 'On',* em *'Estado '.*
5. Clique na caixa de verificação para *enviar para registar análises*.
6. Selecione um espaço de trabalho existente no Log Analytics ou crie um espaço de trabalho.
7. Clique na caixa de verificação em **Registo** para cada um dos tipos de registo para recolher.
8. Clique em *Guardar* para ativar o registo de diagnósticos para o Monitor Azure.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar diagnósticos de rede Azure usando powerShell

O seguinte script PowerShell fornece um exemplo de como ativar o registo de recursos para gateways de aplicações.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Utilizar a análise do Gateway de Aplicações Azure
![imagem do azulejo de análise azure application Gateway](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

Depois de clicar no azulejo de análise do Portal de **Aplicação Azure** na visão geral, pode ver resumos dos seus registos e, em seguida, perfurar detalhes para as seguintes categorias:

* Registos de acesso gateway de aplicação
  * Erros de cliente e servidor para registos de acesso gateway de aplicação
  * Pedidos por hora para cada Gateway de Inscrição
  * Pedidos falhados por hora para cada Gateway de Aplicação
  * Erros por agente de utilizador para Gateways de Aplicação
* Desempenho do Gateway de Aplicação
  * Saúde de hospedeiro para Gateway de Aplicação
  * Percentil máximo e 95º para pedidos falhados do Gateway de Aplicação

![imagem do painel de análise azure application Gateway](media/azure-networking-analytics/log-analytics-appgateway01.png)

![imagem do painel de análise azure application Gateway](media/azure-networking-analytics/log-analytics-appgateway02.png)

No painel de análise do Portal de **Aplicações Azure,** reveja as informações sumárias numa das lâminas e clique numa para ver informações detalhadas na página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode visualizar resultados por tempo, resultados detalhados e o seu histórico de pesquisa de registo. Também pode filtrar por facetas para reduzir os resultados.


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Solução de análise do Grupo de Segurança da Rede Azure no Monitor Azure

![Símbolo de Análise do Grupo de Segurança da Rede Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> A solução de análise do Grupo de Segurança da Rede está a mover-se para o suporte comunitário, uma vez que a sua funcionalidade foi substituída pela [Traffic Analytics.](../../network-watcher/traffic-analytics.md)
> - A solução já está disponível nos [Modelos De Arranque Rápido do Azure](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) e em breve deixará de estar disponível no Azure Marketplace.
> - Para os clientes existentes que já adicionaram a solução ao seu espaço de trabalho, continuará a funcionar sem alterações.
> - A Microsoft continuará a suportar o envio de registos de recursos NSG para o seu espaço de trabalho utilizando definições de diagnóstico.

Os seguintes registos são suportados para grupos de segurança da rede:

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes instruções para instalar e configurar a solução Azure Networking Analytics:

1. Ative a solução de análise do Grupo de Segurança da Rede Azure do [mercado Azure](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) ou utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria Solutions](../../azure-monitor/insights/solutions.md).
2. Ative o registo de diagnósticos para os recursos do Grupo de Segurança da [Rede](../../virtual-network/virtual-network-nsg-manage-log.md) que pretende monitorizar.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Ativar diagnósticos de grupo de segurança de rede Azure no portal

1. No portal Azure, navegue para o recurso do Grupo de Segurança da Rede para monitorizar
2. Selecione *registos* de Diagnóstico para abrir a seguinte página

   ![imagem do recurso do Grupo de Segurança da Rede Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Clique *em ligar diagnósticos* para abrir a seguinte página

   ![imagem do recurso do Grupo de Segurança da Rede Azure](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Para ativar os diagnósticos, clique *em 'Em 'Status'.*
5. Clique na caixa de verificação para *enviar para registar análises*
6. Selecione um espaço de trabalho existente no Log Analytics ou crie um espaço de trabalho
7. Clique na caixa de verificação em **Registo** para cada um dos tipos de registo para recolher
8. Clique em *Guardar* para ativar o registo de diagnósticos para registar análises

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar diagnósticos de rede Azure usando powerShell

O seguinte script PowerShell fornece um exemplo de como ativar o registo de recursos para grupos de segurança de rede
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Utilize a análise do Grupo de Segurança da Rede Azure
Depois de clicar no azulejo de análise do Grupo de **Segurança da Rede Azure** na visão geral, pode ver resumos dos seus registos e, em seguida, perfurar detalhes para as seguintes categorias:

* Grupo de segurança da rede bloqueou fluxos
  * Regras do grupo de segurança da rede com fluxos bloqueados
  * Endereços MAC com fluxos bloqueados
* Grupo de segurança da rede permitiu fluxos
  * Regras do grupo de segurança da rede com fluxos permitidos
  * Endereços MAC com fluxos permitidos

![imagem do painel de análise do Grupo de Segurança da Rede Azure](media/azure-networking-analytics/log-analytics-nsg01.png)

![imagem do painel de análise do Grupo de Segurança da Rede Azure](media/azure-networking-analytics/log-analytics-nsg02.png)

No painel de análise do Grupo de **Segurança da Rede Azure,** reveja as informações sumárias numa das lâminas e clique numa para ver informações detalhadas na página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode visualizar resultados por tempo, resultados detalhados e o seu histórico de pesquisa de registo. Também pode filtrar por facetas para reduzir os resultados.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrando da antiga solução Networking Analytics
Em janeiro de 2017, a forma suportada de enviar registos de Gateways de Aplicações Azure e Grupos de Segurança da Rede Azure para um espaço de trabalho log Analytics mudou. Estas alterações proporcionam as seguintes vantagens:
+ Os registos são escritos diretamente para o Monitor Azure sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir do momento em que os registos são gerados para que estejam disponíveis no Monitor Azure
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnósticos Azure

Para utilizar as soluções atualizadas:

1. [Configure diagnósticos a serem enviados diretamente para o Monitor Azure a partir de Gateways de Aplicação Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configure diagnósticos a serem enviados diretamente para o Monitor Azure dos Grupos de Segurança da Rede Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Ative o *Portal de Aplicação Azure Analytics* e a solução Analytics do Grupo de Segurança da Rede *Azure* utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria solutions](solutions.md)
3. Atualizar quaisquer consultas, dashboards ou alertas guardados para usar o novo tipo de dados
   + O tipo é para AzureDiagnostics. Pode utilizar o ResourceType para filtrar os registos de rede Azure.

     | Em vez de: | Utilização: |
     | --- | --- |
     | Gateways de &#124; Aplicações de Rede onde operationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; onde ResourceType="APPLICATIONGATEWAYS" e OperationName=="ApplicationGatewayAccess" |
     | Gateways de &#124; Aplicações de Rede onde operationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; onde ResourceType="APPLICATIONGATEWAYS" e OperationName=="ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + Para qualquer campo que tenha um sufixo de \_s, \_d, ou \_g no nome, mude o primeiro personagem para minúscula
   + Para qualquer campo que tenha um sufixo de \_o em nome, os dados são divididos em campos individuais com base nos nomes de campo aninhados.
4. Remova a solução *Azure Networking Analytics (Depreciada).*
   + Se estiver a utilizar o PowerShell, utilize `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Os dados recolhidos antes da alteração não são visíveis na nova solução. Pode continuar a consultar estes dados utilizando os antigos nomes de Tipo e campo.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Utilize consultas de [registo no Monitor Azure](../log-query/log-query-overview.md) para visualizar dados detalhados de diagnóstico do Azure.
