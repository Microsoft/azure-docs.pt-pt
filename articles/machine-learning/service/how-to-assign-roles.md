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
ms.openlocfilehash: f6c74da2e9189c5dddb88cb80f04422f49cfaee2
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594258"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Gerir utilizadores e funções numa área de trabalho do Azure Machine Learning

Neste artigo, saiba como adicionar utilizadores a uma área de trabalho do Azure Machine Learning e atribuí-las com funções diferentes. Também irá aprender a criar funções personalizadas.

## <a name="built-in-roles"></a>Funções incorporadas
Área de trabalho de Machine Learning do Azure é um recurso do Azure. E como qualquer recurso do Azure, quando é criada uma nova área de trabalho do Azure Machine Learning, ele vem com três funções padrão. Pode adicionar utilizadores à área de trabalho e atribuí-las a uma destas funções incorporadas.

- **Leitor**
    
    Esta função permite as ações de só de leitura na área de trabalho. Com esta função, pode listar e os recursos de exibição numa área de trabalho, tais como de experimentações, é executado, computação, modelos, serviços da web, publicados pipelines e assim por diante. Mas não tem permissão para criar ou atualizar estes recursos.

- **Contribuidor**
    
    Esta função permite aos utilizadores ver, criar, editar ou eliminar (quando aplicável) ativos numa área de trabalho. Por exemplo, com esta função pode criar uma experimentação, criar ou anexar um cluster de cálculo, submeta um registro de execução, um modelo e implementar um serviço web.

- **Proprietário**
    
    Esta função fornece total acesso à área de trabalho, permitindo-lhe ver, criar, editar ou eliminar (quando aplicável) ativos numa área de trabalho. Além disso, também pode adicionar ou remover utilizadores e alterar as suas atribuições de funções.

## <a name="add-or-remove-users"></a>Adicionar ou remover utilizadores
Se for um proprietário de uma área de trabalho, pode adicionar os utilizadores ou remover utilizadores da área de trabalho ao selecionar qualquer um dos seguintes métodos para efetuar esta ação:
- [Portal do Azure da interface do Usuário](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos de gestão de recursos do Azure](/azure/role-based-access-control/role-assignments-template).

Em alternativa, se tiver instalado [da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), também pode utilizar comandos da CLI conveniente para adicionar o utilizador para a área de trabalho.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Tenha em atenção que o `user` campo é o endereço de e-mail de um utilizador existente no mesmo Azure Active Directory onde reside o elemento principal de subscrição do Azure da área de trabalho. Segue-se um exemplo de como utilizar este comando:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar função personalizada
Se as funções incorporadas não são suficientes para as suas necessidades, também pode criar funções personalizadas. As funções personalizadas podem ter ler, escrever ou eliminar as permissões na área de trabalho e o recurso de computação nessa área de trabalho. E pode disponibilizar a função de um nível de área de trabalho específica, um nível de grupo de recursos específico ou um nível de subscrição específica. 

> [!NOTE]
> Tem de ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro construa um ficheiro JSON da definição de função especificando a permissão e o escopo que pretende para a função. Por exemplo, segue-se um ficheiro de definição de função para uma função personalizada com o nome "Cientista de dados" no escopo num nível de área de trabalho específica.

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

Esta função permite que faça tudo na área de trabalho, exceto as seguintes ações:
- Ela não é possível criar ou atualizar um recurso de computação.
- Ele não é possível eliminar um recurso de computação.
- Não é possível adicionar, eliminar o utilizador ou altere as atribuições de funções de qualquer utilizador.
- Ele não é possível eliminar a área de trabalho.

Para implementar esta função personalizada, utilize o seguinte comando da CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Depois de implementada, esta função fica disponível na área de trabalho especificada. Agora pode adicionar utilizadores e atribuir-lhes esta função no portal do Azure. Em alternativa, pode adicionar um utilizador com esta função com o `az ml workspace share` comando da CLI:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Também pode alterar o `AssignableScopes` campo para definir o âmbito desta função personalizada ao nível da subscrição, o nível de grupo de recursos, ou num nível de área de trabalho específica (como mostrado).

Para obter mais informações de funções personalizadas como trabalhar no Azure, veja [este documento](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Passos Seguintes

Siga o tutorial completo para saber como utilizar uma área de trabalho para criar, preparar e implementar modelos com o serviço Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutorial: Preparação de modelos](tutorial-train-models-with-aml.md)
