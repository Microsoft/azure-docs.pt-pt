---
title: Use o check de saúde do Gestor de Destacamento Azure
description: Utilize um exame de saúde para mobilizar com segurança os recursos azure com o Gestor de Implantação azure.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 765c73a3ab8d5fa8939abe597d0141b24b59ac52
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76152482"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Tutorial: Utilize o exame de saúde no Gestor de Implantação do Azure (pré-visualização pública)

Saiba como integrar o health check no [Azure Deployment Manager](./deployment-manager-overview.md). Este tutorial baseia-se no Gestor de Implementação do [Use Azure com modelos](./deployment-manager-tutorial.md) de gestor de recursos tutorial. Tens de completar esse tutorial antes de avançares com este.

No modelo de lançamento utilizado no [Use Azure Deployment Manager com modelos](./deployment-manager-tutorial.md)de Gestor de Recursos, usou um passo de espera. Neste tutorial, substitui-se o passo de espera por um passo de verificação de saúde.

> [!IMPORTANT]
> Se a sua subscrição estiver marcada para a Canária testar novas funcionalidades do Azure, só pode utilizar o Gestor de Implantação Azure para se deslocar para as regiões canárias. 

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Criar um simulador de serviço de verificação de saúde
> * Reveja o modelo de lançamento
> * Implementar a topologia
> * Implementar o lançamento com um estado pouco saudável
> * Verifique a implantação do lançamento
> * Implementar o lançamento com um estado saudável
> * Verifique a implantação do lançamento
> * Limpar recursos

Recursos adicionais:

* Referência a API do Gestor de [Destacamento azure](https://docs.microsoft.com/rest/api/deploymentmanager/).
* Uma amostra do Gestor de [Implantação Azure.](https://github.com/Azure-Samples/adm-quickstart)

Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, precisa de:

* Gestor de [implantação azure](./deployment-manager-tutorial.md)de uso completo com modelos de Gestor de Recursos .

## <a name="install-the-artifacts"></a>Instale os artefactos

Descarregue [os modelos e os artefactos](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) e desaperte-os localmente se ainda não o fez. E, em seguida, executar o script PowerShell encontrado em [Preparar os artefactos](./deployment-manager-tutorial.md#prepare-the-artifacts). O script cria um grupo de recursos, cria um recipiente de armazenamento, cria um recipiente de blob, carrega os ficheiros descarregados e, em seguida, cria um token SAS.

Faça uma cópia do URL com token SAS. Este URL é necessário para preencher um campo nos dois ficheiros de parâmetros, os ficheiros parâmetros da topologia e parâmetros do lançamento.

Abra CreateADMServiceTopology.Parameters.json e atualize os valores do **projetoNome** e **artefactoSourceSASLocation**.

Abra CreateADMRollout.Parameters.parson e atualize os valores do **projetoNome** e **artefactoSourceSASLocation**.

## <a name="create-a-health-check-service-simulator"></a>Criar um simulador de serviço de verificação de saúde

Na produção, normalmente utiliza-se um ou mais fornecedores de monitorização. Para facilitar a integração em saúde o mais possível, a Microsoft tem trabalhado com algumas das principais empresas de monitorização da saúde para lhe fornecer uma solução simples de cópia/pasta para integrar os controlos de saúde com as suas implementações. Para obter uma lista destas empresas, consulte os prestadores de monitorização da [Saúde.](./deployment-manager-health-check.md#health-monitoring-providers) Para efeitos deste tutorial, cria uma [Função Azure](/azure/azure-functions/) para simular um serviço de monitorização da saúde. Esta função pega num código de estado e devolve o mesmo código. O modelo do Gestor de Implementação do Azure utiliza o código de estado para determinar como proceder com a implementação.

Os dois ficheiros seguintes são utilizados para a implementação da Função Azure. Não precisas de descarregar estes ficheiros para ver o tutorial.

* Um modelo de [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)Gestor de Recursos localizado em . Implementa este modelo para criar uma Função Azure.
* Um ficheiro postal do código fonte [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip)da Função Azure, . Este zip chamado é chamado pelo modelo de Gestor de Recursos.

Para implementar a função Azure, selecione **Experimente-a** para abrir a concha azure cloud e, em seguida, colar o seguinte script na janela da concha.  Para colar o código, clique à direita na janela da concha e, em seguida, **selecione Pasta**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Para verificar e testar a função Azure:

1. Abra o [portal Azure.](https://portal.azure.com)
1. Abra o grupo de recursos.  O nome padrão é o nome do projeto com **rg** anexado.
1. Selecione o serviço de aplicações do grupo de recursos.  O nome padrão do serviço de aplicações é o nome do projeto com **webapp** anexado.
1. Expandir **funções,** e, em seguida, selecionar **HttpTrigger1**.

    ![Azure Deployment Manager verifica ção de saúde Função Azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. ** &lt;Selecione /> Obtenha URL de função**.
1. Selecione **Copiar** para copiar o URL para a área de redação.  O URL é semelhante a:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Substitua `{healthStatus}` no URL por um código de estado. Neste tutorial, use **insalubre** para testar o cenário pouco saudável, e use **saudável** ou **aviso** para testar o cenário saudável. Crie dois URLs, um com o estado pouco saudável, e o outro com um estado saudável. Por exemplo:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Precisa de ambos os URLs para completar este tutorial.

1. Para testar o simulador de monitorização da saúde, abra os URLs que criou no último passo.  Os resultados do estado pouco saudável devem ser semelhantes aos seguintes:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Reveja o modelo de lançamento

O objetivo desta secção é mostrar-lhe como incluir um passo de verificação de saúde no modelo de lançamento.

1. Abra **CreateADMRollout.json** que criou no [Use Azure Deployment Manager com modelos](./deployment-manager-tutorial.md)de Gestor de Recursos . Este ficheiro JSON faz parte do download.  Veja [Pré-requisitos](#prerequisites).
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

1. Substitua a definição de recurso de passo de espera por uma definição de recurso de passo de verificação de saúde:

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

    Com base na definição, o lançamento prossegue se o estado de saúde for *saudável* ou *aviso*.

1. Atualize o **depende** da definição de lançamento para incluir a etapa de verificação de saúde recentemente definida:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Atualizar **os grupos** para incluir o passo do exame de saúde. O **healthCheckStep** é chamado em **postDeploymentSteps** do **stepGroup2**. **passoGrupo3** e **stepGroup4** só são implantados se o estado saudável for *saudável* ou *aviso*.

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

    Se comparar a secção **stepGroup3** antes e depois de revista, esta secção depende agora do **stepGroup2**.  Isto é necessário quando o **stepGroup3** e os grupos de passo subsequentes dependem dos resultados da monitorização da saúde.

    A seguinte imagem ilustra as áreas modificadas e como é utilizada a etapa do exame de saúde:

    ![Modelo de verificação de saúde do Gestor de Destacamento azure](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Implementar a topologia

Execute o seguinte script PowerShell para implementar a topologia. Precisa do mesmo **CreateADMServiceTopology.json** e **CreateADMServiceTopology.Parameters.json** que usou no Gestor de [Implementação do Azure com modelos](./deployment-manager-tutorial.md)de Gestor de Recursos .

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

Utilize o URL de estado pouco saudável que criou em Criar um simulador de serviço de [verificação de saúde](#create-a-health-check-service-simulator). Você precisa do **revisto CreateADMServiceTopology.json** e o mesmo **CreateADMServiceTopology.Parameters.json** que usou no Gestor de [Implementação de Azure use com modelos](./deployment-manager-tutorial.md)de Gestor de Recursos .

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
> `New-AzResourceGroupDeployment`é uma chamada assíncrona. A mensagem de sucesso significa apenas que a implantação começou com sucesso. Para verificar a `Get-AZDeploymentManagerRollout`implantação, utilize .  Veja o próximo procedimento.

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

Após a conclusão do lançamento, verá um grupo de recursos adicionais criado para os EUA Ocidentais.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Implementar o lançamento com o estado saudável

Repita esta secção para recolocar o lançamento com o URL de estado saudável.  Após a conclusão do lançamento, verá mais um grupo de recursos criado para os EUA Orientais.

## <a name="verify-the-deployment"></a>Verificar a implementação

1. Abra o [portal Azure.](https://portal.azure.com)
2. Navegue para as aplicações Web criadas recentemente, nos grupos de recursos novos que foram criados com a implementação do lançamento.
3. Abra a aplicação Web num browser. Verifique a localização e a versão no ficheiro index.html.

## <a name="clean-up-resources"></a>Limpar recursos

Quando os recursos do Azure já não forem necessários, limpe os recursos implementados ao eliminar o grupo de recursos.

1. A partir do portal Azure, selecione **Grupo Recurso** do menu esquerdo.
2. Utilize o campo **Filtrar por nome** para reduzir os grupos de recursos criados neste tutorial. Deverá haver entre 3 a 4.

    * projectName>rg: contém os recursos do Gestor de Implantação. ** &lt;**
    * projectName>ServiceWUSrg: contém os recursos definidos pelo ServiceWUS. ** &lt;**
    * projectName>ServiceEUSrg: contém os recursos definidos pelo ServiceEUS. ** &lt;**
    * O grupo de recursos da identidade gerida atribuída pelo utilizador.
3. Selecione o nome do grupo de recursos.
4. **Selecione Eliminar** o grupo de recursos do menu superior.
5. Repita os dois últimos passos para eliminar outros grupos de recursos criados neste tutorial.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a usar a funcionalidade de verificação de saúde do Gestor de Implantação do Azure. Para saber mais, veja a [Documentação do Azure Resource Manager](/azure/azure-resource-manager/).
