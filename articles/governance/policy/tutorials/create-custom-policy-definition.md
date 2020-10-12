---
title: 'Tutorial: Criar uma definição de política personalizada'
description: Neste tutorial, você elabora uma definição de política personalizada para a Azure Policy para impor regras de negócio personalizadas sobre os seus recursos Azure.
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 72282cbc5ed1877cf3f61b792235e8dc2f72fffe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649832"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Tutorial: Criar uma definição de política personalizada

Uma definição de política personalizada permite que os clientes definam as suas próprias regras para a utilização do Azure. Estas regras aplicam frequentemente:

- Práticas de segurança
- Gestão de custos
- Regras específicas da organização (como nomeação ou locais)

Qualquer que seja o motorista de negócios para criar uma política personalizada, os passos são os mesmos para definir a nova política personalizada.

Antes de criar uma política personalizada, consulte as [amostras de política](../samples/index.md) para ver se já existe uma política que corresponda às suas necessidades.

A abordagem para criar uma política personalizada segue estes passos:

> [!div class="checklist"]
> - Identifique os seus requisitos de negócio
> - Mapear cada requisito para uma propriedade de recursos Azure
> - Mapear a propriedade para um pseudónimo
> - Determinar qual o efeito a utilizar
> - Compor a definição de política

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="identify-requirements"></a>Identificar requisitos

Antes de criar a definição de política, é importante entender a intenção da política. Para este tutorial, usaremos um requisito comum de segurança da empresa como objetivo para ilustrar os passos envolvidos:

- Cada conta de armazenamento deve ser ativada para HTTPS
- Cada conta de armazenamento deve ser desativada para HTTP

Os seus requisitos devem identificar claramente tanto os estados de "ser" como os "não ser".

Embora tenhamos definido o estado esperado do recurso, ainda não definimos o que queremos fazer com recursos não conformes. A Política Azure apoia uma série de [efeitos](../concepts/effects.md). Para este tutorial, vamos definir a exigência do negócio como prevenção da criação de recursos se não estiverem em conformidade com as regras do negócio. Para atingir este objetivo, usaremos o efeito [Deny.](../concepts/effects.md#deny) Queremos também a opção de suspender a política para atribuições específicas. Como tal, usaremos o efeito [de deficientes](../concepts/effects.md#disabled) e faremos do efeito um [parâmetro](../concepts/definition-structure.md#parameters) na definição de política.

## <a name="determine-resource-properties"></a>Determinar propriedades de recursos

Com base na exigência do negócio, o recurso Azure para auditar com a Azure Policy é uma conta de armazenamento. No entanto, não sabemos as propriedades a usar na definição de política. A Azure Policy avalia contra a representação JSON do recurso, por isso precisamos entender as propriedades disponíveis nesse recurso.

Existem muitas maneiras de determinar as propriedades de um recurso Azure. Vamos olhar cada um para este tutorial:

- Extensão do Azure Policy para o VS Code
- Modelos de gestor de recursos Azure (modelos ARM)
  - Exportar recurso existente
  - Experiência de criação
  - Modelos quickstart (GitHub)
  - Documentos de referência do modelo
- Explorador de Recursos do Azure

### <a name="view-resources-in-vs-code-extension"></a>Ver recursos na extensão do Código VS

A [extensão do Código VS](../how-to/extension-for-vscode.md#search-for-and-view-resources) pode ser usada para navegar recursos no seu ambiente e ver as propriedades do Gestor de Recursos em cada recurso.

### <a name="arm-templates"></a>Modelos de ARM

Existem várias formas de olhar para um [modelo de Gestor de Recursos](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) que inclui a propriedade que você está procurando gerir.

#### <a name="existing-resource-in-the-portal"></a>Recurso existente no portal

A forma mais simples de encontrar propriedades é olhar para um recurso existente do mesmo tipo. Os recursos já configurados com a configuração que pretende impor também fornecem o valor para comparar.
Veja a página do **modelo de exportação** (em **Definições)** no portal Azure para esse recurso específico.

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="Screenshot da página do modelo de exportação num recurso existente no portal Azure." border="false":::

Fazê-lo para uma conta de armazenamento revela um modelo semelhante a este exemplo:

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

Sob **propriedades** é um valor chamado **suportesHttpsTrafficOnly** definido para **falso**. Esta propriedade parece ser a propriedade que procuramos. Além disso, o **tipo** de recurso é **Microsoft.Storage/storageAcounts**. O tipo permite limitar a política apenas a recursos deste tipo.

#### <a name="create-a-resource-in-the-portal"></a>Criar um recurso no portal

Outra forma através do portal é a experiência de criação de recursos. Ao criar uma conta de armazenamento através do portal, uma opção sob o separador **Avançado** é **necessária transferência de segurança**. Esta propriedade tem opções _desativadas_ e _ativadas._ O ícone de informação tem texto adicional que confirma que esta opção é provavelmente a propriedade que queremos. No entanto, o portal não nos diz o nome da propriedade neste ecrã.

No **separador 'Rever +' criar,** um link está na parte inferior da página para **descarregar um modelo para automatização.** A seleção do link abre o modelo que cria o recurso que configuramos. Neste caso, vemos duas peças-chave de informação:

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

Esta informação diz-nos o tipo de propriedade e também confirma **o suporteHttpsTrafficOnly** é a propriedade que procuramos.

#### <a name="quickstart-templates-on-github"></a>Modelos de quickstart no GitHub

Os [modelos de arranque rápido do Azure](https://github.com/Azure/azure-quickstart-templates) no GitHub têm centenas de modelos ARM construídos para diferentes recursos. Estes modelos podem ser uma ótima maneira de encontrar a propriedade de recursos que você procura. Algumas propriedades podem parecer o que procura, mas controle outra coisa.

#### <a name="resource-reference-docs"></a>Documentos de referência de recursos

Para validar **suportesHttpsTrafficOnly** é propriedade correta, verifique a referência do modelo ARM para o recurso da [conta de armazenamento](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) no fornecedor de armazenamento. O objeto de propriedades tem uma lista de parâmetros válidos. A seleção da ligação [de objetos StorageAccountCountCountPropertiesCreateParameters](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) mostra uma tabela de propriedades aceitáveis. **supportsHttpsTrafficOnly** está presente e a descrição corresponde ao que procuramos para satisfazer os requisitos de negócio.

### <a name="azure-resource-explorer"></a>Explorador de Recursos do Azure

Outra forma de explorar os seus recursos Azure é através do [Azure Resource Explorer](https://resources.azure.com) (Preview). Esta ferramenta utiliza o contexto da sua subscrição, pelo que tem de autenticar o website com as suas credenciais Azure. Uma vez autenticado, pode navegar por fornecedores, subscrições, grupos de recursos e recursos.

Localize um recurso de conta de armazenamento e olhe para as propriedades. Vemos a propriedade **de suportehttpsTrafficOnly** aqui também. Selecionando o **separador Documentação,** vemos que a descrição da propriedade corresponde ao que encontramos nos documentos de referência anteriormente.

## <a name="find-the-property-alias"></a>Encontre o pseudónimo da propriedade

Identificamos a propriedade de recursos, mas precisamos mapear essa propriedade para [um pseudónimo.](../concepts/definition-structure.md#aliases)

Há algumas maneiras de determinar os pseudónimos de um recurso Azure. Vamos olhar cada um para este tutorial:

- Extensão do Azure Policy para o VS Code
- CLI do Azure
- Azure PowerShell
- Azure Resource Graph

### <a name="get-aliases-in-vs-code-extension"></a>Obtenha pseudónimos na extensão do Código VS

A extensão da Política Azure para a extensão do Código VS facilita a navegação dos seus recursos e [descobre pseudónimos.](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties)

> [!NOTE]
> A extensão do Código VS expõe apenas as propriedades do modo Gestor de Recursos e não apresenta nenhumas propriedades [do modo Fornecedor de Recursos.](../concepts/definition-structure.md#mode)

### <a name="azure-cli"></a>CLI do Azure

No Azure CLI, o `az provider` grupo de comando é usado para procurar pseudónimos de recursos. Vamos filtrar o espaço de nomes **microsoft.Storage** com base nos detalhes que obtivemos sobre o recurso Azure anteriormente.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Nos resultados, vemos um pseudónimo suportado pelas contas de armazenamento denominadas **suportesHttpsTrafficOnly**. Esta existência deste pseudónimo significa que podemos escrever a política para fazer cumprir os nossos requisitos comerciais!

### <a name="azure-powershell"></a>Azure PowerShell

Em Azure PowerShell, o `Get-AzPolicyAlias` cmdlet é usado para procurar pseudónimos de recursos. Vamos filtrar o espaço de nomes **microsoft.Storage** com base nos detalhes que obtivemos sobre o recurso Azure anteriormente.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Tal como o Azure CLI, os resultados mostram um pseudónimo suportado pelas contas de armazenamento denominadas **supportsHttpsTrafficOnly**.

### <a name="azure-resource-graph"></a>Azure Resource Graph

[Azure Resource Graph](../../resource-graph/overview.md) é um serviço que fornece outro método para encontrar propriedades de recursos Azure. Aqui está uma consulta de amostra para olhar para uma única conta de armazenamento com Gráfico de Recursos:

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1"
```

Os resultados são semelhantes ao que vemos nos modelos ARM e através do Azure Resource Explorer. No entanto, os [resultados](../concepts/definition-structure.md#aliases) do Azure Resource Graph também podem incluir detalhes do pseudónimo _projetando_ a matriz de _pseudónimos:_

```kusto
Resources
| where type=~'microsoft.storage/storageaccounts'
| limit 1
| project aliases
```

```azurecli-interactive
az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
```

Aqui está a saída exemplo de uma conta de armazenamento para pseudónimos:

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

O Azure Resource Graph pode ser usado através da [Cloud Shell,](https://shell.azure.com)tornando-o uma forma rápida e fácil de explorar as propriedades dos seus recursos.

## <a name="determine-the-effect-to-use"></a>Determinar o efeito de utilização

Decidir o que fazer com os seus recursos não conformes é quase tão importante como decidir o que avaliar em primeiro lugar. Cada resposta possível a um recurso não conforme é chamada de [efeito](../concepts/effects.md). O efeito controla se o recurso não conforme for registado, bloqueado, tiver dados anexados ou tiver uma implantação associada a ele para reencaminhar o recurso num estado conforme.

Para o nosso exemplo, Deny é o efeito que queremos, pois não queremos recursos não conformes criados no nosso ambiente Azure. A auditoria é uma boa primeira escolha para um efeito político para determinar qual é o impacto de uma política antes de defini-la para Deny. Uma maneira de facilitar a alteração do efeito por atribuição é parametrizar o efeito. Consulte [os parâmetros](#parameters) abaixo para saber como.

## <a name="compose-the-definition"></a>Compor a definição

Agora temos os detalhes da propriedade e pseudónimo para o que planejamos gerir. Em seguida, vamos compor a própria regra da política. Se ainda não está familiarizado com a linguagem política, a estrutura de [definição de políticas](../concepts/definition-structure.md) de referência para estruturar a definição de política. Aqui está um modelo vazio do que uma definição de política parece:

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

Os três primeiros componentes são metadados de política. Estes componentes são fáceis de fornecer valores, uma vez que sabemos para que estamos a criar a regra. [O modo](../concepts/definition-structure.md#mode) é principalmente sobre tags e localização de recursos. Uma vez que não precisamos de limitar a avaliação aos recursos que suportam etiquetas, usaremos _todo_ o valor para o **modo.**

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Parâmetros

Embora não tenhamos usado um parâmetro para alterar a avaliação, queremos usar um parâmetro para permitir alterar o **efeito** para a resolução de problemas. Vamos definir um parâmetro **de efeitoType** e limitá-lo apenas a **Deny** e **Desativado.** Estas duas opções correspondem às nossas necessidades de negócio. O bloco de parâmetros acabados parece este exemplo:

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

### <a name="policy-rule"></a>Regra política

A composição da [regra política](../concepts/definition-structure.md#policy-rule) é o passo final na construção da nossa definição de política personalizada. Identificámos duas declarações para testar:

- Que o **tipo** de conta de armazenamento é **Microsoft.Storage/storageA contas**
- Que a conta de armazenamento **suportaHttpsTrafficOnly** não é **verdade**

Uma vez que precisamos que ambas as declarações sejam **verdadeiras,** usaremos o [operador lógico.](../concepts/definition-structure.md#logical-operators) Passaremos o **parâmetro de efeitoType** para o efeito em vez de fazer uma declaração estática. A nossa regra acabada parece este exemplo:

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

### <a name="completed-definition"></a>Definição completa

Com as três partes da política definidas, aqui está a nossa definição completa:

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

A definição completa pode ser usada para criar uma nova política. Portal e cada SDK (Azure CLI, Azure PowerShell e REST API) aceitam a definição de diferentes maneiras, por isso reveja os comandos para cada um validar o uso correto. Em seguida, atribua-o, utilizando o efeito parametrizado, a recursos adequados para gerir a segurança das suas contas de armazenamento.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já acabou de trabalhar com recursos deste tutorial, use os seguintes passos para eliminar qualquer uma das atribuições ou definições acima criadas:

1. Selecione **Definições** (ou **Atribuições** se estiver a tentar apagar uma atribuição) em **Autoria** no lado esquerdo da página Política Azure.

1. Procure a nova definição de iniciativa ou de política (ou atribuição) que acabou de remover.

1. Clique com o botão direito do rato na linha ou selecione as reticências no fim da definição (ou atribuição) e selecione **Eliminar definição** (ou **Eliminar atribuição**).

## <a name="review"></a>Revisão

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Identificou os seus requisitos de negócio
> - Mapeado cada requisito para uma propriedade de recursos Azure
> - Mapeou a propriedade para um pseudónimo
> - Determinou o efeito de usar
> - Compôs a definição de política

## <a name="next-steps"></a>Passos seguintes

Em seguida, use a sua definição de política personalizada para criar e atribuir uma política:

> [!div class="nextstepaction"]
> [Criar e atribuir uma definição de política](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
