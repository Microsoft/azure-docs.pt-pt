---
title: Configurar scripts anteriores e posteriores em sua implantação do Gerenciamento de Atualizações no Azure
description: Este artigo descreve como configurar e gerenciar scripts anteriores e posteriores para implantações de atualização
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f13851dd43c80a63ec628e04b98271894c15afc0
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542857"
---
# <a name="manage-pre-and-post-scripts"></a>Gerenciar scripts anteriores e posteriores

Os scripts anteriores e posteriores permitem executar runbooks do PowerShell em sua conta de automação antes (antes da tarefa) e após (pós-tarefa) uma implantação de atualização. Os scripts Pre e post são executados no contexto do Azure e não localmente. Pré-scripts executados no início da implantação da atualização. Os scripts de postagem são executados no final da implantação e após qualquer reinicialização configurada.

## <a name="runbook-requirements"></a>Requisitos de runbook

Para que um runbook seja usado como um script anterior ou posterior, o runbook precisa ser importado para sua conta de automação e publicado. Para saber mais sobre esse processo, consulte [publicando um runbook](manage-runbooks.md#publish-a-runbook).

## <a name="using-a-prepost-script"></a>Usando um script de pré/pós

Para usar um script pre e or post em uma implantação de atualização, comece criando uma implantação de atualização. Selecione **pré-scripts + pós-** scripts. Essa ação abre a página **selecionar pré-scripts + pós-scripts** .  

![Selecionar scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que você deseja usar, neste exemplo, você usou o runbook **UpdateManagement-TurnOnVms** . Quando você seleciona o runbook a página **Configurar script** é aberta, escolha **pré-script**. Clique em **OK** quando terminar.

Repita esse processo para o script **UpdateManagement-TurnOffVms** . Mas ao escolher o **tipo de script**, escolha **pós-script**.

A seção **itens selecionados** agora mostra ambos os scripts selecionados e em é um pré-script e o outro é um post-script.

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Conclua a configuração da implantação de atualizações.

Quando a implantação de atualizações for concluída, você poderá acessar as implantações de **atualização** para exibir os resultados. Como você pode ver, o status de pré e pós-script são fornecidos.

![Resultados da atualização](./media/pre-post-scripts/update-results.png)

Ao clicar na execução da implantação de atualização, você receberá detalhes adicionais para os scripts anteriores e posteriores. Um link para a origem do script no momento da execução é fornecido.

![Resultados da execução da implantação](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Passando parâmetros

Ao configurar scripts Pre e post, você pode passar parâmetros assim como agendar um runbook. Os parâmetros são definidos no momento da criação da implantação de atualização. Os scripts anteriores e posteriores dão suporte aos seguintes tipos:

* º
* minuciosa
* [int]
* completa
* [decimal]
* exclusivo
* Clique
* Horário
* Strings

Se você precisar de outro tipo de objeto, poderá convertê-lo em outro tipo com sua própria lógica no runbook.

Além dos parâmetros de runbook padrão, um parâmetro adicional é fornecido. Esse parâmetro é **SoftwareUpdateConfigurationRunContext**. Esse parâmetro é uma cadeia de caracteres JSON e, se você definir o parâmetro em seu script anterior ou posterior, ele será automaticamente passado pela implantação de atualização. O parâmetro contém informações sobre a implantação de atualização, que é um subconjunto de informações retornado pela [API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) a tabela a seguir mostra as propriedades que são fornecidas na variável:


### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriedades de SoftwareUpdateConfigurationRunContext

|Propriedade  |Descrição  |
|---------|---------|
|SoftwareUpdateConfigurationName     | O nome da configuração de atualização de software        |
|SoftwareUpdateConfigurationRunId     | A ID exclusiva para a execução.        |
|SoftwareUpdateConfigurationSettings     | Uma coleção de propriedades relacionadas à configuração de atualização de software         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas operacionais destinados à implantação de atualização         |
|SoftwareUpdateConfigurationSettings.duration     | A duração máxima da implantação de atualização é executada `PT[n]H[n]M[n]S` como por ISO8601, também chamada de "janela de manutenção"          |
|SoftwareUpdateConfigurationSettings.Windows     | Uma coleção de propriedades relacionadas a computadores Windows         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Uma lista de KBs que são excluídos da implantação de atualização        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificações de atualização selecionadas para a implantação de atualização        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Configurações de reinicialização para a implantação de atualização        |
|azureVirtualMachines     | Uma lista de ResourceId para as VMs do Azure na implantação de atualização        |
|nonAzureComputerNames|Uma lista de FQDNs de computadores não Azure na implantação de atualização|

O exemplo a seguir é uma cadeia de caracteres JSON passada para o parâmetro **SoftwareUpdateConfigurationRunContext** :

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

Um exemplo completo com todas as propriedades pode ser encontrado em: [Configurações de atualização de software – obter por nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples)

> [!NOTE]
> O `SoftwareUpdateConfigurationRunContext` objeto pode conter entradas duplicadas para computadores. Isso pode fazer com que os scripts anteriores e posteriores sejam executados várias vezes no mesmo computador. Para solucionar esse comportamento, `Sort-Object -Unique` use para selecionar apenas nomes de VM exclusivos em seu script.


## <a name="stopping-a-deployment"></a>Interrompendo uma implantação

Se você quiser interromper uma implantação com base em um pré-script, você deve [lançar](automation-runbook-execution.md#throw) uma exceção. Se você não lançar uma exceção, a implantação e o script de postagem ainda serão executados. O [runbook de exemplo](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44?redir=0) na galeria mostra como você pode fazer isso. Veja a seguir um trecho de código desse runbook.

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


## <a name="samples"></a>Amostras

Exemplos de scripts Pre e post podem ser encontrados na [Galeria do script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell)ou importados por meio do portal do Azure. Para importá-los por meio do portal, na sua conta de automação, em **automação de processo**, selecione **Galeria de Runbooks**. Use **Gerenciamento de atualizações** para o filtro.

![Lista da Galeria](./media/pre-post-scripts/runbook-gallery.png)

Ou você pode procurá-los por seu nome de script, como mostrado na lista a seguir:

* Gerenciamento de Atualizações-ativar as VMs
* Gerenciamento de Atualizações-desligar as VMs
* Gerenciamento de Atualizações-executar script localmente
* Gerenciamento de Atualizações-modelo para scripts Pre/post
* Gerenciamento de Atualizações-executar script com o comando executar

> [!IMPORTANT]
> Depois de importar os runbooks, você deve **publicá** -los antes que possam ser usados. Para fazer isso, localize o runbook em sua conta de automação, selecione **Editar**e clique em **publicar**.

Os exemplos são todos baseados no modelo básico que é definido no exemplo a seguir. Este modelo pode ser usado para criar seu próprio runbook para usar com scripts Pre e post. A lógica necessária para autenticação com o Azure e o `SoftwareUpdateConfigurationRunContext` tratamento do parâmetro é incluída.

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

## <a name="interacting-with-machines"></a>Interagindo com computadores

As tarefas anteriores e posteriores são executadas como um runbook em sua conta de automação e não diretamente nas máquinas em sua implantação. As tarefas anteriores e posteriores também são executadas no contexto do Azure e não têm acesso a computadores não Azure. As seções a seguir mostram como você pode interagir com os computadores diretamente se eles são uma VM do Azure ou um computador não Azure:

### <a name="interacting-with-azure-machines"></a>Interagindo com máquinas do Azure

As tarefas anteriores e posteriores são executadas como runbooks e não são executadas nativamente em suas VMs do Azure em sua implantação. Para interagir com suas VMs do Azure, você deve ter os seguintes itens:

* Uma conta Executar como
* Um runbook que você deseja executar

Para interagir com máquinas do Azure, você deve usar o cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) para interagir com suas VMs do Azure. Para obter um exemplo de como fazer isso, consulte o exemplo de runbook [Gerenciamento de atualizações-executar script com o comando executar](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc).

### <a name="interacting-with-non-azure-machines"></a>Interagindo com computadores não Azure

As tarefas anteriores e posteriores são executadas no contexto do Azure e não têm acesso a computadores não Azure. Para interagir com os computadores não Azure, você deve ter os seguintes itens:

* Uma conta Executar como
* Hybrid Runbook Worker instalado no computador
* Um runbook que você deseja executar localmente
* Runbook pai

Para interagir com computadores não Azure, um runbook pai é executado no contexto do Azure. Esse runbook chama um runbook filho com o cmdlet [Start-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Você deve especificar o `-RunOn` parâmetro e fornecer o nome do Hybrid runbook Worker no qual o script será executado. Para obter um exemplo de como fazer isso, consulte o exemplo de runbook [Gerenciamento de atualizações-executar script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Anular implantação de patch

Se o seu pré-script retornar um erro, talvez você queira anular a implantação. Para fazer isso, você deve [lançar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro em seu script para qualquer lógica que possa constituir uma falha.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.  
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Problemas conhecidos

* Você não pode passar um booliano, objetos ou matrizes para parâmetros ao usar scripts Pre e post. O runbook falhará. Para obter uma lista completa dos tipos com suporte, consulte [parâmetros](#passing-parameters).

## <a name="next-steps"></a>Passos seguintes

Continue no tutorial para saber como gerenciar atualizações para suas máquinas virtuais do Windows.

> [!div class="nextstepaction"]
> [Gerenciar atualizações e patches para suas VMs do Windows do Azure](automation-tutorial-update-management.md)

