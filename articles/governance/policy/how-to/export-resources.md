---
title: Exportar recursos do Azure Policy
description: Aprenda a exportar recursos da Política Azure para o GitHub, tais como definições políticas e atribuições políticas.
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94919570"
---
# <a name="export-azure-policy-resources"></a>Exportar recursos do Azure Policy

Este artigo fornece informações sobre como exportar os seus recursos existentes da Política Azure. Exportar os seus recursos é útil e recomendado para o backup, mas também é um passo importante na sua jornada com a Cloud Governance e tratar a sua [política como código.](../concepts/policy-as-code.md) Os recursos da Política Azure podem ser exportados através do [portal Azure](#export-with-azure-portal), [Azure CLI,](#export-with-azure-cli) [Azure PowerShell,](#export-with-azure-powershell)e cada um dos SDKs suportados.

## <a name="export-with-azure-portal"></a>Exportação com portal Azure

Para exportar uma definição de política do portal Azure, siga estes passos:

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

1. Selecione **Definições** no lado esquerdo da página Política Azure.

1. Utilize o botão **de definições de exportação** ou selecione a elipse na linha de uma definição de política e, em seguida, selecione **a definição de Exportação**.

1. Selecione o Iniciar sção com o botão **GitHub.** Se ainda não autenticou com o GitHub para autorizar a Azure Policy a exportar o recurso, reveja o acesso que a [Ação GitHub](https://github.com/features/actions) necessita na nova janela que abre e selecione **Authorize AzureGitHubActions** para continuar com o processo de exportação. Uma vez concluída, a nova janela fecha-se.

1. No separador **Básicos,** desa estale as seguintes opções e, em seguida, selecione o separador **Políticas** ou **Seguinte : Políticas** na parte inferior da página.

   - **Filtro repositório**: Desaça para _os meus repositórios_ para ver apenas repositórios que possui ou _todos os repositórios_ para ver tudo o que concedeu à Ação GitHub.
   - **Repositório**: Desconfie ao repositório para o que pretende exportar os recursos da Política Azure para.
   - **Ramo**: Coloque o ramo no repositório. Usar um ramo diferente do padrão é uma boa forma de validar as suas atualizações antes de se fundir ainda mais no seu código fonte.
   - **Diretório**: A _pasta de nível de raiz_ para exportar os recursos da Política Azure para. As sub-dobradeiras ao abrigo deste diretório são criadas com base nos recursos exportados.

1. No separador **Políticas,** desaprova o âmbito de pesquisa selecionando a elipse e escolhendo uma combinação de grupos de gestão, subscrições ou grupos de recursos.
   
1. Utilize o botão **(s) de definição de política adicionar** para pesquisar o âmbito para qual os objetos a exportar. Na janela lateral que se abre, selecione cada objeto para exportar. Filtrar a seleção pela caixa de pesquisa ou pelo tipo. Depois de selecionar todos os objetos para exportar, utilize o botão **Adicionar** na parte inferior da página.

1. Para cada objeto selecionado, selecione as opções de exportação desejadas, tais como _Apenas Definição_ ou _Definição e Atribuição para_ uma definição de política. Em seguida, selecione o **separador Rever + Exportação** ou **Seguinte : Rever + Exportar** na parte inferior da página.

   > [!NOTE]
   > Se forem escolhidas as _definições e atribuições de opções,_ apenas são exportadas as atribuições políticas dentro do âmbito definido pelo filtro quando a definição de política é adicionada.

1. No **separador 'Rever + Exportação',** verifique os detalhes correspondem e, em seguida, utilize o botão **Exportação** na parte inferior da página.

1. Verifique a sua pasta gitHub repo, branch e _root level_ para ver se os recursos selecionados são agora exportados para o seu controlo de origem.

Os recursos da Política Azure são exportados para a seguinte estrutura dentro da pasta selecionada do repositório e _do nível de raiz_ do GitHub:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exportação com Azure CLI

As definições, iniciativas e atribuições da Política Azure podem ser exportadas como JSON com [Azure CLI](/cli/azure/install-azure-cli). Cada um destes comandos usa um parâmetro **de nome** para especificar para que objeto obter o JSON. A propriedade **do nome** é frequentemente um _GUID_ e não é o nome de **exibição** do objeto.

- Definição - [az definição de política mostra](/cli/azure/policy/definition#az_policy_definition_show)
- Iniciativa - [az definição de definição de política mostra](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Atribuição - [az policy assignment assignment show](/cli/azure/policy/assignment#az_policy_assignment_show)

Aqui está um exemplo de obter o JSON para uma definição de política com **o nome** de _VirtualMachineStorage:_

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportação com Azure PowerShell

As definições, iniciativas e atribuições da Política Azure podem ser exportadas como JSON com [Azure PowerShell](/powershell/azure/). Cada um destes cmdlets usa um parâmetro **Nome** para especificar qual o objeto para obter o JSON. A propriedade **Name** é frequentemente um _GUID_ e não é o nome de **exibição** do objeto.

- Definição - [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Iniciativa - [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Atribuição - [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Aqui está um exemplo de obter o JSON para uma definição de política com **Nome** de _VirtualMachineStorage_:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Passos seguintes

- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Veja [Compreender os efeitos do Policy](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Saiba como [remediar recursos não conformes.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
