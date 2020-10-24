---
title: Utilizar a Política Azure para implementar a governação e os controlos dos recursos DB da Azure Cosmos
description: Saiba como usar a Política Azure para implementar a governação e os controlos para os recursos DB da Azure Cosmos.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 493064bc22b1144df6878d71c99e50fd7c15ba1d
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482235"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Utilizar a Política Azure para implementar a governação e os controlos dos recursos DB da Azure Cosmos

[A Azure Policy](../governance/policy/overview.md) ajuda a impor normas de governação organizacional, avaliar o cumprimento dos recursos e implementar a remediação automática. Os casos de uso comum incluem segurança, gestão de custos e consistência de configuração.

A Azure Policy fornece definições políticas incorporadas. Pode criar definições de política personalizadas para cenários que não são abordados pelas definições políticas incorporadas. Consulte a [documentação da Política Azure](../governance/policy/overview.md) para obter mais detalhes.

> [!IMPORTANT]
> A Azure Policy é aplicada ao nível do fornecedor de recursos para os serviços Azure. Os SDKs cosmos podem realizar a maioria das operações de gestão em bases de dados, contentores e recursos de produção que contornam o fornecedor de recursos da Cosmos DB, ignorando assim quaisquer políticas criadas através da Azure Policy. Para garantir a aplicação das políticas ver, [Prevenção de alterações dos SDKs Azure Cosmos](role-based-access-control.md#prevent-sdk-changes)

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição de política incorporada

As definições de política descrevem as condições de conformidade dos recursos e o efeito a ter em conta se uma condição for satisfeita. As _atribuições de políticas_ são criadas a partir de _definições_políticas. Você pode usar definições de política incorporadas ou personalizadas para os seus recursos DB Azure Cosmos. As atribuições de política são procuradas por um grupo de gestão Azure, uma subscrição do Azure ou um grupo de recursos e são aplicadas aos recursos dentro do âmbito selecionado. Opcionalmente, pode excluir recursos específicos do âmbito de aplicação.

Pode criar atribuições de política com o [portal Azure](../governance/policy/assign-policy-portal.md), [Azure PowerShell,](../governance/policy/assign-policy-powershell.md) [Azure CLI](../governance/policy/assign-policy-azurecli.md)ou [modelo ARM](../governance/policy/assign-policy-template.md).

Para criar uma atribuição de política a partir de uma definição de política incorporada para Azure Cosmos DB, use os passos para criar uma atribuição de política com o artigo [do portal Azure.](../governance/policy/assign-policy-portal.md)

Na etapa para selecionar uma definição de política, insira `Cosmos DB` no campo Procurar para filtrar a lista de definições de política incorporadas disponíveis. Selecione uma das definições de política incorporadas disponíveis e, em seguida, escolha **Selecione** para continuar a criar a atribuição de políticas.

> [!TIP]
> Também pode utilizar os nomes de definição de política incorporados indicados no painel **definições disponíveis** com modelos Azure PowerShell, Azure CLI ou ARM para criar atribuições de políticas.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Pesquisa de definições políticas incorporadas da Azure Cosmos":::

## <a name="create-a-custom-policy-definition"></a>Criar uma definição de política personalizada

Para cenários específicos que não sejam abordados por políticas incorporadas, pode criar [uma definição de política personalizada.](../governance/policy/tutorials/create-custom-policy-definition.md) Mais tarde, cria uma _atribuição_ de Política a partir da sua _definição_de política personalizada.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tipos de propriedade e pseudónimos de propriedade nas regras políticas

Use as [etapas de definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md) para identificar as propriedades dos recursos e os pseudónimos de propriedade, que são necessários para criar regras de política.

Para identificar pseudónimos de propriedade específica Azure Cosmos DB, utilize o espaço de nome `Microsoft.DocumentDB` com um dos métodos mostrados no artigo de definição de política personalizada.

#### <a name="use-the-azure-cli"></a>Utilize o Azure CLI:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Use Azure PowerShell:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Estes comandos desemprezam a lista de nomes de pseudónimos de propriedade para a propriedade DB Azure Cosmos. Segue-se um excerto da saída:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Você pode usar qualquer um destes nomes de pseudónimos de propriedade nas [regras de definição de política personalizada.](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule)

Segue-se uma definição de política de exemplo que verifica se uma conta DB Azure Cosmos está configurada para vários locais de escrita. A definição de política personalizada inclui duas regras: uma para verificar o tipo específico de pseudónimo de propriedade, e a segunda para a propriedade específica do tipo, neste caso o campo que armazena a configuração de localização de escrita múltipla. Ambas as regras usam os nomes de pseudónimos.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

As definições de política personalizada podem ser usadas para criar atribuições políticas, tal como as definições de política incorporada são utilizadas.

## <a name="policy-compliance"></a>Conformidade com a política

Após a criação das atribuições políticas, a Azure Policy avalia os recursos no âmbito da atribuição. O _cumprimento_ da política de cada recurso é avaliado. O _efeito_ especificado na política é então aplicado a recursos não conformes.

Pode rever os resultados de conformidade e os detalhes de reparação no [portal Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou através do [CLI Azure](../governance/policy/how-to/get-compliance-data.md#command-line) ou dos registos do [Azure Monitor](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

A imagem que se segue mostra duas atribuições de política de exemplo.

Uma atribuição baseia-se numa definição de política incorporada, que verifica que os recursos DB do Azure Cosmos são implantados apenas nas regiões de Azure permitidas. A conformidade com os recursos mostra o resultado da avaliação da política (conforme ou não conforme) para recursos em âmbito.

A outra atribuição baseia-se numa definição de política personalizada. Esta atribuição verifica se as contas da Cosmos DB estão configuradas para vários locais de escrita.

Após a implementação das atribuições políticas, o painel de conformidade apresenta os resultados da avaliação. Note que isto pode demorar até 30 minutos após a implementação de uma atribuição de política. Além disso, [as análises de avaliação de políticas podem ser iniciadas](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan) a pedido imediatamente após a criação de atribuições políticas.

A imagem mostra os seguintes resultados de avaliação de conformidade para contas DB da Azure Cosmos no âmbito:

- Zero de duas contas estão em conformidade com uma política que a filtragem da Rede Virtual (VNet) deve ser configurada.
- Zero de duas contas estão em conformidade com uma política que exige que a conta seja configurada para vários locais de escrita
- Zero de duas contas estão em conformidade com uma política que os recursos foram mobilizados para permitir às regiões de Azure.

:::image type="content" source="./media/policy/compliance.png" alt-text="Pesquisa de definições políticas incorporadas da Azure Cosmos":::

Para remediar os recursos não conformes, ver [como remediar os recursos com a Política Azure.](../governance/policy/how-to/remediate-resources.md)

## <a name="next-steps"></a>Passos seguintes

- [Reveja as definições de política personalizada da amostra para Azure Cosmos DB,](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)incluindo para a localização de escrita múltipla e as políticas de filtragem VNet mostradas acima.
- [Criar uma atribuição de políticas no portal Azure](../governance/policy/assign-policy-portal.md)
- [Rever definições políticas incorporadas da Azure Cosmos para Azure Cosmos DB](./policy-reference.md)