---
title: Use a Política Azure para implementar a governação e os controlos dos recursos db da Azure Cosmos
description: Aprenda a usar a Política Azure para implementar a governação e os controlos dos recursos DB da Azure Cosmos.
author: plzm
ms.author: paelaz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 2249dbdebecc52a8f5d6decccb83d3b1fc0777f7
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747384"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Use a Política Azure para implementar a governação e os controlos dos recursos db da Azure Cosmos

[A Política Azure](../governance/policy/overview.md) ajuda a impor as normas de governação organizacional, a avaliar a conformidade com os recursos e a implementar a reparação automática. Os casos de uso comum incluem segurança, gestão de custos e consistência de configuração.

A Política Azure fornece definições políticas incorporadas. Pode criar definições políticas personalizadas para cenários que não sejam abordados pelas definições políticas incorporadas. Consulte a documentação da [Política Azure](../governance/policy/overview.md) para mais detalhes.

## <a name="assign-a-built-in-policy-definition"></a>Atribuir uma definição política incorporada

As definições de política descrevem as condições de conformidade dos recursos e o efeito a tomar se uma condição for satisfeita. As atribuições políticas são _criadas_ a partir de _definições_políticas. Você pode usar definições políticas incorporadas ou personalizadas para os seus recursos Azure Cosmos DB. As atribuições políticas são aplicadas a um grupo de gestão Azure, a uma subscrição Azure ou a um grupo de recursos e são aplicadas aos recursos dentro do âmbito selecionado. Opcionalmente, pode excluir recursos específicos do âmbito.

Pode criar atribuições políticas com o [portal Azure,](../governance/policy/assign-policy-portal.md) [Azure PowerShell,](../governance/policy/assign-policy-powershell.md) [Azure CLI](../governance/policy/assign-policy-azurecli.md)ou [modelo ARM](../governance/policy/assign-policy-template.md).

Para criar uma atribuição política a partir de uma definição política incorporada para o Azure Cosmos DB, use os passos para criar uma atribuição de políticas com o artigo [do portal Azure.](../governance/policy/assign-policy-portal.md)

Na altura de selecionar uma definição de política, insira `Cosmos DB` no campo de Pesquisa para filtrar a lista de definições políticas incorporadas disponíveis. Selecione uma das definições políticas incorporadas disponíveis e, em seguida, escolha **Selecionar** para continuar a criar a atribuição de políticas.

> [!TIP]
> Também pode utilizar os nomes de definição de política incorporados mostrados no painel **Definições Disponíveis** com modelos Azure PowerShell, Azure CLI ou ARM para criar atribuições políticas.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Pesquisa de definições políticas integradas da Azure Cosmos DB":::

## <a name="create-a-custom-policy-definition"></a>Criar uma definição de política personalizada

Para cenários específicos que não são abordados por políticas incorporadas, pode criar [uma definição de política personalizada.](../governance/policy/tutorials/create-custom-policy-definition.md) Mais tarde, cria uma _atribuição_ de Política a partir da sua _definição_de política personalizada.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Tipos de propriedades e pseudónimos imobiliários nas regras políticas

Utilize os [passos de definição de política personalizadas](../governance/policy/tutorials/create-custom-policy-definition.md) para identificar as propriedades de recursos e pseudónimos imobiliários, que são necessários para criar regras políticas.

Para identificar pseudónimos específicos da propriedade azure Cosmos DB, utilize o espaço de nome `Microsoft.DocumentDB` com um dos métodos mostrados no artigo de definição de política personalizada.

#### <a name="use-the-azure-cli"></a>Utilize o Azure CLI:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Utilize a PowerShell Azure:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Estes comandos saem da lista de nomes de pseudónimos de propriedade para a propriedade Azure Cosmos DB. Segue-se um excerto da saída:

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

Você pode usar qualquer um destes nomes de pseudónimos de propriedade nas regras de [definição de política personalizada](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule).

Segue-se uma definição de política de exemplo que verifica se a entrada prevista de uma base de dados Azure Cosmos DB SQL é superior a um limite máximo permitido de 400 RU/s. Uma definição de política personalizada inclui duas regras: uma para verificar o tipo específico de pseudónimo de propriedade, e a segunda para a propriedade específica do tipo. Ambas as regras usam os nomes de pseudónimos.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
      "field": "type",
      "equals": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings"
      },
      {
      "field": "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/throughputSettings/default.resource.throughput",
      "greater": 400
      }
    ]
  }
}
```

As definições políticas personalizadas podem ser usadas para criar atribuições políticas, tal como as definições políticas incorporadas são utilizadas.

## <a name="policy-compliance"></a>Conformidade com a política

Após a criação das atribuições políticas, a Azure Policy avalia os recursos no âmbito da atribuição. O _cumprimento_ de cada recurso com a política é avaliado. O _efeito_ especificado na política é então aplicado a recursos não conformes.

Pode rever os resultados de conformidade e os detalhes de reparação no [portal Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou através do [Azure CLI](../governance/policy/how-to/get-compliance-data.md#command-line) ou dos [registos do Azure Monitor](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs).

A imagem que se segue mostra dois exemplos de atribuições políticas. Uma missão baseia-se numa definição política incorporada, que verifica se os recursos da Azure Cosmos DB são implantados apenas para as regiões de Azure permitidas. A outra atribuição baseia-se numa definição de política personalizada. Esta atribuição verifica que a provisão prevista para os recursos DB da Azure Cosmos não excede um limite máximo especificado.

Após a implementação das atribuições políticas, o painel de controlo de conformidade mostra os resultados da avaliação. Note que isto pode demorar até 30 minutos após a implementação de uma atribuição de política.

A imagem mostra os seguintes resultados de avaliação de conformidade:

- Zero de uma conta Azure Cosmos DB no âmbito especificado estão em conformidade com a atribuição de políticas para verificar se os recursos foram mobilizados para regiões permitidas.
- Um em cada dois recursos de base de dados da Azure Cosmos DB ou recursos de recolha no âmbito especificado estão em conformidade com a atribuição de políticas para verificar se a provisão prevista excede o limite máximo especificado.

:::image type="content" source="./media/policy/compliance.png" alt-text="Pesquisa de definições políticas integradas da Azure Cosmos DB":::

Para remediar os recursos não conformes, consulte o remediado com o artigo da [Política Azure.](../governance/policy/how-to/remediate-resources.md)

## <a name="next-steps"></a>Passos Seguintes

- [Reveja as definições de política personalizadas da amostra para o Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB)
- [Criar uma atribuição de políticas no portal Azure](../governance/policy/assign-policy-portal.md)
- [Rever definições políticas integradas da Política Azure para o Azure Cosmos DB](./policy-samples.md)
