---
title: Detalhes da estrutura de definição de iniciativa
description: Descreve como as definições de iniciativa política são usadas para agrupar definições de políticas para implantação de recursos Azure na sua organização.
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: edd3f25dd528d1a718c9287c9f30988b87fb73e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587224"
---
# <a name="azure-policy-initiative-definition-structure"></a>Estrutura de definição de iniciativa política Azure

As iniciativas permitem-lhe agrupar várias definições de políticas relacionadas para simplificar atribuições e gestão porque trabalha com um grupo como um único item. Por exemplo, pode agrupar definições de políticas de marcação relacionadas numa única iniciativa. Em vez de atribuir cada política individualmente, aplica-se a iniciativa.

Usas o JSON para criar uma definição de iniciativa política. A definição de iniciativa política contém elementos para:

- nome de exibição
- descrição
- do IdP
- parâmetros
- definições de política
- grupos de política (esta propriedade faz parte da [funcionalidade conformidade regulamentar (pré-visualização)](./regulatory-compliance.md)

O exemplo a seguir ilustra como criar uma iniciativa para o manuseamento de duas tags: `costCenter` e `productName` . Utiliza duas políticas incorporadas para aplicar o valor da etiqueta padrão.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy incorporados e padrões estão em [amostras da Política Azure](../samples/index.md).

## <a name="metadata"></a>Metadados

A propriedade opcional `metadata` armazena informações sobre a definição de iniciativa política.
Os clientes podem definir quaisquer propriedades e valores úteis à sua organização `metadata` em. No entanto, existem algumas propriedades _comuns_ usadas pela Azure Policy e em incorporados.

### <a name="common-metadata-properties"></a>Propriedades comuns de metadados

- `version` (cadeia): Rastreia detalhes sobre a versão do conteúdo de uma definição de iniciativa política.
- `category` (cadeia): Determina em que categoria no portal Azure é apresentada a definição de política.

  > [!NOTE]
  > Para uma iniciativa [de Conformidade Regulamentar,](./regulatory-compliance.md) `category` deve ser a Conformidade **Regulamentar.**

- `preview` (boolean: bandeira verdadeira ou falsa para se a definição de iniciativa política for _a pré-visualização_.
- `deprecated` (boolean: verdadeira ou falsa bandeira para se a definição de iniciativa política tiver sido marcada como _precída_.

> [!NOTE]
> O serviço Azure Policy `version` utiliza, `preview` e propriedades para transmitir `deprecated` o nível de mudança a uma definição ou iniciativa e iniciativa de política incorporada. O formato `version` de: `{Major}.{Minor}.{Patch}` . Estados específicos, tais como _prevadidos_ ou _pré-visualização,_ são anexados à `version` propriedade ou em outra propriedade como um **booleano**. Para obter mais informações sobre a forma como as versões Azure Policy incorporadas, consulte [a versão incorporada.](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md)

## <a name="parameters"></a>Parâmetros

Os parâmetros ajudam a simplificar a sua gestão de políticas reduzindo o número de definições políticas. Pense em parâmetros como os campos numa forma , `name` `address` , , , `city` `state` . Estes parâmetros mantêm-se sempre os mesmos, no entanto os seus valores mudam com base no indivíduo que preenche o formulário.
Os parâmetros funcionam da mesma forma na construção de iniciativas políticas. Ao incluir parâmetros numa definição de iniciativa política, pode reutilizar esse parâmetro nas políticas incluídas.

> [!NOTE]
> Uma vez atribuída uma iniciativa, os parâmetros de nível initativo não podem ser alterados. Devido a isso, a recomendação é definir um **padrãoValue** ao definir o parâmetro.

### <a name="parameter-properties"></a>Propriedades de parâmetros

Um parâmetro tem as seguintes propriedades que são usadas na definição de iniciativa política:

- `name`: O nome do seu parâmetro. Utilizado pela `parameters` função de implantação dentro da regra da política. Para obter mais informações, consulte [utilizando um valor de parâmetro.](#passing-a-parameter-value-to-a-policy-definition)
- `type`: Determina se o parâmetro é uma **corda,** **matriz,** **objeto,** **boolean,** **inteiro,** **boia** ou **data**.
- `metadata`: Define subpropriedades utilizadas principalmente pelo portal Azure para apresentar informações fáceis de utilizar:
  - `description`: A explicação para o que o parâmetro é usado. Pode ser usado para fornecer exemplos de valores aceitáveis.
  - `displayName`: O nome amigável indicado no portal para o parâmetro.
  - `strongType`: (Opcional) Utilizado ao atribuir a definição de política através do portal. Fornece uma lista de conscientes de contexto. Para obter mais informações, consulte [o StrongType](#strongtype).
- `defaultValue`: (Opcional) Define o valor do parâmetro numa atribuição se não for dado qualquer valor.
- `allowedValues`: (Opcional) Fornece uma matriz de valores que o parâmetro aceita durante a atribuição.

Como exemplo, poderia definir uma definição de iniciativa política para limitar as localizações dos recursos nas várias definições políticas incluídas. Poderia ser permitido um parâmetro para essa definição de iniciativa **política.** O parâmetro está então disponível para cada definição de política incluída e definido durante a atribuição da iniciativa política.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Passando um valor de parâmetro para uma definição de política

Declara quais os parâmetros de iniciativa a que passa, que incluíam definições políticas na definição de definições de [definições](#policy-definitions) de políticas. Embora o nome do parâmetro possa ser o mesmo, usar nomes diferentes nas iniciativas do que nas definições políticas simplifica a legibilidade do código.

Por exemplo, o parâmetro de iniciativa **init_allowedLocations** definido anteriormente pode ser passado para várias definições políticas incluídas e seus parâmetros, **sql_locations** e **vm_locations,** como este:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Esta amostra refere o **init_allowedLocations** parâmetro que foi demonstrado em [propriedades de parâmetros](#parameter-properties).

### <a name="strongtype"></a>ForteType

Dentro da `metadata` propriedade, você pode usar **o StrongType** para fornecer uma lista multi-selecionada de opções dentro do portal Azure. **StrongType** pode ser um tipo de _recurso_ suportado ou um valor permitido. Para determinar se um _tipo de recurso_ é válido para o **StrongType,** utilize [o Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Alguns tipos de recursos não devolvidos pela **Get-AzResourceProvider** são suportados. Estes tipos de recursos são:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

O tipo de não recursos permitiu valores para **o Tipo forte** são:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Definições de política

A `policyDefinitions` parte da definição de iniciativa é um _conjunto_ de definições políticas existentes incluídas na iniciativa. Como mencionado na [Passagem de um valor de parâmetro para uma definição de política,](#passing-a-parameter-value-to-a-policy-definition)este imóvel é onde os parâmetros de iniciativa são [passados](#parameters) para a definição de política.

### <a name="policy-definition-properties"></a>Propriedades de definição de política

Cada elemento _de matriz_ que representa uma definição de política tem as seguintes propriedades:

- `policyDefinitionId` (cadeia): O ID da definição de política personalizada ou incorporada para incluir.
- `policyDefinitionReferenceId` Um nome curto para a definição de política incluída.
- `parameters`: (Opcional) Os pares de nome/valor para passar um parâmetro de iniciativa para a definição de política incluída como propriedade nessa definição política. Para obter mais informações, consulte [parâmetros.](#parameters)
- `groupNames` (matriz de cordas): (Opcional) O grupo da definição de política é membro. Para obter mais informações, consulte [os grupos de política.](#policy-definition-groups)

Aqui está um exemplo `policyDefinitions` disso tem duas definições políticas incluídas que são aprovadas cada uma no mesmo parâmetro de iniciativa:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Grupos de definição de políticas

As definições políticas numa definição de iniciativa podem ser agrupadas e categorizadas. A funcionalidade de [Conformidade Regulamentar](./regulatory-compliance.md) (pré-visualização) da Azure Policy utiliza esta propriedade para agrupar definições em **domínios** **de controlo** e conformidade . Esta informação é definida na propriedade `policyDefinitionGroups` _da matriz._ Detalhes adicionais de agrupamento podem ser encontrados num objeto **policyMetadata** criado pela Microsoft. Para obter informações, consulte [os objetos de metadados.](#metadata-objects)

### <a name="policy-definition-groups-parameters"></a>Parâmetros de grupos de definição de política

Cada elemento _de matriz_ deve ter ambas `policyDefinitionGroups` as seguintes propriedades:

- `name` (corda) \[ requer \] : O nome curto para o **grupo**. Na Conformidade Regulamentar, o **controlo.** O valor deste imóvel é utilizado `groupNames` `policyDefinitions` por.
- `category` A hierarquia a que pertence o grupo. Na Conformidade Regulamentar, o domínio de **conformidade** do controlo.
- `displayName` (corda): O nome amigável para o **grupo** ou **controlo**. Usado pelo portal.
- `description` (cadeia): uma descrição do que o **grupo** ou o **controlo** cobre.
- `additionalMetadataId` (cadeia): a localização do objeto [policyMetadata](#metadata-objects) que tem detalhes adicionais sobre o domínio de **controlo** e **conformidade**.

  > [!NOTE]
  > Os clientes podem apontar para um objeto [de política existenteMetadata.](#metadata-objects) No entanto, estes objetos são _apenas de leitura_ e criados apenas pela Microsoft.

Um exemplo da `policyDefinitionGroups` propriedade da definição de iniciativa incorporada do NIST é o seguinte:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Objetos de metadados

Os incorporados de Conformidade Regulamentar criados pela Microsoft têm informações adicionais sobre cada controlo.
Esta informação é:

- Apresentado no portal Azure sobre a visão geral de um **controlo** sobre uma iniciativa de Conformidade Regulamentar.
- Disponível via REST API. Consulte o `Microsoft.PolicyInsights` fornecedor de recursos e o grupo de [operações policyMetadata](/rest/api/policy/policymetadata/getresource).
- Disponível via Azure CLI. Consulte o comando [de metadados de política az.](/cli/azure/policy/metadata)

> [!IMPORTANT]
> Os objetos de metadados para conformidade regulamentar são _apenas de leitura_ e não podem ser criados pelos clientes.

Os metadados para um agrupamento de políticas têm as seguintes informações no `properties` nó:

- `metadataId`: O **ID de controlo** a que o agrupamento diz respeito.
- `category` (obrigatório): O **domínio de conformidade** a que o **controlo** pertence.
- `title` (obrigatório): O nome amigável do **ID de controlo**.
- `owner` (obrigatório): Identifica quem tem a responsabilidade pelo controlo em Azure: _Cliente,_ _Microsoft,_ _Shared_.
- `description`: Informações adicionais sobre o controlo.
- `requirements`: Pormenores sobre a responsabilidade da implementação do controlo.
- `additionalContentUrl`: Uma ligação a mais informações sobre o controlo. Esta propriedade é tipicamente uma ligação com a secção de documentação que cobre este controlo na norma de conformidade.

Abaixo está um exemplo do objeto **de políticaMetadata.** Este exemplo de metadados pertence ao comando _NIST SP 800-53 R4 AC-1._

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Passos seguintes

- Ver a estrutura de [definição](./definition-structure.md)
- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
