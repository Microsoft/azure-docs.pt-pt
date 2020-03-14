---
title: Gerencie papéis no seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Aprenda a aceder a um espaço de trabalho azure machine learning utilizando o controlo de acesso baseado em funções (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270098"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerir o acesso a um espaço de trabalho azure machine learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, aprende-se a gerir o acesso a um espaço de trabalho azure machine learning. [O controlo de acesso baseado em funções (RBAC)](/azure/role-based-access-control/overview) é utilizado para gerir o acesso aos recursos do Azure. Aos utilizadores do seu Diretório Ativo Azure são atribuídas funções específicas, que concedem acesso a recursos. O Azure fornece tanto papéis incorporados como a capacidade de criar papéis personalizados.

## <a name="default-roles"></a>Funções padrão

Um espaço de trabalho Azure Machine Learning é um recurso Azure. Tal como outros recursos azure, quando é criado um novo espaço de trabalho azure machine learning, ele vem com três funções padrão. Pode adicionar os utilizadores ao espaço de trabalho e atribuí-los a uma destas funções incorporadas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Apenas as ações de leitura no espaço de trabalho. Os leitores podem listar e ver os ativos num espaço de trabalho, mas não podem criar ou atualizar estes ativos. |
| **Contribuidor** | Ver, criar, editar ou excluir (quando aplicável) ativos num espaço de trabalho. Por exemplo, os contribuintes podem criar uma experiência, criar ou anexar um cluster de cálculo, submeter uma execução e implantar um serviço web. |
| **Proprietário** | Acesso total ao espaço de trabalho, incluindo a capacidade de visualizar, criar, editar ou eliminar (quando aplicável) ativos num espaço de trabalho. Além disso, pode alterar as atribuições de funções. |

> [!IMPORTANT]
> O acesso a funções pode ser acessível a vários níveis em Azure. Por exemplo, alguém com acesso do proprietário a um espaço de trabalho pode não ter acesso do proprietário ao grupo de recursos que contém o espaço de trabalho. Para mais informações, consulte como funciona o [RBAC.](/azure/role-based-access-control/overview#how-rbac-works)

Para obter mais informações sobre papéis específicos incorporados, consulte [as funções incorporadas para o Azure.](/azure/role-based-access-control/built-in-roles)

## <a name="manage-workspace-access"></a>Gerir o acesso ao espaço de trabalho

Se for proprietário de um espaço de trabalho, pode adicionar e remover funções para o espaço de trabalho. Também pode atribuir funções aos utilizadores. Utilize os seguintes links para descobrir como gerir o acesso:
- [Azure portal UI](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [CLI do Azure](/azure/role-based-access-control/role-assignments-cli)
- [API REST](/azure/role-based-access-control/role-assignments-rest)
- [Modelos de Gestor de Recursos Azure](/azure/role-based-access-control/role-assignments-template)

Se tiver instalado o CLI de [aprendizagem automática Azure,](reference-azure-machine-learning-cli.md)também pode utilizar um comando CLI para atribuir funções aos utilizadores.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

O campo `user` é o endereço de e-mail de um utilizador existente no caso do Azure Ative Diretório onde vive a subscrição dos pais do espaço de trabalho. Aqui está um exemplo de como usar este comando:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Criar função personalizada

Se os papéis incorporados forem insuficientes, pode criar papéis personalizados. Os papéis personalizados podem ter lido, escrito, apagado e calcular permissões de recursos nesse espaço de trabalho. Pode disponibilizar o papel a um nível específico do espaço de trabalho, a um nível específico de grupo de recursos ou a um nível de subscrição específico.

> [!NOTE]
> Deve ser proprietário do recurso a esse nível para criar papéis personalizados dentro desse recurso.

Para criar uma função personalizada, primeiro construa um ficheiro JSON de definição de função que especifica a permissão e o âmbito para o papel. O exemplo que se segue define uma função personalizada chamada "Data Scientist" mapeada a um nível específico do espaço de trabalho:

`data_scientist_role.json`:
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

Pode alterar o campo `AssignableScopes` para definir o âmbito desta função personalizada ao nível da subscrição, ao nível do grupo de recursos ou a um nível específico de espaço de trabalho.

Este papel personalizado pode fazer tudo no espaço de trabalho, exceto para as seguintes ações:

- Não pode criar ou atualizar um recurso de computação.
- Não pode apagar um recurso de computação.
- Não pode adicionar, apagar ou alterar atribuições de papéis.
- Não pode apagar o espaço de trabalho.

Para implementar esta função personalizada, utilize o seguinte comando Azure CLI:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, esta função fica disponível no espaço de trabalho especificado. Agora pode adicionar e atribuir este papel no portal Azure. Ou, pode atribuir esta função a um utilizador utilizando o comando `az ml workspace share` CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Para obter mais informações sobre papéis personalizados, consulte [funções personalizadas para os recursos Do Azure.](/azure/role-based-access-control/custom-roles)

Para obter mais informações sobre as operações (ações) utilizáveis com funções personalizadas, consulte [as operações do fornecedor](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)de recursos.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>P. Quais são as permissões necessárias para realizar várias ações no serviço de Aprendizagem automática Azure?

A tabela seguinte é um resumo das atividades de Aprendizagem automática de Azure e as permissões necessárias para as realizar pelo menos no âmbito. Como exemplo se uma atividade pode ser realizada com um âmbito de espaço de trabalho (Coluna 4), então todo o âmbito mais alto com essa permissão também funcionará automaticamente. Todos os caminhos nesta tabela são **caminhos relativos** para `Microsoft.MachineLearningServices/`.

| Atividade | Âmbito de subscrição | Âmbito de nível de grupo de recursos | Âmbito de nível de espaço de trabalho |
|---|---|---|---|
| Criar novo espaço de trabalho | Não é necessário | Proprietário ou contribuinte | N/A (torna-se Proprietário ou herda maior função de âmbito após a criação) |
| Criar novo cluster de computação | Não é necessário | Não é necessário | Proprietário, colaborador ou função personalizada permitindo: `workspaces/computes/write` |
| Criar novo Caderno VM | Não é necessário | Proprietário ou contribuinte | Não é possível |
| Criar nova instância computacional | Não é necessário | Não é necessário | Proprietário, colaborador ou função personalizada permitindo: `workspaces/computes/write` |
| Atividade de avião de dados como submeter corrida, aceder a dados, implementar modelo ou publicar pipeline | Não é necessário | Não é necessário | Proprietário, colaborador ou função personalizada permitindo: `workspaces/*/write` <br/> Tenha em anote que também necessita de uma loja de dados registada no espaço de trabalho para permitir que a MSI aceda aos dados na sua conta de armazenamento. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>P. Como enumerei todos os papéis personalizados na minha subscrição?

No Azure CLI, execute o seguinte comando.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>P. Como encontro a definição de papel para um papel na minha subscrição?

No Azure CLI, execute o seguinte comando. Note que `<role-name>` deve estar no mesmo formato devolvido pelo comando acima.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>P. Como atualiza uma definição de papel?

No Azure CLI, execute o seguinte comando.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Note que precisa de ter permissões em todo o âmbito da sua nova definição de papel. Por exemplo, se esta nova função tiver um alcance em três subscrições, você precisa ter permissões nas três subscrições. 

> [!NOTE]
> As atualizações de papéis podem demorar 15 minutos a uma hora para se aplicarem em todas as atribuições de funções nesse âmbito.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>P. Posso definir um papel que impeça a atualização da Edição do espaço de trabalho? 

Sim, pode definir um papel que impede a atualização da Edição do espaço de trabalho. Uma vez que a atualização do espaço de trabalho é uma chamada patch sobre o objeto espaço de trabalho, você faz isso colocando a seguinte ação na `"NotActions"` array na sua definição JSON: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>P. Que permissões são necessárias para realizar operações de quotas num espaço de trabalho? 

Precisa de permissões de nível de subscrição para realizar qualquer operação relacionada com quotas no espaço de trabalho. Isto significa que a definição de quota de nível de subscrição ou de nível de espaço de trabalho para os seus recursos computacionais geridos só pode acontecer se tiver permissões de escrita no âmbito da subscrição. 


## <a name="next-steps"></a>Passos seguintes

- [Visão geral da segurança da empresa](concept-enterprise-security.md)
- [Executar experiências e inferência/pontuação de forma segura dentro de uma rede virtual](how-to-enable-virtual-network.md)
- [Tutorial: Modelos de comboio](tutorial-train-models-with-aml.md)
- [Operações de prestador de recursos](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
