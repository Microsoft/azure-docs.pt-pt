---
title: Gerir funções no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como aceder a um espaço de trabalho Azure Machine Learning utilizando o controlo de acesso baseado em funções Azure (Azure RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18, devx-track-azurecli
ms.openlocfilehash: aa84d7cce09b370ab35ef67029f4dbe2ca29cabb
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320851"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerir o acesso a uma área de trabalho do Azure Machine Learning

Neste artigo, aprende-se a gerir o acesso a um espaço de trabalho de Aprendizagem automática Azure. [O controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/overview.md) é utilizado para gerir o acesso aos recursos Azure. Os utilizadores do seu Diretório Ativo Azure são atribuídos a funções específicas, que concedem acesso a recursos. O Azure fornece papéis incorporados e a capacidade de criar papéis personalizados.

## <a name="default-roles"></a>Funções predefinidos

Uma área de trabalho do Azure Machine Learning é um recurso do Azure. Tal como outros recursos do Azure, quando cria uma nova área de trabalho do Azure Machine Learning, esta inclui três funções por predefinição. Pode adicionar utilizadores ao espaço de trabalho e atribuí-los a uma destas funções incorporadas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações só de leitura no espaço de trabalho. Os leitores podem listar e visualizar ativos, incluindo credenciais [de datastore,](how-to-access-data.md) num espaço de trabalho. Os leitores não podem criar ou atualizar estes ativos. |
| **Contribuinte** | Ver, criar, editar ou apagar (se aplicável) ativos num espaço de trabalho. Por exemplo, os contribuidores podem criar uma experimentação, criar ou anexar um cluster de cálculo, submeter uma execução e implementar um serviço Web. |
| **Proprietário** | Acesso total ao espaço de trabalho, incluindo a capacidade de visualizar, criar, editar ou apagar (se aplicável) ativos num espaço de trabalho. Além disso, pode alterar as atribuições de funções. |
| **Papel personalizado** | Permite-lhe personalizar o acesso a operações específicas de controle ou data plane dentro de um espaço de trabalho. Por exemplo, submeter uma execução, criar um cálculo, implementar um modelo ou registar um conjunto de dados. |

> [!IMPORTANT]
> O acesso a funções pode ser alargado a vários níveis em Azure. Por exemplo, alguém com acesso ao proprietário a um espaço de trabalho pode não ter acesso ao grupo de recursos que contém o espaço de trabalho. Para mais informações, consulte [como funciona o Azure RBAC.](../role-based-access-control/overview.md#how-azure-rbac-works)

Para obter mais informações sobre funções específicas incorporadas, consulte [as funções incorporadas do Azure.](../role-based-access-control/built-in-roles.md)

## <a name="manage-workspace-access"></a>Gerir o acesso ao espaço de trabalho

Se você é proprietário de um espaço de trabalho, você pode adicionar e remover papéis para o espaço de trabalho. Também pode atribuir funções aos utilizadores. Use os seguintes links para descobrir como gerir o acesso:
- [Azure portal UI](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [CLI do Azure](../role-based-access-control/role-assignments-cli.md)
- [API REST](../role-based-access-control/role-assignments-rest.md)
- [Modelos de gestor de recursos Azure](../role-based-access-control/role-assignments-template.md)

Se tiver instalado o [CLI de Aprendizagem de Máquinas Azure,](reference-azure-machine-learning-cli.md)pode utilizar comandos CLI para atribuir funções aos utilizadores:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de e-mail de um utilizador existente no caso do Azure Ative Directory onde vive a subscrição dos pais do espaço de trabalho. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> O comando "az ml workspace share" não funciona para conta federada pela Azure Ative Directory B2B. Por favor, use o portal Azure UI em vez de comandar.


## <a name="azure-machine-learning-operations"></a>Operações de Aprendizagem automática Azure

Azure Machine Learning em ações incorporadas para muitas operações e tarefas. Para obter uma lista completa, consulte [as operações do fornecedor de recursos Azure](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices).

## <a name="mlflow-operations-in-azure-machine-learning"></a>MLflow operações em aprendizagem de máquinas Azure

Estas tabelas descrevem o âmbito de permissão que deve ser adicionado às ações na função personalizada criada para executar operações de MLflow.

| Operação MLflow | Âmbito |
| --- | --- |
| Liste todas as experiências na loja de rastreio do espaço de trabalho, faça uma experiência por id, obtenha uma experiência pelo nome | Microsoft.MachineLearningServices/workspaces/experiments/read |
| Crie uma experiência com um nome, coloque uma etiqueta numa experiência, restaure uma experiência marcada para a eliminação| Microsoft.MachineLearningServices/workspaces/experiments/write | 
| Excluir uma experiência | Microsoft.MachineLearningServices/workspaces/experiments/delete |
| Obtenha uma execução e dados relacionados e metadados, obtenha uma lista de todos os valores para a métrica especificada para uma determinada execução, liste artefactos para uma corrida | Microsoft.MachineLearningServices/workspaces/experiments/runs/read |
| Criar uma nova execução dentro de uma experiência, eliminar runs, restaurar corridas eliminadas, registar métricas sob a execução atual, definir tags em uma corrida, eliminar tags em uma corrida, pára-quedamentos de log (par de valores-chave) usado para uma corrida, registar um lote de métricas, parames e tags para uma execução, estado de execução de atualização | Microsoft.MachineLearningServices/workspaces/experiments/runs/write |
| Obtenha modelo registado pelo nome, obtenha uma lista de todos os modelos registados no registo, procure modelos registados, modelos de versão mais recentes para cada fase de pedidos, obtenha uma versão de modelo registada, versões de modelos de pesquisa, obter URI onde os artefactos de uma versão modelo são armazenados, pesquisa por execuções por ids de experiência | Microsoft.MachineLearningServices/workspaces/models/read |
| Criar um novo modelo registado, atualizar o nome/descrição de um modelo registado, mudar o nome do modelo registado existente, criar nova versão do modelo, atualizar a descrição de uma versão modelo, transitar um modelo registado para uma das fases | Microsoft.MachineLearningServices/workspaces/models/write |
| Eliminar um modelo registado juntamente com toda a sua versão, eliminar versões específicas de um modelo registado | Microsoft.MachineLearningServices/workspaces/models/delete |


## <a name="create-custom-role"></a>Criar função personalizada

Se as funções incorporadas forem insuficientes, poderá criar funções personalizadas. As funções personalizadas podem ter lido, escrito, apagado e computado permissões de recursos nesse espaço de trabalho. Pode disponibilizar a função a um nível específico do espaço de trabalho, a um nível específico do grupo de recursos ou a um nível de subscrição específico.

> [!NOTE]
> Você deve ser um proprietário do recurso a esse nível para criar funções personalizadas dentro desse recurso.

Para criar um papel personalizado, primeiro construa um ficheiro JSON de definição de papel que especifica a permissão e o alcance para o papel. O exemplo a seguir define uma função personalizada chamada "Data Scientist Custom" a um nível específico do espaço de trabalho:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> Pode alterar o `AssignableScopes` campo para definir o âmbito desta função personalizada ao nível da subscrição, ao nível do grupo de recursos ou a um nível específico de espaço de trabalho.
> O papel personalizado acima é apenas um exemplo, veja alguns papéis personalizados sugeridos [para o serviço de Aprendizagem automática Azure](#customroles).

Esta função personalizada pode fazer tudo no espaço de trabalho, exceto nas seguintes ações:

- Não pode criar ou atualizar um recurso computacional.
- Não pode apagar um recurso computacional.
- Não pode adicionar, excluir ou alterar atribuições de funções.
- Não pode apagar o espaço de trabalho.

Para implementar esta função personalizada, utilize o seguinte comando Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implementação, esta função fica disponível no espaço de trabalho especificado. Agora pode adicionar e atribuir este papel no portal Azure. Ou, pode atribuir esta função a um utilizador utilizando o `az ml workspace share` comando CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Para obter mais informações sobre funções personalizadas, consulte [as funções personalizadas Azure](../role-based-access-control/custom-roles.md). Para obter mais informações sobre as operações (ações e não ações) utilizáveis com funções personalizadas, consulte [as operações do fornecedor de recursos.](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>P. Quais são as permissões necessárias para realizar alguns cenários comuns no serviço Azure Machine Learning?

O quadro a seguir é um resumo das atividades de Aprendizagem automática do Azure e as permissões necessárias para as executar no mínimo. Por exemplo, se uma atividade pode ser realizada com um âmbito de espaço de trabalho (Coluna 4), então todo o âmbito mais elevado com essa permissão também funcionará automaticamente:

> [!IMPORTANT]
> Todos os caminhos desta tabela que começam `/` são **caminhos relativos** `Microsoft.MachineLearningServices/` para:

| Atividade | Âmbito de nível de subscrição | Âmbito de grupo de recursos | Âmbito ao nível do espaço de trabalho |
| ----- | ----- | ----- | ----- |
| Criar novo espaço de trabalho | não é necessário | Proprietário ou colaborador | N/A (torna-se Proprietário ou herda um papel de âmbito superior após a criação) |
| Solicitar nível de subscrição Quota Amlcompute ou quota de nível de espaço de trabalho definido | Proprietário, colaborador ou papel personalizado </br>permitindo `/locations/updateQuotas/action`</br> no âmbito de subscrição | Não Autorizado | Não Autorizado |
| Criar novo cluster computacional | não é necessário | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `/workspaces/computes/write` |
| Criar nova instância computacional | não é necessário | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `/workspaces/computes/write` |
| Submeter qualquer tipo de execução | não é necessário | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Publicação de um ponto final do gasoduto | não é necessário | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Implantação de um modelo registado num recurso AKS/ACI | não é necessário | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Pontuação contra um ponto final AKS implantado | não é necessário | não é necessário | Titular, colaborador ou papel personalizado que permite: `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` (quando não estiver a utilizar auth Azure Ative Directory) OR `"/workspaces/read"` (quando estiver a utilizar auth token) |
| Acesso ao armazenamento usando cadernos interativos | não é necessário | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Criar novo papel personalizado | Titular, colaborador ou papel personalizado permitindo `Microsoft.Authorization/roleDefinitions/write` | não é necessário | Função de proprietário, colaborador ou personalizado que permite: `/workspaces/computes/write` |

> [!TIP]
> Se receber uma falha ao tentar criar um espaço de trabalho pela primeira vez, certifique-se de que o seu papel permite `Microsoft.MachineLearningServices/register/action` . Esta ação permite-lhe registar o fornecedor de recursos Azure Machine Learning com a sua assinatura Azure.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>P. Estamos a publicar papéis incorporados no Azure para o serviço de Machine Learning?

Não estamos atualmente a publicar [papéis integrados do Azure](../role-based-access-control/built-in-roles.md) para o serviço de Machine Learning. Uma função incorporada uma vez publicada não pode ser atualizada, e ainda estamos a firmar as definições de papel baseadas em cenários e feedback do cliente. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>P. Existem modelos de papel personalizado para os cenários mais comuns no serviço machine learning?

Sim, aqui estão alguns cenários comuns com definições de papéis propostas personalizadas que pode usar como base para definir os seus próprios papéis personalizados:

* __Data Scientist Custom__ : Permite que um cientista de dados execute todas as operações dentro de um espaço de **trabalho, exceto:**

    * Criação de computação
    * Implantação de modelos para um cluster AKS de produção
    * Implantação de um ponto final de gasoduto na produção

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Data Scientist Restricted Custom__ : Uma definição de função mais restrita sem wildcards nas ações permitidas. Pode realizar todas as operações dentro de um espaço de **trabalho, exceto:**

    * Criação de computação
    * Implantação de modelos para um cluster AKS de produção
    * Implantação de um ponto final de gasoduto na produção

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```
     
* __MLflow Data Scientist Custom__ : Permite que um cientista de dados execute todas as operações apoiadas pela MLflow AzureML, **exceto:**

   * Criação de computação
   * Implantação de modelos para um cluster AKS de produção
   * Implantação de um ponto final de gasoduto na produção

   `mlflow_data_scientist_custom_role.json` :
   ```json
   {
        "Name": "MLFlow Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/experiments/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/delete",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/models/read",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/models/delete"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
     "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```   

* __MLOps Custom__ : Permite-lhe atribuir uma função a um diretor de serviço e usá-lo para automatizar os seus oleodutos MLOps. Por exemplo, submeter corridas contra um oleoduto já publicado:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Workspace Admin__ : Permite-lhe realizar todas as operações no âmbito de um espaço de trabalho, **exceto:**

    * Criação de um novo espaço de trabalho
    * Atribuição de quotas de subscrição ou de nível de trabalho

    O administrador do espaço de trabalho também não pode criar um novo papel. Só pode atribuir funções incorporadas ou personalizadas existentes no âmbito do seu espaço de trabalho:

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Labeler Custom__ : Permite-lhe definir uma função apenas para rotular dados:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>P. Como posso listar todas as funções personalizadas na minha assinatura?

No Azure CLI, executar o seguinte comando.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>P. Como encontro as operações apoiadas pelo Serviço de Aprendizagem Automática?

No Azure CLI, executar o seguinte comando.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Também podem ser encontrados na lista de operações do fornecedor de [recursos.](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>P. O que são alguns gotchas comuns quando se usa Azure RBAC?

Eis algumas coisas a ter em conta enquanto utiliza o controlo de acesso baseado em funções Azure (Azure RBAC):

- Quando se cria um recurso em Azure, digamos um espaço de trabalho, não é diretamente o proprietário do espaço de trabalho. O seu papel é herdado do mais alto papel de âmbito contra o que está autorizado nessa subscrição. Como exemplo, se for um Administrador de Rede e tiver as permissões para criar um espaço de trabalho de Machine Learning, será-lhe atribuída a função de Administrador de Rede contra esse espaço de trabalho, e não a função de Proprietário.
- Quando existem duas atribuições de funções ao mesmo utilizador do Azure Ative Directory com secções contraditórias de Ações/NotActions, as suas operações listadas em NotActions de uma função podem não produzir efeitos se também estiverem listadas como Ações noutra função. Para saber mais sobre como a Azure analisa atribuições de funções, leia [como o Azure RBAC determina se um utilizador tem acesso a um recurso](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- Para implementar os seus recursos de computação dentro de um VNet, precisa de ter explicitamente permissões para as seguintes ações:
    - "Microsoft.Network/virtualNetworks/join/action" no recurso VNet.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" no recurso sub-rede.
    
    Para obter mais informações sobre o Azure RBAC com a rede, consulte as [funções embutidas em rede](../role-based-access-control/built-in-roles.md#networking).

- Às vezes pode levar até 1 hora para as suas novas tarefas de função fazerem efeito sobre permissões em cache através da pilha.

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>P. Que permissões preciso para usar uma identidade gerida atribuída pelo utilizador com os meus clusters Amlcompute?

Para atribuir uma identidade atribuída a um utilizador em clusters Amlcompute, é preciso ter permissões de escrita para criar computação e ter [Função de Operador de Identidade Gerida](../role-based-access-control/built-in-roles.md#managed-identity-operator). Para obter mais informações sobre o Azure RBAC com identidades geridas, leia [Como gerir a identidade atribuída pelo utilizador](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>P. Apoiamos o controlo de acesso baseado em funções no portal Studio?

O Azure Machine Learning Studio suporta o controlo de acesso baseado em funções Azure (Azure RBAC). 

> [!IMPORTANT]
> Uma vez atribuída uma função personalizada com permissões específicas a um cientista de dados no seu espaço de trabalho, as ações correspondentes (como adicionar um botão de computação) são automaticamente ocultadas dos utilizadores. Ocultar estes itens impede qualquer confusão de ver controlos que devolvam uma notificação de Acesso Não Autorizado do serviço quando utilizados.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>P. Como encontro a definição de papel para um papel na minha subscrição?

No Azure CLI, executar o seguinte comando. O `<role-name>` deve estar no mesmo formato devolvido pelo comando acima.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>P. Como atualizei uma definição de função?

No Azure CLI, executar o seguinte comando.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Precisa de permissões em todo o âmbito da sua nova definição de papel. Por exemplo, se esta nova função tiver um alcance em três subscrições, você precisa ter permissões nas três subscrições. 

> [!NOTE]
> As atualizações de funções podem demorar 15 minutos a uma hora a aplicar-se em todas as atribuições de funções nesse âmbito.


### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>P. Que permissões são necessárias para realizar operações de quotas num espaço de trabalho? 

Precisa de permissões de nível de subscrição para realizar qualquer operação relacionada com quotas no espaço de trabalho. Isto significa que definir a quota de nível de subscrição ou a quota de nível de espaço de trabalho para os seus recursos de computação geridos só pode acontecer se tiver permissões de escrita no âmbito de subscrição. 


## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da segurança empresarial](concept-enterprise-security.md)
- [Isolamento de rede virtual e visão geral da privacidade](how-to-network-security-overview.md)
- [Tutorial: Modelos de comboio](tutorial-train-models-with-aml.md)
- [Operações do fornecedor de recursos](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)