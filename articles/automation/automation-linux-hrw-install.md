---
title: Implementar um Trabalhador de Runbook Híbrido Linux na Azure Automation
description: Este artigo diz como instalar um Azure Automation Hybrid Runbook Worker para executar livros em máquinas baseadas em Linux no seu datacenter local ou ambiente em nuvem.
services: automation
ms.subservice: process-automation
ms.date: 06/17/2020
ms.topic: conceptual
ms.openlocfilehash: a8679c189e77fe7b191a645b07c68b6101604644
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85079151"
---
# <a name="deploy-a-linux-hybrid-runbook-worker"></a>Implementar um Trabalhador de Runbook Híbrido Linux

Você pode usar a funcionalidade Hybrid Runbook Worker da Azure Automation para executar runbooks diretamente na máquina que está hospedando o papel e contra recursos no ambiente para gerir esses recursos locais. O Trabalhador de Runbook Híbrido Linux executa runbooks como um utilizador especial que pode ser elevado para executar comandos que precisam de elevação. A Azure Automation armazena e gere os runbooks e depois entrega-os a uma ou mais máquinas designadas. Este artigo descreve como instalar o Trabalhador de Runbook Híbrido numa máquina Linux, como remover o trabalhador e como remover um grupo híbrido de runbook worker.

Depois de implementar com sucesso um trabalhador de runbook, [reveja os runbooks de runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md) para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que tem o seguinte:

### <a name="a-log-analytics-workspace"></a>Um espaço de trabalho Log Analytics

O papel de Trabalhador de Runbook Híbrido depende de um espaço de trabalho Azure Monitor Log Analytics para instalar e configurar o papel. Pode criá-lo através [do Azure Resource Manager,](../azure-monitor/samples/resource-manager-workspace.md#create-a-log-analytics-workspace)através [do PowerShell,](../azure-monitor/scripts/powershell-sample-create-workspace.md?toc=/powershell/module/toc.json)ou no [portal Azure](../azure-monitor/learn/quick-create-workspace.md).

Se não tiver um espaço de trabalho do Azure Monitor Log Analytics, reveja a orientação de [design do Registo do Monitor Azure](../azure-monitor/platform/design-logs-deployment.md) antes de criar o espaço de trabalho.

Se tiver um espaço de trabalho, mas não estiver ligado à sua conta de Automação, permitir uma funcionalidade de Automação adiciona funcionalidade para a Azure Automation, incluindo suporte para o Trabalhador de Runbook Híbrido. Quando ativa uma das funcionalidades da Azure Automation no seu espaço de trabalho Log Analytics, especificamente [Gestão de Atualização](automation-update-management.md) ou [Rastreio e Inventário de Alterações,](change-tracking.md)os componentes do trabalhador são automaticamente empurrados para a máquina do agente.

   Para adicionar a função de Gestão de Atualização ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell:

    ```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "Updates" -Enabled $true
    ```

   Para adicionar a função de Rastreio e Inventário de Alterações ao seu espaço de trabalho, execute o seguinte cmdlet PowerShell:

    ```powershell-interactive
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <logAnalyticsWorkspaceName> -IntelligencePackName "ChangeTracking" -Enabled $true
    ```

### <a name="log-analytics-agent"></a>Agente do Log Analytics

O papel de Trabalhador de Runbook Híbrido requer o [agente Log Analytics](../azure-monitor/platform/log-analytics-agent.md) para o sistema operativo Linux suportado.

### <a name="supported-linux-operating-systems"></a>Sistemas operativos Linux suportados

A funcionalidade Hybrid Runbook Worker suporta as seguintes distribuições:

* Amazon Linux 2012.09 a 2015.09 (x86/x64)
* CentOS Linux 5, 6 e 7 (x86/x64)
* Oracle Linux 5, 6 e 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6 e 7 (x86/x64)
* Debian GNU/Linux 6, 7 e 8 (x86/x64)
* Ubuntu 12.04 LTS, 14.04 LTS, 16.04 LTS e 18.04 (x86/x64)
* SUSE Linux Enterprise Server 11 e 12 (x86/x64)

### <a name="minimum-requirements"></a>Requisitos mínimos

Os requisitos mínimos para um Trabalhador de Runbook Híbrido Linux são:

* Dois núcleos
* 4 GB de RAM
* Porto 443 (saída)

| **Pacote obrigatório** | **Descrição** | **Versão mínima**|
|--------------------- | --------------------- | -------------------|
|Glibc |Biblioteca GNU C| 2.5-12 |
|Openssl| Bibliotecas OpenSSL | 1.0 (TLS 1.1 e TLS 1.2 são suportados)|
|Curl | cURL cliente web | 7.15.5|
|Pitão-ctypes | Python 2.x é necessário |
|PAM | Módulos de Autenticação Incorporável|
| **Pacote opcional** | **Descrição** | **Versão mínima**|
| PowerShell Core | Para executar os livros powerShell, o PowerShell Core precisa de ser instalado. Consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo. | 6.0.0 |

## <a name="supported-runbook-types"></a>Tipos de runbook suportados

Os Trabalhadores de Runbook Híbridos Linux suportam um conjunto limitado de tipos de runbook na Azure Automation, e são descritos na tabela seguinte.

|Tipo de livro de execução | Suportado |
|-------------|-----------|
|Python 2 |Yes |
|PowerShell |Sim<sup>1</sup> |
|Fluxo de Trabalho do PowerShell |No |
|Gráficos |No |
|Fluxo de trabalho gráfico powershell |No |

<sup>1</sup> Os livros powerShell requerem que o PowerShell Core seja instalado na máquina Linux. Consulte [a instalação do PowerShell Core no Linux](/powershell/scripting/install/installing-powershell-core-on-linux) para aprender a instalá-lo.

## <a name="install-a-linux-hybrid-runbook-worker"></a>Instale um Trabalhador de Runbook Híbrido Linux

Para instalar e configurar um Trabalhador de Runbook Híbrido Linux, execute os seguintes passos.

1. Insaque o agente Log Analytics na máquina alvo.

    * Para Azure VMs, instale o agente Log Analytics para Linux utilizando a [extensão da máquina virtual para Linux](../virtual-machines/extensions/oms-linux.md). A extensão instala o agente Log Analytics em máquinas virtuais Azure e inscreve máquinas virtuais num espaço de trabalho log analytics existente utilizando um modelo de Gestor de Recursos Azure ou o CLI Azure. Uma vez instalado o agente, o VM pode ser adicionado a um grupo híbrido de runbook worker na sua conta Automation.

    * Para VMs não-Azure, instale o agente Log Analytics para Linux utilizando as opções de implementação descritas nos computadores Connect Linux para o artigo [do Azure Monitor.](../azure-monitor/platform/agent-linux.md) Pode repetir este processo para que várias máquinas adicione vários trabalhadores ao seu ambiente. Uma vez instalado o agente, os VMs podem ser adicionados a um grupo híbrido de Runbook Worker na sua conta Automation.

    > [!NOTE]
    > Para gerir a configuração das máquinas que suportam a função de Trabalhador de Runbook Híbrido com Configuração de Estado Desejada (DSC), deve adicionar as máquinas como nós DSC.

    > [!NOTE]
    > A [conta de nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) com as permissões de sudo correspondentes deve estar presente durante a instalação do Trabalhador Híbrido Linux. Se tentar instalar o trabalhador e a conta não estiver presente ou não tiver as permissões adequadas, a instalação falha.

2. Verifique se o agente está a reportar-se ao espaço de trabalho.

    O agente Log Analytics do Linux liga as máquinas a um espaço de trabalho Azure Monitor Log Analytics. Quando instala o agente na sua máquina e o liga ao seu espaço de trabalho, descarrega automaticamente os componentes necessários para o Trabalhador de Runbook Híbrido.

    Quando o agente tiver ligado com sucesso ao seu espaço de trabalho Log Analytics após alguns minutos, pode executar a seguinte consulta para verificar se está a enviar dados de batimentos cardíacos para o espaço de trabalho.

    ```kusto
    Heartbeat 
    | where Category == "Direct Agent"
    | where TimeGenerated > ago(30m)
    ```

    Nos resultados da pesquisa, deverá consultar os registos de batimentos cardíacos da máquina, indicando que está ligado e a reportar ao serviço. Por defeito, cada agente encaminha um registo de batimentos cardíacos para o seu espaço de trabalho atribuído.

3. Executar o seguinte comando para adicionar a máquina a um grupo híbrido de trabalhador runbook, alterando os valores para os parâmetros *-w*, *-k*, *-g*, e *-e*. Para o parâmetro *-g,* substitua o valor pelo nome do grupo híbrido Runbook Worker a que o novo Trabalhador de Runbook Híbrido Linux deve aderir. Se o nome não existir na sua conta automation, é criado um novo grupo híbrido runbook worker com esse nome.

   ```bash
   sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <logAnalyticsworkspaceId> -k <automationSharedKey> -g <hybridGroupName> -e <automationEndpoint>
   ```

4. Após a conclusão do comando, a página do Grupo operário Híbrido no portal Azure mostra o novo grupo e o número de membros. Se este for um grupo existente, o número de membros é incrementado. Pode selecionar o grupo na lista na página grupos de trabalhadores híbridos e selecionar o azulejo **híbrido operário.** Na página "Trabalhadores Híbridos", vê cada membro do grupo listado.

    > [!NOTE]
    > Se estiver a utilizar a extensão da máquina virtual Log Analytics para o Linux para um Azure VM, recomendamos que a definição `autoUpgradeMinorVersion` `false` para versões de auto-actualização possa causar problemas com o Trabalhador de Runbook Híbrido. Para aprender a atualizar a extensão manualmente, consulte a [implementação do CLI Azure](../virtual-machines/extensions/oms-linux.md#azure-cli-deployment).

## <a name="turn-off-signature-validation"></a>Desativar a validação da assinatura

Por padrão, os Trabalhadores do Runbook Híbrido Linux requerem validação de assinaturas. Se fizeres um livro de corridas não assinado contra um trabalhador, vês um `Signature validation failed` erro. Para desligar a validação da assinatura, executar o seguinte comando. Substitua o segundo parâmetro pelo seu ID do espaço de trabalho Log Analytics.

 ```bash
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <logAnalyticsworkspaceId>
 ```

## <a name="remove-the-hybrid-runbook-worker-from-an-on-premises-linux-machine"></a><a name="remove-linux-hybrid-runbook-worker"></a>Remova o Trabalhador de Runbook Híbrido de uma máquina Linux no local

Pode utilizar o comando `ls /var/opt/microsoft/omsagent` do Trabalhador de Runbook Híbrido para obter o ID do espaço de trabalho. É criada uma pasta que é nomeada com o ID do espaço de trabalho.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Este script não remove o agente Log Analytics para Linux da máquina. Apenas remove a funcionalidade e configuração da função De Trabalhador de Runbook Híbrido.

## <a name="remove-a-hybrid-worker-group"></a>Remove a Hybrid Worker group (Remover um grupo de Função de Trabalho Híbrida)

Para remover um grupo híbrido de runbook worker de máquinas Linux, você usa os mesmos passos que para um grupo de trabalhadores híbridos Windows. Ver [Remover um grupo de trabalhadores híbridos](automation-windows-hrw-install.md#remove-a-hybrid-worker-group).

## <a name="next-steps"></a>Passos seguintes

* Para aprender a configurar os seus livros para automatizar processos no seu datacenter no local ou noutro ambiente em nuvem, consulte [runbooks Runbooks num Trabalhador de Runbook Híbrido](automation-hrw-run-runbooks.md).

* Para aprender a resolver problemas com os seus Trabalhadores de Runbook Híbridos, consulte problemas de trabalho híbrido de [resolução de problemas - Linux](troubleshoot/hybrid-runbook-worker.md#linux).
