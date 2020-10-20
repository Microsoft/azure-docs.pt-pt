---
title: Gerir pré-scripts e pós-scripts na sua implementação de Gestão de Atualização em Azure
description: Este artigo diz como configurar e gerir pré-scripts e pós-scripts para atualizações de implementações.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: bb2a272829374cfeba5c334ff87268c4928885f5
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222495"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Gerir pré-scripts e pós-scripts

Os pré-scripts e pós-scripts são livros para executar na sua conta Azure Automation antes (pré-tarefa) e depois (pós-tarefa) uma implementação de atualização. Os pré-scripts e pós-scripts são executados no contexto Azure, não localmente. Os pré-scripts são executados no início da implementação da atualização. Os post-scripts são executados no final da implementação e após quaisquer reboots que estejam configurados.

## <a name="pre-script-and-post-script-requirements"></a>Requisitos de pré-script e pós-script

Para que um livro de bordo seja utilizado como pré-script ou pós-script, deve importá-lo na sua conta de Automação e [publicar o livro de bordo](../manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parâmetros pré-script e pós-script

Quando configurar pré-scripts e post-scripts, pode passar em parâmetros como agendar um livro de corridas. Os parâmetros são definidos no momento da criação da atualização. Os pré-scripts e pós-scripts suportam os seguintes tipos:

* [char]
* [byte]
* [int]
* [longo]
* [decimal]
* [single]
* [Duplo]
* [Hora da Data]
* [corda]

Os parâmetros do livro de execução pré-script e pós-script não suportam tipos de boolean, objeto ou matriz. Estes valores fazem com que os livros falhem. 

Se precisar de outro tipo de objeto, pode lanhá-lo para outro tipo com a sua própria lógica no livro de recortes.

Além dos parâmetros padrão do livro de correr, o `SoftwareUpdateConfigurationRunContext` parâmetro (cadeia tipo JSON) é fornecido. Se definir o parâmetro no seu pré-script ou no livro de execução pós-script, é automaticamente transmitido pela implementação da atualização. O parâmetro contém informações sobre a implementação da atualização, que é um subconjunto de informação devolvido pela [API de Configurações do SoftwareUpdate.](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) As secções abaixo definem as propriedades associadas.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext

|Propriedade  |Descrição  |
|---------|---------|
|Nome de Configuração de SoftwareUpdate     | O nome da configuração da atualização do software.        |
|SoftwareUpdateConfigurationRunId     | A identificação única para a corrida.        |
|SoftwareUpdateConfigurationSettings     | Uma coleção de propriedades relacionadas com a configuração da atualização do software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas operativos direcionados para a implementação da atualização.         |
|SoftwareUpdateConfigurationSettings.duração     | A duração máxima da implementação da atualização é executada `PT[n]H[n]M[n]S` de acordo com a ISO8601; também chamada de janela de manutenção.          |
|SoftwareUpdateConfigurationSettings.Windows     | Uma coleção de propriedades relacionadas com computadores Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excluiuKbNumbers     | Uma lista de KBs que estão excluídos da implementação da atualização.        |
|SoftwareUpdateConfigurationSettings.Windows.includeUpdateClassifications     | Atualizar classificações selecionadas para a implementação da atualização.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Reinicie as definições para a implementação da atualização.        |
|azureVirtualMachines     | Uma lista de recursosids para os VMs Azure na implementação da atualização.        |
|nãoAzureComputerNames|Uma lista dos FQDNs de computadores não-Azure na implementação da atualização.|

O exemplo a seguir é uma cadeia JSON transmitida para o parâmetro **SoftwareUpdateConfigurationRunContext:**

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

Um exemplo completo com todas as propriedades pode ser encontrado em: [Obtenha configuração de atualização de software pelo nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> O `SoftwareUpdateConfigurationRunContext` objeto pode conter entradas duplicadas para máquinas. Isto pode fazer com que os pré-scripts e os post-scripts corram várias vezes na mesma máquina. Para contornar este comportamento, utilize `Sort-Object -Unique` para selecionar apenas nomes VM únicos.

> [!NOTE]
> Atualmente, apenas os livros de execução PowerShell são suportados como scripts Pré/Post. Outros tipos de runbook como Python, Graphical, PowerShell Workflow, Graphical PowerShell Workflow não são atualmente suportados como scripts Pré/Post.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Use um pré-script ou pós-script numa implementação

Para utilizar um pré-script ou pós-script numa implementação de atualização, comece por criar uma implementação de atualização. Selecione **Pré-scripts + Post-Scripts**. Esta ação abre a página **Select Pre-scripts + Post-scripts.**

![Selecione scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que pretende utilizar. Neste exemplo, utilizamos o **runbook UpdateManagement-TurnOnVms.** Quando seleciona o livro de recortes, abre-se a página **Configure Script.** Selecione **Pré-Script**e, em seguida, selecione **OK**.

Repita este processo para o script **UpdateManagement-TurnOffVms.** Mas quando escolher o **tipo de Script,** selecione **Post-Script**.

A secção **de itens selecionados** mostra agora ambos os seus scripts selecionados. Um é um pré-guião e o outro é um pós-script:

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Termine de configurar a sua implementação de atualização.

Quando a sua atualização estiver concluída, pode ir a **atualizações** para visualizar os resultados. Como pode ver, o estado é fornecido para o pré-script e pós-script:

![Atualizar resultados](./media/pre-post-scripts/update-results.png)

Ao selecionar a execução da atualização, é-lhe mostrado detalhes adicionais de pré-scripts e pós-scripts. É fornecida uma ligação com a fonte do script no momento da execução.

![Resultados de execução de implementação](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Parar uma implantação

Se quiser parar uma implantação com base num pré-script, tem de [lançar](../automation-runbook-execution.md#throw) uma exceção. Se não o fizeres, a implantação e o pós-script continuarão a funcionar. O seguinte código corta-códigos mostra como lançar uma exceção.

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

## <a name="interact-with-machines"></a>Interagir com máquinas

Os pré-scripts e pós-scripts funcionam como livros de execução na sua conta de Automação e não diretamente nas máquinas da sua implementação. As pré-tarefas e pós-tarefas também são executadas no contexto Azure e não têm acesso a máquinas não-Azure. As seguintes secções mostram como pode interagir diretamente com as máquinas, sejam elas VMs Azure ou máquinas não-Azure.

### <a name="interact-with-azure-machines"></a>Interaja com máquinas Azure

As pré-tarefas e pós-tarefas funcionam como livros de execução e não funcionam de forma nativa nos seus VMs Azure na sua implementação. Para interagir com os seus VMs Azure, tem de ter os seguintes itens:

* Uma conta Run As
* Um livro de corridas que quer executar

Para interagir com as máquinas Azure, deve utilizar o cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0) para interagir com os seus VMs Azure. Para um exemplo de como fazê-lo, consulte o exemplo de [gestão de atualização do](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)exemplo do runbook – executar script com comando Run .

### <a name="interact-with-non-azure-machines"></a>Interaja com máquinas não-Azure

As pré-tarefas e pós-tarefas são executadas no contexto Azure e não têm acesso a máquinas não-Azure. Para interagir com as máquinas não-Azure, deve ter os seguintes itens:

* Uma conta Run As
* Trabalhador de runbook híbrido instalado na máquina
* Um livro de corridas que você quer executar localmente
* Um livro de corridas dos pais

Para interagir com máquinas não-Azure, um livro de bordo dos pais é executado no contexto Azure. Este livro de recortes chama um livro de crianças com o [cmdlet Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) Tem de especificar o `RunOn` parâmetro e fornecer o nome do Trabalhador do Runbook Híbrido para o script ser executado. Consulte o exemplo de runbook [Update Management – executar script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Abortar implantação de patch

Se o seu pré-guião retornar um erro, é melhor abortar a sua implementação. Para isso, tem de [lançar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro no seu script para qualquer lógica que constitua uma falha.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Amostras

As amostras para pré-scripts e pós-scripts podem ser encontradas na [Galeria script Center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e na [PowerShell Gallery,](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)ou pode importá-las através do portal Azure. Para isso, na sua conta de Automação, em **Processo de Automação,** selecione **Runbooks Gallery**. Utilize **a Atualização De gestão** para o filtro.

![Lista de galerias](./media/pre-post-scripts/runbook-gallery.png)

Ou pode procurá-los pelo nome do guião, como mostra a seguinte lista:

* Gestão de Atualização - Ligue VMs
* Gestão de Atualização - Desligue os VMs
* Gestão de Atualização - Executar Script Local
* Gestão de atualização - Modelo para scripts pré/post
* Gestão de atualização - Executar script com comando de execução

> [!IMPORTANT]
> Depois de importar os livros, deve publicá-los antes de poderem ser usados. Para isso, encontre o livro de contas na sua conta de Automação, **selecione Editar**e, em seguida, selecione **Publicar**.

As amostras são todas baseadas no modelo básico que é definido no exemplo seguinte. Este modelo pode ser usado para criar o seu próprio runbook para usar com pré-scripts e post-scripts. Está incluída a lógica necessária para autenticar com a Azure e manusear o `SoftwareUpdateConfigurationRunContext` parâmetro.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
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

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
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
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Para livros de execução powershell não gráficos, `Add-AzAccount` e `Add-AzureRMAccount` são pseudónimos para [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Pode utilizar estes cmdlets ou [atualizar os seus módulos](../automation-update-azure-modules.md) na sua conta Automation para as versões mais recentes. Poderá ter de atualizar os seus módulos mesmo que tenha acabado de criar uma nova conta Automation.

## <a name="next-steps"></a>Passos seguintes

* Para obter detalhes sobre a gestão da atualização, consulte [Gerir atualizações e patches para os seus VMs](manage-updates-for-vm.md).
