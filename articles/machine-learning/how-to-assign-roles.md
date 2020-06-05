---
title: Gerir funções no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como aceder a um espaço de trabalho Azure Machine Learning utilizando o controlo de acesso baseado em funções (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 9763cc0d93e6731bb42bcc55f9d8bf9463e2b0dd
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434678"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerir o acesso a um espaço de trabalho de aprendizagem automática Azure
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a gerir o acesso a um espaço de trabalho de Aprendizagem automática Azure. [O controlo de acesso baseado em funções (RBAC)](/azure/role-based-access-control/overview) é utilizado para gerir o acesso aos recursos Azure. Os utilizadores do seu Diretório Ativo Azure são atribuídos a funções específicas, que concedem acesso a recursos. O Azure fornece papéis incorporados e a capacidade de criar papéis personalizados.

## <a name="default-roles"></a>Funções predefinidos

Um espaço de trabalho de aprendizagem automática Azure é um recurso Azure. Como outros recursos Azure, quando um novo espaço de trabalho Azure Machine Learning é criado, ele vem com três papéis padrão. Pode adicionar utilizadores ao espaço de trabalho e atribuí-los a uma destas funções incorporadas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações só de leitura no espaço de trabalho. Os leitores podem listar e visualizar ativos num espaço de trabalho, mas não podem criar ou atualizar estes ativos. |
| **Contribuinte** | Ver, criar, editar ou apagar (se aplicável) ativos num espaço de trabalho. Por exemplo, os contribuintes podem criar uma experiência, criar ou anexar um cluster de computação, submeter uma execução e implementar um serviço web. |
| **Proprietário** | Acesso total ao espaço de trabalho, incluindo a capacidade de visualizar, criar, editar ou apagar (se aplicável) ativos num espaço de trabalho. Além disso, pode alterar atribuições de funções. |

> [!IMPORTANT]
> O acesso a funções pode ser alargado a vários níveis em Azure. Por exemplo, alguém com acesso ao proprietário a um espaço de trabalho pode não ter acesso ao grupo de recursos que contém o espaço de trabalho. Para mais informações, consulte [como funciona o RBAC.](/azure/role-based-access-control/overview#how-rbac-works)

Para obter mais informações sobre funções específicas incorporadas, consulte [as funções incorporadas para o Azure.](/azure/role-based-access-control/built-in-roles)

## <a name="manage-workspace-access"></a>Gerir o acesso ao espaço de trabalho

Se você é proprietário de um espaço de trabalho, você pode adicionar e remover papéis para o espaço de trabalho. Também pode atribuir funções aos utilizadores. Use os seguintes links para descobrir como gerir o acesso:
- [Azure portal UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos do Azure Resource Manager](/azure/role-based-access-control/role-assignments-template)

Se instalou o [CLI de Aprendizagem de Máquinas Azure,](reference-azure-machine-learning-cli.md)também pode utilizar um comando CLI para atribuir funções aos utilizadores.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O `user` campo é o endereço de e-mail de um utilizador existente no caso do Azure Ative Directory onde vive a subscrição dos pais do espaço de trabalho. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar função personalizada

Se os papéis incorporados forem insuficientes, pode criar papéis personalizados. As funções personalizadas podem ter lido, escrito, apagado e computado permissões de recursos nesse espaço de trabalho. Pode disponibilizar a função a um nível específico do espaço de trabalho, a um nível específico do grupo de recursos ou a um nível de subscrição específico.

> [!NOTE]
> Você deve ser um proprietário do recurso a esse nível para criar funções personalizadas dentro desse recurso.

Para criar um papel personalizado, primeiro construa um ficheiro JSON de definição de papel que especifica a permissão e o alcance para o papel. O exemplo a seguir define uma função personalizada chamada "Data Scientist" a um nível específico do espaço de trabalho:

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

Pode alterar o `AssignableScopes` campo para definir o âmbito desta função personalizada ao nível da subscrição, ao nível do grupo de recursos ou a um nível específico de espaço de trabalho.

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

Para obter mais informações sobre funções personalizadas, consulte [as funções personalizadas para recursos Azure](/azure/role-based-access-control/custom-roles).

Para obter mais informações sobre as operações (ações) utilizáveis com funções personalizadas, consulte [as operações do fornecedor de recursos.](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>P. Quais são as permissões necessárias para realizar várias ações no serviço de Aprendizagem automática Azure?

O quadro a seguir é um resumo das atividades de Aprendizagem automática do Azure e as permissões necessárias para as executar no mínimo. Como exemplo, se uma atividade pode ser realizada com um âmbito de espaço de trabalho (Coluna 4), então todo o âmbito mais elevado com essa permissão também funcionará automaticamente. Todos os caminhos desta tabela são **caminhos relativos** `Microsoft.MachineLearningServices/` para.

| Atividade | Âmbito de nível de subscrição | Âmbito de grupo de recursos | Âmbito ao nível do espaço de trabalho |
|---|---|---|---|
| Criar novo espaço de trabalho | Não é necessária | Proprietário ou colaborador | N/A (torna-se Proprietário ou herda um papel de âmbito superior após a criação) |
| Criar novo cluster computacional | Não é necessária | Não é necessária | Função de proprietário, colaborador ou personalizado que permite:`workspaces/computes/write` |
| Criar novo VM de Caderno | Não é necessária | Proprietário ou colaborador | Não é possível |
| Criar nova instância computacional | Não é necessária | Não é necessária | Função de proprietário, colaborador ou personalizado que permite:`workspaces/computes/write` |
| Atividade de plano de dados como submeter, aceder a dados, implementar modelo ou publicar pipeline | Não é necessária | Não é necessária | Função de proprietário, colaborador ou personalizado que permite:`workspaces/*/write` <br/> Note que também precisa de uma datastore registada no espaço de trabalho para permitir que o MSI aceda a dados na sua conta de armazenamento. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>P. Como posso listar todas as funções personalizadas na minha assinatura?

No Azure CLI, executar o seguinte comando.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>P. Como encontro a definição de papel para um papel na minha subscrição?

No Azure CLI, executar o seguinte comando. Note que `<role-name>` deve estar no mesmo formato devolvido pelo comando acima.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>P. Como atualizei uma definição de função?

No Azure CLI, executar o seguinte comando.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Note que precisa de permissões em todo o âmbito da sua nova definição de função. Por exemplo, se esta nova função tiver um alcance em três subscrições, você precisa ter permissões nas três subscrições. 

> [!NOTE]
> As atualizações de funções podem demorar 15 minutos a uma hora a aplicar-se em todas as atribuições de funções nesse âmbito.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>P. Posso definir um papel que impeça a atualização da edição do espaço de trabalho? 

Sim, pode definir um papel que impede a atualização da edição do espaço de trabalho. Uma vez que a atualização do espaço de trabalho é uma chamada PATCH no objeto do espaço de trabalho, fá-lo colocando a seguinte ação na `"NotActions"` matriz na definição JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>P. Que permissões são necessárias para realizar operações de quotas num espaço de trabalho? 

Precisa de permissões de nível de subscrição para realizar qualquer operação relacionada com quotas no espaço de trabalho. Isto significa que definir a quota de nível de subscrição ou a quota de nível de espaço de trabalho para os seus recursos de computação geridos só pode acontecer se tiver permissões de escrita no âmbito de subscrição. 


## <a name="next-steps"></a>Passos seguintes

- [Descrição geral da segurança empresarial](concept-enterprise-security.md)
- [Executar de forma segura experiências e inferência/pontuação dentro de uma rede virtual](how-to-enable-virtual-network.md)
- [Tutorial: Modelos de comboio](tutorial-train-models-with-aml.md)
- [Operações do fornecedor de recursos](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
