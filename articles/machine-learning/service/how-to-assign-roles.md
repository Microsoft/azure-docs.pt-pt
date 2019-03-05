---
title: Gerir utilizadores e funções numa área de trabalho do Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Aprenda a gerir utilizadores e funções numa área de trabalho do serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hning86
ms.author: haining
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: 623aaff3cba86e8e523a86e4adcb0a359c339c45
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336469"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Gerir utilizadores e funções numa área de trabalho do Azure Machine Learning

Neste artigo, saiba como adicionar utilizadores a uma área de trabalho do Azure Machine Learning. Também irá aprender a atribuir utilizadores a funções diferentes e criar funções personalizadas.

## <a name="built-in-roles"></a>Funções incorporadas
Uma área de trabalho do Azure Machine Learning é um recurso do Azure. Como os outros recursos do Azure, quando é criada uma nova área de trabalho do Azure Machine Learning, ele vem com três funções padrão. Pode adicionar utilizadores à área de trabalho e atribuí-las a uma destas funções incorporadas.

- **Leitor**
    
    Esta função permite as ações de só de leitura na área de trabalho. Os leitores podem listar e ver ativos na área de trabalho, mas não é possível criar ou atualizar estes recursos.

- **Contribuidor**
    
    Esta função permite aos utilizadores ver, criar, editar ou eliminar (quando aplicável) ativos numa área de trabalho. Por exemplo, os contribuintes podem criar uma experimentação, criar ou anexar um cluster de cálculo, submeter uma execução e implementar um serviço web.

- **Proprietário**
    
    Esta função fornece aos usuários acesso total à área de trabalho, incluindo a capacidade de ver, criar, editar ou eliminar (quando aplicável) ativos numa área de trabalho. Além disso, pode adicionar ou remover utilizadores e alterar as atribuições de função.

## <a name="add-or-remove-users"></a>Adicionar ou remover utilizadores
Se for um proprietário de uma área de trabalho, pode adicionar e remover utilizadores da área de trabalho ao escolher um dos seguintes métodos:
- [Portal do Azure da interface do Usuário](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Se tiver instalado o [da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), também pode utilizar comandos da CLI para adicionar utilizadores à área de trabalho.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de e-mail de um utilizador existente na instância do Azure Active Directory onde reside a subscrição de principal de área de trabalho. Eis um exemplo de como utilizar este comando:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar função personalizada
Se as funções incorporadas não são suficientes, pode criar funções personalizadas. Funções personalizadas podem ter ler, escrever, eliminar e permissões de recursos nessa área de trabalho de computação. Pode disponibilizar a função num nível de área de trabalho específica, um nível de grupo de recursos específico ou um nível de subscrição específica. 

> [!NOTE]
> Tem de ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro construa um ficheiro JSON de definição de função que especifica a permissão e o escopo que pretende para a função. Por exemplo, este é um ficheiro de definição de função para uma função personalizada com o nome "Cientista de dados" no escopo num nível de área de trabalho específica:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```
Pode alterar o `AssignableScopes` campo para definir o âmbito desta função personalizada ao nível da subscrição, a nível do grupo de recursos ou um nível de área de trabalho específica.

Esta função personalizada pode fazer tudo na área de trabalho, exceto as seguintes ações:
- Ela não é possível criar ou atualizar um recurso de computação.
- Ele não é possível eliminar um recurso de computação.
- Não é possível adicionar, eliminar ou alterar as atribuições de funções.
- Ele não é possível eliminar a área de trabalho.

Para implementar esta função personalizada, utilize o seguinte comando da CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implementação, esta função fica disponível na área de trabalho especificada. Agora pode adicionar e atribuir esta função no portal do Azure. Em alternativa, pode adicionar um utilizador com esta função utilizando o `az ml workspace share` comando da CLI:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Para obter mais informações sobre funções personalizadas no Azure, consulte [este documento](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Passos Seguintes

Siga o tutorial completo para saber como utilizar uma área de trabalho para criar, preparar e implementar modelos com o serviço Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Preparação de modelos](tutorial-train-models-with-aml.md)
