---
title: Configure pré e pós-scripts na sua implementação de Gestão de Atualizações em Azure
description: Este artigo descreve como configurar e gerir pré-scripts e pós-scripts para implementações de atualizações.
services: automation
ms.subservice: update-management
ms.date: 05/17/2019
ms.topic: conceptual
ms.openlocfilehash: bbf7f2945ad6a94c51cfd0c7db1e8c85d739c6ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631625"
---
# <a name="manage-pre-and-post-scripts"></a>Gerir pré e pós-scripts

Pré-scripts e pós-scripts permitem executar os livros de execução powerShell na sua conta De automação Azure antes (pré-tarefa) e depois (pós-tarefa) uma implementação de atualização. Os pré e os pós-scripts funcionam no contexto Azure, não localmente. Os pré-scripts são executados no início da implementação da atualização. Os pós-scripts são executados no final da implementação e após quaisquer reboots que estejam configurados.

## <a name="runbook-requirements"></a>Requisitos do livro de execução

Para que um livro de execução seja usado como pré ou pós-script, o livro de execução deve ser importado para a sua conta de Automação e publicado. Para saber mais sobre este processo, consulte [Publicar um livro de execução](manage-runbooks.md#publishing-a-runbook).

## <a name="using-a-pre-script-or-post-script"></a>Usando um pré-script ou pós-script

Para utilizar um pré-script ou pós-script numa implementação de atualização, comece por criar uma implementação de atualização. Selecione **Pré-scripts + Post-Scripts**. Esta ação abre a página **Select Pre-scripts + Post-scripts.**

![Selecione scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que pretende utilizar. Neste exemplo, utilizamos o livro de execução **UpdateManagement-TurnOnVms.** Quando selecionar o livro de execução, abre-se a página **'Script Configure'.** Selecione **Pré-Script**e, em seguida, selecione **OK**.

Repita este processo para o script **UpdateManagement-TurnOffVms.** Mas quando escolher o **tipo script,** selecione **Post-Script**.

A secção de **itens Selecionados** mostra agora ambos os seus scripts selecionados. Um é um pré-script e o outro é um pós-script:

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Termine de configurar a sua implementação da atualização.

Quando a sua implementação da atualização estiver concluída, pode ir às implementações de **Atualização** para visualizar os resultados. Como pode ver, o estado está previsto para o pré-script e pós-script:

![Atualizar resultados](./media/pre-post-scripts/update-results.png)

Ao selecionar a execução da atualização, são mostrados detalhes adicionais para os pré e pós-scripts. É fornecida uma ligação com a fonte do script no momento da execução.

![Resultados da execução de implementação](./media/pre-post-scripts/deployment-run.png)

## <a name="passing-parameters"></a>Parâmetros de passagem

Quando configura pré e pós-scripts, pode passar em parâmetros, tal como agendar um livro de execução. Os parâmetros são definidos no momento da criação de implementação da atualização. Pré e pós-scripts suportam os seguintes tipos:

* [char]
* [byte]
* [int]
* [longo]
* [decimal]
* [único]
* [duplo]
* [Data]
* [corda]

Se precisar de outro tipo de objeto, pode lançá-lo para outro tipo com a sua própria lógica no livro de execução.

Além dos parâmetros padrão do livro de execução, é fornecido outro parâmetro: **SoftwareUpdateConfigurationRunContext**

Este parâmetro é uma cadeia JSON, e se definir o parâmetro no seu pré ou pós-script, é automaticamente passado pela implementação da atualização. O parâmetro contém informações sobre a implementação da atualização, que é um subconjunto de informações devolvidas pela API de Configurações de [Atualizações](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration)de Software . 

A tabela seguinte mostra-lhe as propriedades que são fornecidas na variável.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriedades de SoftwareUpdateConfigurationRunContext

|Propriedade  |Descrição  |
|---------|---------|
|Nome de configuração de atualização de software     | O nome da configuração da atualização do software.        |
|Configuração de SoftwareRunid     | A identificação única para a corrida.        |
|Configurações de Configurações de Atualizações de Software     | Uma coleção de propriedades relacionadas com a configuração da atualização de software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas operativos direcionados para a implementação da atualização.         |
|SoftwareUpdateConfigurationSettings.duração     | A duração máxima da `PT[n]H[n]M[n]S` execução da atualização de acordo com o ISO8601; também chamou a *janela de manutenção.*          |
|Configurações de Configurações de Atualizações de Software.Windows     | Uma coleção de propriedades relacionadas com computadores Windows.         |
|Configurações de Configurações de SoftwareUpdate.Windows.exclusouKbNumbers     | Uma lista de KBs que estão excluídos da implementação da atualização.        |
|Configurações de Configurações de SoftwareUpdate.Windows.incluiuClassificações de Atualizações     | Atualize as classificações selecionadas para a implementação da atualização.        |
|Configurações de Configurações de SoftwareUpdate.Windows.rebootDefinição     | Reiniciar as definições para a implementação da atualização.        |
|máquinas azureVirtuais     | Uma lista de recursos Ids para os VMs Azure na implementação da atualização.        |
|nonAzureComputerNames|Uma lista dos computadores não-Azure FQDNs na implementação da atualização.|

O exemplo seguinte é uma cadeia JSON passada para o parâmetro **SoftwareUpdateConfigurationRunContext:**

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

Um exemplo completo com todas as propriedades pode ser encontrado em: [Obtenha configuração de atualização de software por nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> O `SoftwareUpdateConfigurationRunContext` objeto pode conter entradas duplicadas para máquinas. Isto pode fazer com que os pré e os pós-scripts corram várias vezes na mesma máquina. Para contornar este comportamento, utilize `Sort-Object -Unique` para selecionar apenas nomes VM únicos no seu script.


## <a name="stopping-a-deployment"></a>Parar uma implantação

Se quiser parar uma implantação com base num pré-script, tem de [lançar](automation-runbook-execution.md#throw) uma exceção. Se não o fizeres, a implantação e o pós-guião continuarão a funcionar. O seguinte código de corte mostra como lançar uma exceção.

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

As amostras para pré e pós-scripts podem ser encontradas na Galeria script [center](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B0%5D.Text=Windows%20Azure&f%5B1%5D.Type=SubCategory&f%5B1%5D.Value=WindowsAzure_automation&f%5B1%5D.Text=Automation&f%5B2%5D.Type=SearchText&f%5B2%5D.Value=update%20management&f%5B3%5D.Type=Tag&f%5B3%5D.Value=Patching&f%5B3%5D.Text=Patching&f%5B4%5D.Type=ProgrammingLanguage&f%5B4%5D.Value=PowerShell&f%5B4%5D.Text=PowerShell) e na [PowerShell Gallery,](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22)ou pode importá-las através do portal Azure. Para isso, na sua conta de **Automação,** no âmbito da Automation, selecione **Runbooks Gallery**. Utilize a Gestão de **Atualizações** para o filtro.

![Lista de galerias](./media/pre-post-scripts/runbook-gallery.png)

Ou pode procurá-los pelo seu nome de script, como mostra a seguinte lista:

* Gestão de Atualizações - Ligar VMs
* Gestão de Atualizações - Desligue os VMs
* Gestão de Atualizações - Executar script localmente
* Gestão de Atualizações - Modelo para Scripts Pré/Post
* Gestão de Atualizações - Executar script com comando de execução

> [!IMPORTANT]
> Depois de importar os livros de execução, deve publicá-los antes de poderem ser utilizados. Para isso, encontre o livro de recortes na sua conta Automation, selecione **Editar**, e depois selecione **Publicar**.

As amostras são todas baseadas no modelo básico que é definido no exemplo seguinte. Este modelo pode ser usado para criar o seu próprio livro de corridas para usar com pré e pós-scripts. Está incluída a lógica necessária para autenticar com o Azure e manusear o `SoftwareUpdateConfigurationRunContext` parâmetro.

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

## <a name="interacting-with-machines"></a>Interagindo com máquinas

As pré e pós-tarefas funcionam como um livro de execução na sua conta Automation e não diretamente nas máquinas na sua implementação. As pré e pós-tarefas também funcionam no contexto Azure e não têm acesso a máquinas não-Azure. As seguintes secções mostram como pode interagir diretamente com as máquinas, sejam elas VMs Azure ou máquinas não Azure.

### <a name="interacting-with-azure-machines"></a>Interagindo com máquinas Azure

As pré e pós-tarefas são executadas como livros de execução e não funcionam de forma nativa nos seus VMs Azure na sua implantação. Para interagir com os seus VMs Azure, deve ter os seguintes itens:

* Uma conta Run As
* Um livro de corridas que quer correr

Para interagir com as máquinas Azure, deve utilizar o cmdlet [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) para interagir com os seus VMs Azure. Para um exemplo de como fazê-lo, consulte o exemplo de [Atualização](https://gallery.technet.microsoft.com/Update-Management-Run-40f470dc)do livro de execução – executar script com comando Executar .

### <a name="interacting-with-non-azure-machines"></a>Interagir com máquinas não-Azure

As pré e pós-tarefas funcionam no contexto Azure e não têm acesso a máquinas não-Azure. Para interagir com as máquinas não-Azure, deve ter os seguintes itens:

* Uma conta Run As
* Trabalhador de livro híbrido instalado na máquina
* Um livro de corridas que você quer executar localmente
* Um livro de pais

Para interagir com máquinas não-Azure, um livro-mãe é executado no contexto Azure. Este livro de corridas chama um livro de corridas para crianças com o [cmdlet Start-AzureRmAutomationRunbook.](/powershell/module/azurerm.automation/start-azurermautomationrunbook) Deve especificar `-RunOn` o parâmetro e fornecer o nome do Trabalhador do Livro Híbrido para que o script seja executado. Para mais informações, consulte o exemplo do livro de execução [Management – executar script localmente](https://gallery.technet.microsoft.com/Update-Management-Run-6949cc44).

## <a name="abort-patch-deployment"></a>Abortar a implantação de patch

Se o seu pré-script devolver um erro, talvez queira abortar a sua implementação. Para isso, tens de [lançar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro no teu guião por qualquer lógica que constitua um fracasso.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="known-issues"></a>Problemas conhecidos

* Não se pode passar uma booleana, objetos ou matrizes aos parâmetros quando se está a usar pré e pós-scripts. Se o fizeres, o livro de corridas falha. Para obter uma lista completa de tipos suportados, consulte [os parâmetros de passagem](#passing-parameters).

## <a name="next-steps"></a>Passos seguintes

Vá ao seguinte tutorial para aprender a gerir atualizações para as suas máquinas virtuais windows:

> [!div class="nextstepaction"]
> [Gerir atualizações e correções para as VMs Windows do Azure](automation-tutorial-update-management.md)

