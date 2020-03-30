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
ms.date: 02/12/2020
ms.openlocfilehash: 8bbb11a8811582bea26e784636564eb5d5a4d284
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384335"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas na Azure Data Factory

## <a name="overview"></a>Descrição geral

A integração contínua é a prática de testar cada alteração feita à sua base de código automaticamente e o mais cedo possível.A entrega contínua segue os testes que odeiam durante a integração contínua e impulsiona alterações para um sistema de encenação ou produção.

Na Azure Data Factory, integração contínua e entrega (CI/CD) significa mover os gasodutos da Fábrica de Dados de um ambiente (desenvolvimento, teste, produção) para outro. Pode utilizar a integração ux da Fábrica de Dados com modelos do Gestor de Recursos Azure para fazer CI/CD.

Na Fábrica de Dados UX, pode gerar um modelo de Gestor de Recursos a partir do menu de entrega do **modelo ARM.** Quando seleciona o modelo de BRAÇO de **Exportação,** o portal gera o modelo de Gestor de Recursos para a fábrica de dados e um ficheiro de configuração que inclui todas as suas cordas de ligação e outros parâmetros. Em seguida, cria-se um ficheiro de configuração para cada ambiente (desenvolvimento, teste, produção). O ficheiro principal do modelo do Gestor de Recursos permanece o mesmo para todos os ambientes.

Para uma introdução de nove minutos a esta funcionalidade e uma demonstração, veja este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida CI/CD

Abaixo está uma visão geral do ciclo de vida CI/CD numa fábrica de dados Azure que está configurada com Azure Repos Git. Para obter mais informações sobre como configurar um repositório Git, consulte o controlo de origem na Fábrica de [Dados Azure](source-control.md).

1.  Uma fábrica de dados de desenvolvimento é criada e configurada com O Azure Repos Git. Todos os desenvolvedores devem ter permissão para autor de recursos da Data Factory, como oleodutos e conjuntos de dados.

1.  À medida que os desenvolvedores fazem alterações nos seus balcões de funcionalidades, desinam o seu pipeline com as suas mudanças mais recentes. Para obter mais informações sobre como depurar um gasoduto, consulte o [desenvolvimento iterativo e a depuração com](iterative-development-debugging.md)a Azure Data Factory .

1.  Após os desenvolvedores estarem satisfeitos com as suas alterações, criam um pedido de pull do seu ramo de recurso para o ramo de mestrado ou colaboração para que as suas mudanças sejam revistas pelos pares.

1.  Após a aprovação de um pedido de retirada e as alterações se fundirem no ramo principal, as alterações podem ser publicadas na fábrica de desenvolvimento.

1.  Quando a equipa estiver pronta para implementar as alterações na fábrica de teste e depois para a fábrica de produção, a equipa exporta o modelo de Gestor de Recursos do ramo principal.

1.  O modelo de Gestor de Recursos exportado é implantado com diferentes ficheiros de parâmetros para a fábrica de teste e a fábrica de produção.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Crie um modelo de Gestor de Recursos para cada ambiente

1. Na lista de **modelos ARM,** selecione modelo de BRAÇO de **Exportação** para exportar o modelo de Gestor de Recursos para a sua fábrica de dados no ambiente de desenvolvimento.

   ![Exportar um modelo de Gestor de Recursos](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Nas suas fábricas de dados de teste e produção, selecione **Import ARM Template**. Esta ação leva-o ao portal Azure, onde pode importar o modelo exportado. Selecione **Construir o seu próprio modelo no editor** para abrir o editor de modelo de Gestor de Recursos.

   ![Construa o seu próprio modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecione **ficheiro load**, e, em seguida, selecione o modelo de Gestor de Recursos gerado. Este é o ficheiro **arm_template.json** localizado no ficheiro .zip exportado no passo 1.

   ![Modelo de edição](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na secção definições, introduza os valores de configuração, como credenciais de serviço ligadas. Quando terminar, selecione **Comprar** para implementar o modelo de Gestor de Recursos.

   ![Secção de definições](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Cadeias de ligação

Para obter informações sobre como configurar as cordas de ligação, consulte o artigo do conector. Por exemplo, para a Base de Dados Azure SQL, consulte os dados de cópia de ou para a Base de [Dados Azure SQL utilizando](connector-azure-sql-database.md)a Azure Data Factory . Para verificar uma cadeia de ligação, pode abrir a vista de código para o recurso na Fábrica de Dados UX. Na vista do código, a palavra-passe ou a parte chave da conta da cadeia de ligação é removida. Para abrir a vista de código, selecione o ícone realçado aqui:

![Vista de código aberta para ver a cadeia de ligação](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizar a integração contínua utilizando lançamentos de Pipelines Azure

Segue-se um guia para a criação de uma versão da Azure Pipelines, que automatiza a implantação de uma fábrica de dados para vários ambientes.

![Diagrama de integração contínua com oleodutos Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma subscrição Azure ligada ao Visual Studio Team Foundation Server ou ao Azure Repos que utiliza o ponto final do [serviço Azure Resource Manager.](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)

-   Uma fábrica de dados configurada com integração Azure Repos Git.

-   Um [cofre azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos de cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Criar um lançamento de Pipelines Azure

1.  Na [Azure DevOps,](https://dev.azure.com/)abra o projeto que está configurado com a sua fábrica de dados.

1.  No lado esquerdo da página, selecione **Pipelines**, e, em seguida, selecione **Lançamentos**.

    ![Selecione Pipelines, Lançamentos](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **Novo oleoduto**, ou, se tiver oleodutos existentes, selecione **novo** e, em seguida, novo oleoduto de **lançamento**.

1.  Selecione o modelo de **trabalho vazio.**

    ![Selecione trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na caixa de **nomes do palco,** insira o nome do seu ambiente.

1.  Selecione **Adicionar artefacto,** e, em seguida, selecione o repositório configurado com a sua fábrica de dados. Selecione **adf_publish** para o **ramo Predefinido**. Para a **versão Predefinido,** selecione **O Mais Recente do ramo predefinido**.

    ![Adicionar um artefacto](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de implantação do Gestor de Recursos Azure:

    a.  Na vista do palco, selecione **Ver tarefas**de palco .

    ![Vista de palco](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Criar uma nova tarefa. Procure a implantação do Grupo de **Recursos Azure**e, em seguida, selecione **Adicionar**.

    c.  Na tarefa de Implantação, selecione a subscrição, o grupo de recursos e a localização da fábrica de dados alvo. Forneça credenciais, se necessário.

    d.  Na lista **de Ação,** selecione **Criar ou atualizar o grupo de recursos**.

    e.  Selecione o botão de elipse **(...**) ao lado da caixa **do modelo.** Procure o modelo do Gestor de Recursos Azure que criou utilizando o **modelo de ARM de Importação** no modelo Criar um Gestor de Recursos para cada secção de [ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) deste artigo. Procure este ficheiro <FactoryName> na pasta da filial adf_publish.

    f.  Selecione **...** junto à caixa de **parâmetros do Modelo** para escolher o ficheiro de parâmetros. O ficheiro que escolher dependerá se criou uma cópia ou se está a utilizar o ficheiro predefinido, ARMTemplateParametersForFactory.json.

    g.  Selecione **...** junto à caixa de parâmetros do **modelo de sobreposição** e introduza a informação para a fábrica de dados alvo. Para credenciais que vêm do Cofre chave azure, insira o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for cred1, insira **"$(cred1)"** para este valor.

    h. **Selecione Incremental** para o **modo de implantação**.

    > [!WARNING]
    > Se selecionar **Complete** para o **modo de Implantação,** os recursos existentes podem ser eliminados, incluindo todos os recursos do grupo de recursos-alvo que não estão definidos no modelo de Gestor de Recursos.

    ![Implantação de Prod da fábrica de dados](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salve o oleoduto de libertação.

1. Para desencadear uma libertação, selecione **Criar**.

   ![Selecione criar lançamento](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Nos cenários CI/CD, o tipo de tempo de integração (IR) em diferentes ambientes deve ser o mesmo. Por exemplo, se tiver um IR auto-alojado no ambiente de desenvolvimento, o mesmo IR também deve ser de tipo auto-alojado noutros ambientes, como o teste e a produção. Da mesma forma, se você está partilhando tempos de integração em várias fases, você tem que configurar os tempos de integração como conectado auto-hospedado em todos os ambientes, tais como desenvolvimento, teste e produção.

### <a name="get-secrets-from-azure-key-vault"></a>Obtenha segredos do Cofre de Chaves Azure

Se tiver segredos para passar num modelo de Gestor de Recursos Azure, recomendamos que utilize o Cofre chave Azure com o lançamento dos Pipelines Azure.

Há duas maneiras de lidar com segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para mais informações, consulte [Use Azure Key Vault para passar o valor do parâmetro seguro durante a implementação](../azure-resource-manager/templates/key-vault-parameter.md).

    Crie uma cópia do ficheiro de parâmetros que é enviado para a filial. Defina os valores dos parâmetros que pretende obter do Key Vault utilizando este formato:

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

1. Adicione uma tarefa de cofre de [chave Azure](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de implantação do Gestor de Recursos Azure descrita na secção anterior:

    1.  No separador **Tarefas,** crie uma nova tarefa. Procure o **Cofre de Chaves Azure** e adicione-o.

    1.  Na tarefa Key Vault, selecione a subscrição na qual criou o cofre chave. Forneça credenciais se necessário e, em seguida, selecione o cofre da chave.

    ![Adicione uma tarefa chave vault](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões ao agente da Azure Pipelines

A tarefa do Cofre de Chaves Azure pode falhar com um erro de Acesso Negado se as permissões corretas não estiverem definidas. Descarregue os registos para o lançamento e localize o ficheiro .ps1 que contém o comando para dar permissões ao agente azure pipelines. Pode dirigir o comando diretamente. Ou pode copiar o ID principal do ficheiro e adicionar a política de acesso manualmente no portal Azure. `Get`e `List` são as permissões mínimas necessárias.

### <a name="update-active-triggers"></a>Atualizar gatilhos ativos

A implementação pode falhar se tentar atualizar os gatilhos ativos. Para atualizar os gatilhos ativos, é necessário detê-los manualmente e, em seguida, reiniciá-los após a implementação. Pode fazê-lo utilizando uma tarefa Azure PowerShell:

1.  No separador **Tasks** do lançamento, adicione uma tarefa **Azure PowerShell.** Escolha a versão de tarefa 4.*. 

1.  Selecione a subscrição em que a sua fábrica se encontra.

1.  Selecione **script file path** como o tipo de script. Isto requer que guarde o seu script PowerShell no seu repositório. O seguinte script PowerShell pode ser usado para parar os gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Pode completar passos semelhantes (com a `Start-AzDataFactoryV2Trigger` função) para reiniciar os gatilhos após a colocação.

### <a name="sample-pre--and-post-deployment-script"></a>Exemplo de script pré e pós-implantação

O script da amostra seguinte pode ser usado para parar os gatilhos antes de os utilizar e reiniciá-los posteriormente. O script também inclui código para eliminar recursos que foram removidos. Guarde o script num repositório Azure DevOps git e remecite-o através de uma tarefa Azure PowerShell utilizando a versão 4.*.

Ao executar um script de pré-implementação, terá de especificar uma variação dos **seguintes parâmetros** no campo Argumentos do Script.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Ao executar um script pós-implantação, terá de especificar uma variação dos **seguintes parâmetros** no campo Argumentos do Script.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Tarefa Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Aqui está o script que pode ser usado para pré e pós-implantação. É responsável por recursos eliminados e referências de recursos.

```powershell
param
(
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
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
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Utilizar parâmetros personalizados com o modelo do Resource Manager

Se estiver no modo GIT, pode substituir as propriedades padrão no seu modelo de Gestor de Recursos para definir propriedades que são parametrizadas no modelo e propriedades que estão codificadas. Talvez queira anular o modelo de parametrização padrão nestes cenários:

* Você usa CI/CD automatizado e você quer alterar algumas propriedades durante a implementação do Gestor de Recursos, mas as propriedades não são parametrizadas por padrão.
* A sua fábrica é tão grande que o modelo padrão do Gestor de Recursos é inválido porque tem mais do que os parâmetros máximos permitidos (256).

Nestas condições, para anular o modelo de parametrização predefinido, crie um ficheiro denominado **modelo de braço-modelo-definição.json** na pasta especificada como a pasta raiz para a integração do git da fábrica de dados. Tens de usar o nome exato do ficheiro. Data Factory lê este ficheiro a partir de qualquer filial em que esteja atualmente no portal Azure Data Factory, e não apenas do ramo de colaboração. Pode criar ou editar o ficheiro a partir de uma sucursal privada, onde pode testar as suas alterações selecionando o Modelo braço de **exportação** na UI. Em seguida, pode fundir o ficheiro no ramo de colaboração. Se não for encontrado nenhum ficheiro, o modelo predefinido é utilizado.

> [!NOTE]
> Um modelo de parametrização personalizado não altera o limite de parâmetro do modelo ARM de 256. Permite-lhe escolher e diminuir o número de propriedades parametrizadas.

### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um arquivo de parâmetros personalizados

Seguem-se algumas diretrizes a seguir quando criar o ficheiro de parâmetros personalizados. O ficheiro consiste numa secção para cada tipo de entidade: gatilho, pipeline, serviço ligado, conjunto de dados, tempo de execução de integração, e assim por diante.
* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome `*` de propriedade para indicar que pretende parametrizar todas as propriedades por baixo (apenas até ao primeiro nível, não recursivamente). Também pode fornecer exceções a esta configuração.
* Definir o valor de uma propriedade como uma corda indica que você quer parametrizar a propriedade. Utilize o `<action>:<name>:<stype>`formato .
   *  `<action>` pode ser um destes caracteres:
      * `=` significa manter o valor atual como valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos do Cofre chave Azure para cordas de ligação ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, leva o nome da propriedade. Se o valor `-` começar com um personagem, o nome é encurtado. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria encurtado para `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, `string`o tipo predefinido é . `string`Valores suportados: , `bool`, `number`, `object`e `securestring`.
* Especificar uma matriz no ficheiro de definição indica que a propriedade correspondente no modelo é uma matriz. A Fábrica de Dados iterates através de todos os objetos da matriz usando a definição especificada no objeto de tempo de funcionação de integração da matriz. O segundo objeto, uma corda, torna-se o nome da propriedade, que é usada como nome para o parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recursos. Qualquer definição aplica-se a todos os recursos desse tipo.
* Por padrão, todas as cordas seguras, como segredos do Cofre chave, e cordas seguras, como cordas de ligação, chaves e fichas, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização da amostra

Aqui está um exemplo de como um modelo de parametrização pode parecer:

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
    
* Qualquer propriedade no `activities/typeProperties/waitTimeInSeconds` caminho é parametrizada. Qualquer atividade num oleoduto que tenha uma `waitTimeInSeconds` propriedade de `Wait` nível de código chamada (por exemplo, a atividade) é parametrizada como um número, com um nome predefinido. Mas não terá um valor predefinido no modelo do Gestor de Recursos. Será uma entrada obrigatória durante a implantação do Gestor de Recursos.
* Da mesma forma, `headers` uma propriedade chamada `Web` (por exemplo, numa `object` atividade) é parametrizada com tipo (JObject). Tem um valor predefinido, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegraçãoRuntimes

* Todas as propriedades `typeProperties` sob o caminho são parametrizadas com os respetivos valores predefinidos. Por exemplo, existem `IntegrationRuntimes` duas propriedades `computeProperties` `ssisProperties`em propriedades do tipo: e . Ambos os tipos de propriedades são criados com os respetivos valores e tipos padrão (Objeto).

#### <a name="triggers"></a>Acionadores

* Em `typeProperties`baixo, duas propriedades são parametrizadas. O primeiro `maxConcurrency`é , que é especificado para ter`string`um valor predefinido e é de tipo . Tem o nome `<entityName>_properties_typeProperties_maxConcurrency`de parâmetro predefinido .
* A `recurrence` propriedade também é parametrizada. Por baixo, todas as propriedades a esse nível são especificadas para serem parametrizadas como cordas, com valores predefinidos e nomes de parâmetros. Uma exceção `interval` é a propriedade, que `number`é parametrizada como tipo . O nome do parâmetro é `<entityName>_properties_typeProperties_recurrence_triggerSuffix`sufixo com . Da mesma `freq` forma, a propriedade é uma corda e é parametrizada como uma corda. No entanto, a `freq` propriedade é parametrizada sem um valor predefinido. O nome é encurtado e sufixo. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços ligados são únicos. Como os serviços e conjuntos de dados ligados têm uma vasta gama de tipos, pode fornecer personalização específica do tipo. Neste exemplo, para todos os `AzureDataLakeStore`serviços de tipo ligados, será aplicado um modelo específico. Para todos os `*`outros (via), será aplicado um modelo diferente.
* A `connectionString` propriedade será parametrizada como um `securestring` valor. Não terá um valor padrão. Terá um nome de parâmetro encurtado que `connectionString`é sufixo com .
* A `secretAccessKey` propriedade passa `AzureKeyVaultSecret` a ser um (por exemplo, num serviço ligado à Amazon S3). É automaticamente parametido como um segredo azure key vault e recolhido do cofre de chaves configurado. Também pode parametrizar o cofre chave em si.

#### <a name="datasets"></a>Conjuntos de dados

* Embora a personalização específica do tipo esteja disponível para conjuntos \*de dados, pode fornecer configuração sem ter explicitamente uma configuração de nível. No exemplo anterior, todas as `typeProperties` propriedades do conjunto de dados em baixo são parametrizadas.

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

Segue-se o modelo de parametrização padrão atual. Se precisar de adicionar apenas alguns parâmetros, editar este modelo diretamente pode ser uma boa ideia porque não perderá a estrutura de parametrização existente.

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

O exemplo seguinte mostra como adicionar um único valor ao modelo de parametrização padrão. Apenas queremos adicionar um ID de cluster interativo Azure Databricks existente para um serviço ligado a Databricks ao ficheiro de parâmetros. Note que este ficheiro é o mesmo que `existingClusterId` o ficheiro anterior, exceto a adição no campo de propriedades de `Microsoft.DataFactory/factories/linkedServices`.

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

Se configurar CI/CD para as suas fábricas de dados, poderá exceder os limites do modelo do Gestor de Recursos Azure à medida que a sua fábrica cresce. Por exemplo, um limite é o número máximo de recursos num modelo de Gestor de Recursos. Para acomodar grandes fábricas enquanto gera o modelo completo do Gestor de Recursos para uma fábrica, a Data Factory gera agora modelos ligados ao Gestor de Recursos. Com esta funcionalidade, toda a carga útil da fábrica é dividida em vários ficheiros para que não esteja limitado pelos limites.

Se configurar git, os modelos ligados são gerados e guardados ao lado de todos os modelos do Gestor de Recursos no ramo adf_publish numa nova pasta chamada linkedTemplates:

![Pasta de modelos de gestor de recursos linked](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos ligados do Gestor de Recursos geralmente consistem em um modelo principal e um conjunto de modelos infantis que estão ligados ao mestre. O modelo dos pais é chamado ArmTemplate_master.json, e os modelos infantis são nomeados com o padrão ArmTemplate_0.json, ArmTemplate_1.json, e assim por diante. 

Para utilizar modelos ligados em vez do modelo completo do Gestor de Recursos, atualize a sua tarefa CI/CD para apontar para ArmTemplate_master.json em vez de ArmTemplateForFactory.json (o modelo completo do Gestor de Recursos). O Gestor de Recursos também requer que você carregue os modelos ligados para uma conta de armazenamento para que o Azure possa acessá-los durante a implementação. Para mais informações, consulte [a implementação de modelos de Gestor de Recursos ligados com VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts data Factory no seu pipeline CI/CD antes e depois da tarefa de implantação.

Se não tiver Git configurado, pode aceder aos modelos ligados através do Modelo braço de **exportação** na lista de **modelos** ARM.

## <a name="hotfix-production-branch"></a>Ramo de produção hotfix

Se implantar uma fábrica para produção e perceber que há um bug que precisa de ser corrigido imediatamente, mas não pode implantar o atual ramo de colaboração, talvez precise de implementar um hotfix. Esta abordagem é conhecida como engenharia de correção rápida ou QFE.

1.    Em Azure DevOps, vá ao lançamento que foi implantado para produção. Encontre o último compromisso que foi implantado.

2.    A partir da mensagem de compromisso, obtenha a identificação comprometedora do ramo de colaboração.

3.    Crie um novo ramo de fixação a partir desse compromisso.

4.    Vá à Fábrica de Dados Azure UX e mude para o ramo de hotfix.

5.    Utilizando o Ux da Fábrica de Dados Azure, corrija o bug. Teste as suas alterações.

6.    Depois de verificada a correção, selecione o modelo braço de **exportação** para obter o modelo de Gestor de Recursos de fixação.

7.    Verifique manualmente esta construção no ramo adf_publish.

8.    Se configurar o seu pipeline de libertação para acionar automaticamente com base em adf_publish check-ins, uma nova versão começará automaticamente. Caso contrário, faça uma fila manual de lançamento.

9.    Implante a libertação de hotfix para as fábricas de teste e produção. Esta versão contém a carga útil de produção anterior mais a correção que fez no passo 5.

10.    Adicione as alterações do hotfix para o ramo de desenvolvimento para que os lançamentos posteriores não incluam o mesmo bug.

## <a name="best-practices-for-cicd"></a>Boas práticas para CI/CD

Se estiver a usar a integração git com a sua fábrica de dados e tiver um pipeline CI/CD que move as suas alterações do desenvolvimento para teste e depois para a produção, recomendamos estas melhores práticas:

-   **Integração git.** Precisa configurar apenas a sua fábrica de dados de desenvolvimento com integração git. As alterações ao teste e à produção são implementadas via CI/CD e não precisam de integração git.

-   **Script CI/CD da fábrica**de dados . Antes do passo de implementação do Gestor de Recursos em CI/CD, é necessário completar determinadas tarefas, como parar e reiniciar gatilhos e realizar a limpeza. Recomendamos que utilize scripts PowerShell antes e depois da implantação. Para mais informações, consulte os [gatilhos ativos da Atualização](#update-active-triggers).

-   **Tempos de integração e partilha.** Os tempos de integração não mudam frequentemente e são semelhantes em todas as fases do seu CI/CD. Assim, data Factory espera que você tenha o mesmo nome e tipo de tempo de integração em todas as fases do CI/CD. Se quiser partilhar os tempos de integração em todas as fases, considere usar uma fábrica ternary apenas para conter os tempos de execução de integração partilhada. Você pode usar esta fábrica partilhada em todos os seus ambientes como um tipo de tempo de execução de integração ligado.

-   **Cofre de chaves.** Quando utiliza serviços ligados baseados no Cofre de Chaves Azure, pode aproveitar-se deles mantendo cofres-chave separados para diferentes ambientes. Também pode configurar níveis de permissão separados para cada cofre chave. Por exemplo, pode não querer que os membros da sua equipa tenham permissões para os segredos de produção. Se seguir esta abordagem, recomendamos que mantenha os mesmos nomes secretos em todos os estágios. Se mantiver os mesmos nomes, não tem de alterar os seus modelos de Gestor de Recursos em ambientes CI/CD, porque a única coisa que muda é o nome do cofre chave, que é um dos parâmetros do modelo do Gestor de Recursos.

## <a name="unsupported-features"></a>Características não suportadas

- Por design, data Factory não permite a recolha de cerejas de compromissos ou publicação seletiva de recursos. As publicações incluirão todas as alterações efetuadas na fábrica de dados.

    - As entidades de fábrica de dados dependem umas das outras. Por exemplo, os gatilhos dependem de oleodutos, e os oleodutos dependem de conjuntos de dados e outros oleodutos. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões em que precisa de publicação seletiva, considere usar um hotfix. Para mais informações, consulte o ramo de [produção da Hotfix.](#hotfix-production-branch)

-   Não pode publicar em agências privadas.

-   Não pode saquear projetos no Bitbucket.
