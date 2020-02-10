---
title: Integração e entrega contínuas na Azure Data Factory
description: Aprenda a usar a integração contínua e a entrega para mover os oleodutos data Factory de um ambiente (desenvolvimento, teste, produção) para outro.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: f1b15688004d23e8a568695b565b5b34d7b466d6
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/09/2020
ms.locfileid: "77110179"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas na Azure Data Factory

## <a name="overview"></a>Descrição geral

A integração contínua é a prática de testar cada alteração feita na base de código automaticamente e o mais cedo possível. A entrega contínua segue os testes que ocorrem durante a integração contínua e envia por push as alterações para um sistema de preparo ou de produção.

Em Azure Data Factory, a integração contínua e a entrega (CI/CD) significam mover Data Factory pipelines de um ambiente (desenvolvimento, teste, produção) para outro. Você pode usar a integração do Data Factory UX com modelos de Azure Resource Manager para fazer CI/CD.

Na Fábrica de Dados UX, pode gerar um modelo de Gestor de Recursos a partir do menu de entrega do **modelo ARM.** Quando seleciona o modelo de BRAÇO de **Exportação,** o portal gera o modelo de Gestor de Recursos para a fábrica de dados e um ficheiro de configuração que inclui todas as suas cordas de ligação e outros parâmetros. Em seguida, você cria um arquivo de configuração para cada ambiente (desenvolvimento, teste, produção). O ficheiro principal do modelo do Gestor de Recursos permanece o mesmo para todos os ambientes.

Para uma introdução de nove minutos a esse recurso e uma demonstração, Assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida de CI/CD

Abaixo está um exemplo de visão geral do ciclo de vida de CI/CD em uma data factory do Azure configurada com Azure Repos git. Para obter mais informações sobre como configurar um repositório Git, consulte o controlo de origem na Fábrica de [Dados Azure](source-control.md).

1.  Um data factory de desenvolvimento é criado e configurado com Azure Repos git. Todos os desenvolvedores devem ter permissão para criar Data Factory recursos como pipelines e conjuntos de valores.

1.  À medida que os desenvolvedores fazem alterações em suas ramificações de recursos, eles depuram suas execuções de pipeline com suas alterações mais recentes. Para obter mais informações sobre como depurar um gasoduto, consulte o [desenvolvimento iterativo e a depuração com](iterative-development-debugging.md)a Azure Data Factory .

1.  Depois que os desenvolvedores estão satisfeitos com suas alterações, eles criam uma solicitação de pull de sua ramificação de recursos para o Branch mestre ou de colaboração para que suas alterações sejam revisadas por colegas.

1.  Depois que uma solicitação de pull é aprovada e as alterações são mescladas no Branch mestre, as alterações podem ser publicadas na fábrica de desenvolvimento.

1.  Quando a equipe estiver pronta para implantar as alterações na fábrica de testes e, em seguida, na fábrica de produção, a equipe exportará o modelo do Resource Manager do Branch mestre.

1.  O modelo exportado do Resource Manager é implantado com arquivos de parâmetro diferentes na fábrica de teste e na fábrica de produção.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Crie um modelo de Gestor de Recursos para cada ambiente

1. Na lista de **modelos ARM,** selecione modelo de BRAÇO de **Exportação** para exportar o modelo de Gestor de Recursos para a sua fábrica de dados no ambiente de desenvolvimento.

   ![Exportar um modelo do Resource Manager](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Nas suas fábricas de dados de teste e produção, selecione **Import ARM Template**. Esta ação leva-o ao portal Azure, onde pode importar o modelo exportado. Selecione **Construir o seu próprio modelo no editor** para abrir o editor de modelo de Gestor de Recursos.

   ![Crie seu próprio modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecione **ficheiro load**, e, em seguida, selecione o modelo de Gestor de Recursos gerado.

   ![Modelo de edição](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na seção Configurações, insira os valores de configuração, como credenciais de serviço vinculado. Quando terminar, selecione **Comprar** para implementar o modelo de Gestor de Recursos.

   ![Seção de configurações](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Cadeias de ligação

Para obter informações sobre como configurar cadeias de conexão, consulte o artigo do conector. Por exemplo, para a Base de Dados Azure SQL, consulte os dados de cópia de ou para a Base de [Dados Azure SQL utilizando](connector-azure-sql-database.md)a Azure Data Factory . Para verificar uma cadeia de ligação, pode abrir a vista de código para o recurso na Fábrica de Dados UX. Na vista do código, a palavra-passe ou a parte chave da conta da cadeia de ligação é removida. Para abrir o modo de exibição de código, selecione o ícone realçado aqui:

![Abra o modo de exibição de código para ver a cadeia de conexão](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizar a integração contínua usando versões Azure Pipelines

A seguir, um guia para configurar uma versão Azure Pipelines, que automatiza a implantação de um data factory em vários ambientes.

![Diagrama de integração contínua com oleodutos Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma subscrição Azure ligada ao Visual Studio Team Foundation Server ou ao Azure Repos que utiliza o ponto final do [serviço Azure Resource Manager.](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)

-   Um data factory configurado com Azure Repos integração com o git.

-   Um [cofre azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos de cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Criar um lançamento de Pipelines Azure

1.  Na [Azure DevOps,](https://dev.azure.com/)abra o projeto que está configurado com a sua fábrica de dados.

1.  No lado esquerdo da página, selecione **Pipelines**, e, em seguida, selecione **Lançamentos**.

    ![Selecionar pipelines, versões](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **Novo oleoduto**, ou, se tiver oleodutos existentes, selecione **novo** e, em seguida, novo oleoduto de **lançamento**.

1.  Selecione o modelo de **trabalho vazio.**

    ![Selecionar trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na caixa de **nomes do palco,** insira o nome do seu ambiente.

1.  Selecione **Adicionar artefacto,** e, em seguida, selecione o repositório configurado com a sua fábrica de dados. Selecione **adf_publish** para o **ramo Predefinido**. Para a **versão Predefinido,** selecione **O Mais Recente do ramo predefinido**.

    ![Adicione um artefacto](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de implantação do Gestor de Recursos Azure:

    a.  Na vista do palco, selecione **Ver tarefas**de palco .

    ![Exibição de estágio](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Criar uma nova tarefa. Procure a implantação do Grupo de **Recursos Azure**e, em seguida, selecione **Adicionar**.

    c.  Na tarefa de implantação, selecione a assinatura, o grupo de recursos e o local para o data factory de destino. Forneça as credenciais, se necessário.

    d.  Na lista **de Ação,** selecione **Criar ou atualizar o grupo de recursos**.

    e.  Selecione o botão de elipse **(...** ) ao lado da caixa **do modelo.** Procure o modelo do Gestor de Recursos Azure que criou utilizando o **modelo de ARM de Importação** no modelo Criar um Gestor de Recursos para cada secção de [ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) deste artigo. Procure este ficheiro na pasta <FactoryName> da filial adf_publish.

    f.  Selecione **...** junto à caixa de **parâmetros do Modelo** para escolher o ficheiro de parâmetros. O arquivo que você escolher dependerá se você criou uma cópia ou está usando o arquivo padrão, ARMTemplateParametersForFactory. JSON.

    g.  Selecione **...** junto à caixa de parâmetros do **modelo de sobreposição** e introduza a informação para a fábrica de dados alvo. Para as credenciais provenientes de Azure Key Vault, insira o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for cred1, insira **"$(cred1)"** para este valor.

    h. **Selecione Incremental** para o **modo de implantação**.

    > [!WARNING]
    > Se selecionar **Complete** para o **modo de Implantação,** os recursos existentes podem ser eliminados, incluindo todos os recursos do grupo de recursos-alvo que não estão definidos no modelo de Gestor de Recursos.

    ![Implantação do Data Factory prod](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salve o oleoduto de libertação.

1. Para desencadear uma libertação, selecione **Criar**.

   ![Selecionar criar versão](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Obtenha segredos do Cofre de Chaves Azure

Se você tiver segredos para passar um modelo de Azure Resource Manager, recomendamos que você use Azure Key Vault com a versão Azure Pipelines.

Há duas maneiras de lidar com segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para mais informações, consulte [Use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](../azure-resource-manager/templates/key-vault-parameter.md).

    Crie uma cópia do arquivo de parâmetros que é carregado para a ramificação de publicação. Defina os valores dos parâmetros que você deseja obter de Key Vault usando este formato:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    Quando se usa este método, o segredo é retirado automaticamente do cofre da chave.

    O ficheiro de parâmetros também tem de estar na filial.

-  Adicione uma tarefa de cofre de [chave Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de implantação do Gestor de Recursos Azure descrita na secção anterior:

    1.  No separador **Tarefas,** crie uma nova tarefa. Procure o **Cofre de Chaves Azure** e adicione-o.

    1.  Na tarefa Key Vault, selecione a assinatura na qual você criou o cofre de chaves. Forneça as credenciais, se necessário, e selecione o cofre de chaves.

    ![Adicionar uma tarefa de Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente da Azure Pipelines

   A tarefa Azure Key Vault poderá falhar com um erro de acesso negado se as permissões corretas não estiverem definidas. Baixe os logs da versão e localize o arquivo. ps1 que contém o comando para conceder permissões ao agente de Azure Pipelines. Você pode executar o comando diretamente. Ou você pode copiar a ID da entidade de segurança do arquivo e adicionar a política de acesso manualmente no portal do Azure. `Get` e `List` são as permissões mínimas necessárias.

### <a name="update-active-triggers"></a>Atualizar gatilhos ativos

A implementação pode falhar se tentar atualizar os gatilhos ativos. Para atualizar os gatilhos ativos, você precisa interrompê-los manualmente e reiniciá-los após a implantação. Você pode fazer isso usando uma tarefa de Azure PowerShell:

1.  No separador **Tasks** do lançamento, adicione uma tarefa **Azure PowerShell.**

1.  Selecione **O Gestor de Recursos Azure** como tipo de ligação e, em seguida, selecione a sua subscrição.

1.  Selecione **O Script Inline** como o tipo de script e, em seguida, forneça o seu código. O código a seguir interrompe os gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![Tarefa Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Pode completar passos semelhantes (com a função `Start-AzDataFactoryV2Trigger`) para reiniciar os gatilhos após a colocação.

> [!IMPORTANT]
> Em cenários de CI/CD, o tipo de IR (Integration Runtime) em ambientes diferentes deve ser o mesmo. Por exemplo, se você tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deverá ser do tipo auto-hospedado em outros ambientes, como teste e produção. Da mesma forma, se você estiver compartilhando tempos de execução de integração em vários estágios, precisará configurar os tempos de execução de integração como vinculados internamente em todos os ambientes, como desenvolvimento, teste e produção.

#### <a name="sample-pre--and-post-deployment-script"></a>Script pré e pós-implantação de exemplo

O script de exemplo a seguir mostra como parar os gatilhos antes da implantação e reiniciá-los depois. O script também inclui código para eliminar recursos que foram removidos. Para instalar a versão mais recente do Azure PowerShell, consulte Instalar o [PowerShell Azure no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Use parâmetros personalizados com o modelo de Gestor de Recursos

Se você estiver no modo GIT, poderá substituir as propriedades padrão em seu modelo do Resource Manager para definir propriedades que são parametrizadas no modelo e propriedades que são embutidas em código. Talvez queira anular o modelo de parametrização padrão nestes cenários:

* Você usa CI/CD automatizado e você quer alterar algumas propriedades durante a implementação do Gestor de Recursos, mas as propriedades não são parametrizadas por padrão.
* A sua fábrica é tão grande que o modelo padrão do Gestor de Recursos é inválido porque tem mais do que os parâmetros máximos permitidos (256).

Sob essas condições, para substituir o modelo de parametrização padrão, crie um arquivo chamado ARM-template-Parameters-Definition. JSON na pasta especificada como a pasta raiz para a integração do data factory git. Você deve usar esse nome de arquivo exato. Data Factory lê esse arquivo de qualquer ramificação que você esteja no portal de Azure Data Factory, não apenas da ramificação de colaboração. Pode criar ou editar o ficheiro a partir de uma sucursal privada, onde pode testar as suas alterações selecionando o Modelo braço de **exportação** na UI. Em seguida, você pode mesclar o arquivo no Branch de colaboração. Se não for encontrado nenhum ficheiro, o modelo predefinido é utilizado.

### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um arquivo de parâmetros personalizados

Veja a seguir algumas diretrizes a serem seguidas ao criar o arquivo de parâmetros personalizados. O ficheiro consiste numa secção para cada tipo de entidade: gatilho, pipeline, serviço ligado, conjunto de dados, tempo de execução de integração, e assim por diante.
* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome de propriedade para `*` indica que pretende parametrizar todas as propriedades por baixo (apenas até ao primeiro nível, não recursivamente). Você também pode fornecer exceções a essa configuração.
* Definir o valor de uma propriedade como uma cadeia de caracteres indica que você deseja parametrizar a propriedade. Utilize o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um destes personagens:
      * `=` significa manter o valor atual como valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos do Cofre chave azure para cordas ou chaves de ligação.
   * `<name>` é o nome do parâmetro. Se estiver em branco, leva o nome da propriedade. Se o valor começar com um personagem `-`, o nome é encurtado. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria encurtado para `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, o tipo predefinido é `string`. Valores suportados: `string`, `bool`, `number`, `object`e `securestring`.
* A especificação de uma matriz no arquivo de definição indica que a propriedade correspondente no modelo é uma matriz. Data Factory itera por todos os objetos na matriz usando a definição especificada no objeto de tempo de execução de integração da matriz. O segundo objeto, uma corda, torna-se o nome da propriedade, que é usada como nome para o parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recurso. Qualquer definição aplica-se a todos os recursos desse tipo.
* Por padrão, todas as cadeias de caracteres seguras, como segredos de Key Vault e cadeias de caracteres seguras, como cadeias de conexão, chaves e tokens, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização da amostra

Veja um exemplo de como seria um modelo de parametrização:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Aqui está uma explicação de como o modelo anterior é construído, dividido por tipo de recurso.

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho `activities/typeProperties/waitTimeInSeconds` é parametrizada. Qualquer atividade num oleoduto que tenha uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, a atividade `Wait`) é parametrizada como um número, com um nome predefinido. Mas não terá um valor predefinido no modelo do Gestor de Recursos. Será uma entrada obrigatória durante a implantação do Gestor de Recursos.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, numa atividade `Web`) é parametrizada com tipo `object` (JObject). Ele tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas as propriedades sob o caminho `typeProperties` são parametrizadas com os respetivos valores predefinidos. Por exemplo, existem duas propriedades em propriedades do tipo `IntegrationRuntimes`: `computeProperties` e `ssisProperties`. Ambos os tipos de propriedades são criados com os respetivos valores e tipos padrão (Objeto).

#### <a name="triggers"></a>Acionadores

* Sob `typeProperties`, duas propriedades são parametrizadas. O primeiro é `maxConcurrency`, que é especificado para ter um valor predefinido e é de tipo`string`. Tem o nome de parâmetro padrão `<entityName>_properties_typeProperties_maxConcurrency`.
* A propriedade `recurrence` também é parametrizada. Por baixo, todas as propriedades a esse nível são especificadas para serem parametrizadas como cordas, com valores predefinidos e nomes de parâmetros. Uma exceção é a propriedade `interval`, que é parametrizada como tipo `number`. O nome do parâmetro é sufixo com `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Da mesma forma, a propriedade `freq` é uma corda e é parametrizada como uma corda. No entanto, a propriedade `freq` é parametrizada sem um valor predefinido. O nome é encurtado e sufixo. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços ligados são únicos. Como os serviços e conjuntos de dados ligados têm uma vasta gama de tipos, pode fornecer personalização específica do tipo. Neste exemplo, para todos os serviços ligados do tipo `AzureDataLakeStore`, será aplicado um modelo específico. Para todos os outros (via `*`), será aplicado um modelo diferente.
* A propriedade `connectionString` será parametrizada como um valor `securestring`. Ele não terá um valor padrão. Terá um nome de parâmetro encurtado que é sufixo com `connectionString`.
* A propriedade `secretAccessKey` passa a ser uma `AzureKeyVaultSecret` (por exemplo, num serviço ligado à Amazon S3). É automaticamente parametido como um segredo azure key vault e recolhido do cofre de chaves configurado. Também pode parametrizar o cofre chave em si.

#### <a name="datasets"></a>Conjuntos de dados

* Embora a personalização específica do tipo esteja disponível para conjuntos de dados, pode fornecer configuração sem ter explicitamente uma configuração de nível \*. No exemplo anterior, todas as propriedades do conjunto de dados sob `typeProperties` são parametrizadas.

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

A seguir está o modelo de parametrização padrão atual. Se você precisar adicionar apenas alguns parâmetros, editar esse modelo diretamente pode ser uma boa ideia, pois você não perderá a estrutura de parametrização existente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

O exemplo a seguir mostra como adicionar um único valor ao modelo de parametrização padrão. Queremos apenas adicionar uma ID de cluster interativa de Azure Databricks existente para um serviço vinculado do databricks ao arquivo de parâmetros. Note que este ficheiro é o mesmo que o ficheiro anterior, com exceção da adição de `existingClusterId` no âmbito do campo de propriedades de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
            "poolName": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Modelos de Gestor de Recursos Ligados

Se você tiver configurado o CI/CD para suas fábricas de dados, você poderá exceder os limites do modelo de Azure Resource Manager à medida que sua fábrica aumentar. Por exemplo, um limite é o número máximo de recursos em um modelo do Resource Manager. Para acomodar grandes fábricas ao gerar o modelo completo do Resource Manager para uma fábrica, Data Factory agora gera modelos vinculados do Resource Manager. Com esse recurso, toda a carga de fábrica é dividida em vários arquivos para que você não seja restrito pelos limites.

Se configurar git, os modelos ligados são gerados e guardados ao lado de todos os modelos do Gestor de Recursos no ramo adf_publish numa nova pasta chamada linkedTemplates:

![Pasta de modelos de gestor de recursos linked](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos vinculados do Resource Manager geralmente consistem em um modelo mestre e um conjunto de modelos filho que estão vinculados ao mestre. O modelo dos pais é chamado ArmTemplate_master.json, e os modelos infantis são nomeados com o padrão ArmTemplate_0.json, ArmTemplate_1.json, e assim por diante. 

Para utilizar modelos ligados em vez do modelo completo do Gestor de Recursos, atualize a sua tarefa CI/CD para apontar para ArmTemplate_master.json em vez de ArmTemplateForFactory.json (o modelo completo do Gestor de Recursos). O Gerenciador de recursos também exige que você carregue os modelos vinculados em uma conta de armazenamento para que o Azure possa acessá-los durante a implantação. Para mais informações, consulte [a implementação de modelos de Gestor de Recursos ligados com VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts data Factory no seu pipeline CI/CD antes e depois da tarefa de implantação.

Se não tiver Git configurado, pode aceder aos modelos ligados através do Modelo braço de **exportação** na lista de **modelos** ARM.

## <a name="hotfix-production-branch"></a>Branch de produção de hotfix

Se você implantar uma fábrica na produção e perceber que há um bug que precisa ser corrigido imediatamente, mas não é possível implantar o Branch de colaboração atual, talvez seja necessário implantar um hotfix. Esta abordagem é conhecida como engenharia de correção rápida ou QFE.

1.  No Azure DevOps, vá para a versão que foi implantada para produção. Localize a última confirmação implantada.

2.  Na mensagem de confirmação, obtenha a ID de confirmação da ramificação de colaboração.

3.  Crie uma nova ramificação de hotfix a partir dessa confirmação.

4.  Vá para o Azure Data Factory UX e alterne para o Branch de hotfix.

5.  Usando o Azure Data Factory UX, corrija o bug. Teste as suas alterações.

6.  Depois de verificada a correção, selecione o modelo braço de **exportação** para obter o modelo de Gestor de Recursos de fixação.

7.  Verifique manualmente esta construção no ramo adf_publish.

8.  Se configurar o seu pipeline de libertação para acionar automaticamente com base em adf_publish check-ins, uma nova versão começará automaticamente. Caso contrário, faça uma fila manual de lançamento.

9.  Implante a versão do hotfix nas fábricas de teste e produção. Esta versão contém a carga de produção anterior mais a correção que você fez na etapa 5.

10. Adicione as alterações do hotfix para a ramificação de desenvolvimento para que as versões posteriores não incluam o mesmo bug.

## <a name="best-practices-for-cicd"></a>Boas práticas para CI/CD

Se você estiver usando a integração do git com seu data factory e tiver um pipeline de CI/CD que move as alterações do desenvolvimento para o teste e, em seguida, para a produção, recomendamos estas práticas recomendadas:

-   **Integração git.** Você precisa configurar somente seu data factory de desenvolvimento com a integração do git. As alterações no teste e na produção são implantadas por meio de CI/CD e não precisam de integração com o git.

-   **Script CI/CD da fábrica**de dados . Antes da etapa de implantação do Gerenciador de recursos no CI/CD, você precisa concluir determinadas tarefas, como parar e reiniciar gatilhos e executar a limpeza. Recomendamos que você use scripts do PowerShell antes e depois da implantação. Para mais informações, consulte os [gatilhos ativos da Atualização](#update-active-triggers).

-   **Tempos de integração e partilha.** Os tempos de execução de integração não são alterados com frequência e são semelhantes em todos os estágios em seu CI/CD. Portanto Data Factory espera que você tenha o mesmo nome e tipo de tempo de execução de integração em todos os estágios de CI/CD. Se você quiser compartilhar tempos de execução de integração em todos os estágios, considere o uso de uma fábrica ternário apenas para conter os tempos de execução de integração compartilhados. Você pode usar esta fábrica partilhada em todos os seus ambientes como um tipo de tempo de execução de integração ligado.

-   **Cofre de chaves.** Ao usar serviços vinculados com base em Azure Key Vault, você pode aproveitar ainda mais os benefícios mantendo os cofres de chaves separados para ambientes diferentes. Você também pode configurar níveis de permissão separados para cada cofre de chaves. Por exemplo, talvez você não queira que os membros da equipe tenham permissões para os segredos de produção. Se você seguir essa abordagem, recomendamos que você mantenha os mesmos nomes de segredo em todos os estágios. Se você mantiver os mesmos nomes, não precisará alterar seus modelos do Resource Manager em ambientes de CI/CD porque a única coisa que muda é o nome do cofre de chaves, que é um dos parâmetros do modelo do Resource Manager.

## <a name="unsupported-features"></a>Características não suportadas

- Por design, Data Factory não permite a seleção de Cherry de confirmações ou publicação seletiva de recursos. As publicações incluirão todas as alterações feitas no data factory.

    - As entidades do data Factory dependem umas das outras. Por exemplo, os gatilhos dependem de pipelines e os pipelines dependem de conjuntos de valores e outros pipelines. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões em que você precisa de publicação seletiva, considere o uso de um hotfix. Para mais informações, consulte o ramo de [produção da Hotfix.](#hotfix-production-branch)

-   Não é possível publicar de branches particulares.

-   Atualmente, não é possível hospedar projetos no bitbucket.
