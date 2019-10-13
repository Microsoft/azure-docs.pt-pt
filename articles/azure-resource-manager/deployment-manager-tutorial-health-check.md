---
title: Utilizar o Gestor de Implementações do Azure com modelos do Resource Manager | Microsoft Docs
description: Utilize modelos do Resource Manager com o Gestor de Implementações do Azure para implementar recursos do Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b381c4be5d0c56e14ccd01657542ef3bff2f8894
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285679"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Tutorial: usar a verificação de integridade no Azure Deployment Manager (visualização pública)

Saiba como integrar a verificação de integridade no [Azure Deployment Manager](./deployment-manager-overview.md). Este tutorial é baseado no tutorial [usar o Deployment Manager do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md) . Você deve concluir esse tutorial antes de prosseguir com este.

No modelo de distribuição usado em [usar o Deployment Manager do Azure com modelos do Resource Manager](./deployment-manager-tutorial.md), você usou uma etapa de espera. Neste tutorial, você substitui a etapa de espera por uma etapa de verificação de integridade.

> [!IMPORTANT]
> Se sua assinatura estiver marcada para canário para testar novos recursos do Azure, você só poderá usar o Deployment Manager do Azure para implantar nas regiões do canário. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um simulador de serviço de verificação de integridade
> * Revisar o modelo de distribuição
> * Implantar a topologia
> * Implantar a distribuição com status não íntegro
> * Verificar a implantação da distribuição
> * Implantar a distribuição com status Íntegro
> * Verificar a implantação da distribuição
> * Limpar recursos

Recursos adicionais:

* A [referência da API REST do Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Um exemplo de Deployment Manager do Azure](https://github.com/Azure-Samples/adm-quickstart).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Conclua o [uso do Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Instalar os artefatos

Baixe [os modelos e os artefatos](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) e descompacte-os localmente se você ainda não fez isso. Em seguida, execute o script do PowerShell encontrado em [preparar os artefatos](./deployment-manager-tutorial.md#prepare-the-artifacts). O script cria um grupo de recursos, cria um contêiner de armazenamento, cria um contêiner de BLOBs, carrega os arquivos baixados e, em seguida, cria um token SAS.

Faça uma cópia da URL com o token SAS. Essa URL é necessária para preencher um campo nos dois arquivos de parâmetro, arquivos de parâmetros de topologia e arquivo de parâmetros de distribuição.

Abra CreateADMServiceTopology. Parameters. JSON e atualize os valores de **projectName** e **artifactSourceSASLocation**.

Abra CreateADMRollout. Parameters. JSON e atualize os valores de **projectName** e **artifactSourceSASLocation**.

## <a name="create-a-health-check-service-simulator"></a>Criar um simulador de serviço de verificação de integridade

Em produção, normalmente você usa um ou mais provedores de monitoramento. Para tornar a integração de integridade o mais fácil possível, a Microsoft tem trabalhado com algumas das principais empresas de monitoramento de integridade do serviço para fornecer uma solução simples de copiar/colar para integrar verificações de integridade às suas implantações. Para obter uma lista dessas empresas, consulte [provedores de monitoramento de integridade](./deployment-manager-health-check.md#health-monitoring-providers). Para fins deste tutorial, você cria uma [função do Azure](/azure/azure-functions/) para simular um serviço de monitoramento de integridade. Essa função usa um código de status e retorna o mesmo código. Seu modelo de Deployment Manager do Azure usa o código de status para determinar como prosseguir com a implantação.

Os dois arquivos a seguir são usados para implantar a função do Azure. Você não precisa baixar esses arquivos para passar pelo tutorial.

* Um modelo do Resource Manager localizado em [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json). Você implanta esse modelo para criar uma função do Azure.
* Um arquivo zip do código-fonte da função do Azure, [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip). Esse zip chamado é chamado pelo modelo do Resource Manager.

Para implantar a função do Azure, selecione **Experimente** para abrir o Azure cloud Shell e cole o script a seguir na janela do Shell.  Para colar o código, clique com o botão direito do mouse na janela do Shell e selecione **colar**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Para verificar e testar a função do Azure:

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos.  O nome padrão é o nome do projeto com **RG** acrescentado.
1. Selecione o serviço de aplicativo no grupo de recursos.  O nome padrão do serviço de aplicativo é o nome do projeto com **webapp** anexado.
1. Expanda **funções**e, em seguida, selecione **HttpTrigger1**.

    ![Azure Deployment Manager Health Check do Azure function](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selecione **&lt;/> URL de função Get**.
1. Selecione **copiar** para copiar a URL para a área de transferência.  A URL é semelhante a:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Substitua `{healthStatus}` na URL por um código de status. Neste tutorial, use não **íntegro** para testar o cenário não íntegro e use **íntegro** ou **aviso** para testar o cenário íntegro. Crie duas URLs, uma com o status não íntegro e a outra com status Íntegro. Para obter exemplos:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Você precisa de ambas as URLs para concluir este tutorial.

1. Para testar o simulador de monitoramento de integridade, abra as URLs que você criou na última etapa.  Os resultados para o status não íntegro devem ser semelhantes a:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Revisar o modelo de distribuição

A finalidade desta seção é mostrar como incluir uma etapa de verificação de integridade no modelo de distribuição.

1. Abra o **CreateADMRollout. JSON** que você criou em [usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md). Esse arquivo JSON faz parte do download.  Veja [Pré-requisitos](#prerequisites).
1. Adicione mais dois parâmetros:

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

1. Substitua a definição de recurso da etapa de espera por uma definição de recurso da etapa de verificação de integridade:

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

    Com base na definição, a distribuição continuará se o status de integridade for *íntegro* ou *aviso*.

1. Atualize o **depende** da definição de distribuição para incluir a etapa de verificação de integridade definida recentemente:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Atualize **stepGroups** para incluir a etapa de verificação de integridade. O **healthCheckStep** é chamado em **postDeploymentSteps** de **stepGroup2**. **stepGroup3** e **stepGroup4** serão implantados somente se o status Íntegro for *íntegro* ou *aviso*.

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

    Se você comparar a seção **stepGroup3** antes e depois que ela for revisada, esta seção agora dependerá de **stepGroup2**.  Isso é necessário quando o **stepGroup3** e os grupos de etapas subsequentes dependem dos resultados do monitoramento de integridade.

    A captura de tela a seguir ilustra as áreas modificadas e como a etapa de verificação de integridade é usada:

    ![Modelo de verificação de integridade do Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Implantar a topologia

Execute o seguinte script do PowerShell para implantar a topologia. Você precisa do mesmo **CreateADMServiceTopology. JSON** e **CreateADMServiceTopology. Parameters. JSON** que usou em [usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Utilize o portal do Azure para confirmar que a topologia de serviço e os recursos sublinhados foram criados com êxito:

![Recursos de topologia de serviço implementados do tutorial Gestor de Implementações do Azure](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Para ver os recursos, **Mostrar tipos ocultos** tem de estar selecionado.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Implantar a distribuição com o status não íntegro

Use a URL de status não íntegra que você criou em [criar um simulador de serviço de verificação de integridade](#create-a-health-check-service-simulator). Você precisa do **CreateADMServiceTopology. JSON** revisado e do mesmo **CreateADMServiceTopology. Parameters. JSON** que usou em [usar o Azure Deployment Manager com modelos do Resource Manager](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` é uma chamada assíncrona. A mensagem de êxito só significa que a implantação foi iniciada com êxito. Para verificar a implantação, use `Get-AZDeploymentManagerRollout`.  Consulte o próximo procedimento.

Para verificar o progresso da distribuição usando o seguinte script do PowerShell:

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

A seguinte saída de exemplo mostra que a implantação falhou devido ao status não íntegro:

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

Após a conclusão da distribuição, você deverá ver um grupo de recursos adicional criado para o oeste dos EUA.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Implantar a distribuição com o status Íntegro

Repita esta seção para reimplantar a distribuição com a URL de status Íntegro.  Após a conclusão da distribuição, você deverá ver mais um grupo de recursos criado para o leste dos EUA.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Abra o [Portal do Azure](https://portal.azure.com).
2. Navegue para as aplicações Web criadas recentemente, nos grupos de recursos novos que foram criados com a implementação do lançamento.
3. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro index.html.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. No portal do Azure, selecione **Grupo de recursos** no menu à esquerda.
2. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial. Deverá haver entre 3 a 4.

    * **&lt;projectName > RG**: contém os recursos de Deployment Manager.
    * **&lt;projectName > ServiceWUSrg**: contém os recursos definidos por ServiceWUS.
    * **&lt;projectName > ServiceEUSrg**: contém os recursos definidos por ServiceEUS.
    * O grupo de recursos da identidade gerida atribuída pelo utilizador.
3. Selecione o nome do grupo de recursos.
4. Selecione **Eliminar grupo de recursos** no menu superior.
5. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu a usar o recurso de verificação de integridade do Azure Deployment Manager. Para saber mais, veja a [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
