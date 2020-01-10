---
title: Função de Trabalho de Runbook Híbrida do Windows da Automatização do Azure
description: Este artigo fornece informações sobre como instalar um Hybrid Runbook Worker de automação do Azure que você pode usar para executar runbooks em computadores baseados no Windows em seu datacenter local ou ambiente de nuvem.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 696885fa3e082ae7096954fb55b17da5b77788bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418904"
---
# <a name="deploy-a-windows-hybrid-runbook-worker"></a>Implantar um Hybrid Runbook Worker do Windows

Você pode usar o recurso Hybrid Runbook Worker da automação do Azure para executar runbooks diretamente no computador que está hospedando a função e em recursos no ambiente para gerenciar esses recursos locais. Os Runbooks são armazenados e gerenciados na automação do Azure e, em seguida, entregues a um ou mais computadores designados. Este artigo descreve como instalar o Hybrid Runbook Worker em um computador Windows.

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Instalando o Windows Hybrid Runbook Worker

Para instalar e configurar um Hybrid Runbook Worker do Windows, você pode usar um dos três métodos a seguir:

* Para VMs do Azure, você instala o agente do Log Analytics para Windows usando a [extensão da máquina virtual para Windows](../virtual-machines/extensions/oms-windows.md). A extensão instala o agente de Log Analytics em máquinas virtuais do Azure e registra as máquinas virtuais em um espaço de trabalho Log Analytics existente usando um modelo de Azure Resource Manager ou com o PowerShell. Depois que o agente for instalado, a VM poderá ser adicionada a um grupo de Hybrid Runbook Worker em sua conta de automação seguindo a **etapa 4** na seção [implantação manual](#manual-deployment) abaixo.

* Use um runbook de automação para automatizar completamente o processo de configuração de um computador com Windows. Esse é o método recomendado para computadores em seu datacenter ou em outro ambiente de nuvem.

* Siga um procedimento passo a passo para instalar e configurar manualmente a função Hybrid Runbook Worker em sua VM não Azure.

> [!NOTE]
> Para gerenciar a configuração dos servidores que dão suporte à função de Hybrid Runbook Worker com a DSC (configuração de estado desejado), você precisa adicioná-los como nós DSC.

Os requisitos mínimos para um Hybrid Runbook Worker do Windows são:

* Windows Server 2012 ou posterior.
* Windows PowerShell 5,1 ou posterior ([Baixe o WMF 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).
* .NET Framework 4.6.2 ou posterior.
* Dois núcleos.
* 4 GB de RAM.
* Porta 443 (saída).

Para obter mais requisitos de rede para o Hybrid Runbook Worker, consulte [Configurando sua rede](automation-hybrid-runbook-worker.md#network-planning).

Para obter mais informações sobre servidores de integração para gerenciamento com DSC, consulte [máquinas de integração para gerenciamento pelo Azure DSC de automação](automation-dsc-onboarding.md).
Se você habilitar a [solução gerenciamento de atualizações](../operations-management-suite/oms-solution-update-management.md), qualquer computador Windows conectado ao seu espaço de trabalho log Analytics será configurado automaticamente como um Hybrid runbook Worker para dar suporte a runbooks incluídos nesta solução. No entanto, ele não está registrado com nenhum grupo de Hybrid Worker já definido em sua conta de automação. 

O computador pode ser adicionado a um grupo de Hybrid Runbook Worker em sua conta de automação para dar suporte a runbooks de automação, desde que você esteja usando a mesma conta para a solução e a associação de grupo de Hybrid Runbook Worker. Esta funcionalidade foi adicionada à versão 7.2.12024.0 da Função de Trabalho de Runbook Híbrida.

Depois de implantar com êxito um runbook Worker, examine [executar runbooks em um Hybrid runbook Worker](automation-hrw-run-runbooks.md) para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem.

### <a name="automated-deployment"></a>Implementação automatizada

Execute as seguintes etapas para automatizar a instalação e a configuração da função de Hybrid Worker do Windows:

1. Baixe o script New-onpremisehybridworker. ps1 do [Galeria do PowerShell](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker) diretamente do computador que executa a função Hybrid runbook Worker ou de outro computador em seu ambiente. Copie o script para o trabalho.

   O script New-onpremisehybridworker. ps1 requer os seguintes parâmetros durante a execução:

   * *AutomationAccountName* (obrigatório): o nome da sua conta de automação.
   * *AAResourceGroupName* (obrigatório): o nome do grupo de recursos associado à sua conta de automação.
   * *OMSResourceGroupName* (opcional): o nome do grupo de recursos para o espaço de trabalho log Analytics. Se esse grupo de recursos não for especificado, *AAResourceGroupName* será usado.
   * *HybridGroupName* (obrigatório): o nome de um grupo de Hybrid runbook Worker que você especifica como um destino para os runbooks que dão suporte a esse cenário.
   * *SubscriptionId* (obrigatório): a ID da assinatura do Azure na qual sua conta de automação está.
   * *WorkspaceName* (opcional): o nome do espaço de trabalho log Analytics. Se você não tiver um espaço de trabalho Log Analytics, o script criará e configurará um.

   > [!NOTE]
   > Quando ativar soluções, apenas são suportadas determinadas regiões para associar uma área de trabalho do Log Analytics e uma Conta de Automatização.
   >
   > Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

2. Em seu computador, abra o **Windows PowerShell** na tela **inicial** no modo de administrador.
3. No Shell de linha de comando do PowerShell, navegue até a pasta que contém o script que você baixou. Altere os valores para os parâmetros *-AutomationAccountName*, *-AAResourceGroupName*, *-OMSResourceGroupName*, *-HybridGroupName*, *-SubscriptionId*e *-WorkspaceName*. Em seguida, execute o script.

     > [!NOTE]
     > Você será solicitado a autenticar com o Azure depois de executar o script. Você *deve* entrar com uma conta que seja membro da função Administradores de assinatura e coadministrador da assinatura.

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

4. Você será solicitado a concordar em instalar o NuGet e será solicitado a autenticar com suas credenciais do Azure.

5. Depois que o script for concluído, a página **grupos de Hybrid Worker** mostrará o novo grupo e o número de membros. Se for um grupo existente, o número de membros será incrementado. Você pode selecionar o grupo na lista na página **grupos de Hybrid Worker** e selecionar o bloco **Hybrid Workers** . Na página **Hybrid Workers** , você verá cada membro do grupo listado.

### <a name="manual-deployment"></a>Implementação manual

Execute as duas primeiras etapas uma vez para o ambiente de automação e repita as etapas restantes para cada computador de trabalho.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

#### <a name="1-create-a-log-analytics-workspace"></a>1. criar um espaço de trabalho Log Analytics

Se você ainda não tiver um espaço de trabalho Log Analytics, primeiro examine as [diretrizes de design Azure monitor log](../azure-monitor/platform/design-logs-deployment.md) antes de criar um espaço de trabalho. 

#### <a name="2-add-the-automation-solution-to-the-log-analytics-workspace"></a>2. Adicione a solução de automação ao espaço de trabalho Log Analytics

A solução de automação adiciona funcionalidade para a automação do Azure, incluindo suporte para Hybrid Runbook Worker. Quando você adiciona a solução ao seu espaço de trabalho do Log Analytics, ele envia automaticamente os componentes de trabalho para o computador do agente que você instalará na próxima etapa.

Para adicionar a solução de **automação** ao seu espaço de trabalho, execute o PowerShell a seguir.

```powershell-interactive
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <logAnalyticsResourceGroup> -WorkspaceName <LogAnalyticsWorkspaceName> -IntelligencePackName "AzureAutomation" -Enabled $true
```

#### <a name="3-install-the-log-analytics-agent-for-windows"></a>3. instalar o agente de Log Analytics para Windows

O agente Log Analytics para Windows conecta computadores a um espaço de trabalho Azure Monitor Log Analytics. Quando você instala o agente no seu computador e o conecta ao seu espaço de trabalho, ele baixa automaticamente os componentes necessários para Hybrid Runbook Worker.

Para instalar o agente no computador, siga as instruções em [conectar computadores Windows para Azure monitor logs](../log-analytics/log-analytics-windows-agent.md). Você pode repetir esse processo para que vários computadores adicionem vários trabalhos ao seu ambiente.

Quando o agente tiver se conectado com êxito ao seu espaço de trabalho do Log Analytics, após alguns minutos, você poderá executar a consulta a seguir para verificar se ele está enviando dados de pulsação para o espaço de trabalho:

```kusto
Heartbeat 
| where Category == "Direct Agent" 
| where TimeGenerated > ago(30m)
```

Nos resultados da pesquisa retornados, você deverá ver os registros de pulsação do computador indicando que ele está conectado e relatando para o serviço. O registro de pulsação é encaminhado de cada agente por padrão para seu espaço de trabalho atribuído. Você pode verificar se o agente baixou corretamente a solução de automação quando ela tem uma pasta chamada **AzureAutomationFiles** em C:\Program Files\Microsoft Monitoring Agent\Agent. Para confirmar a versão do Hybrid Runbook Worker, você pode navegar até C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ e anotar a subpasta \\*version* .

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4. instalar o ambiente de runbook e conectar-se à automação do Azure

Quando você configura um agente para relatar a um espaço de trabalho Log Analytics, a solução de automação envia por push o módulo **HybridRegistration** do PowerShell, que contém o cmdlet **Add-HybridRunbookWorker** . Você usa esse cmdlet para instalar o ambiente de runbook no computador e registrá-lo com a automação do Azure.

Abra uma sessão do PowerShell no modo de administrador e execute os seguintes comandos para importar o módulo:

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module .\HybridRegistration.psd1
```

Em seguida, execute o cmdlet **Add-HybridRunbookWorker** usando a seguinte sintaxe:

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

Você pode obter as informações necessárias para esse cmdlet na página **gerenciar chaves** no portal do Azure. Abra essa página selecionando a opção **chaves** na página **configurações** na sua conta de automação.

![Página "gerenciar chaves"](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** é o nome do grupo de Hybrid runbook Worker. Se esse grupo já existir na conta de automação, o computador atual será adicionado a ele. Se esse grupo não existir, ele será adicionado.
* **Ponto de extremidade** é a entrada de **URL** na página **gerenciar chaves** .
* **Token** é a entrada de **chave de acesso primária** na página **gerenciar chaves** .

Para receber informações detalhadas sobre a instalação, use a opção **-Verbose** com **Add-HybridRunbookWorker**.

#### <a name="5-install-powershell-modules"></a>5. instalar módulos do PowerShell

Os Runbooks podem usar qualquer uma das atividades e cmdlets definidos nos módulos instalados em seu ambiente de automação do Azure. Esses módulos não são implantados automaticamente em computadores locais, portanto, você deve instalá-los manualmente. A exceção é o módulo do Azure, que é instalado por padrão e fornece acesso a cmdlets para todos os serviços e atividades do Azure para a automação do Azure.

Como a principal finalidade do recurso de Hybrid Runbook Worker é gerenciar recursos locais, é mais provável que você precise instalar os módulos que dão suporte a esses recursos. Para obter informações sobre a instalação de módulos do Windows PowerShell, consulte [instalando módulos](/powershell/scripting/developer/windows-powershell). 

Os módulos instalados devem estar em um local referenciado pela variável de ambiente **PSModulePath** para que o Hybrid Worker possa importá-los automaticamente. Para obter mais informações, consulte [modificando o caminho de instalação do PSModulePath](/powershell/scripting/developer/windows-powershell).

## <a name="next-steps"></a>Passos seguintes

* Para saber como configurar seus runbooks para automatizar processos em seu datacenter local ou em outro ambiente de nuvem, consulte [executar runbooks em um Hybrid runbook Worker](automation-hrw-run-runbooks.md).
* Para obter instruções sobre como remover Hybrid runbook Workers, consulte [remover Hybrid runbook Workers da automação do Azure](automation-hybrid-runbook-worker.md#remove-a-hybrid-runbook-worker).
* Para saber como solucionar problemas de seus Hybrid runbook Workers, consulte [solução de problemas de Hybrid runbook Workers do Windows](troubleshoot/hybrid-runbook-worker.md#windows)
* Para obter etapas adicionais sobre como solucionar problemas com o Gerenciamento de Atualizações, consulte [Gerenciamento de atualizações: solução de problemas](troubleshoot/update-management.md).
