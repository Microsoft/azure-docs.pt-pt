---
title: Gerir funções numa área de trabalho do Azure Machine Learning
titleSuffix: Azure Machine Learning service
description: Saiba como aceder a uma área de trabalho de serviço do Azure Machine Learning com o controlo de acesso baseado em funções (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: b40edf705ba61713f4b695dd55a6a20028936c82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993822"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerir o acesso a uma área de trabalho do Azure Machine Learning

Neste artigo, irá aprender a gerir o acesso a uma área de trabalho do Azure Machine Learning. [Controlo de acesso baseado em funções (RBAC)](/azure/role-based-access-control/overview) é utilizado para gerir o acesso aos recursos do Azure. Os utilizadores no Azure Active Directory são atribuídos a funções específicas, o qual concedem acesso a recursos. O Azure fornece funções incorporadas e a capacidade de criar funções personalizadas.

## <a name="default-roles"></a>Funções predefinidas

Uma área de trabalho do Azure Machine Learning é um recurso do Azure. Como os outros recursos do Azure, quando é criada uma nova área de trabalho do Azure Machine Learning, ele vem com três funções padrão. Pode adicionar utilizadores à área de trabalho e atribuí-las a uma destas funções incorporadas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações de só de leitura na área de trabalho. Os leitores podem listar e ver ativos na área de trabalho, mas não é possível criar ou atualizar estes recursos. |
| **Contribuidor** | Ver, criar, editar ou eliminar (quando aplicável) ativos numa área de trabalho. Por exemplo, os contribuintes podem criar uma experimentação, criar ou anexar um cluster de cálculo, submeter uma execução e implementar um serviço web. |
| **Proprietário** | Acesso total a área de trabalho, incluindo a capacidade de ver, criar, editar ou eliminar (quando aplicável) ativos numa área de trabalho. Além disso, pode alterar as atribuições de funções. |

> [!IMPORTANT]
> Acesso de função pode ser confinado a vários níveis no Azure. Por exemplo, alguém com acesso de proprietário para um grupo de trabalho pode não ter acesso de proprietário para o grupo de recursos que contém o grupo de trabalho. Para obter mais informações, consulte [funciona como o RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Para obter mais informações sobre as funções incorporadas específicas, consulte [funções incorporadas para o Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Gerir o acesso de área de trabalho

Se for um proprietário de uma área de trabalho, pode adicionar e remover funções para a área de trabalho. Também pode atribuir funções a utilizadores. Utilize as seguintes ligações para saber como gerir o acesso:
- [Portal do Azure da interface do Usuário](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Se tiver instalado o [da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md), também pode utilizar comandos da CLI para atribuir funções a utilizadores.

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

Para criar uma função personalizada, primeiro construa um ficheiro JSON de definição de função que especifica a permissão e o âmbito para a função. O exemplo seguinte define uma função personalizada com o nome "Cientista de dados" no escopo num nível de área de trabalho específica:

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

Após a implementação, esta função fica disponível na área de trabalho especificada. Agora pode adicionar e atribuir esta função no portal do Azure. Em alternativa, pode atribuir esta função a um utilizador, utilizando o `az ml workspace share` comando da CLI:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Para obter mais informações, consulte [funções personalizadas para recursos do Azure](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Passos Seguintes

- [Descrição geral da segurança da empresa](concept-enterprise-security.md)
- [Executar com segurança inferência numa rede virtual e de experimentações](how-to-enable-virtual-network.md)
- [Tutorial: Preparação de modelos](tutorial-train-models-with-aml.md)
