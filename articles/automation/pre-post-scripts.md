---
title: Configurar scripts de pré e post em sua implementação de gestão de atualizações no Azure (pré-visualização)
description: Este artigo descreve como configurar e gerir o pré e implementações de atualizações de scripts para postagem
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 02/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dc30b28203ad416370f1304436e7e6e642921be9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441513"
---
# <a name="manage-pre-and-post-scripts-preview"></a>Gerir scripts do anteriores e post (pré-visualização)

Scripts de pré e post permitem executar runbooks do PowerShell na sua conta de automatização antes (pré-tarefa) e depois da implantação (pós-tarefa) uma atualização. Scripts de pré e post são executados no contexto do Azure e não localmente. Pré-scripts de são executados no início da implantação da atualização. Scripts de publicação são executados no final da implementação e depois de quaisquer reinícios que estão configurados.

## <a name="runbook-requirements"></a>Requisitos do Runbook

Para um runbook a ser utilizado como um script de pré ou post, o runbook tem de ser importados para a sua conta de automatização e publicado. Para saber mais sobre este processo, veja [publicar um runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Usando um script de pré/pós

Para utilizar o pré e ou postagem de script numa implementação de atualização, comece por criar uma implementação de atualização. Selecione **pré- scripts de + Scripts de publicação (pré-visualização)**. Esta ação abre o **pré-scripts de selecione + pós-scripts** página.  

![Selecione os scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que pretende utilizar, neste exemplo, utilizou o **UpdateManagement TurnOnVms** runbook. Ao selecionar o runbook a **configurar o Script** é aberta a página, fornecer valores para os parâmetros e escolha **Script prévio**. Clique em **OK** quando tiver terminado.

![Configurar o script](./media/pre-post-scripts/configure-script.png)

Repita este processo para o **UpdateManagement TurnOffVms** script. Mas ao escolher o **tipo de Script**, escolha **Script posterior**.

O **itens selecionados** secção mostra agora ambos os seus scripts selecionados e tem um script prévio e o outro é um script posterior.

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Conclua a configuração da sua implementação de atualização.

Quando a implementação de atualização estiver concluída, pode aceder à **implementações de atualizações** para ver os resultados. Como pode ver, o estado do script prévio e o script posterior são fornecidos.

![Resultados da atualização](./media/pre-post-scripts/update-results.png)

Ao clicar em executar a implementação da atualização, é-lhe fornecido detalhes adicionais para os scripts de pré e post. É fornecida uma ligação para a origem do script no momento da execução.

![Resultados de execução de implementação](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passando parâmetros

Quando configurar o pré e post scripts, pode passar parâmetros, assim como agendar um runbook. Os parâmetros são definidos no momento da criação de implementação de atualização. Scripts de pré e Post suportam os seguintes tipos:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [único]
* [double]
* [DateTime]
* [string]

Se precisar de outro tipo de objeto, pode convertê-lo a outro tipo com a sua própria lógica no runbook.

Além dos parâmetros do runbook padrão, é fornecido um parâmetro adicional. Este parâmetro é **SoftwareUpdateConfigurationRunContext**. Este parâmetro é uma cadeia de caracteres do JSON e, se definir o parâmetro no script anterior ou post, é automaticamente passado pela implementação de atualizações. O parâmetro contém informações sobre a implementação da atualização, que é um subconjunto de informações retornado pelos [SoftwareUpdateconfigurations API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) a tabela seguinte mostra as propriedades que são fornecidas na variável:

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriedades de SoftwareUpdateConfigurationRunContext

|Propriedade  |Descrição  |
|---------|---------|
|SoftwareUpdateConfigurationName     | O nome da configuração de atualização de Software        |
|SoftwareUpdateConfigurationRunId     | O id exclusivo para a execução.        |
|SoftwareUpdateConfigurationSettings     | Uma coleção de propriedades relacionados com a configuração de atualização de Software         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas de operativos visados para a implementação da atualização         |
|SoftwareUpdateConfigurationSettings.duration     | A duração máxima de implementação de atualizações executar como `PT[n]H[n]M[n]S` de acordo com ISO8601, também chamado de "janela de manutenção"          |
|SoftwareUpdateConfigurationSettings.Windows     | Uma coleção de propriedades relacionadas a computadores Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Uma lista de KBs que são excluídos da implementação de atualizações        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificações de atualização selecionadas para a implementação da atualização        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Definições para a implementação da atualização de reinício        |
|azureVirtualMachines     | Uma lista de resourceIds para as VMs do Azure na implementação de atualizações        |
|nonAzureComputerNames|Uma lista dos computadores não pertencentes ao Azure FQDNs da implementação de atualizações|

O exemplo seguinte é uma cadeia de caracteres do JSON passada para o **SoftwareUpdateConfigurationRunContext** parâmetro:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ], 
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Um exemplo completo com todas as propriedades pode ser encontrado em: [Para obter as configurações de atualização de software - nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

## <a name="samples"></a>Amostras

Exemplos de scripts de pré e post podem ser encontrados na [Galeria de centro de scripts](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell), ou importados através do portal do Azure. A importação dos mesmos através do portal, na sua conta de automatização, em **automatização de processos**, selecione **Galeria de Runbooks**. Uso **gestão de atualizações** para o filtro.

![Lista de galeria](./media/pre-post-scripts/runbook-gallery.png)

Ou pode pesquisá-los pelo respetivo nome de script como visto na lista seguinte:

* Gestão de atualizações - ativar VMs
* Gestão de atualizações - desative VMs
* Gestão de atualizações - execução de Script localmente
* Atualizar gestão - modelo para os Scripts de pré/pós
* Gestão de atualizações - execução de Script com o comando de execução

> [!IMPORTANT]
> Depois de importar os runbooks, deve **publicar** -los antes de poderem ser utilizados. Para fazer o runbook que encontrar na sua conta de automatização, selecione **edite**e clique em **Publish**.

Os exemplos são baseados no modelo básico que está definido no exemplo a seguir. Este modelo pode ser utilizado para criar seu próprio runbook para utilizar com os scripts de pré e post. A lógica necessária para autenticar com o Azure e a manipulação do `SoftwareUpdateConfigurationRunContext` parâmetro estão incluídos.

```powershell
<# 
.SYNOPSIS 
 Barebones script for Update Management Pre/Post 
 
.DESCRIPTION 
  This script is intended to be run as a part of Update Management Pre/Post scripts.  
  It requires a RunAs account. 
 
.PARAMETER SoftwareUpdateConfigurationRunContext 
  This is a system variable which is automatically passed in by Update Management during a deployment. 
#> 
 
param( 
    [string]$SoftwareUpdateConfigurationRunContext 
) 
#region BoilerplateAuthentication 
#This requires a RunAs account 
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection' 
 
Add-AzureRmAccount ` 
    -ServicePrincipal ` 
    -TenantId $ServicePrincipalConnection.TenantId ` 
    -ApplicationId $ServicePrincipalConnection.ApplicationId ` 
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint 
 
$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID 
#endregion BoilerplateAuthentication 
 
#If you wish to use the run context, it must be converted from JSON 
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext 
#Access the properties of the SoftwareUpdateConfigurationRunContext 
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines 
$runId = $context.SoftwareUpdateConfigurationRunId 
 
Write-Output $context 
 
#Example: How to create and write to a variable using the pre-script: 
<# 
#Create variable named after this run so it can be retrieved 
New-AzureRmAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false 
#Set value of variable  
Set-AutomationVariable –Name $runId -Value $vmIds 
#> 
 
#Example: How to retrieve information from a variable set during the pre-script 
<# 
$variable = Get-AutomationVariable -Name $runId 
#>      
```

## <a name="interacting-with-non-azure-machines"></a>Interagir com máquinas não Azure

Tarefas de pré e post executados no contexto do Azure e não tem acesso a máquinas não Azure. Para interagir com as máquinas não Azure, tem de ter os seguintes itens:

* Uma conta Run As
* Runbook Worker híbrido instalado na máquina
* Um runbook que pretende executar localmente
* Runbook principal

Para interagir com máquinas não Azure, um runbook principal é executado no contexto do Azure. Este runbook chama um runbook subordinado com o [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet. Tem de especificar o `-RunOn` parâmetro e forneça o nome da função de trabalho de Runbook híbrida para o script seja executado.

```powershell
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$resourceGroup = "AzureAutomationResourceGroup"
$aaName = "AzureAutomationAccountName"

$output = Start-AzureRmAutomationRunbook -Name "StartService" -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName -RunOn "hybridWorker"

$status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
while ($status.status -ne "Completed")
{ 
    Start-Sleep -Seconds 5
    $status = Get-AzureRmAutomationJob -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName
}

$summary = Get-AzureRmAutomationJobOutput -Id $output.jobid -ResourceGroupName $resourceGroup  -AutomationAccountName $aaName

if ($summary.Type -eq "Error")
{
    Write-Error -Message $summary.Summary
}
```

## <a name="known-issues"></a>Problemas conhecidos

* Não é possível passar objetos ou matrizes para parâmetros ao utilizar scripts de pré e post. O runbook irá falhar.

## <a name="next-steps"></a>Passos Seguintes

Avance para o tutorial para saber como gerir atualizações para as suas máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerir atualizações e correções para as VMs do Windows Azure](automation-tutorial-update-management.md)

