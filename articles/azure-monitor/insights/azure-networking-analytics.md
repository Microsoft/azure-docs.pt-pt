---
title: Solução Azure Networking Analytics no Azure Monitor Microsoft Docs
description: Pode utilizar a solução Azure Networking Analytics no Azure Monitor para rever os registos do grupo de segurança da rede Azure e os registos do Gateway de Aplicações Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2018
ms.openlocfilehash: 9e2210cdbcc2916723c8c2e2ed1ef514d427c9d6
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032189"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Soluções de monitorização da rede Azure no Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O Azure Monitor oferece as seguintes soluções para monitorizar as suas redes:
* Monitor de Desempenho de Rede (NPM) para
    * Monitorize a saúde da sua rede
* Azure Application Gateway analytics para revisão
    * Registos de gateway de aplicação Azure
    * Métricas de Gateway de Aplicação Azure
* Soluções para monitorizar e auditar a atividade da rede na sua rede de nuvem
    * [Análise de Tráfego](../../networking/network-monitoring-overview.md#traffic-analytics) 
    * Azure Network Security Group Analytics

## <a name="network-performance-monitor-npm"></a>Monitor de desempenho da rede (NPM)

A solução de gestão [do Monitor de Desempenho da Rede](../../networking/network-monitoring-overview.md) é uma solução de monitorização da rede, que monitoriza a saúde, disponibilidade e alcance das redes.  É utilizado para monitorizar a conectividade entre:

* Nuvem pública e no local
* Centros de dados e localizações de utilizadores (sucursais)
* Sub-redes que hospedam vários níveis de uma aplicação multi-camadas.

Para obter mais informações, consulte [o Monitor de Desempenho da Rede.](../../networking/network-monitoring-overview.md)

## <a name="network-security-group-analytics"></a>Análise do Grupo de Segurança de Rede

1. Adicione a solução de gestão ao Azure Monitor, e
2. Habilitar os diagnósticos para direcionar os diagnósticos para um espaço de trabalho Log Analytics no Azure Monitor. Não é necessário escrever os registos para o armazenamento da Azure Blob.

Se os registos de diagnóstico não estiverem ativados, as lâminas do painel de instrumentos para esse recurso estão em branco e exibem uma mensagem de erro.

## <a name="azure-application-gateway-analytics"></a>Azure Application Gateway analytics

1. Habilitar os diagnósticos para direcionar os diagnósticos para um espaço de trabalho Log Analytics no Azure Monitor.
2. Consuma o resumo detalhado do seu recurso utilizando o modelo de livro para Application Gateway.

Se os registos de diagnóstico não estiverem ativados para o Gateway de Aplicação, apenas os dados métricos predefinidos serão preenchidos dentro do livro.


> [!NOTE]
> Em janeiro de 2017, a forma suportada de enviar registos de Gateways de Aplicações e Grupos de Segurança de Rede para um espaço de trabalho Log Analytics mudou. Se vir a solução **Azure Networking Analytics (prevada),** consulte a [migração da antiga solução Networking Analytics](#migrating-from-the-old-networking-analytics-solution) para os passos que precisa de seguir.
>
>

## <a name="review-azure-networking-data-collection-details"></a>Rever detalhes da recolha de dados de redes Azure
A análise do Azure Application Gateway e as soluções de gestão de análise do Grupo de Segurança de Rede recolhem registos de diagnósticos diretamente dos Gateways de Aplicações Azure e dos Grupos de Segurança da Rede. Não é necessário escrever os registos para o armazenamento da Azure Blob e nenhum agente é necessário para a recolha de dados.

A tabela seguinte mostra métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para análise de Gateway de Aplicação Azure e a análise do Grupo de Segurança da Rede.

| Plataforma | Agente direto | Agente gestor de operações do Centro de Sistemas | Azure | Diretor de Operações necessário? | Dados de agente do Gestor de Operações enviados através do grupo de gestão | Frequência da recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |quando registado |


### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Ativar diagnósticos de Gateway de Aplicação Azure no portal

1. No portal Azure, navegue para o recurso Application Gateway para monitorizar.
2. Selecione *Definições de Diagnóstico* para abrir a página seguinte.

   ![Screenshot do diagnóstico Configurações config para recurso Application Gateway.](media/azure-networking-analytics/diagnostic-settings-1.png)

   [![Screenshot da página para configurar as definições de Diagnóstico.](media/azure-networking-analytics/diagnostic-settings-2.png)](media/azure-networking-analytics/application-gateway-diagnostics-2.png#lightbox)

5. Clique na caixa de verificação para *Enviar para Registar Analytics*.
6. Selecione um espaço de trabalho log analytics existente ou crie um espaço de trabalho.
7. Clique na caixa de verificação em **Log** para cada um dos tipos de registo a recolher.
8. Clique em *Guardar* para ativar o registo de diagnósticos ao Azure Monitor.

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar diagnósticos de rede Azure usando PowerShell

O seguinte script PowerShell fornece um exemplo de como permitir a registo de recursos para gateways de aplicações.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

#### <a name="accessing-azure-application-gateway-analytics-via-azure-monitor-network-insights"></a>Aceder a análises de Gateway de Aplicações Azure através de insights da Rede monitor Azure

Os insights da aplicação podem ser acedidos através do separador insights dentro do seu recurso Application Gateway.

![Screenshot de insights do Gateway de aplicações ](media/azure-networking-analytics/azure-appgw-insights.png
)

O separador "ver métricas detalhadas" abrirá o livro pré-povoado resumindo os dados do seu Gateway de Aplicação.

[![Screenshot do livro de aplicação Gateway](media/azure-networking-analytics/azure-appgw-workbook.png)](media/azure-networking-analytics/application-gateway-workbook.png#lightbox)

## <a name="migrating-from-azure-gateway-analytics-solution-to-azure-monitor-workbooks"></a>Migrar da solução de análise Azure Gateway para os livros de trabalho do Azure Monitor

> [!NOTE]
> A solução de análise Azure Application Gateway está desatualizada e a forma recomendada de consumir análises é através de livros expostos através de insights da Rede monitor Azure para o recurso Gateway de aplicação.

* Se a definição de diagnóstico já estiver ativada para armazenar registos num espaço de trabalho do Log Analytics, o livro de insights da Rede Azure Monitor pode consumir dados a partir do mesmo local. Não é necessária uma nova configuração.

* Todos os dados anteriores já estão disponíveis no livro a partir das definições de diagnóstico pontual. Não é necessária transferência de dados.

* Não é necessário um toggle ativo para mudar para livros de trabalho. Tanto a solução de análise como o manual de insight da Rede podem funcionar em paralelo.

* Não existem custos adicionais associados aos livros de trabalho do Azure Monitor. Log Analytics workspace continuará a ser cobrado de acordo com o uso.

* Para limpar a solução de análise Azure Gateway a partir do seu espaço de trabalho, pode eliminar a solução da página de recursos da solução.

[![Screenshot da opção de eliminação para solução de análise Azure Application Gateway.](media/azure-networking-analytics/azure-appgw-analytics-delete.png)](media/azure-networking-analytics/application-gateway-analytics-delete.png#lightbox)

Para obter mais informações sobre as capacidades da nova solução de livro, consulte [a visão geral dos livros de trabalho](../platform/workbooks-overview.md)

## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Solução de análise do Grupo de Segurança da Rede Azure no Azure Monitor

![Símbolo de análise do grupo de segurança da rede Azure](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> A solução de análise do Grupo de Segurança de Rede está a mover-se para o apoio à comunidade, uma vez que a sua funcionalidade foi substituída pela [Traffic Analytics](../../network-watcher/traffic-analytics.md).
> - A solução já se encontra disponível nos [Modelos Azure Quickstart](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/) e em breve deixará de estar disponível no Azure Marketplace.
> - Para os clientes existentes que já adicionaram a solução ao seu espaço de trabalho, continuará a funcionar sem alterações.
> - A Microsoft continuará a suportar o envio de registos de recursos NSG para o seu espaço de trabalho utilizando Definições de Diagnóstico.

Os seguintes registos são suportados para grupos de segurança da rede:

* Evento networkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>Instale e configuure a solução
Utilize as seguintes instruções para instalar e configurar a solução Azure Networking Analytics:

1. Ativar a solução de análise do Grupo de Segurança da Rede Azure utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria de Soluções](./solutions.md).
2. Ativar o registo de diagnósticos dos recursos [do Grupo de Segurança](../../virtual-network/virtual-network-nsg-manage-log.md) da Rede que pretende monitorizar.

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Ativar diagnósticos de grupos de segurança de rede Azure no portal

1. No portal Azure, navegue para o recurso do Grupo de Segurança da Rede para monitorizar
2. Selecione *registos de Diagnóstico* para abrir a página seguinte

   ![Screenshot da página de registos de diagnóstico para um recurso do Grupo de Segurança de Rede mostrando a opção de ligar os diagnósticos.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. Clique *em Ligar os diagnósticos* para abrir a página seguinte

   ![Screenshot da página para configurar as definições de Diagnóstico. O estado está definido para On, Send to Log Analytics é selecionado e dois tipos de Log são selecionados.](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. Para ligar os diagnósticos, clique *em 'On'* 
5. Clique na caixa de verificação para *Enviar para Registar Analytics*
6. Selecione um espaço de trabalho log analytics existente ou crie um espaço de trabalho
7. Clique na caixa de verificação em **Log** para cada um dos tipos de registo para recolher
8. Clique em *Guardar* para permitir o registo de diagnósticos para registar análises

### <a name="enable-azure-network-diagnostics-using-powershell"></a>Ativar diagnósticos de rede Azure usando PowerShell

O seguinte script PowerShell fornece um exemplo de como permitir a registo de recursos para grupos de segurança de rede
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Use a análise do Grupo de Segurança da Rede Azure
Depois de clicar no azulejo **analítico do Grupo de Segurança da Rede Azure** na Visão Geral, pode visualizar resumos dos seus registos e, em seguida, perfurar em detalhes para as seguintes categorias:

* Grupo de segurança de rede bloqueou fluxos
  * Regras do grupo de segurança da rede com fluxos bloqueados
  * Endereços MAC com fluxos bloqueados
* Grupo de segurança de rede permitiu fluxos
  * Regras do grupo de segurança da rede com fluxos permitidos
  * Endereços MAC com fluxos permitidos

![Screenshot de azulejos com dados para grupo de segurança de rede bloqueou fluxos, incluindo regras com fluxos bloqueados e endereços MAC com fluxos bloqueados.](media/azure-networking-analytics/log-analytics-nsg01.png)

![Screenshot de azulejos com dados para grupo de segurança de rede permitiu fluxos, incluindo regras com fluxos permitidos e endereços MAC com fluxos permitidos.](media/azure-networking-analytics/log-analytics-nsg02.png)

No painel de análise do **Azure Network Security Group,** reveja as informações resumidas numa das lâminas e, em seguida, clique numa para ver informações detalhadas na página de pesquisa de registo.

Em qualquer uma das páginas de pesquisa de registo, pode ver resultados pelo tempo, resultados detalhados e o histórico de pesquisa de registo. Também pode filtrar por facetas para reduzir os resultados.

## <a name="migrating-from-the-old-networking-analytics-solution"></a>Migrar da antiga solução Networking Analytics
Em janeiro de 2017, a forma suportada de enviar registos de Gateways de Aplicação Azure e grupos de segurança da rede Azure para um espaço de trabalho Log Analytics mudou. Estas alterações fornecem as seguintes vantagens:
+ Os registos são escritos diretamente para o Azure Monitor sem a necessidade de usar uma conta de armazenamento
+ Menos latência a partir do momento em que os registos são gerados para que estejam disponíveis no Azure Monitor
+ Menos passos de configuração
+ Um formato comum para todos os tipos de diagnósticos Azure

Para utilizar as soluções atualizadas:

1. [Configurar diagnósticos a enviar diretamente para o Monitor Azure a partir de Gateways de aplicação Azure](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Configurar diagnósticos a enviar diretamente para o Monitor Azure dos Grupos de Segurança da Rede Azure](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. Ativar a *Azure Application Gateway Analytics* e a solução *Azure Network Security Group Analytics* utilizando o processo descrito nas [soluções Add Azure Monitor da Galeria de Soluções](solutions.md)
3. Atualize quaisquer consultas guardadas, dashboards ou alertas para utilizar o novo tipo de dados
   + Tipo é para AzureDiagnostics. Pode utilizar o ResourceType para filtrar os registos de rede Azure.

     | Em vez de: | Utilização: |
     | --- | --- |
     | NetworkApplicationgateways &#124; onde OperaçãoName="ApplicationGatewayAccess" | AzureDiagnostics &#124; onde ResourceType=="APPLICATIONGATEWAYS" e OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; onde OperaçãoName="ApplicationGatewayPerformance" | AzureDiagnostics &#124; onde ResourceType=="APPLICATIONGATEWAYS" e OperationName=="ApplicationGatewayPerformance" |
     | Grupos de Segurança de Rede | AzureDiagnostics &#124; onde ResourceType=="NETWORKSECURITYGROUPS" |

   + Para qualquer campo que tenha um sufixo de \_ s, \_ d ou g \_ no nome, mude o primeiro personagem para minúsculas
   + Para qualquer campo que tenha um sufixo de \_ o em nome, os dados são divididos em campos individuais com base nos nomes de campo aninhados.
4. Remova a solução *Azure Networking Analytics (Deprecada).*
   + Se estiver a utilizar o PowerShell, use `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

Os dados recolhidos antes da alteração não são visíveis na nova solução. Pode continuar a consultar estes dados utilizando os nomes antigos do Tipo e do campo.

## <a name="troubleshooting"></a>Resolução de problemas
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Passos seguintes
* Utilize [consultas de log no Azure Monitor](../log-query/log-query-overview.md) para ver dados de diagnósticos detalhados do Azure.

