---
title: Criar uma definição de política personalizada
description: Crie uma definição de política personalizada do Azure Policy para impor regras de negócio personalizada.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: e38eb1315cde3400b70925059d4dd50475a47835
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979674"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Tutorial: Criar uma definição de política personalizada

Uma definição de política personalizada permite aos clientes definir suas próprias regras para utilizar o Azure. Muitas vezes, impõem estas regras:

- Práticas de segurança
- Gestão de custos
- Regras específicas da organização (como de nomenclatura ou localizações)

Seja qual for o driver de negócios para a criação de uma política personalizada, os passos são os mesmos para definir a nova política personalizada.

Antes de criar uma política personalizada, verifique os [exemplos de política](../samples/index.md) se uma política que atende às suas necessidades já existe.

A abordagem para criar uma política personalizada segue estes passos:

> [!div class="checklist"]
> - Identificar os requisitos de negócios
> - Mapear cada requisito para uma propriedade de recursos do Azure
> - Mapear a propriedade para um alias
> - Determinar qual efeito a utilizar
> - Compor a definição de política

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar os requisitos

Antes de criar a definição de política, é importante compreender a intenção da política. Para este tutorial, vamos utilizar um requisito comum de segurança empresarial como o objetivo para ilustrar as etapas envolvidas:

- Cada conta de armazenamento tem de estar ativada para HTTPS
- Cada conta de armazenamento tem de ser desativada para HTTP

Os seus requisitos devem identificar claramente ambas a "para ser" e os Estados de recursos "não deve ser".

Embora definimos o estado esperado do recurso, que ainda não foram definidas, o que queremos fizemos com recursos não compatíveis. Política do Azure suporta um número de [efeitos](../concepts/effects.md). Para este tutorial, vamos definir o requisito de negócio como impedir a criação de recursos, caso não estejam em conformidade com as regras de negócio. Para cumprir este objetivo, usaremos o [negar](../concepts/effects.md#deny) efeito. Queremos também a opção para suspender a política para atribuições específicas. Como tal, vamos utilizar o [desativada](../concepts/effects.md#disabled) efeitos e o efeito de tornar um [parâmetro](../concepts/definition-structure.md#parameters) na definição de política.

## <a name="determine-resource-properties"></a>Determinar as propriedades de recurso

Com base no requisito de negócio, o recurso do Azure para fazer auditoria com o Azure Policy é uma conta de armazenamento. No entanto, não sabemos as propriedades para utilizar na definição de política. O Azure Policy é avaliada em relação a representação JSON do recurso, portanto precisamos entender as propriedades disponíveis esse recurso.

Existem várias formas de determinar as propriedades de um recurso do Azure. Vamos examinar cada uma para este tutorial:

- Modelos do Resource Manager
  - Exportar o recurso existente
  - Experiência de criação
  - Modelos de início rápido (GitHub)
  - Documentos de referência de modelo
- Explorador de Recursos do Azure

### <a name="resource-manager-templates"></a>Modelos do Resource Manager

Existem várias formas de considerar uma [modelo do Resource Manager](../../../azure-resource-manager/resource-manager-tutorial-create-encrypted-storage-accounts.md) que inclui a propriedade que pretende para gerir.

#### <a name="existing-resource-in-the-portal"></a>Recurso existente no portal

A forma mais simples de localizar as propriedades é examinar um recurso existente do mesmo tipo. Recursos já configurados com a definição que deseja impor também fornecem o valor de comparação.
Examinar os **exportar modelo** página (sob **definições**) no portal do Azure para esse recurso específico.

![Exportar modelo de página de recursos existentes](../media/create-custom-policy-definition/export-template.png)

Fazendo isso para uma conta de armazenamento revela um modelo semelhante a este exemplo:

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

Sob **propriedades** é um valor chamado **supportsHttpsTrafficOnly** definido como **false**. Esta propriedade parece que poderá ser a propriedade que estamos procurando. Além disso, o **tipo** do recurso é **Storage/storageaccounts**. O tipo permite-na limitar a política únicos recursos deste tipo.

#### <a name="create-a-resource-in-the-portal"></a>Criar um recurso no portal

Outra forma através do portal é a experiência de criação de recursos. Ao criar uma conta de armazenamento através do portal, uma opção no **avançadas** é separador **transferência de segurança necessária**. Esta propriedade tem _desativada_ e _ativado_ opções. O ícone de informações tem texto adicional que confirme que esta opção é, provavelmente, a propriedade que queremos. No entanto, o portal não forneça o nome da propriedade neste ecrã.

Sobre o **revisão + criar** separador, é de um link na parte inferior da página para **transferir um modelo para a automatização**. Selecionar a ligação é aberto o modelo que cria o recurso que foi configurados. Neste caso, vemos duas informações cruciais:

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

Estas informações nos informa o tipo de propriedade e também confirma **supportsHttpsTrafficOnly** é a propriedade que estamos procurando.

#### <a name="quickstart-templates-on-github"></a>Modelos de início rápido no GitHub

O [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) no GitHub tem centenas de modelos do Resource Manager criados para diferentes recursos. Estes modelos podem ser uma excelente forma de localizar a propriedade de recurso que está procurando. Algumas propriedades podem parecem ser o que precisa, mas controlar algo mais.

#### <a name="resource-reference-docs"></a>Documentos de referência do recurso

Para validar **supportsHttpsTrafficOnly** é a propriedade de corrigir, verifique a referência de modelo do Resource Manager para o [recurso de conta de armazenamento](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) no fornecedor de armazenamento.
O objecto de propriedades tem uma lista de parâmetros válidos. Selecionar o [StorageAccountPropertiesCreateParameters-object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) ligação mostra uma tabela de propriedades aceitáveis. **supportsHttpsTrafficOnly** está presente e a descrição corresponde ao precisa para cumprir os requisitos de negócios.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Outra forma de explorar os recursos do Azure é através da [Explorador de recursos do Azure](https://resources.azure.com) (pré-visualização). Essa ferramenta usa o contexto da sua subscrição, por isso terá de autenticar para o Web site com as suas credenciais do Azure. Uma vez autenticado, pode navegar por fornecedores, subscrições, grupos de recursos e recursos.

Localize um recurso de conta de armazenamento e ver as propriedades. Vemos o **supportsHttpsTrafficOnly** propriedade aqui também. Selecionar o **documentação** separador, podemos ver que a descrição de propriedade corresponde a que Descobrimos nos documentos de referência anteriormente.

## <a name="find-the-property-alias"></a>Encontrar o alias de propriedade

Identificámos que a propriedade de recurso, mas é necessário mapear essa propriedade para um [alias](../concepts/definition-structure.md#aliases).

Existem algumas formas de determinar os aliases para um recurso do Azure. Vamos examinar cada uma para este tutorial:

- CLI do Azure
- Azure PowerShell
- Azure Resource Graph

### <a name="azure-cli"></a>CLI do Azure

Na CLI do Azure, o `az provider` grupo de comandos é utilizado para procurar os aliases de recursos. Vamos filtrar para o **Microsoft. Storage** espaço de nomes com base em detalhes sobre o recurso do Azure, temos anteriormente.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Nos resultados, podemos ver um suportados pelas contas de armazenamento com o nome de alias **supportsHttpsTrafficOnly**. Esta existência deste alias significa que podemos escrever a política para impor a nossos requisitos de negócios!

### <a name="azure-powershell"></a>Azure PowerShell

No Azure PowerShell, o `Get-AzPolicyAlias` cmdlet é utilizado para procurar os aliases de recursos. Vamos filtrar para o **Microsoft. Storage** espaço de nomes com base em detalhes sobre o recurso do Azure, temos anteriormente.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Como a CLI do Azure, os resultados mostram um suportados pelas contas de armazenamento com o nome de alias **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Gráfico de recursos do Azure](../../resource-graph/overview.md) é um novo serviço em pré-visualização. Permite que outro método localizar as propriedades de recursos do Azure. Eis uma consulta de exemplo para observar uma única conta de armazenamento com o gráfico de recursos:

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

Os resultados semelhante-se de que o que vemos nos modelos do Resource Manager e através do Explorador de recursos do Azure. No entanto, também podem incluir resultados de gráfico de recursos do Azure [alias](../concepts/definition-structure.md#aliases) detalha por _projekci_ o _aliases_ matriz:

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

Este é o resultado de exemplo de uma conta de armazenamento para aliases:

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

Gráfico de recursos do Azure (pré-visualização) pode ser utilizado através de [Cloud Shell](https://shell.azure.com), tornando-o uma forma rápida e fácil de explorar as propriedades dos seus recursos.

## <a name="determine-the-effect-to-use"></a>Determinar o efeito para utilizar

Decidir o que fazer com os seus recursos em não conformidades é tão importante quanto a decidir o que avaliar em primeiro lugar. Cada resposta possível para um recurso de conformidade é chamada um [efeito](../concepts/effects.md).
Os controles de efeito se o recurso de conformidade é iniciado, bloqueado, tem dados acrescentado, ou tem uma implementação associados a ele para colocar o recurso de volta ao estado compatível.

No nosso exemplo, a negação é o efeito que Desejamos que não queremos recursos incompatíveis criados no nosso ambiente do Azure. Auditoria é uma boa primeira opção para um efeito de política determinar o que é o impacto de uma política antes de defini-la para negar. Uma forma de alterar o efeito por atribuição mais fácil de fazer é parametrizar o efeito. Ver [parâmetros](#parameters) abaixo para obter os detalhes sobre como.

## <a name="compose-the-definition"></a>A definição de composição

Agora, temos os detalhes de propriedade e o alias para o que estamos a planear gerir. Em seguida, iremos irá compor a regra de política em si. Se ainda não estiver familiarizado com a linguagem de política, referenciar [estrutura de definição de política](../concepts/definition-structure.md) para saber como estruturar a definição de política. Este é um modelo em branco da aparência uma definição de política:

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

Os primeiros três componentes são metadados de política. Esses componentes são fáceis de fornecer valores para uma vez que sabemos o que estamos a criar a regra para. [Modo](../concepts/definition-structure.md#mode) se principalmente de etiquetas e a localização do recurso. Pois não é necessário limitar a avaliação para recursos que suportam etiquetas, vamos utilizar o _todos os_ valor **modo**.

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parâmetros

Embora não tenhamos usado um parâmetro para alterar a avaliação, queremos usar um parâmetro para permitir que a alteração da **efeito** para resolução de problemas. Vamos definir uma **effectType** parâmetro e a limitá-la à única **negar** e **desativado**. Estas duas opções de corresponder ao nossos requisitos de negócios. O bloco de parâmetros terminado este aspeto:

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

### <a name="policy-rule"></a>Regra de política

Compor a [regra de política](../concepts/definition-structure.md#policy-rule) é a etapa final na criação de nossa definição de política personalizada. Identificámos duas instruções para testar para:

- Que a conta de armazenamento **tipo** é **Storage/storageaccounts**
- Que a conta de armazenamento **supportsHttpsTrafficOnly** não é **verdadeiro**

Uma vez que precisamos ambas estas instruções para ser verdade, vamos utilizar o **tudo** [operador lógico](../concepts/definition-structure.md#logical-operators). Vamos passar a **effectType** parâmetro para o efeito em vez de fazer uma declaração estática. Nossa regra concluída é semelhante a este exemplo:

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

Com todas as três partes da política definida, aqui está nossa definição concluída:

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

A definição completa pode ser utilizada para criar uma nova política. Portal e cada SDK (CLI do Azure, Azure PowerShell e REST API) aceite a definição de formas diferentes, por isso, reveja os comandos de cada um para validar a utilização correta. Em seguida, atribuí-la, usando o efeito parametrizado, para recursos adequados para gerir a segurança das suas contas de armazenamento.

## <a name="review"></a>Rever

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Identificar os requisitos de negócio
> - Mapeado cada requisito para uma propriedade de recursos do Azure
> - Mapeado a propriedade para um alias
> - Determinar o efeito de usar
> - Composto a definição de política

## <a name="next-steps"></a>Passos Seguintes

Em seguida, utilize a definição de política personalizada para criar e atribuir uma política:

> [!div class="nextstepaction"]
> [Criar e atribuir uma definição de política](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)