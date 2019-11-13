---
title: Criar uma definição de política personalizada
description: Crie uma definição de política personalizada para Azure Policy para impor regras de negócio personalizadas.
ms.date: 04/23/2019
ms.topic: tutorial
ms.openlocfilehash: 1a5be5a3e81dec6f4369e6b01dcda3d5de5f6dac
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73959273"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Tutorial: criar uma definição de política personalizada

Uma definição de política personalizada permite que os clientes definam suas próprias regras para usar o Azure. Essas regras geralmente se aplicam:

- Práticas de segurança
- Gestão de custos
- Regras específicas da organização (como nomes ou locais)

Seja qual for o driver de negócios para criar uma política personalizada, as etapas serão as mesmas para definir a nova política personalizada.

Antes de criar uma política personalizada, verifique os [exemplos de política](../samples/index.md) para ver se uma política que corresponde às suas necessidades já existe.

A abordagem para criar uma política personalizada segue estas etapas:

> [!div class="checklist"]
> - Identificar seus requisitos de negócios
> - Mapear cada requisito para uma propriedade de recurso do Azure
> - Mapear a propriedade para um alias
> - Determinar qual efeito usar
> - Compor a definição de política

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar requisitos

Antes de criar a definição de política, é importante entender a intenção da política. Para este tutorial, usaremos um requisito de segurança empresarial comum como o objetivo de ilustrar as etapas envolvidas:

- Cada conta de armazenamento deve ser habilitada para HTTPS
- Cada conta de armazenamento deve ser desabilitada para HTTP

Seus requisitos devem identificar claramente os Estados de recurso "para serem" e "não ser".

Enquanto definimos o estado esperado do recurso, ainda não definimos o que queremos fazer com recursos sem conformidade. Azure Policy dá suporte a vários [efeitos](../concepts/effects.md). Para este tutorial, definiremos o requisito de negócios como prevenção de criação de recursos se eles não estiverem em conformidade com as regras de negócio. Para atender a essa meta, usaremos o efeito de [negação](../concepts/effects.md#deny) . Também queremos a opção de suspender a política para atribuições específicas. Dessa forma, usaremos o efeito [desabilitado](../concepts/effects.md#disabled) e fazemos o efeito de um [parâmetro](../concepts/definition-structure.md#parameters) na definição de política.

## <a name="determine-resource-properties"></a>Determinar propriedades do recurso

Com base no requisito de negócios, o recurso do Azure para auditar com Azure Policy é uma conta de armazenamento. No entanto, não sabemos as propriedades a serem usadas na definição de política. Azure Policy é avaliada em relação à representação JSON do recurso, portanto, precisaremos entender as propriedades disponíveis nesse recurso.

Há várias maneiras de determinar as propriedades de um recurso do Azure. Vamos examinar cada um para este tutorial:

- Modelos do Resource Manager
  - Exportar recurso existente
  - Experiência de criação
  - Modelos de início rápido (GitHub)
  - Documentos de referência de modelo
- Explorador de Recursos do Azure

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Há várias maneiras de examinar um modelo do [Resource Manager](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) que inclui a propriedade que você está procurando gerenciar.

#### <a name="existing-resource-in-the-portal"></a>Recurso existente no portal

A maneira mais simples de localizar Propriedades é examinar um recurso existente do mesmo tipo. Os recursos já configurados com a configuração que você deseja impor também fornecem o valor a ser comparado.
Examine a página **Exportar modelo** (em **configurações**) no portal do Azure para esse recurso específico.

![Exportar página de modelo no recurso existente](../media/create-custom-policy-definition/export-template.png)

Fazer isso para uma conta de armazenamento revela um modelo semelhante a este exemplo:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

Em **Propriedades** é um valor chamado **supportsHttpsTrafficOnly** definido como **false**. Essa propriedade parece que ela pode ser a propriedade que estamos procurando. Além disso, o **tipo** do recurso é **Microsoft. Storage/storageAccounts**. O tipo nos permite limitar a política a apenas recursos desse tipo.

#### <a name="create-a-resource-in-the-portal"></a>Criar um recurso no portal

Outra maneira por meio do portal é a experiência de criação de recursos. Ao criar uma conta de armazenamento por meio do portal, uma opção na guia **avançado** é a **transferência de segurança necessária**. Esta propriedade tem opções _desabilitadas_ e _habilitadas_ . O ícone de informações tem texto adicional que confirma que essa opção é provavelmente a propriedade que desejamos. No entanto, o portal não nos informa o nome da propriedade nesta tela.

Na guia **revisar + criar** , um link está na parte inferior da página para **baixar um modelo de automação**. Selecionar o link abre o modelo que cria o recurso que configuramos. Nesse caso, vemos duas partes principais de informações:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Essa informação informa o tipo de propriedade e também confirma que **supportsHttpsTrafficOnly** é a propriedade que estamos procurando.

#### <a name="quickstart-templates-on-github"></a>Modelos de início rápido no GitHub

Os [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) no GitHub têm centenas de modelos do Resource Manager criados para diferentes recursos. Esses modelos podem ser uma ótima maneira de localizar a propriedade de recurso que você está procurando. Algumas propriedades podem parecer que você está procurando, mas controlar algo mais.

#### <a name="resource-reference-docs"></a>Documentos de referência de recurso

Para validar **supportsHttpsTrafficOnly** é a propriedade correta, verifique a referência de modelo do Resource Manager para o [recurso de conta de armazenamento](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) no provedor de armazenamento.
O objeto Properties tem uma lista de parâmetros válidos. Selecionar o link [StorageAccountPropertiesCreateParameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) mostra uma tabela de propriedades aceitáveis. **supportsHttpsTrafficOnly** está presente e a descrição corresponde ao que estamos procurando para atender aos requisitos de negócios.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Outra maneira de explorar os recursos do Azure é por meio do [Azure Resource Explorer](https://resources.azure.com) (versão prévia). Essa ferramenta usa o contexto de sua assinatura, portanto, você precisa se autenticar no site com suas credenciais do Azure. Depois de autenticado, você pode navegar por provedores, assinaturas, grupos de recursos e recursos.

Localize um recurso de conta de armazenamento e examine as propriedades. Vemos também a propriedade **supportsHttpsTrafficOnly** aqui. Selecionando a guia **documentação** , vemos que a descrição da propriedade corresponde ao que encontramos nos documentos de referência anteriormente.

## <a name="find-the-property-alias"></a>Localizar o alias da propriedade

Identificamos a Propriedade Resource, mas precisamos mapear essa propriedade para um [alias](../concepts/definition-structure.md#aliases).

Há algumas maneiras de determinar os aliases para um recurso do Azure. Vamos examinar cada um para este tutorial:

- CLI do Azure
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>CLI do Azure

No CLI do Azure, o grupo de comandos `az provider` é usado para pesquisar aliases de recursos. Vamos filtrar o namespace **Microsoft. Storage** com base nos detalhes que obtivemos sobre o recurso do Azure anteriormente.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Nos resultados, vemos um alias com suporte nas contas de armazenamento chamadas **supportsHttpsTrafficOnly**. Essa existência desse alias significa que podemos escrever a política para impor nossos requisitos de negócios!

### <a name="azure-powershell"></a>Azure PowerShell

No Azure PowerShell, o cmdlet `Get-AzPolicyAlias` é usado para pesquisar aliases de recursos. Vamos filtrar o namespace **Microsoft. Storage** com base nos detalhes que obtivemos sobre o recurso do Azure anteriormente.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Assim como CLI do Azure, os resultados mostram um alias com suporte das contas de armazenamento chamadas **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

O [grafo de recursos do Azure](../../resource-graph/overview.md) é um novo serviço na versão prévia. Ele permite que outro método encontre Propriedades de recursos do Azure. Aqui está um exemplo de consulta para examinar uma única conta de armazenamento com o grafo de recursos:

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Os resultados são semelhantes ao que vemos nos modelos do Resource Manager e por meio do Azure Resource Explorer. No entanto, os resultados do grafo de recursos do Azure também podem incluir detalhes de [alias](../concepts/definition-structure.md#aliases) _projetando_ a matriz de _aliases_ :

```kusto
where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Aqui está um exemplo de saída de uma conta de armazenamento para aliases:

```json
"aliases": {
    "Microsoft.Storage/storageAccounts/accessTier": null,
    "Microsoft.Storage/storageAccounts/accountType": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/enableBlobEncryption": true,
    "Microsoft.Storage/storageAccounts/enableFileEncryption": true,
    "Microsoft.Storage/storageAccounts/encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            },
            "file": {
                "enabled": true,
                "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
            }
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.keySource": "Microsoft.Storage",
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyname": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyvaulturi": null,
    "Microsoft.Storage/storageAccounts/encryption.keyvaultproperties.keyversion": null,
    "Microsoft.Storage/storageAccounts/encryption.services": {
        "blob": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        },
        "file": {
            "enabled": true,
            "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
        }
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.blob.enabled": true,
    "Microsoft.Storage/storageAccounts/encryption.services.file": {
        "enabled": true,
        "lastEnabledTime": "2018-06-04T17:59:14.4970000Z"
    },
    "Microsoft.Storage/storageAccounts/encryption.services.file.enabled": true,
    "Microsoft.Storage/storageAccounts/networkAcls": {
        "bypass": "AzureServices",
        "defaultAction": "Allow",
        "ipRules": [],
        "virtualNetworkRules": []
    },
    "Microsoft.Storage/storageAccounts/networkAcls.bypass": "AzureServices",
    "Microsoft.Storage/storageAccounts/networkAcls.defaultAction": "Allow",
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*]": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].action": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].id": [],
    "Microsoft.Storage/storageAccounts/networkAcls.virtualNetworkRules[*].state": [],
    "Microsoft.Storage/storageAccounts/primaryEndpoints": {
        "blob": "https://mystorageaccount.blob.core.windows.net/",
        "file": "https://mystorageaccount.file.core.windows.net/",
        "queue": "https://mystorageaccount.queue.core.windows.net/",
        "table": "https://mystorageaccount.table.core.windows.net/"
    },
    "Microsoft.Storage/storageAccounts/primaryEndpoints.blob": "https://mystorageaccount.blob.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.file": "https://mystorageaccount.file.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.queue": "https://mystorageaccount.queue.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.table": "https://mystorageaccount.table.core.windows.net/",
    "Microsoft.Storage/storageAccounts/primaryEndpoints.web": null,
    "Microsoft.Storage/storageAccounts/primaryLocation": "eastus2",
    "Microsoft.Storage/storageAccounts/provisioningState": "Succeeded",
    "Microsoft.Storage/storageAccounts/sku.name": "Standard_LRS",
    "Microsoft.Storage/storageAccounts/sku.tier": "Standard",
    "Microsoft.Storage/storageAccounts/statusOfPrimary": "available",
    "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly": false
}
```

O grafo de recursos do Azure (visualização) pode ser usado por meio de [Cloud Shell](https://shell.azure.com), tornando-o uma maneira rápida e fácil de explorar as propriedades de seus recursos.

## <a name="determine-the-effect-to-use"></a>Determinar o efeito a ser usado

Decidir o que fazer com seus recursos sem conformidade é tão importante quanto decidir o que deve ser avaliado em primeiro lugar. Cada resposta possível a um recurso sem conformidade é chamada de [efeito](../concepts/effects.md).
O efeito controla se o recurso sem conformidade é registrado, bloqueado, tem dados anexados ou tem uma implantação associada a ele para colocar o recurso de volta em um estado compatível.

Para nosso exemplo, DENY é o efeito que desejamos, pois não queremos que recursos não compatíveis sejam criados em nosso ambiente do Azure. A auditoria é uma boa primeira opção para um efeito de política para determinar qual é o impacto de uma política antes de defini-la como Deny. Uma maneira de alterar o efeito por atribuição é mais fácil é parametrizar o efeito. Consulte os [parâmetros](#parameters) abaixo para obter detalhes sobre como.

## <a name="compose-the-definition"></a>Compor a definição

Agora temos os detalhes da propriedade e o alias para o que planejamos gerenciar. Em seguida, vamos compor a própria regra de política. Se você ainda não estiver familiarizado com a linguagem de política, consulte a [estrutura de definição de política](../concepts/definition-structure.md) de referência para saber como estruturar a definição de política. Aqui está um modelo vazio do que é uma definição de política:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metadados

Os três primeiros componentes são metadados de política. Esses componentes são fáceis de fornecer valores para, pois sabemos de que estamos criando a regra. O [modo](../concepts/definition-structure.md#mode) é basicamente sobre as marcas e o local do recurso. Como não precisamos limitar a avaliação a recursos que dão suporte a marcas, usaremos o valor _All_ para o **modo**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parâmetros

Embora não tenhamos usado um parâmetro para alterar a avaliação, queremos usar um parâmetro para permitir a alteração do **efeito** para a solução de problemas. Vamos definir um parâmetro **effecttype** e limitá-lo para somente **Deny** e **Disabled**. Essas duas opções correspondem a nossos requisitos de negócios. O bloco de parâmetros concluído é semelhante a este exemplo:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>regra de política

Compor a [regra de política](../concepts/definition-structure.md#policy-rule) é a etapa final na criação de nossa definição de política personalizada. Identificamos duas instruções para teste:

- Se o **tipo** de conta de armazenamento é **Microsoft. Storage/storageAccounts**
- Que a conta de armazenamento **supportsHttpsTrafficOnly** não é **verdadeira**

Como precisamos que essas duas instruções sejam verdadeiras, usaremos o [operador lógico](../concepts/definition-structure.md#logical-operators) **allOf** . Vamos passar o parâmetro **effecttype** para o efeito em vez de fazer uma declaração estática. Nossa regra concluída é semelhante a este exemplo:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Definição concluída

Com todas as três partes da política definidas, aqui está nossa definição concluída:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

A definição concluída pode ser usada para criar uma nova política. O portal e cada SDK (CLI do Azure, Azure PowerShell e API REST) aceitam a definição de maneiras diferentes, portanto, examine os comandos para cada um para validar o uso correto. Em seguida, atribua-o, usando o efeito com parâmetros, aos recursos apropriados para gerenciar a segurança de suas contas de armazenamento.

## <a name="review"></a>Rever

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Identificado seus requisitos de negócios
> - Mapeado cada requisito para uma propriedade de recurso do Azure
> - Mapeou a propriedade para um alias
> - Determinado o efeito a ser usado
> - Composição da definição de política

## <a name="next-steps"></a>Passos seguintes

Em seguida, use sua definição de política personalizada para criar e atribuir uma política:

> [!div class="nextstepaction"]
> [Criar e atribuir uma definição de política](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)