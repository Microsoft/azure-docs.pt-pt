---
title: Utilizar o Gestor de Implementações do Azure com modelos do Resource Manager | Microsoft Docs
description: Utilize modelos do Resource Manager com o Gestor de Implementações do Azure para implementar recursos do Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/06/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 4f3f43ba063ed25389860183576c4dbd94e2bfcd
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466329"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Tutorial: Utilizar a verificação de estado de funcionamento no Azure Deployment Manager (pré-visualização pública)

Saiba como integrar a verificação de estado de funcionamento no [Gestor de implementação do Azure](./deployment-manager-overview.md). Este tutorial baseia-se do [utilize o Gestor de implementação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md) tutorial. Tem de concluir esse tutorial antes de continuar com este.

Em que o modelo de implementação utilizado [utilize o Gestor de implementação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md), utilizou um passo de espera. Neste tutorial, é possível substituir a etapa de espera com um passo de verificação de estado de funcionamento.

> [!IMPORTANT]
> Se a sua subscrição está marcada para Canárias testar novas funcionalidades do Azure, só pode utilizar o Gestor de implementação do Azure para implementar para as regiões Canary. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um simulador de serviço de verificação de estado de funcionamento
> * Rever o modelo de implementação
> * Implementar a topologia
> * Implementar a implementação com o estado de mau estado de funcionamento
> * Verificar a implementação de implementação
> * Implementar a implementação com o estado de funcionamento correto
> * Verificar a implementação de implementação
> * Limpar recursos

A referência de API de REST do Gestor de implementação do Azure pode ser encontrada [aqui](https://docs.microsoft.com/rest/api/deploymentmanager/).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Concluída [utilize o Gestor de implementação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md).
* Baixe [os modelos e os artefactos](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) que é utilizado por este tutorial. 

## <a name="create-a-health-check-service-simulator"></a>Criar um simulador de serviço de verificação de estado de funcionamento

Na produção, geralmente usa um ou mais fornecedores de monitorização. Para fazer a integração de estado de funcionamento mais fácil possível, Microsoft tem trabalhado com algumas da empresas para lhe fornecer uma solução simples de copiar/colar para integrar as verificações de estado de funcionamento com as suas implementações de monitoramento de integridade de principais de serviço. Para obter uma lista destas empresas, consulte [fornecedores de monitorização de estado de funcionamento](./deployment-manager-health-check.md#health-monitoring-providers). Para efeitos deste tutorial, vai criar uma [função do Azure](/azure/azure-functions/) para simular um Estado de funcionamento do serviço de monitorização. Esta função aceita um código de estado e retorna o mesmo código. O modelo de Gestor de implementação do Azure utiliza o código de status para determinar como proceder com a implementação. 

Os seguintes dois ficheiros são utilizados para implementar a função do Azure. Não precisa de transferir estes ficheiros para seguir o tutorial.

* Um modelo do Resource Manager, localizado em [ https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json ](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Implementar este modelo para criar uma função do Azure.  
* Um ficheiro zip do código-fonte função do Azure, [ https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip ](https://armtutorials.blob.core.windows.net/admtutorial/RestHealthTest.zip). Este zip chamado é chamada pelo modelo do Resource Manager.

Para implementar a função do Azure, selecione **experimente** para abrir o Azure Cloud shell e, em seguida, cole o seguinte script para a janela do shell.  Colar o código, com o botão direito da janela do shell e, em seguida, selecione **colar**. 

> [!IMPORTANT]
> **projectName** no PowerShell script é utilizado para gerar nomes para os serviços do Azure que são implementados neste tutorial. Diferentes serviços do Azure têm requisitos diferentes nos nomes. Para garantir que a implementação for bem sucedida, escolha um nome com menos de 12 carateres com apenas letras minúsculas e números.
> Guarde uma cópia do nome do projeto. Utilize o mesmo projectName o tutorial.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Para verificar e testar a função do Azure:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos.  O nome predefinido é o nome do projeto com **rg** anexado.
1. Selecione o serviço de aplicações do grupo de recursos.  O nome predefinido do serviço de aplicações é o nome de projeto com **webapp** anexado.
1. Expanda **funções**e, em seguida, selecione **HttpTrigger1**. 

    ![Verificação de estado de funcionamento do Gestor de implementação do Azure função do Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selecione  **&lt;/ > obter URL de função**.
1. Selecione **cópia** para copiar o URL para a área de transferência.  O URL é semelhante a:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Substitua `{healthStatus}` no URL com um código de estado. Neste tutorial, utilize **mau estado de funcionamento** para testar o cenário de mau estado de funcionamento e a utilização de uma **bom estado de funcionamento** ou **aviso** para testar o cenário de bom estado de funcionamento. Crie dois URLs, um com o estado de mau estado de funcionamento e a outra com o estado de funcionamento correto. Para obter exemplos:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Terá de ambos os URLs para concluir este tutorial.

1. Para testar o simulador de monitorização de estado de funcionamento, abra os URLs que criou no último passo.  Os resultados para o estado de mau estado de funcionamento deverá ser semelhantes a:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Rever o modelo de implementação

O objetivo desta secção é mostrar como incluem um passo de verificação de estado de funcionamento no modelo de implementação. Não precisa de criar seu próprio ficheiro CreateADMRollout.json para concluir este tutorial. O modelo de implementação revisado é partilhado na conta de armazenamento que é utilizada nas secções subsequentes.

1. Open **CreateADMRollout.json**. Este ficheiro JSON é uma parte do download.  Veja [Pré-requisitos](#prerequisites).
1. Adicione dois parâmetros adicionais:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Substitua a definição de recurso de passo de espera de uma definição de recurso de passo de verificação de estado de funcionamento:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Com base na definição, a implementação continua se o estado de funcionamento *bom estado de funcionamento* ou *aviso*. 

1. Atualização do **dependsON** da definição de implementação para incluir o passo de verificação de estado de funcionamento definidas recentemente:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Atualização **stepGroups** para incluir o passo de verificação de estado de funcionamento. O **healthCheckStep** denomina-se **postDeploymentSteps** de **stepGroup2**. **stepGroup3** e **stepGroup4** são implementadas apenas se o estado de funcionamento correto *bom estado de funcionamento* ou *aviso*. 

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Se comparar o **stepGroup3** secção antes e depois é revisto, esta secção agora depende **stepGroup2**.  Isso é necessário quando **stepGroup3** e os grupos de passo seguinte dependem dos resultados de monitorização de estado de funcionamento.

    Captura de ecrã seguinte ilustra as áreas modificadas e, como o passo de verificação de estado de funcionamento é usado:

    ![Modelo de verificação de estado de funcionamento de Gestor de implementação do Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Implementar a topologia

Para simplificar o tutorial, o modelo de topologia e os artefactos são compartilhados nas seguintes localizações para que não precisa de preparar a sua própria cópia. Se quiser usar sua própria, siga as instruções em [Tutorial: Utilizar o Gestor de implementação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md).

* Modelo de topologia: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Armazenamento de artefactos: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Para implementar a topologia, selecione **experimente** para abrir o Cloud shell e, em seguida, cole o script do PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Utilize o portal do Azure para confirmar que a topologia de serviço e os recursos sublinhados foram criados com êxito:

![Recursos de topologia de serviço implementados do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Para ver os recursos, **Mostrar tipos ocultos** tem de estar selecionado.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Implementar a implementação com o estado de mau estado de funcionamento

Para simplificar o tutorial, o modelo de implementação revisado é partilhado na seguinte localização para que não precisa de preparar a sua própria cópia. Se quiser usar sua própria, siga as instruções em [Tutorial: Utilizar o Gestor de implementação do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md).

* Modelo de topologia: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Armazenamento de artefactos: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Utilize o URL de estado de mau estado de funcionamento que criou no [criar um simulador de serviço de verificação de estado de funcionamento](#create-a-health-check-service-simulator). Para **managedIdentityID**, consulte [criar a identidade gerida atribuído ao utilizador](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` é uma chamada assíncrona. O sucesso da mensagem apenas significa que a implementação foi iniciada com êxito. Para verificar a implementação, utilize `Get-AZDeploymentManagerRollout`.  Consulte o procedimento seguinte.

Para verificar o progresso da implementação com o seguinte script do PowerShell:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

O resultado de exemplo seguinte mostra a implementação falhou devido ao estado de mau estado de funcionamento:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Depois de concluída a implementação, deverá ver um grupo de recursos adicionais criado para E.U.A. oeste.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Implementar a implementação com o estado de funcionamento correto

Repita esta secção para implementar novamente a implementação com o URL de estado de funcionamento correto.  Depois de concluída a implementação, deverá ver um grupo de recursos mais criado nos e.u.a. Leste.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Navegue para as aplicações Web criadas recentemente, nos grupos de recursos novos que foram criados com a implementação do lançamento.
3. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro index.html.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial. Deverá haver entre 3 a 4.

    * **&lt;namePrefix>rg**: contém os recursos do Gestor de Implementações.
    * **&lt;namePrefix>ServiceWUSrg**: contém os recursos definidos por ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: contém os recursos definidos por ServiceEUS.
    * O grupo de recursos da identidade gerida atribuída pelo utilizador.
3. Selecione o nome do grupo de recursos.  
4. Selecione **Eliminar grupo de recursos** no menu superior.
5. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu a utilizar a funcionalidade de verificação de estado de funcionamento do Gestor de implementação do Azure. Para saber mais, veja a [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
