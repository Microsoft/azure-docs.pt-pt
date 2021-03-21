---
title: GitHub Actions para CI/CD
titleSuffix: Azure Machine Learning
description: Saiba como criar um fluxo de trabalho gitHub actions para treinar um modelo em Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: b21f53f8ec76257fc19e0e30cd025ecc46ad2188
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218286"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Use ações do GitHub com aprendizagem de máquinas Azure

Começa com as [ações do GitHub](https://docs.github.com/en/actions) para treinar um modelo no Azure Machine Learning. 

> [!NOTE]
> GitHub Actions for Azure Machine Learning são fornecidos como é, e não são totalmente suportados pela Microsoft. Se encontrar problemas com uma ação específica, abra uma questão no repositório para a ação. Por exemplo, se encontrar um problema com a ação de implantação da AML, informe o problema no [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) repo.

## <a name="prerequisites"></a>Pré-requisitos 

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Uma conta do GitHub. Se não tiver um, inscreva-se [de graça.](https://github.com/join)  

## <a name="workflow-file-overview"></a>Visão geral do ficheiro do fluxo de trabalho

Um fluxo de trabalho é definido por um ficheiro YAML (.yml) no caminho do `/.github/workflows/` seu repositório. Esta definição contém os vários passos e parâmetros que compõem o fluxo de trabalho.

O ficheiro tem quatro secções:

|Section  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Defina um diretor de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Ligar** | 1. Ligue-se ao espaço de trabalho de aprendizagem automática. <br /> 2. Ligue-se a um alvo de computação. |
|**Executar** | 1. Submeter um treino. |
|**Implementar** | 1. Registar o modelo no registo Azure Machine Learning. 1. Implementar o modelo. |

## <a name="create-repository"></a>Criar repositório

Crie um novo repositório fora das [Operações ML com as ações do GitHub e o modelo de aprendizagem automática Azure.](https://github.com/machine-learning-apps/ml-template-azure) 

1. Abra o [modelo](https://github.com/machine-learning-apps/ml-template-azure) no GitHub. 
2. Selecione **Utilize este modelo.** 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Selecione use este modelo":::
3. Crie um novo repositório a partir do modelo. Desaça o nome do repositório `ml-learning` ou um nome à sua escolha. 


## <a name="generate-deployment-credentials"></a>Gerar credenciais de implantação

Pode criar um [principal de serviço](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) com o comando [ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) no [Azure CLI](/cli/azure/). Executar este comando com [Azure Cloud Shell](https://shell.azure.com/) no portal Azure ou selecionando o botão **Try it.**

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

No exemplo acima, substitua os espaços reservados pelo seu ID de subscrição, nome de grupo de recursos e nome de aplicação. A saída é um objeto JSON com as credenciais de atribuição de funções que fornecem acesso à sua aplicação de Serviço de Aplicações semelhante abaixo. Copie este objeto JSON para mais tarde.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Configure o segredo do GitHub

1. No [GitHub,](https://github.com/)navegue no seu repositório, selecione **Definições > Segredos > Adicione um novo segredo**.

2. Cole toda a saída JSON do comando Azure CLI para o campo de valor do segredo. Dê o nome ao `AZURE_CREDENTIALS` segredo.

## <a name="connect-to-the-workspace"></a>Ligar-se ao espaço de trabalho

Utilize a [ação Azure Machine Learning Workspace](https://github.com/marketplace/actions/azure-machine-learning-workspace) para ligar ao seu espaço de trabalho de aprendizagem de máquinas Azure. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Por padrão, a ação espera um `workspace.json` ficheiro. Se o seu ficheiro JSON tiver um nome diferente, pode especiá-lo com o `parameters_file` parâmetro de entrada. Se não houver um ficheiro, um novo será criado com o nome do repositório.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
A ação escreve as propriedades do Workspace Azure Resource Manager (ARM) para um ficheiro config, que será escolhido por todas as futuras Ações GitHub de Aprendizagem de Máquinas Azure. O ficheiro está guardado para `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Ligue-se a um alvo de computação em aprendizagem automática Azure

Utilize a ação [Azure Machine Learning Compute](https://github.com/Azure/aml-compute) para ligar-se a um alvo de computação em Azure Machine Learning.  Se o alvo do cálculo existir, a ação ligar-se-á a ele. Caso contrário, a ação criará um novo alvo de computação. A [ação AML Compute](https://github.com/Azure/aml-compute) suporta apenas o cluster de computação Azure ML e o Serviço Azure Kubernetes (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Submeter execução de preparação

Utilize a [ação Azure Machine Learning Training](https://github.com/Azure/aml-run) para submeter um ScriptRun, um Estimador ou um Pipeline para Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Modelo de registo no registo

Utilize a ação [Azure Machine Learning Register Model](https://github.com/Azure/aml-registermodel) para registar um modelo para Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Implementar o modelo para Azure Machine Learning para ACI

Utilize a ação [Azure Machine Learning Deploy](https://github.com/Azure/aml-deploy) para implementar um modelo e criar um ponto final para o modelo. Também pode utilizar o Azure Machine Learning Deploy para implementar no Serviço Azure Kubernetes. Consulte [este fluxo de trabalho da amostra](https://github.com/Azure-Samples/mlops-enterprise-template) para um modelo que se desdobra para o Serviço Azure Kubernetes.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Exemplo completo

Treine o seu modelo e desloque-se para a Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o seu grupo de recursos e repositório já não forem necessários, limpe os recursos que implementou eliminando o grupo de recursos e o seu repositório GitHub. 

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar e executar gasodutos de aprendizagem automática com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)