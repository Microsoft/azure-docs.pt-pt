---
title: Configurar scripts de pré e post em sua implementação de gestão de atualizações no Azure
description: Este artigo descreve como configurar e gerir o pré e implementações de atualizações de scripts para postagem
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7317b634ee4c8886ce5c99bb2b3395d7d1f646d5
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65913869"
---
# <a name="manage-pre-and-post-scripts"></a>Gerir scripts de pré e post

Scripts de pré e post permitem executar runbooks do PowerShell na sua conta de automatização antes (pré-tarefa) e depois da implantação (pós-tarefa) uma atualização. Scripts de pré e post são executados no contexto do Azure e não localmente. Pré-scripts de são executados no início da implantação da atualização. Scripts de publicação são executados no final da implementação e depois de quaisquer reinícios que estão configurados.

## <a name="runbook-requirements"></a>Requisitos do Runbook

Para um runbook a ser utilizado como um script de pré ou post, o runbook tem de ser importados para a sua conta de automatização e publicado. Para saber mais sobre este processo, veja [publicar um runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Usando um script de pré/pós

Para utilizar o pré e ou postagem de script numa implementação de atualização, comece por criar uma implementação de atualização. Selecione **pré- scripts de + Scripts pós**. Esta ação abre o **pré-scripts de selecione + pós-scripts** página.  

![Selecione os scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que pretende utilizar, neste exemplo, utilizou o **UpdateManagement TurnOnVms** runbook. Ao selecionar o runbook a **configurar o Script** é aberta a página, escolha **Script prévio**. Clique em **OK** quando tiver terminado.

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

## <a name="stopping-a-deployment"></a>A parar uma implementação

Se pretender parar uma implementação com base num script de pré tem [lançar](automation-runbook-execution.md#throw) uma exceção. Se não lance uma exceção, a implementação e o script de mensagem ainda serão executado. O [runbook de exemplo](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) na Galeria mostra como pode fazer isso. Segue-se um trecho de código a partir desse runbook.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.  
        throw $summary.Summary
    }
}
```

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

> [!NOTE]
> O `SoftwareUpdateConfigurationRunContext` objeto pode conter entradas duplicadas para máquinas. Isso pode fazer com que os scripts de pré e Post ser executado várias vezes na mesma máquina. Para resolver esse comportamento, utilize `Sort-Object -Unique` para selecionar apenas os nomes VM exclusivos em seu script.

## <a name="samples"></a>Exemplos

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
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
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

## <a name="interacting-with-machines"></a>Interagir com as máquinas

Executam tarefas de pré e post como um runbook na conta de automatização e não diretamente nas máquinas na sua implementação. Tarefas de pré e post também executados no contexto do Azure e não tem acesso a máquinas não Azure. As secções seguintes mostram como pode interagir com as máquinas diretamente se eles são uma VM do Azure ou uma máquina não pertencentes ao Azure:

### <a name="interacting-with-azure-machines"></a>Interagir com as máquinas do Azure

Tarefas de pré e post são eram executados como runbooks e não são executados nativamente nas suas VMs do Azure na sua implementação. Para interagir com as VMs do Azure, tem de ter os seguintes itens:

* Uma conta Run As
* Um runbook que pretende executar

Para interagir com as máquinas do Azure, deve utilizar o [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet para interagir com as VMs do Azure. Para obter um exemplo de como fazê-lo, veja o exemplo de runbook [gestão de atualizações - executar o Script com o comando executar](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interagir com máquinas não Azure

Tarefas de pré e post executados no contexto do Azure e não tem acesso a máquinas não Azure. Para interagir com as máquinas não Azure, tem de ter os seguintes itens:

* Uma conta Run As
* Runbook Worker híbrido instalado na máquina
* Um runbook que pretende executar localmente
* Runbook principal

Para interagir com máquinas não Azure, um runbook principal é executado no contexto do Azure. Este runbook chama um runbook subordinado com o [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet. Tem de especificar o `-RunOn` parâmetro e forneça o nome da função de trabalho de Runbook híbrida para o script seja executado. Para obter um exemplo de como fazê-lo, veja o exemplo de runbook [gestão de atualizações - execute o Script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Abortar a implantação de patches

Se o script de pré devolver um erro, poderá abortar a sua implementação. Para fazer isso, deve [lançar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro no seu script de qualquer lógica que teria constituem uma falha.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```
## <a name="known-issues"></a>Problemas conhecidos

* Não é possível passar objetos ou matrizes para parâmetros ao utilizar scripts de pré e post. O runbook irá falhar.

## <a name="next-steps"></a>Passos Seguintes

Avance para o tutorial para saber como gerir atualizações para as suas máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerir atualizações e correções para as VMs do Windows Azure](automation-tutorial-update-management.md)

