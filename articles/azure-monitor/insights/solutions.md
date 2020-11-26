---
title: Soluções de monitorização no Azure Monitor Microsoft Docs
description: As soluções de monitorização no Azure Monitor são uma recolha de regras lógicas, de visualização e de aquisição de dados que fornecem métricas em torno de uma determinada área problemática.  Este artigo fornece informações sobre a instalação e utilização de soluções de monitorização.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/16/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9ced3dfeccdbac5f0eb220cf0e104679f263aac
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186869"
---
# <a name="monitoring-solutions-in-azure-monitor"></a>Soluções de monitorização no Azure Monitor

As soluções de monitorização no Azure Monitor fornecem uma análise do funcionamento de uma determinada aplicação ou serviço Azure. Este artigo fornece uma breve visão geral das soluções de monitorização em Azure e detalhes sobre a sua utilização e instalação. Pode adicionar soluções de monitorização ao Azure Monitor para quaisquer aplicações e serviços que utilize. Normalmente estão disponíveis sem custos, mas recolhem dados que podem invocar taxas de utilização.

## <a name="use-monitoring-solutions"></a>Utilizar soluções de monitorização

A página **de visão geral das** soluções no Azure Monitor exibe um azulejo para cada solução instalada num espaço de trabalho log analytics. Para abrir esta página, aceda ao **Monitor Azure** no [portal Azure](https://ms.portal.azure.com). No menu **Insights,** selecione **Mais** para abrir o **Insights Hub** e, em seguida, clique em **espaços de trabalho Log Analytics**.

[![Centro de Insights](media/solutions/insights-hub.png)](media/solutions/insights-hub.png#lightbox)


Utilize as caixas de dropdown na parte superior do ecrã para alterar o espaço de trabalho ou o intervalo de tempo utilizado para os azulejos. Clique no azulejo para obter uma solução para abrir a sua visão que inclui uma análise mais detalhada dos seus dados recolhidos.

[![O Screenshot mostra o menu do portal Azure com soluções selecionadas e soluções exibidas no painel soluções.](media/solutions/overview.png)](media/solutions/overview.png#lightbox)

As soluções de monitorização podem conter vários tipos de recursos Azure, e você pode ver quaisquer recursos incluídos com uma solução como qualquer outro recurso. Por exemplo, quaisquer consultas de registo incluídas na solução estão listadas em **Consultas de Solução** no [Explorador de Consultas.](../log-query/log-analytics-tutorial.md) Pode utilizar essas consultas ao realizar análises ad hoc com [consultas de registo](../log-query/log-query-overview.md).

## <a name="list-installed-monitoring-solutions"></a>Listar soluções de monitorização instaladas

### <a name="portal"></a>[Portal](#tab/portal)

Utilize o seguinte procedimento para listar as soluções de monitorização instaladas na sua subscrição.

1. Aceda ao [portal do Azure](https://ms.portal.azure.com). Procure e selecione **Soluções.**
1. As soluções instaladas em todos os seus espaços de trabalho estão listadas. O nome da solução é seguido pelo nome do espaço de trabalho em que está instalado.
1. Utilize as caixas de dropdown na parte superior do ecrã para filtrar por subscrição ou grupo de recursos.

![Listar todas as soluções](media/solutions/list-solutions-all.png)

Clique no nome de uma solução para abrir a sua página de resumo. Esta página exibe quaisquer visualizações incluídas na solução e fornece diferentes opções para a própria solução e seu espaço de trabalho. Consulte a página de resumo para obter uma solução utilizando um dos procedimentos acima para listar soluções e, em seguida, clique no nome da solução.

![Propriedades de solução](media/solutions/solution-properties.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando [da lista de soluções de análise de registos](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-list) do monitor az para listar as soluções de monitorização instaladas na sua subscrição.   Antes de executar o `list` comando, siga os pré-requisitos encontrados na [Instalação de uma solução de monitorização](#install-a-monitoring-solution).

```azurecli
# List all log-analytics solutions in the current subscription.
az monitor log-analytics solution list

# List all log-analytics solutions for a specific subscription
az monitor log-analytics solution list --subscription MySubscription

# List all log-analytics solutions in a resource group
az monitor log-analytics solution list --resource-group MyResourceGroup
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Utilize o [cmdlet Get-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/get-azmonitorloganalyticssolution) para listar as soluções de monitorização instaladas na sua subscrição. Antes de executar estes comandos, siga os pré-requisitos encontrados na [Instalação de uma solução de monitorização](#install-a-monitoring-solution).

```azurepowershell-interactive
# List all log-analytics solutions in the current subscription.
Get-AzMonitorLogAnalyticsSolution

# List all log-analytics solutions for a specific subscription
Get-AzMonitorLogAnalyticsSolution -SubscriptionId 00000000-0000-0000-0000-000000000000

# List all log-analytics solutions in a resource group
Get-AzMonitorLogAnalyticsSolution -ResourceGroupName MyResourceGroup
```

* * *

## <a name="install-a-monitoring-solution"></a>Instale uma solução de monitorização

### <a name="portal"></a>[Portal](#tab/portal)

As soluções de monitorização da Microsoft e dos parceiros estão disponíveis no [Azure Marketplace.](https://azuremarketplace.microsoft.com) Pode pesquisar as soluções disponíveis e instalá-las utilizando o seguinte procedimento. Quando instalar uma solução, deve selecionar um [espaço de trabalho Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde os seus dados serão recolhidos.

1. A partir da [lista de soluções para a sua subscrição,](#list-installed-monitoring-solutions)clique em **Adicionar**.
1. Procure ou procure uma solução. Também pode navegar em soluções a partir [deste link de pesquisa.](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/management-tools?page=1&subcategories=management-solutions)
1. Localize a solução de monitorização desejada e leia a sua descrição.
1. Clique em **Criar** para iniciar o processo de instalação.
1. Quando o processo de instalação começar, é-lhe solicitado que especifique o espaço de trabalho do Log Analytics e forneça qualquer configuração necessária para a solução.

![Instalar uma solução](media/solutions/install-solution.png)

### <a name="install-a-solution-from-the-community"></a>Instalar uma solução da comunidade

Os membros da comunidade podem submeter soluções de gestão aos modelos Azure Quickstart. Pode instalar estas soluções diretamente ou descarregá-las modelos para posterior instalação.

1. Acompanhe o processo descrito no [espaço de trabalho do Log Analytics e na conta Dem automação](#log-analytics-workspace-and-automation-account) para ligar um espaço de trabalho e conta.
2. Vá a [Azure Quickstart Modelos](https://azure.microsoft.com/documentation/templates/).
3. Procure uma solução que lhe interesse.
4. Selecione a solução a partir dos resultados para ver os seus detalhes.
5. Clique no **botão Implementar para Azure.**
6. É solicitado que forneça informações como o grupo de recursos e a localização, além de valores para quaisquer parâmetros na solução.
7. Clique **em Comprar** para instalar a solução.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

### <a name="prepare-your-environment"></a>Preparar o ambiente

1. Instalar a CLI do Azure

   Tem de [instalar o Azure CLI](/cli/azure/install-azure-cli) antes de executar os comandos de referência CLI.  Se preferir, também pode usar a Azure Cloud Shell para completar os passos deste artigo.  Azure Cloud Shell é um ambiente de conchas interativas que utiliza através do seu navegador.  Inicie a Cloud Shell utilizando um destes métodos:

   - Open Cloud Shell indo para [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menu no canto superior direito no [portal Azure](https://portal.azure.com)

1. Inicie sessão.

   Se estiver a utilizar uma instalação local do CLI, inicie sessão com o comando [de login az.](/cli/azure/reference-index#az-login)  Siga os passos apresentados no seu terminal para concluir o processo de autenticação.

    ```azurecli
    az login
    ```

1. Instale a `log-analytics-solution` extensão

   O `log-analytics-solution` comando é uma extensão experimental do núcleo Azure CLI. Saiba mais sobre referências de extensão de [extensão de utilização com Azure CLI](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name log-analytics-solution
   ```

   Espera-se o seguinte aviso.

   ```output
   The installed extension `log-analytics-solution` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="install-a-solution-with-the-azure-cli"></a>Instale uma solução com o Azure CLI

Quando instalar uma solução, deve selecionar um [espaço de trabalho Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde os seus dados serão recolhidos.  Com o Azure CLI, gere espaços de trabalho utilizando os comandos de referência do [espaço de trabalho de monitorização de log-analytics az](/cli/azure/monitor/log-analytics/workspace) monitor.  Acompanhe o processo descrito no [espaço de trabalho do Log Analytics e na conta Dem automação](#log-analytics-workspace-and-automation-account) para ligar um espaço de trabalho e conta.

Utilize a [solução de análise de registos](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution) do monitor az para instalar uma solução de monitorização.  Os parâmetros nos suportes quadrados são opcionais.

```azurecli
az monitor log-analytics solution create --name
                                         --plan-product
                                         --plan-publisher
                                         --resource-group
                                         --workspace
                                         [--no-wait]
                                         [--tags]
```

Aqui está uma amostra de código que cria uma solução de log-analytics para o produto plano de OMSGallery/Containers.

```azurecli
az monitor log-analytics solution create --resource-group MyResourceGroup \
                                         --name Containers({SolutionName}) \
                                         --tags key=value \
                                         --plan-publisher Microsoft  \
                                         --plan-product "OMSGallery/Containers" \
                                         --workspace "/subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/ \
                                           Microsoft.OperationalInsights/workspaces/{WorkspaceName}"
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="prepare-your-environment"></a>Preparar o ambiente

1. Instalar o Azure PowerShell

   Tem de instalar o [Azure PowerShell](/powershell/azure/install-az-ps) antes de executar os comandos de referência Azure PowerShell. Se preferir, também pode usar a Azure Cloud Shell para completar os passos deste artigo. Azure Cloud Shell é um ambiente de conchas interativas que utiliza através do seu navegador. Inicie a Cloud Shell utilizando um destes métodos:

   - Open Cloud Shell indo para [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menu no canto superior direito no [portal Azure](https://portal.azure.com)

   > [!IMPORTANT]
   > Enquanto o módulo **Az.MonitoringSolutions** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Depois de este módulo PowerShell ficar geralmente disponível, fará parte das futuras versões do módulo Az PowerShell e disponível por padrão a partir de Azure Cloud Shell.

   ```azurepowershell-interactive
   Install-Module -Name Az.MonitoringSolutions
   ```

1. Inicie sessão.

   Se estiver a utilizar uma instalação local do PowerShell, inscreva-se utilizando o [cmdlet Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Siga os passos apresentados no PowerShell para concluir o processo de autenticação.

   ```azurepowershell
   Connect-AzAccount
   ```

### <a name="install-a-solution-with-azure-powershell"></a>Instale uma solução com a Azure PowerShell

Quando instalar uma solução, deve selecionar um [espaço de trabalho Log Analytics](../platform/manage-access.md) onde a solução será instalada e onde os seus dados serão recolhidos. Com o Azure PowerShell, gere espaços de trabalho utilizando os cmdlets no módulo [Az.MonitoringSolutions](/powershell/module/az.monitoringsolutions) PowerShell. Acompanhe o processo descrito no [espaço de trabalho do Log Analytics e na conta Dem automação](#log-analytics-workspace-and-automation-account) para ligar um espaço de trabalho e conta.

Utilize o [cmdlet New-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/new-azmonitorloganalyticssolution) para instalar uma solução de monitorização. Os parâmetros nos suportes quadrados são opcionais.

```azurepowershell
New-AzMonitorLogAnalyticsSolution -ResourceGroupName <string> -Type <string> -Location <string>
-WorkspaceResourceId <string> [-SubscriptionId <string>] [-Tag <hashtable>]
[-DefaultProfile <psobject>] [-Break] [-HttpPipelineAppend <SendAsyncStep[]>]
[-HttpPipelinePrepend <SendAsyncStep[]>] [-Proxy <uri>] [-ProxyCredential <pscredential>]
[-ProxyUseDefaultCredentials] [-WhatIf] [-Confirm] [<CommonParameters>]
```

O exemplo a seguir cria uma solução de análise de registo de monitor para o espaço de trabalho de análise de registo.

```azurepowershell-interactive
$workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName MyResourceGroup -Name WorkspaceName
New-AzMonitorLogAnalyticsSolution -Type Containers -ResourceGroupName MyResourceGroup -Location $workspace.Location -WorkspaceResourceId $workspace.ResourceId
```

* * *

## <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics espaço de trabalho e conta de Automação

Todas as soluções de monitorização requerem um [espaço de trabalho Log Analytics](../platform/manage-access.md) para armazenar dados recolhidos pela solução e para hospedar as suas pesquisas e vistas de registo. Algumas soluções também requerem uma [conta De Automação](../../automation/automation-security-overview.md) para conter livros de execução e recursos relacionados. O espaço de trabalho e a conta devem satisfazer os seguintes requisitos.

* Cada instalação de uma solução só pode utilizar um espaço de trabalho log Analytics e uma conta Automation. Pode instalar a solução separadamente em vários espaços de trabalho.
* Se uma solução requer uma conta Demôm automação, então o espaço de trabalho do Log Analytics e a conta de Automação devem estar ligados entre si. Um espaço de trabalho Log Analytics só pode estar ligado a uma conta de Automação, e uma conta de Automação só pode estar ligada a um espaço de trabalho do Log Analytics.

Quando instala uma solução através do Azure Marketplace, é solicitado para um espaço de trabalho e conta de Automação. A ligação entre eles é criada se ainda não estiverem ligadas.

### <a name="verify-the-link-between-a-log-analytics-workspace-and-automation-account"></a>Verifique a ligação entre um espaço de trabalho do Log Analytics e uma conta de Automação

Pode verificar a ligação entre um espaço de trabalho Log Analytics e uma conta Automation utilizando o seguinte procedimento.

1. Selecione a conta Automation no portal Azure.
1. Percorra a secção **de Recursos Relacionados** do menu e selecione **espaço de trabalho linked**.
1. Se o **Espaço de Trabalho** está ligado a uma conta Dem automação, então esta página lista o espaço de trabalho a que está ligada. Se selecionar o nome do espaço de trabalho listado, é redirecionado para a página geral para esse espaço de trabalho.

## <a name="remove-a-monitoring-solution"></a>Remover uma solução de monitorização

### <a name="portal"></a>[Portal](#tab/portal)

Para remover uma solução instalada utilizando o portal, localize-a na [lista de soluções instaladas](#list-installed-monitoring-solutions). Clique no nome da solução para abrir a sua página de resumo e, em seguida, clique em **Apagar**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma solução instalada utilizando o CLI Azure, utilize o comando [de eliminação da solução de registo do monitor az.](/cli/azure/ext/log-analytics-solution/monitor/log-analytics/solution#ext-log-analytics-solution-az-monitor-log-analytics-solution-delete)

```azurecli
az monitor log-analytics solution delete --name
                                         --resource-group
                                         [--no-wait]
                                         [--yes]
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para remover uma solução instalada utilizando a Azure PowerShell, utilize o [cmdlet remove-AzMonitorLogAnalyticsSolution.](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution)

```azurepowershell-interactive
Remove-AzMonitorLogAnalyticsSolution  -ResourceGroupName MyResourceGroup -Name WorkspaceName
```

* * *

## <a name="next-steps"></a>Passos seguintes

* Obtenha uma [lista de soluções de monitorização da Microsoft.](../monitor-reference.md)
* Saiba como criar consultas para analisar [dados](../log-query/log-query-overview.md) recolhidos através da monitorização de soluções.
* Consulte todos os [comandos Azure CLI para O Monitor Azure](/cli/azure/azure-cli-reference-for-monitor).