---
title: Use o cheque de saúde do Gestor de Implementação Azure
description: Utilize o controlo de saúde para implementar com segurança os recursos da Azure com o Gestor de Implementação da Azure.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3c7b74d31bc3c4e2276cd52c8e6450630dc99bcd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86058032"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Tutorial: Use o cheque de saúde no Azure Deployment Manager (visualização pública)

Saiba como integrar o controlo de saúde no [Azure Deployment Manager](./deployment-manager-overview.md). Este tutorial é baseado no Gestor de Implementação do [Uso Azure com o tutorial de modelos de gestor de recursos.](./deployment-manager-tutorial.md) Tens de completar esse tutorial antes de prosseguires com este.

No modelo de lançamento utilizado no [Use Azure Deployment Manager com modelos de Gestor de Recursos,](./deployment-manager-tutorial.md)utilizou um passo de espera. Neste tutorial, substitui-se o passo de espera por um passo de verificação de saúde.

> [!IMPORTANT]
> Se a sua subscrição estiver marcada para o Canário para testar novas funcionalidades do Azure, só pode utilizar o Azure Deployment Manager para implantar nas regiões canárias. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um simulador de serviço de verificação de saúde
> * Rever o modelo de lançamento
> * Implementar a topologia
> * Implementar o lançamento com um estado pouco saudável
> * Verifique a implementação do lançamento
> * Implementar o lançamento com um estado saudável
> * Verifique a implementação do lançamento
> * Limpar os recursos

Recursos adicionais:

* Referência [Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Uma amostra do Gestor de Implantação Azure](https://github.com/Azure-Samples/adm-quickstart).

Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Utilização completa [Do Gestor de Implementação Azure com modelos de Gestor de Recursos](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>Instalar os artefactos

Descarregue [os modelos e os artefactos](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) e desaperte-os localmente se não o tiver feito. E, em seguida, executar o script PowerShell encontrado na [Prepare os artefactos](./deployment-manager-tutorial.md#prepare-the-artifacts). O script cria um grupo de recursos, cria um recipiente de armazenamento, cria um recipiente blob, carrega os ficheiros descarregados e, em seguida, cria um token SAS.

Faça uma cópia do URL com ficha SAS. Este URL é necessário para preencher um campo nos dois ficheiros de parâmetros, os ficheiros parâmetros da topologia e parâmetros do lançamento.

Abra CreateADMServiceTopology.Parameters.jse atualize os valores do **projectName** e **artefactoSourceSASLocation**.

Abra CreateADMRollout.Parameters.jse atualize os valores do **projectName** e **artefactoSourceSASLocation**.

## <a name="create-a-health-check-service-simulator"></a>Criar um simulador de serviço de verificação de saúde

Na produção, normalmente utiliza-se um ou mais fornecedores de monitorização. De forma a facilitar a integração na saúde, a Microsoft tem vindo a trabalhar com algumas das principais empresas de monitorização de saúde de serviços para lhe fornecer uma solução simples de cópia/pasta para integrar os controlos de saúde com as suas implementações. Para obter uma lista destas empresas, consulte [os prestadores de monitorização da saúde.](./deployment-manager-health-check.md#health-monitoring-providers) Para efeitos deste tutorial, cria uma [Função Azure](../../azure-functions/index.yml) para simular um serviço de monitorização de saúde. Esta função requer um código de estado e devolve o mesmo código. O seu modelo de Gestor de Implementação Azure utiliza o código de estado para determinar como proceder com a implementação.

Os dois ficheiros seguintes são utilizados para a implantação da Função Azure. Não é preciso descarregar estes ficheiros para ver o tutorial.

* Um modelo de Gestor de Recursos localizado em [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json) . Você implementa este modelo para criar uma Função Azure.
* Um ficheiro postal do código-fonte da Função Azure, [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip) . Este zip chamado é chamado pelo modelo de Gestor de Recursos.

Para implantar a função Azure, selecione **Experimente-a** para abrir a casca da Nuvem Azure e, em seguida, cole o seguinte script na janela do invólucro.  Para colar o código, clique com o botão direito na janela da concha e, em seguida, **selecione Pasta**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Para verificar e testar a função Azure:

1. Abra o [portal do Azure](https://portal.azure.com).
1. Abra o grupo de recursos.  O nome padrão é o nome do projeto com **rg** anexado.
1. Selecione o serviço de aplicações do grupo de recursos.  O nome padrão do serviço de aplicações é o nome do projeto com **webapp** anexado.
1. Expandir **funções**e, em seguida, selecionar **HttpTrigger1**.

    ![Azure Deployment Manager verifica saúde Azure Function](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selecione ** &lt; /> Obtenha URL de função**.
1. Selecione **Copy** para copiar o URL para a área de transferência.  O URL é semelhante a:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Substitua `{healthStatus}` no URL por um código de estado. Neste tutorial, use **insalubre** para testar o cenário pouco saudável, e use **saudável** ou **alerta** para testar o cenário saudável. Crie dois URLs, um com o estado pouco saudável, e o outro com estatuto saudável. Por exemplo:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Precisa de ambos os URLs para completar este tutorial.

1. Para testar o simulador de monitorização de saúde, abra os URLs que criou no último passo.  Os resultados do estado de saúde não saudáveis são semelhantes:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Rever o modelo de lançamento

O objetivo desta secção é mostrar-lhe como incluir um passo de verificação de saúde no modelo de lançamento.

1. Abra **CreateADMRollout.jssobre** o que criou no Use [Azure Deployment Manager com modelos de Gestor de Recursos](./deployment-manager-tutorial.md). Este ficheiro JSON faz parte do download.  Veja [Pré-requisitos](#prerequisites).
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

1. Substitua a definição de recursos do passo de espera por uma definição de recursos de passo de verificação de saúde:

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

    Com base na definição, o lançamento prossegue se o estado de saúde for *saudável* ou *avisado.*

1. Atualizar a **parte dependente** da definição de lançamento para incluir o novo passo de verificação de saúde definido:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Atualizar **stepGroups** para incluir o passo de verificação de saúde. O **healthCheckStep** é chamado em **postDeploymentSteps** do **stepGroup2**. **o stepGroup3** e **o stepGroup4** só são implantados se o estado saudável for *saudável* ou *avisado*.

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

    Se comparar a secção **stepGroup3** antes e depois de revista, esta secção depende agora do **passoGroup2**.  Isto é necessário quando o **grupo de intervenção** e os grupos de passo subsequente dependem dos resultados da monitorização da saúde.

    A imagem que se segue ilustra as áreas modificadas e a forma como é utilizado o passo de verificação da saúde:

    ![Modelo de verificação de saúde do Gestor de Implementação de Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Implementar a topologia

Executar o seguinte script PowerShell para implantar a topologia. Você precisa do mesmo **CreateADMServiceTopology.jse** **CreateADMServiceTopology.Parameters.jsem** que usou no [Use Azure Deployment Manager com modelos de Gestor de Recursos](./deployment-manager-tutorial.md).

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

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Implementar o lançamento com o estado pouco saudável

Utilize o URL de estado pouco saudável que criou na [Criar um simulador de serviço de verificação de saúde](#create-a-health-check-service-simulator). Você precisa doCreateADMServiceTopology.jsrevisto ** e** o mesmo **CreateADMServiceTopology.Parameters.jsem** que usou no Use [Azure Deployment Manager com modelos de Gestor de Recursos](./deployment-manager-tutorial.md).

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
> `New-AzResourceGroupDeployment` é uma chamada assíncronea. A mensagem de sucesso significa apenas que a implantação começou com sucesso. Para verificar a implantação, utilize `Get-AZDeploymentManagerRollout` .  Veja o próximo procedimento.

Para verificar o progresso do lançamento utilizando o seguinte script PowerShell:

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

A seguinte saída da amostra mostra que a implantação falhou devido ao estado pouco saudável:

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

Após o lançamento estar concluído, verá um grupo de recursos adicional criado para os EUA Ocidentais.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Implementar o lançamento com o estado saudável

Repita esta secção para recolocar o lançamento com o URL de estado saudável.  Após o lançamento estar concluído, verá mais um grupo de recursos criado para os EUA Orientais.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Abra o [portal do Azure](https://portal.azure.com).
2. Navegue para as aplicações Web criadas recentemente, nos grupos de recursos novos que foram criados com a implementação do lançamento.
3. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro index.html.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione Grupo de **Recursos** do menu esquerdo.
2. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial. Deverá haver entre 3 a 4.

    * ** &lt; projectName>rg**: contém os recursos do Gestor de Implantação.
    * ** &lt; projectName>ServiceWUSrg:** contém os recursos definidos pelo ServiceWUS.
    * ** &lt; projectName>ServiceEUSrg:** contém os recursos definidos pelo ServiceEUS.
    * O grupo de recursos da identidade gerida atribuída pelo utilizador.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar o grupo** de recursos do menu superior.
5. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar a funcionalidade de verificação de saúde do Azure Deployment Manager. Para saber mais, veja a [Documentação do Azure Resource Manager](../index.yml).
