---
title: Integração contínua e entrega no Azure Data Factory | Documentos da Microsoft
description: Saiba como utilizar a integração contínua e entrega mover pipelines da fábrica de dados de um ambiente (desenvolvimento, teste, produção) para outra.
services: data-factory
documentationcenter: ''
author: gauravmalhot
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: gamal
manager: craigg
ms.openlocfilehash: 5f5a9ef689fefd5683f7b6f1ebc9b2193ce020e4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995782"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Integração contínua e entrega (CI/CD) no Azure Data Factory

Integração contínua é a prática de teste de cada alteração feita à sua base de código automaticamente e mais cedo possível. Entrega contínua segue os testes que acontece durante a integração contínua e envia por push as alterações para um sistema de teste ou produção.

Azure Data Factory, integração contínua e entrega significa mover pipelines do Data Factory de um ambiente (desenvolvimento, teste, produção) para outro. Para fazer a integração contínua e entrega, pode utilizar a integração de IU do Data Factory com modelos Azure Resource Manager. IU do Data Factory pode gerar um modelo do Resource Manager, ao selecionar o **modelo ARM** opções. Quando seleciona **modelo de ARM exportar**, o portal gera o modelo do Resource Manager para a fábrica de dados e um ficheiro de configuração que inclui todas as suas cadeias de caracteres de ligações e outros parâmetros. Em seguida, terá de criar um ficheiro de configuração para cada ambiente (desenvolvimento, teste, produção). O principal arquivo de modelo do Resource Manager permanece o mesmo para todos os ambientes.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para obter uma introdução de nove minutos e demonstração desta funcionalidade, veja o vídeo seguinte:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Criar um modelo do Resource Manager para cada ambiente
Selecione **modelo de ARM exportar** para exportar o modelo do Resource Manager para a fábrica de dados no ambiente de desenvolvimento.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Em seguida, aceda à sua fábrica de dados de teste e a fábrica de dados de produção e selecione **modelo de ARM de importação**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Esta ação leva-o para o portal do Azure, onde é possível importar o modelo exportado. Selecione **criar seu próprio modelo no editor** e, em seguida **carregar ficheiro** e selecione o modelo do Resource Manager gerado. Forneça as definições e a fábrica de dados e todo o pipeline é importado no seu ambiente de produção.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Selecione **carregar ficheiro** para selecionar o modelo do Resource Manager exportado e forneça todos os valores de configuração (por exemplo, serviços ligados).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Cadeias de ligação**. Pode encontrar as informações necessárias para criar cadeias de ligação nos artigos sobre os conectores individuais. Por exemplo, para a base de dados SQL do Azure, consulte [copiar dados de ou para a base de dados do Azure SQL com o Azure Data Factory](connector-azure-sql-database.md). Para verificar a cadeia de ligação correta - para um serviço ligado, por exemplo, - também pode abrir a vista de código para o recurso na IU do Data Factory. No entanto, na vista de código, é removida a palavra-passe ou conta parte da chave a cadeia de ligação. Para abrir a vista de código, selecione o ícone de realçado na captura de ecrã seguinte.

![Vista de código aberto para ver a cadeia de ligação](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Ciclo de vida de integração contínua
Aqui está todo o ciclo de vida para integração contínua e entrega que pode utilizar depois de ativar a integração com o Azure repositórios Git na IU do Data Factory:

1.  Defina uma fábrica de dados de desenvolvimento com repositórios do Azure na qual todos os desenvolvedores podem criar recursos do Data Factory, como conjuntos de dados, pipelines etc.

1.  Em seguida, os desenvolvedores podem modificar recursos, tais como pipelines. À medida que faz a suas modificações, pode selecionar **depurar** para ver como o pipeline é executado com as alterações mais recentes.

1.  Depois dos desenvolvedores estiver satisfeitos com as alterações, podem criar um pedido pull do respetivo ramo para o ramo principal (ou o ramo de colaboração) para obter as alterações revisadas por itens de mesmo nível.

1.  Depois das alterações no ramo principal, podem publicar à fábrica de desenvolvimento, selecionando **publicar**.

1.  Quando a equipe está pronta para promover as alterações para a fábrica de teste e a fábrica de produção, eles podem exportar o modelo do Resource Manager do ramo principal ou a partir de qualquer outra ramificação no caso do desenvolvimento em direto Data Factory cria cópias de segurança de seus ramo principal.

1.  O modelo exportado do Resource Manager pode ser implementado com ficheiros de parâmetros diferentes para a fábrica de teste e a fábrica de produção.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatizar a integração contínua com as versões de Pipelines do Azure

Eis os passos para configurar uma versão de Pipelines do Azure, para que pode automatizar a implementação de uma fábrica de dados em vários ambientes.

![Diagrama da integração contínua com Pipelines do Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Requisitos

-   Uma subscrição do Azure ligada ao Team Foundation Server ou de repositórios do Azure utilizando o [*ponto final de serviço do Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Uma fábrica de dados com a integração de Git de repositórios do Azure configurada.

-   Uma [do Azure Key Vault](https://azure.microsoft.com/services/key-vault/) que contém os segredos.

### <a name="set-up-an-azure-pipelines-release"></a>Configurar uma versão de Pipelines do Azure

1.  Aceda à sua página de repositórios do Azure no mesmo projeto que aquela configurado com a fábrica de dados.

1.  Clique no menu superior **Pipelines do Azure** &gt; **versões** &gt; **Criar definição de versão**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Selecione o **vazio processo** modelo.

1.  Introduza o nome do seu ambiente.

1.  Adicione um artefacto de Git e selecione o mesmo repositório configurado com a fábrica de dados. Escolha `adf_publish` como o ramo predefinido com a versão mais recente do padrão.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Adicione uma tarefa de implementação do Gestor de recursos do Azure:

    a.  Criar uma nova tarefa, procure **implementação de grupo de recursos do Azure**e adicioná-lo.

    b.  A tarefa de implementação, escolha a subscrição, grupo de recursos e localização para a fábrica de dados de destino e fornecer credenciais, se necessário.

    c.  Selecione o **criar ou atualizar grupo de recursos** ação.

    d.  Selecione **...** na **modelo** campo. Navegue para o modelo do Resource Manager (*ARMTemplateForFactory.json*) que foi criada com a ação de publicar no portal. Examinar esse arquivo na pasta `<FactoryName>` do `adf_publish` ramo.

    e.  Fazer a mesma coisa para o ficheiro de parâmetros. Escolha o ficheiro correto, dependendo se criou uma cópia ou estiver a utilizar o ficheiro predefinido *ARMTemplateParametersForFactory.json*.

    f.  Selecione **...** junto a **substituir parâmetros de modelo** campo e preencher as informações para a fábrica de dados de destino. Para as credenciais do Cofre de chaves, utilize o mesmo nome para o segredo no seguinte formato: supondo que o nome do segredo é `cred1`, introduza `"$(cred1)"` (entre aspas).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Selecione o **Incremental** modo de implementação.

    > [!WARNING]
    > Se selecionou **Complete** modo de implementação existente de recursos pode ser eliminado, incluindo todos os recursos no grupo de recursos de destino que não estão definidas no modelo do Resource Manager.

1.  Guarde o pipeline de lançamento.

1.  Crie uma nova versão partir este pipeline de lançamento.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Opcional – obter os segredos no Azure Key Vault

Se tiver de segredos para passar num modelo Azure Resource Manager, recomendamos que utilize o Azure Key Vault com o lançamento de Pipelines do Azure.

Existem duas formas de lidar com os segredos:

1.  Adicione os segredos ao ficheiro de parâmetros. Para mais informações, veja [do Azure com o Key Vault para transmitir o valor do parâmetro segura durante a implementação](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Criar uma cópia do ficheiro de parâmetros que é carregado para o ramo de publicação e defina os valores dos parâmetros que pretende obter a partir do Cofre de chaves com o seguinte formato:

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

    -   Quando utiliza este método, o segredo é obtido a partir do Cofre de chaves automaticamente.

    -   O ficheiro de parâmetros tem de ser no ramo de publicação.

1.  Adicionar uma [tarefas de Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) antes da implantação de Gestor de recursos do Azure, descrito na secção anterior:

    -   Selecione o **tarefas** separador, criar uma nova tarefa, procure **Azure Key Vault** e adicioná-lo.

    -   A tarefa de Key Vault, escolha a subscrição na qual criou o Cofre de chaves, forneça as credenciais se necessário e, em seguida, selecione o Cofre de chaves.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Conceder permissões para o agente de Pipelines do Azure
A tarefa do Azure Key Vault pode falhar a hora de tempo de execução fIntegration com um erro de acesso negado. Transferir os registos para a versão e localize o `.ps1` ficheiro com o comando para atribuir permissões para o agente de Pipelines do Azure. Pode executar o comando diretamente ou pode copiar o ID de principal do ficheiro e adicionar a política de acesso manualmente no portal do Azure. (*Obtenha* e *lista* são as permissões mínimas necessárias).

### <a name="update-active-triggers"></a>Atualizar o Active Directory acionadores
Se tentar atualizar os acionadores de Active Directory, a implementação pode falhar. Para atualizar os acionadores de Active Directory, terá de manualmente pará-los e iniciá-las após a implementação. Pode adicionar uma tarefa do Azure Powershell para essa finalidade, conforme mostrado no exemplo a seguir:

1.  No separador tarefas da versão, procure **do Azure Powershell** e adicioná-lo.

1.  Escolher **do Azure Resource Manager** da ligação escreva e selecione a sua subscrição.

1.  Escolher **o Inline Script de** como o script escreva e, em seguida, fornecer o seu código. O seguinte exemplo para os acionadores:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Pode seguir passos semelhantes e utilizar um código semelhante (com o `Start-AzDataFactoryV2Trigger` função) para reiniciar os disparadores após a implementação.

> [!IMPORTANT]
> Em cenários de implementação e integração contínua, o tipo de Runtime de integração entre ambientes diferentes têm de ser iguais. Por exemplo, se tem um *Autoalojado* Runtime de integração (IR) no ambiente de desenvolvimento, o mesmo Runtime de integração tem de ser do tipo *Autoalojado* em outros ambientes, tais como teste e produção também. Da mesma forma, se estiver a partilhar runtimes de integração em várias fases, tem de configurar os Runtimes de integração como *ligado Autoalojado* em todos os ambientes, como o desenvolvimento, teste e produção.

## <a name="sample-deployment-template"></a>Modelo de implementação de exemplo

Aqui está um modelo de implementação de exemplo que pode ser importado em Pipelines do Azure.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Exemplo de script para parar e reiniciar acionadores e limpar

Eis um script de exemplo para acionadores antes da implantação de parar e reiniciar acionadores posteriormente. O script também inclui código para eliminar os recursos que foram removidos. Para instalar a versão mais recente do Azure PowerShell, consulte [instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Utilizar parâmetros personalizados com o modelo do Resource Manager

Se estiver no modo GIT, pode substituir as propriedades predefinidas no modelo do Resource Manager para definir as propriedades que são parametrizadas no modelo e propriedades que estão hard-coded. Convém substituir o modelo de parametrização de padrão nos seguintes cenários:

* Utilizar o CI/CD automatizada e para alterar algumas propriedades durante a implementação do Resource Manager, mas as propriedades não são parametrizadas por predefinição.
* A fábrica é tão grande que o modelo do Resource Manager padrão é inválido porque tem mais do que o máximo permitido de parâmetros (256).

Nestas condições, para substituir o modelo de parametrização predefinido, crie um ficheiro denominado *arm-modelo-parâmetros-definition.json* na pasta raiz do repositório. O nome do ficheiro deve corresponder exatamente. Fábrica de dados tenta ler este ficheiro a partir de qualquer ramo que se encontra no portal do Azure Data Factory, não apenas do ramo a colaboração. Pode criar ou editar o ficheiro a partir de uma ramificação particular, onde pode testar as suas alterações com o **modelo de ARM exportar** na interface do Usuário. Em seguida, é possível intercalar o ficheiro para o ramo de colaboração. Se nenhum arquivo for encontrado, é utilizado o modelo predefinido.


### <a name="syntax-of-a-custom-parameters-file"></a>Sintaxe de um ficheiro de parâmetros personalizados

Aqui estão algumas diretrizes para utilizar quando cria o ficheiro de parâmetros personalizados. O ficheiro é composta por uma seção para cada tipo de entidade: acionador, pipeline, linkedservice, dataset, integrationruntime e assim por diante.
* Introduza o caminho de propriedade no tipo de entidade relevante.
* Quando define um nome de propriedade para '\*', indica que queira parametrizar todas as propriedades sob a mesma (apenas até o primeiro nível, não recursivamente). Também pode fornecer quaisquer exceções a isso.
* Ao definir o valor de uma propriedade como uma cadeia de caracteres, indica que deseja parametrize a propriedade. Utilize o formato `<action>:<name>:<stype>`.
   *  `<action>` pode ser um dos seguintes carateres:
      * `=` significa manter o valor atual como o valor predefinido para o parâmetro.
      * `-` significa que não tenha o valor predefinido para o parâmetro.
      * `|` é um caso especial para segredos no Azure Key Vault para cadeias de ligação ou chaves.
   * `<name>` é o nome do parâmetro. Se estiver em branco, ele obtém o nome da propriedade. Se o valor começa com um `-` caráter, o nome baixou. Por exemplo, `AzureStorage1_properties_typeProperties_connectionString` seria baixou para `AzureStorage1_connectionString`.
   * `<stype>` é o tipo de parâmetro. Se `<stype>` está em branco, o tipo de padrão é `string`. Valores suportados: `string`, `bool`, `number`, `object`, e `securestring`.
* Quando especificar uma matriz no ficheiro de definição, indica que a propriedade correspondente no modelo é uma matriz. Fábrica de dados repete-se a todos os objetos da matriz, utilizando a definição que está especificada no objeto de Runtime de integração da matriz. O segundo objeto, uma cadeia de caracteres, torna-se o nome da propriedade, que é utilizado como o nome para o parâmetro para cada iteração.
* Não é possível ter uma definição específica para uma instância de recurso. Qualquer definição aplica-se a todos os recursos desse tipo.
* Por predefinição, todas as cadeias de seguras, tais como segredos do Cofre de chaves e cadeias de caracteres de seguras, como cadeias de ligação, as chaves e tokens, são parametrizadas.
 
## <a name="sample-parameterization-template"></a>Modelo de parametrização de exemplo

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

### <a name="explanation"></a>Explicação:

#### <a name="pipelines"></a>Pipelines
    
* Qualquer propriedade no caminho atividades/typeProperties/waitTimeInSeconds é parametrizada. Isso significa que todas as atividades num pipeline que possui uma propriedade de nível de código chamada `waitTimeInSeconds` (por exemplo, o `Wait` atividade) é parametrizado como um número, com um nome predefinido. No entanto, ele não terá um valor predefinido no modelo do Resource Manager. É uma entrada obrigatória durante a implementação do Resource Manager.
* Da mesma forma, uma propriedade denominada `headers` (por exemplo, num `Web` atividade) é parametrizada com tipo `object` (JObject). Ele tem um valor predefinido, que é o mesmo valor no fábrica de origem.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Apenas as propriedades e todas as propriedades, no caminho `typeProperties` parametrizados, com os valores predefinidos respectivos. Por exemplo, a partir do esquema de hoje, existem duas propriedades sob **IntegrationRuntimes** as propriedades de tipo: `computeProperties` e `ssisProperties`. Ambos os tipos de propriedade são criados com seus valores padrão respectivos e tipos (objeto).

#### <a name="triggers"></a>Acionadores

* Em `typeProperties`, duas propriedades são parametrizadas. A primeira é `maxConcurrency`, que é especificado para ter um valor predefinido e o tipo seria `string`. Tem o nome de parâmetro padrão de `<entityName>_properties_typeProperties_maxConcurrency`.
* O `recurrence` propriedade também é parametrizada. Sob a mesma, todas as propriedades nesse nível são especificadas para ser parametrizado como cadeias de caracteres, com valores padrão e os nomes de parâmetros. Uma exceção é o `interval` propriedade, que é parametrizada como tipo de número e com o nome do parâmetro com o sufixo `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Da mesma forma, o `freq` propriedade é uma cadeia de caracteres e é parametrizada como uma cadeia de caracteres. No entanto, o `freq` propriedade é parametrizada sem um valor predefinido. O nome é baixou e o sufixo. Por exemplo, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Serviços ligados é exclusivo. Como os serviços ligados e conjuntos de dados potencialmente podem ser de vários tipos, pode fornecer específicos do tipo de personalização. Por exemplo, pode dizer que para todos os serviços do tipo ligados `AzureDataLakeStore`, um modelo específico será aplicada e para todos os outros (via \*) será aplicado a um modelo diferente.
* No exemplo anterior, o `connectionString` propriedade irá ser parametrizada como uma `securestring` valor, ele não terá um valor predefinido, e ele terá um nome abreviado de parâmetro que é o sufixo com `connectionString`.
* A propriedade `secretAccessKey`, no entanto, vem a ser um `AzureKeyVaultSecret` (por exemplo, um `AmazonS3` serviço ligado). Assim, ele é parametrizado automaticamente como um segredo do Cofre de chaves do Azure e é obtido a partir do Cofre de chaves que está configurado com na fábrica de origem. Pode também parametrizar o Cofre de chaves, em si.

#### <a name="datasets"></a>Conjuntos de dados

* Apesar de personalização de específicos do tipo está disponível para conjuntos de dados, a configuração pode ser fornecida sem que seja explicitamente necessário um \*-configuração do nível. No exemplo anterior, todas as propriedades do conjunto de dados em `typeProperties` são parametrizada.

Pode alterar o modelo de parametrização padrão, mas este é o modelo atual. Isso será útil se só precisa de adicionar uma propriedade adicional como um parâmetro, mas também se não quiser perder os parameterizations existentes e tem de voltar a criá-los.


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

**Exemplo**: Adicione um ID de cluster interativos do Databricks (a partir de um serviço ligado do Databricks) para o ficheiro de parâmetros:

```
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


## <a name="linked-resource-manager-templates"></a>Modelos do Gestor de recursos ligados

Se tiver configurado a integração contínua e implementação (CI/CD) para as suas fábricas de dados, pode observar que, à medida que sua fábrica cresce maior, se deparar com os limites de modelo do Resource Manager, como o número máximo de recursos ou o máximo payload num recurso Modelo do gestor. Para cenários como esses, juntamente com a gerar o modelo do Resource Manager completo para uma fábrica, o Data Factory gera agora também modelos ligados do Resource Manager. Como resultado, tem o payload de toda a fábrica dividido em vários arquivos, para que não se deparar com os limites mencionados.

Se tiver configurado do Git, os modelos ligados são gerados e guardados juntamente com os modelos do Resource Manager completo, na `adf_publish` ramo, numa nova pasta chamada `linkedTemplates`.

![Pasta de modelos do Resource Manager ligado](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Os modelos do Gestor de recursos ligado normalmente têm um modelo global e um conjunto de modelos de subordinados vinculado ao principal. O modelo de principal é chamado `ArmTemplate_master.json`, e modelos de subordinados são nomeados com o padrão `ArmTemplate_0.json`, `ArmTemplate_1.json`e assim por diante. Para mover-se através da utilização do modelo do Resource Manager completo para utilizar os modelos ligados, atualizar a tarefa de CI/CD para que apontem para `ArmTemplate_master.json` em vez de apontar para `ArmTemplateForFactory.json` (ou seja, o modelo do Resource Manager completo). Gestor de recursos também exige que carregar os modelos ligados para uma conta de armazenamento para que possam ser acedidos pelo Azure durante a implementação. Para mais informações, veja [implementar modelos de ARM ligados com o VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Não se esqueça de adicionar os scripts de fábrica de dados no seu pipeline de CI/CD antes e depois da tarefa de implementação.

Se não tiver configurado do Git, os modelos ligados são acessíveis através da **modelo de ARM exportar** gesto.

## <a name="best-practices-for-cicd"></a>Melhores práticas para CI/CD

Se estiver a utilizar a integração de Git com sua fábrica de dados e que tem um pipeline de CI/CD que move as alterações de desenvolvimento em teste e, em seguida, para a produção, recomendamos as seguintes práticas recomendadas:

-   **Integração de Git**. Só tem de configurar a fábrica de dados de desenvolvimento com a integração de Git. As alterações para teste e produção são implementadas por meio de CI/CD e não precisam de integração de Git.

-   **Script de CI/CD da fábrica de dados**. Antes do passo de implementação do Resource Manager no CI/CD, deve ter cuidado de coisas como parar os acionadores e um tipo diferente de limpeza de fábrica. Recomendamos que utilize [este script](#sample-script-to-stop-and-restart-triggers-and-clean-up) como cuida de todas essas coisas. Execute o script, uma vez antes da implantação e uma vez depois, utilizando sinalizadores apropriados.

-   **Runtimes de integração e partilha**. Runtimes de integração são um dos componentes infraestrutura na sua fábrica de dados, que passar por alterações menos vezes e são semelhantes em todas as fases no seu CI/CD. Como resultado, o Data Factory espera que tem o mesmo nome e o mesmo tipo de Runtimes de integração em todas as fases de CI/CD. Se quiser para partilhar Runtimes de integração em todas as fases - por exemplo, os Runtimes de integração autoalojado, uma forma de compartilhar é o IR autoalojado numa fábrica ternária, apenas para que contém os Runtimes de integração compartilhado de hospedagem. Em seguida, pode utilizá-los Dev/teste/produção como um tipo de Runtime de integração ligado.

-   **Cofre de chaves**. Quando utiliza o recomendado Azure Key Vault com base em serviços ligados, pode demorar um ainda mais seu nível vantagens ao potencialmente manter cofres de chaves separados para Dev/teste/Prod. Também pode configurar níveis de permissão separado para cada uma delas. Pode não desejar os membros da Equipe tenham permissões para os segredos de produção. Também recomendamos que mantenha os mesmos nomes secretos em todas as fases. Se mantiver os mesmos nomes, não precisa de alterar os modelos do Resource Manager em CI/CD, uma vez que a única coisa que precisa ser alterado é o nome do Cofre de chaves, o que é um dos parâmetros de modelo do Resource Manager.

## <a name="unsupported-features"></a>Funcionalidades não suportadas

-   Não é possível publicar recursos individuais, porque as entidades do data factory são dependentes entre si. Por exemplo, os acionadores dependem de pipelines, pipelines dependem de conjuntos de dados e outros pipelines, etc. Dependências de alteração de controlo é difícil. Caso tenha sido possível selecionar os recursos para publicar manualmente, seria possível selecionar apenas um subconjunto de todo o conjunto de alterações, o que poderia levar a um comportamento inesperado coisas após a publicação.

-   Não é possível publicar de ramos privados.

-   Não é possível hospedar projetos no Bitbucket.
