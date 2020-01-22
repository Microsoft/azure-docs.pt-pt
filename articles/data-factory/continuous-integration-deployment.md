---
title: Integração e entrega contínuas no Azure Data Factory
description: Saiba como usar a integração e a entrega contínuas para mover Data Factory pipelines de um ambiente (desenvolvimento, teste, produção) para outro.
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
ms.openlocfilehash: 2e14b1bcc991a009ed9b3267477933706e1ec474
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289956"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Integração e entrega contínuas no Azure Data Factory

## <a name="overview"></a>Visão geral

A integração contínua é a prática de testar cada alteração feita na base de código automaticamente e o mais cedo possível. A entrega contínua segue os testes que ocorrem durante a integração contínua e envia por push as alterações para um sistema de preparo ou de produção.

Em Azure Data Factory, a integração contínua e a entrega (CI/CD) significam mover Data Factory pipelines de um ambiente (desenvolvimento, teste, produção) para outro. Você pode usar a integração do Data Factory UX com modelos de Azure Resource Manager para fazer CI/CD.

No Data Factory UX, você pode gerar um modelo do Resource Manager no menu suspenso **modelo ARM** . Quando você seleciona **Exportar modelo do ARM**, o portal gera o modelo do Resource Manager para o data Factory e um arquivo de configuração que inclui todas as cadeias de conexão e outros parâmetros. Em seguida, você cria um arquivo de configuração para cada ambiente (desenvolvimento, teste, produção). O arquivo de modelo principal do Resource Manager permanece o mesmo para todos os ambientes.

Para uma introdução de nove minutos a esse recurso e uma demonstração, Assista a este vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Ciclo de vida de CI/CD

Abaixo está um exemplo de visão geral do ciclo de vida de CI/CD em uma data factory do Azure configurada com Azure Repos git. Para obter mais informações sobre como configurar um repositório git, consulte [controle do código-fonte em Azure data Factory](source-control.md).

1.  Um data factory de desenvolvimento é criado e configurado com Azure Repos git. Todos os desenvolvedores devem ter permissão para criar Data Factory recursos como pipelines e conjuntos de valores.

1.  À medida que os desenvolvedores fazem alterações em suas ramificações de recursos, eles depuram suas execuções de pipeline com suas alterações mais recentes. Para obter mais informações sobre como depurar uma execução de pipeline, consulte [desenvolvimento iterativo e depuração com Azure data Factory](iterative-development-debugging.md).

1.  Depois que os desenvolvedores estão satisfeitos com suas alterações, eles criam uma solicitação de pull de sua ramificação de recursos para o Branch mestre ou de colaboração para que suas alterações sejam revisadas por colegas.

1.  Depois que uma solicitação de pull é aprovada e as alterações são mescladas no Branch mestre, as alterações podem ser publicadas na fábrica de desenvolvimento.

1.  Quando a equipe estiver pronta para implantar as alterações na fábrica de testes e, em seguida, na fábrica de produção, a equipe exportará o modelo do Resource Manager do Branch mestre.

1.  O modelo exportado do Resource Manager é implantado com arquivos de parâmetro diferentes na fábrica de teste e na fábrica de produção.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Criar um modelo do Resource Manager para cada ambiente

1. Na lista **modelo do ARM** , selecione **Exportar modelo do ARM** para exportar o modelo do Resource Manager para seu data Factory no ambiente de desenvolvimento.

   ![Exportar um modelo do Resource Manager](media/continuous-integration-deployment/continuous-integration-image1.png)

1. Em suas fábricas de dados de teste e de produção, selecione **Importar modelo de ARM**. Essa ação leva você para a portal do Azure, onde você pode importar o modelo exportado. Selecione **criar seu próprio modelo no editor** para abrir o editor de modelos do Resource Manager.

   ![Crie seu próprio modelo](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Selecione **carregar arquivo**e, em seguida, selecione o modelo do Resource Manager gerado.

   ![Editar modelo](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. Na seção Configurações, insira os valores de configuração, como credenciais de serviço vinculado. Quando terminar, selecione **comprar** para implantar o modelo do Resource Manager.

   ![Seção de configurações](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Cadeias de ligação

Para obter informações sobre como configurar cadeias de conexão, consulte o artigo do conector. Por exemplo, para o banco de dados SQL do Azure, confira [copiar ou fazer a partir do Azure SQL Database usando Azure data Factory](connector-azure-sql-database.md). Para verificar uma cadeia de conexão, você pode abrir a exibição de código para o recurso no Data Factory UX. Na exibição de código, a parte de chave de conta ou senha da cadeia de conexão é removida. Para abrir o modo de exibição de código, selecione o ícone realçado aqui:

![Abra o modo de exibição de código para ver a cadeia de conexão](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizar a integração contínua usando versões Azure Pipelines

A seguir, um guia para configurar uma versão Azure Pipelines, que automatiza a implantação de um data factory em vários ambientes.

![Diagrama de integração contínua com o Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma assinatura do Azure vinculada ao Visual Studio Team Foundation Server ou Azure Repos que usa o [ponto de extremidade de serviço do Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Um data factory configurado com Azure Repos integração com o git.

-   Um [cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) que contém os segredos para cada ambiente.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar uma versão Azure Pipelines

1.  No [Azure DevOps](https://dev.azure.com/), abra o projeto que está configurado com seu data Factory.

1.  No lado esquerdo da página, selecione **pipelines**e, em seguida, selecione **versões**.

    ![Selecionar pipelines, versões](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione **novo pipeline**ou, se você tiver pipelines existentes, selecione **novo** e, em seguida, **novo pipeline de liberação**.

1.  Selecione o modelo de **trabalho vazio** .

    ![Selecionar trabalho vazio](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Na caixa **nome do estágio** , digite o nome do seu ambiente.

1.  Selecione **Adicionar artefato**e, em seguida, selecione o repositório configurado com sua data Factory. Selecione **adf_publish** para a **ramificação padrão**. Para a **versão padrão**, selecione **mais recente do Branch padrão**.

    ![Add an artifact](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicionar uma tarefa de implantação de Azure Resource Manager:

    a.  No modo de exibição de estágio, selecione **Exibir tarefas de estágio**.

    ![Exibição de estágio](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Crie uma nova tarefa. Pesquise **implantação do grupo de recursos do Azure**e, em seguida, selecione **Adicionar**.

    c.  Na tarefa de implantação, selecione a assinatura, o grupo de recursos e o local para o data factory de destino. Forneça as credenciais, se necessário.

    d.  Na lista **ação** , selecione **criar ou atualizar grupo de recursos**.

    e.  Selecione o botão de reticências ( **...** ) ao lado da caixa **modelo** . Procure o modelo de Azure Resource Manager que você criou usando **Importar modelo de ARM** na seção [criar um modelo do Resource Manager para cada ambiente](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) deste artigo. Procure esse arquivo na pasta <FactoryName> da ramificação adf_publish.

    f.  Selecionar **...** ao lado da caixa **parâmetros de modelo** para escolher o arquivo de parâmetros. O arquivo que você escolher dependerá se você criou uma cópia ou está usando o arquivo padrão, ARMTemplateParametersForFactory. JSON.

    g.  Selecionar **...** ao lado da caixa **Substituir parâmetros do modelo** e insira as informações para o data Factory de destino. Para as credenciais provenientes de Azure Key Vault, insira o nome do segredo entre aspas duplas. Por exemplo, se o nome do segredo for cred1, insira **"$ (cred1)"** para esse valor.

    h. Selecione **incremental** para o **modo de implantação**.

    > [!WARNING]
    > Se você selecionar **concluir** para o **modo de implantação**, os recursos existentes poderão ser excluídos, incluindo todos os recursos no grupo de recursos de destino que não estão definidos no modelo do Resource Manager.

    ![Implantação do Data Factory prod](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Salve o pipeline de liberação.

1. Para disparar uma versão, selecione **criar versão**.

   ![Selecionar criar versão](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Obter segredos de Azure Key Vault

Se você tiver segredos para passar um modelo de Azure Resource Manager, recomendamos que você use Azure Key Vault com a versão Azure Pipelines.

Há duas maneiras de lidar com os segredos:

1.  Adicione os segredos ao arquivo de parâmetros. Para obter mais informações, consulte [usar Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](../azure-resource-manager/templates/key-vault-parameter.md).

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

    Quando você usa esse método, o segredo é extraído do cofre de chaves automaticamente.

    O arquivo de parâmetros também deve estar na ramificação de publicação.

-  Adicione uma [tarefa de Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da tarefa de implantação de Azure Resource Manager descrita na seção anterior:

    1.  Na guia **tarefas** , crie uma nova tarefa. Pesquise **Azure Key Vault** e adicione-o.

    1.  Na tarefa Key Vault, selecione a assinatura na qual você criou o cofre de chaves. Forneça as credenciais, se necessário, e selecione o cofre de chaves.

    ![Adicionar uma tarefa de Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões para o agente de Azure Pipelines

   A tarefa Azure Key Vault poderá falhar com um erro de acesso negado se as permissões corretas não estiverem definidas. Baixe os logs da versão e localize o arquivo. ps1 que contém o comando para conceder permissões ao agente de Azure Pipelines. Você pode executar o comando diretamente. Ou você pode copiar a ID da entidade de segurança do arquivo e adicionar a política de acesso manualmente no portal do Azure. `Get` e `List` são as permissões mínimas necessárias.

### <a name="update-active-triggers"></a>Atualizar gatilhos ativos

A implantação poderá falhar se você tentar atualizar os gatilhos ativos. Para atualizar os gatilhos ativos, você precisa interrompê-los manualmente e reiniciá-los após a implantação. Você pode fazer isso usando uma tarefa de Azure PowerShell:

1.  Na guia **tarefas** da versão, adicione uma tarefa de **Azure PowerShell** .

1.  Selecione **Azure Resource Manager** como o tipo de conexão e, em seguida, selecione sua assinatura.

1.  Selecione **script embutido** como o tipo de script e, em seguida, forneça seu código. O código a seguir interrompe os gatilhos:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![Azure PowerShell tarefa](media/continuous-integration-deployment/continuous-integration-image11.png)

Você pode concluir etapas semelhantes (com a função `Start-AzDataFactoryV2Trigger`) para reiniciar os gatilhos após a implantação.

> [!IMPORTANT]
> Em cenários de CI/CD, o tipo de IR (Integration Runtime) em ambientes diferentes deve ser o mesmo. Por exemplo, se você tiver um IR auto-hospedado no ambiente de desenvolvimento, o mesmo IR também deverá ser do tipo auto-hospedado em outros ambientes, como teste e produção. Da mesma forma, se você estiver compartilhando tempos de execução de integração em vários estágios, precisará configurar os tempos de execução de integração como vinculados internamente em todos os ambientes, como desenvolvimento, teste e produção.

#### <a name="sample-pre--and-post-deployment-script"></a>Script pré e pós-implantação de exemplo

O script de exemplo a seguir mostra como parar os gatilhos antes da implantação e reiniciá-los depois. O script também inclui código para excluir os recursos que foram removidos. Para instalar a versão mais recente do Azure PowerShell, consulte [instalar Azure PowerShell no Windows com PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Usar parâmetros personalizados com o modelo do Resource Manager

Se você estiver no modo GIT, poderá substituir as propriedades padrão em seu modelo do Resource Manager para definir propriedades que são parametrizadas no modelo e propriedades que são embutidas em código. Talvez você queira substituir o modelo de parametrização padrão nesses cenários:

* Você usa CI/CD automatizado e deseja alterar algumas propriedades durante a implantação do Resource Manager, mas as propriedades não são parametrizadas por padrão.
* Sua fábrica é tão grande que o modelo padrão do Resource Manager é inválido porque ele tem mais do que os parâmetros máximos permitidos (256).

Sob essas condições, para substituir o modelo de parametrização padrão, crie um arquivo chamado ARM-template-Parameters-Definition. JSON na pasta especificada como a pasta raiz para a integração do data factory git. Você deve usar esse nome de arquivo exato. Data Factory lê esse arquivo de qualquer ramificação que você esteja no portal de Azure Data Factory, não apenas da ramificação de colaboração. Você pode criar ou editar o arquivo de um Branch privado, no qual você pode testar suas alterações selecionando **Exportar modelo ARM** na interface do usuário. Em seguida, você pode mesclar o arquivo no Branch de colaboração. Se nenhum arquivo for encontrado, o modelo padrão será usado.

### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um arquivo de parâmetros personalizado

Veja a seguir algumas diretrizes a serem seguidas ao criar o arquivo de parâmetros personalizados. O arquivo consiste em uma seção para cada tipo de entidade: gatilho, pipeline, serviço vinculado, conjunto de serviços, tempo de execução de integração e assim por diante.
* Insira o caminho da propriedade sob o tipo de entidade relevante.
* Definir um nome de propriedade como `*` indica que você deseja parametrizar todas as propriedades abaixo dele (somente até o primeiro nível, não recursivamente). Você também pode fornecer exceções a essa configuração.
* Definir o valor de uma propriedade como uma cadeia de caracteres indica que você deseja parametrizar a propriedade. Use o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um destes caracteres:
      * `=` significa manter o valor atual como o valor padrão para o parâmetro.
      * `-` significa não manter o valor padrão para o parâmetro.
      * `|` é um caso especial para segredos de Azure Key Vault para cadeias de conexão ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, ele usará o nome da propriedade. Se o valor começar com um `-` caractere, o nome será reduzido. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria reduzida para `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` estiver em branco, o tipo padrão será `string`. Valores com suporte: `string`, `bool`, `number`, `object`e `securestring`.
* A especificação de uma matriz no arquivo de definição indica que a propriedade correspondente no modelo é uma matriz. Data Factory itera por todos os objetos na matriz usando a definição especificada no objeto de tempo de execução de integração da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é usado como o nome do parâmetro para cada iteração.
* Uma definição não pode ser específica para uma instância de recurso. Qualquer definição se aplica a todos os recursos desse tipo.
* Por padrão, todas as cadeias de caracteres seguras, como segredos de Key Vault e cadeias de caracteres seguras, como cadeias de conexão, chaves e tokens, são parametrizadas.
 
### <a name="sample-parameterization-template"></a>Modelo de parametrização de exemplo

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
    
* Qualquer propriedade no caminho `activities/typeProperties/waitTimeInSeconds` é parametrizada. Qualquer atividade em um pipeline que tenha uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, a atividade `Wait`) é parametrizada como um número, com um nome padrão. Mas ele não terá um valor padrão no modelo do Resource Manager. Será uma entrada obrigatória durante a implantação do Gerenciador de recursos.
* Da mesma forma, uma propriedade chamada `headers` (por exemplo, em uma atividade de `Web`) é parametrizada com o tipo `object` (JObject). Ele tem um valor padrão, que é o mesmo valor da fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Todas as propriedades no caminho `typeProperties` são parametrizadas com seus respectivos valores padrão. Por exemplo, há duas propriedades em Propriedades de tipo `IntegrationRuntimes`: `computeProperties` e `ssisProperties`. Ambos os tipos de propriedade são criados com seus respectivos valores e tipos padrão (objeto).

#### <a name="triggers"></a>Acionadores

* Em `typeProperties`, duas propriedades são parametrizadas. O primeiro é `maxConcurrency`, que é especificado para ter um valor padrão e é do tipo`string`. Ele tem o nome de parâmetro padrão `<entityName>_properties_typeProperties_maxConcurrency`.
* A propriedade `recurrence` também é parametrizada. Sob ele, todas as propriedades nesse nível são especificadas para serem parametrizadas como cadeias de caracteres, com valores padrão e nomes de parâmetro. Uma exceção é a propriedade `interval`, que é parametrizada como tipo `number`. O nome do parâmetro é sufixado com `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Da mesma forma, a propriedade `freq` é uma cadeia de caracteres e é parametrizada como uma cadeia de caracteres. No entanto, a propriedade `freq` é parametrizada sem um valor padrão. O nome é reduzido e sufixado. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Os serviços vinculados são exclusivos. Como os serviços vinculados e os conjuntos de linhas têm uma ampla gama de tipos, você pode fornecer personalização específica de tipo. Neste exemplo, para todos os serviços vinculados do tipo `AzureDataLakeStore`, um modelo específico será aplicado. Para todos os outros (via `*`), um modelo diferente será aplicado.
* A propriedade `connectionString` será parametrizada como um valor de `securestring`. Ele não terá um valor padrão. Ele terá um nome de parâmetro abreviado com um sufixo `connectionString`.
* A propriedade `secretAccessKey` é uma `AzureKeyVaultSecret` (por exemplo, em um serviço vinculado do Amazon S3). Ele é parametrizado automaticamente como um Azure Key Vault segredo e buscado a partir do cofre de chaves configurado. Você também pode parametrizar o cofre de chaves em si.

#### <a name="datasets"></a>Conjuntos de dados

* Embora a personalização específica de tipo esteja disponível para conjuntos de informações, você pode fornecer configuração sem ter explicitamente uma configuração de nível \*. No exemplo anterior, todas as propriedades de DataSet em `typeProperties` são parametrizadas.

### <a name="default-parameterization-template"></a>Modelo de parametrização padrão

A seguir está o modelo de parametrização padrão atual. Se você precisar adicionar apenas alguns parâmetros, editar esse modelo diretamente pode ser uma boa ideia, pois você não perderá a estrutura de parametrização existente.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

O exemplo a seguir mostra como adicionar um único valor ao modelo de parametrização padrão. Queremos apenas adicionar uma ID de cluster interativa de Azure Databricks existente para um serviço vinculado do databricks ao arquivo de parâmetros. Observe que esse arquivo é o mesmo que o arquivo anterior, exceto para a adição de `existingClusterId` sob o campo Propriedades de `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
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

## <a name="linked-resource-manager-templates"></a>Modelos vinculados do Resource Manager

Se você tiver configurado o CI/CD para suas fábricas de dados, você poderá exceder os limites do modelo de Azure Resource Manager à medida que sua fábrica aumentar. Por exemplo, um limite é o número máximo de recursos em um modelo do Resource Manager. Para acomodar grandes fábricas ao gerar o modelo completo do Resource Manager para uma fábrica, Data Factory agora gera modelos vinculados do Resource Manager. Com esse recurso, toda a carga de fábrica é dividida em vários arquivos para que você não seja restrito pelos limites.

Se você tiver configurado o Git, os modelos vinculados serão gerados e salvos junto com os modelos completos do Resource Manager na ramificação adf_publish em uma nova pasta chamada linkedTemplates:

![Pasta de modelos do Resource Manager vinculado](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos vinculados do Resource Manager geralmente consistem em um modelo mestre e um conjunto de modelos filho que estão vinculados ao mestre. O modelo pai é chamado de ArmTemplate_master. JSON, e os modelos filho são nomeados com o padrão ArmTemplate_0. JSON, ArmTemplate_1. JSON e assim por diante. 

Para usar modelos vinculados em vez do modelo completo do Resource Manager, atualize sua tarefa de CI/CD para apontar para ArmTemplate_master. JSON em vez de ArmTemplateForFactory. JSON (o modelo completo do Resource Manager). O Gerenciador de recursos também exige que você carregue os modelos vinculados em uma conta de armazenamento para que o Azure possa acessá-los durante a implantação. Para obter mais informações, consulte [implantando modelos vinculados do Resource Manager com o VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Lembre-se de adicionar os scripts de Data Factory em seu pipeline de CI/CD antes e depois da tarefa de implantação.

Se você não tiver o Git configurado, poderá acessar os modelos vinculados por meio do **modelo de ARM de exportação** na lista de modelos do **ARM** .

## <a name="hotfix-production-branch"></a>Branch de produção de hotfix

Se você implantar uma fábrica na produção e perceber que há um bug que precisa ser corrigido imediatamente, mas não é possível implantar o Branch de colaboração atual, talvez seja necessário implantar um hotfix. Essa abordagem é conhecida como engenharia de correção rápida ou QFE.

1.  No Azure DevOps, vá para a versão que foi implantada para produção. Localize a última confirmação implantada.

2.  Na mensagem de confirmação, obtenha a ID de confirmação da ramificação de colaboração.

3.  Crie uma nova ramificação de hotfix a partir dessa confirmação.

4.  Vá para o Azure Data Factory UX e alterne para o Branch de hotfix.

5.  Usando o Azure Data Factory UX, corrija o bug. Teste suas alterações.

6.  Depois que a correção for verificada, selecione **Exportar modelo ARM** para obter o modelo do Gerenciador de recursos de hotfix.

7.  Verifique manualmente essa compilação na ramificação adf_publish.

8.  Se você configurou o pipeline de liberação para disparar automaticamente com base em adf_publish check-ins, uma nova versão será iniciada automaticamente. Caso contrário, enfileirar manualmente uma versão.

9.  Implante a versão do hotfix nas fábricas de teste e produção. Esta versão contém a carga de produção anterior mais a correção que você fez na etapa 5.

10. Adicione as alterações do hotfix para a ramificação de desenvolvimento para que as versões posteriores não incluam o mesmo bug.

## <a name="best-practices-for-cicd"></a>Práticas recomendadas para CI/CD

Se você estiver usando a integração do git com seu data factory e tiver um pipeline de CI/CD que move as alterações do desenvolvimento para o teste e, em seguida, para a produção, recomendamos estas práticas recomendadas:

-   **Integração com o Git**. Você precisa configurar somente seu data factory de desenvolvimento com a integração do git. As alterações no teste e na produção são implantadas por meio de CI/CD e não precisam de integração com o git.

-   **Script data Factory CI/CD**. Antes da etapa de implantação do Gerenciador de recursos no CI/CD, você precisa concluir determinadas tarefas, como parar e reiniciar gatilhos e executar a limpeza. Recomendamos que você use scripts do PowerShell antes e depois da implantação. Para obter mais informações, consulte [Atualizar gatilhos ativos](#update-active-triggers).

-   **Tempos de execução de integração e compartilhamento**. Os tempos de execução de integração não são alterados com frequência e são semelhantes em todos os estágios em seu CI/CD. Portanto Data Factory espera que você tenha o mesmo nome e tipo de tempo de execução de integração em todos os estágios de CI/CD. Se você quiser compartilhar tempos de execução de integração em todos os estágios, considere o uso de uma fábrica ternário apenas para conter os tempos de execução de integração compartilhados. Você pode usar essa fábrica compartilhada em todos os seus ambientes como um tipo de tempo de execução de integração vinculado.

-   **Key Vault**. Ao usar serviços vinculados com base em Azure Key Vault, você pode aproveitar ainda mais os benefícios mantendo os cofres de chaves separados para ambientes diferentes. Você também pode configurar níveis de permissão separados para cada cofre de chaves. Por exemplo, talvez você não queira que os membros da equipe tenham permissões para os segredos de produção. Se você seguir essa abordagem, recomendamos que você mantenha os mesmos nomes de segredo em todos os estágios. Se você mantiver os mesmos nomes, não precisará alterar seus modelos do Resource Manager em ambientes de CI/CD porque a única coisa que muda é o nome do cofre de chaves, que é um dos parâmetros do modelo do Resource Manager.

## <a name="unsupported-features"></a>Recursos sem suporte

- Por design, Data Factory não permite a seleção de Cherry de confirmações ou publicação seletiva de recursos. As publicações incluirão todas as alterações feitas no data factory.

    - As entidades do data Factory dependem umas das outras. Por exemplo, os gatilhos dependem de pipelines e os pipelines dependem de conjuntos de valores e outros pipelines. A publicação seletiva de um subconjunto de recursos pode levar a comportamentos e erros inesperados.
    - Em raras ocasiões em que você precisa de publicação seletiva, considere o uso de um hotfix. Para saber mais, confira [Branch de produção de hotfix](#hotfix-production-branch).

-   Não é possível publicar de branches particulares.

-   Atualmente, não é possível hospedar projetos no bitbucket.
