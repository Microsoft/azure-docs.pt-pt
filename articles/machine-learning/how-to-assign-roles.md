---
title: Gerenciar funções em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como acessar um espaço de trabalho Azure Machine Learning usando o RBAC (controle de acesso baseado em função).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5257d9f94f6304c2a8dbea3f1648a71d0ba65e94
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064755"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Gerenciar o acesso a um espaço de trabalho do Azure Machine Learning
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a gerenciar o acesso a um espaço de trabalho do Azure Machine Learning. [O controlo de acesso baseado em funções (RBAC)](/azure/role-based-access-control/overview) é utilizado para gerir o acesso aos recursos do Azure. Os usuários em seu Azure Active Directory recebem funções específicas, que concedem acesso aos recursos. O Azure fornece funções internas e a capacidade de criar funções personalizadas.

## <a name="default-roles"></a>Funções padrão

Um espaço de trabalho Azure Machine Learning é um recurso do Azure. Assim como outros recursos do Azure, quando um novo espaço de trabalho Azure Machine Learning é criado, ele vem com três funções padrão. Você pode adicionar usuários ao espaço de trabalho e atribuí-los a uma dessas funções internas.

| Função | Nível de acesso |
| --- | --- |
| **Leitor** | Ações somente leitura no espaço de trabalho. Os leitores podem listar e exibir ativos em um espaço de trabalho, mas não podem criar ou atualizar esses ativos. |
| **Contribuidor** | Exiba, crie, edite ou exclua (onde aplicável) ativos em um espaço de trabalho. Por exemplo, os colaboradores podem criar um experimento, criar ou anexar um cluster de computação, enviar uma execução e implantar um serviço Web. |
| **Proprietário** | Acesso completo ao espaço de trabalho, incluindo a capacidade de exibir, criar, editar ou excluir ativos (onde aplicável) em um espaço de trabalho. Além disso, você pode alterar as atribuições de função. |

> [!IMPORTANT]
> O acesso à função pode ser definido para vários níveis no Azure. Por exemplo, alguém com acesso de proprietário a um espaço de trabalho pode não ter acesso de proprietário ao grupo de recursos que contém o espaço de trabalho. Para mais informações, consulte como funciona o [RBAC.](/azure/role-based-access-control/overview#how-rbac-works)

Para obter mais informações sobre papéis específicos incorporados, consulte [as funções incorporadas para o Azure.](/azure/role-based-access-control/built-in-roles)

## <a name="manage-workspace-access"></a>Gerenciar o acesso ao espaço de trabalho

Se você for um proprietário de um espaço de trabalho, poderá adicionar e remover funções para o espaço de trabalho. Você também pode atribuir funções a usuários. Use os links a seguir para descobrir como gerenciar o acesso:
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

Se as funções internas forem insuficientes, você poderá criar funções personalizadas. As funções personalizadas podem ter permissões de recurso de leitura, gravação, exclusão e computação nesse espaço de trabalho. Você pode tornar a função disponível em um nível de espaço de trabalho específico, um nível de grupo de recursos específico ou um nível de assinatura específico.

> [!NOTE]
> Você deve ser um proprietário do recurso nesse nível para criar funções personalizadas dentro desse recurso.

Para criar uma função personalizada, primeiro Construa um arquivo JSON de definição de função que especifique a permissão e o escopo para a função. O exemplo a seguir define uma função personalizada denominada "cientista de dados" com escopo em um nível de espaço de trabalho específico:

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

Essa função personalizada pode fazer tudo no espaço de trabalho, exceto pelas seguintes ações:

- Ele não pode criar ou atualizar um recurso de computação.
- Ele não pode excluir um recurso de computação.
- Ele não pode adicionar, excluir ou alterar atribuições de função.
- Ele não pode excluir o espaço de trabalho.

Para implantar essa função personalizada, use o seguinte comando de CLI do Azure:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Após a implantação, essa função fica disponível no espaço de trabalho especificado. Agora você pode adicionar e atribuir essa função no portal do Azure. Ou, pode atribuir esta função a um utilizador utilizando o comando `az ml workspace share` CLI:

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
