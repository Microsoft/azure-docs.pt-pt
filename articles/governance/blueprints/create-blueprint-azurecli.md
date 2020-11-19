---
title: 'Quickstart: Criar uma planta com Azure CLI'
description: Neste arranque rápido, utiliza-se a Azure Blueprints para criar, definir e implantar artefactos utilizando o Azure CLI.
ms.date: 10/14/2020
ms.topic: quickstart
ms.openlocfilehash: 88ccd3fe6271a79d201007954e7997b68d2dc69b
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920564"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-azure-cli"></a>Quickstart: Definir e Atribuir uma Planta Azure com Azure CLI

Aprender a criar e atribuir plantas permite que a definição de padrões comuns desenvolva configurações reutilizáveis e rapidamente implantáveis com base em modelos de Gestor de Recursos Azure (modelos ARM), política, segurança e muito mais. Neste tutorial, vai aprender a utilizar o Azure Blueprints para realizar algumas das tarefas comuns relacionadas com a criação, publicação e atribuição de um esquema na sua organização, tais como:

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-blueprint-extension"></a>Adicione a extensão Blueprint

Para permitir que o Azure CLI gere definições e atribuições de projetos, a extensão deve ser adicionada.
Esta extensão funciona onde quer que a CLI do Azure possa ser utilizada, incluindo [bash no Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (autónomo e no portal), a [imagem do Docker da CLI do Azure](https://hub.docker.com/_/microsoft-azure-cli), ou instalada localmente.

1. Verifique se o último Azure CLI está instalado (pelo menos **2.0.76**). Se não estiver ainda instalado, siga [estas instruções](/cli/azure/install-azure-cli-windows).

1. No ambiente da CLI do Azure à escolha, importe-a com o seguinte comando:

   ```azurecli-interactive
   # Add the Blueprint extension to the Azure CLI environment
   az extension add --name blueprint
   ```

1. Validar que a extensão foi instalada e é a versão prevista (pelo menos **0.1.0**):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for extension options
   az blueprint -h
   ```

## <a name="create-a-blueprint"></a>Criar um esquema

O primeiro passo na definição de um padrão de conformidade é compor um esquema a partir dos recursos disponíveis. Vamos criar um esquema com o nome "MyBlueprint" para configurar as atribuições de função e política para a subscrição. Em seguida, adicionaremos um grupo de recursos, um modelo ARM, e uma atribuição de papel no grupo de recursos.

> [!NOTE]
> Ao utilizar o Azure CLI, o objeto _de planta_ é criado primeiro. Para cada _artefacto_ a adicionar que tenha parâmetros, os parâmetros precisam de ser definidos com antecedência no _esquema_ inicial.

1. Crie o objeto _esquema_ inicial. O parâmetro **dos parâmetros** requer um ficheiro JSON que inclui todos os parâmetros de nível da planta. Os parâmetros são definidos durante a atribuição e utilizados pelos artefactos adicionados nos passos posteriores.

   - Ficheiro JSON - blueprintparms.jsem

     ```json
     {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
                "Standard_LRS",
                "Standard_GRS",
                "Standard_ZRS",
                "Premium_LRS"
            ],
            "metadata": {
                "displayName": "storage account type.",
                "description": null
            }
        },
        "tagName": {
            "type": "string",
            "metadata": {
                "displayName": "The name of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "tagValue": {
            "type": "string",
            "metadata": {
                "displayName": "The value of the tag to provide the policy assignment.",
                "description": null
            }
        },
        "contributors": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                "strongType": "PrincipalId"
            }
        },
        "owners": {
            "type": "array",
            "metadata": {
                "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                "strongType": "PrincipalId"
            }
        }
     }
     ```

   - Comando Azure CLI

     ```azurecli-interactive
     # Login first with az login if not using Cloud Shell

     # Create the blueprint object
     az blueprint create \
        --name 'MyBlueprint' \
        --description 'This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.' \
        --parameters blueprintparms.json
     ```

     > [!NOTE]
     > Utilize o nome de ficheiro _blueprint.jsquando_ importar as definições do seu projeto.
     > Este nome de ficheiro é usado quando se chama [az blueprint import](/cli/azure/ext/blueprint/blueprint#ext_blueprint_az_blueprint_import).

     O objeto blueprint é criado na subscrição padrão por padrão por padrão. Para especificar o grupo de gestão, utilize **o grupo de gestão de parâmetros**. Para especificar a subscrição, utilize **a subscrição do** parâmetro .

1. Adicione o grupo de recursos para os artefactos de armazenamento à definição.

   ```azurecli-interactive
   az blueprint resource-group add \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'storageRG' \
      --description 'Contains the resource template deployment and a role assignment.'
   ```

1. Adicione a atribuição de função no momento da subscrição. No exemplo abaixo, as identidades do principal a quem foi concedida a função especificada estão configuradas para um parâmetro que é definido durante a atribuição do esquema. Este exemplo utiliza o _papel de Contribuinte_ incorporado com um GUID de `b24988ac-6180-42a0-ab88-20f7382dd24c` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleContributor' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c' \
      --principal-ids "[parameters('contributors')]"
   ```

1. Adicione a atribuição de política no momento da subscrição. Este exemplo utiliza a etiqueta Apply e o _seu valor padrão para grupos_ de recursos integrados com um GUID de `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Ficheiro JSON - artifacts\policyTags.jsem

     ```json
     {
        "tagName": {
           "value": "[parameters('tagName')]"
        },
        "tagValue": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Comando Azure CLI

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply tag and its default value to resource groups' \
        --description 'Apply tag and its default value to resource groups' \
        --parameters artifacts\policyTags.json
     ```

1. Adicione outra atribuição de política para a etiqueta de Armazenamento (reutilizando o parâmetro _storageAccountType_) na subscrição. Este artefacto de atribuição de política adicional demonstra que um parâmetro definido no esquema é utilizável por mais do que um artefacto. No exemplo, o **storageAccountType** é utilizado para definir uma etiqueta no grupo de recursos. Este valor apresenta informações sobre a conta de armazenamento que é criada no passo seguinte. Este exemplo utiliza a etiqueta Apply e o _seu valor padrão para grupos_ de recursos integrados com um GUID de `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71` .

   - Ficheiro JSON - artifacts\policyStorageTags.jsem

     ```json
     {
        "tagName": {
           "value": "StorageType"
        },
        "tagValue": {
           "value": "[parameters('storageAccountType')]"
        }
     }
     ```

   - Comando Azure CLI

     ```azurecli-interactive
     az blueprint artifact policy create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'policyStorageTags' \
        --policy-definition-id '/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71' \
        --display-name 'Apply storage tag to resource group' \
        --description 'Apply storage tag and the parameter also used by the template to resource groups' \
        --parameters artifacts\policyStorageTags.json
     ```

1. Adicione um modelo no grupo de recursos. O parâmetro **do modelo** para um modelo ARM inclui os componentes JSON normais do modelo. O modelo também reutiliza os parâmetros de esquema **storageAccountType**, **tagName** e **tagValue** ao passar cada um para o modelo. Os parâmetros da planta estão disponíveis para o modelo usando **parâmetros** e dentro do modelo JSON que o par de valores-chave é usado para injetar o valor. Os nomes de parâmetros da planta e do modelo podem ser os mesmos.

   - Arquivo de modelo JSON ARM - artifacts\templateStorage.jsem

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - Arquivo de parâmetro de modelo JSON ARM - artifacts\templateStorageParams.jsem

     ```json
     {
        "storageAccountTypeFromBP": {
           "value": "[parameters('storageAccountType')]"
        },
        "tagNameFromBP": {
           "value": "[parameters('tagName')]"
        },
        "tagValueFromBP": {
           "value": "[parameters('tagValue')]"
        }
     }
     ```

   - Comando Azure CLI

     ```azurecli-interactive
     az blueprint artifact template create \
        --blueprint-name 'MyBlueprint' \
        --artifact-name 'templateStorage' \
        --template artifacts\templateStorage.json \
        --parameters artifacts\templateStorageParams.json \
        --resource-group-art 'storageRG'
     ```

1. Adicione atribuição de função no grupo de recursos. À semelhança da entrada de atribuição de função anterior, o exemplo abaixo utiliza o identificador de definição para a função **Proprietário** e fornece-lhe um parâmetro diferente do esquema. Este exemplo utiliza a função incorporada _do Proprietário_ com um GUID de `8e3af657-a8ff-443c-a75c-2fe8c4bcb635` .

   ```azurecli-interactive
   az blueprint artifact role create \
      --blueprint-name 'MyBlueprint' \
      --artifact-name 'roleOwner' \
      --role-definition-id '/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635' \
      --principal-ids "[parameters('owners')]" \
      --resource-group-art 'storageRG'
   ```

## <a name="publish-a-blueprint"></a>Publicar um esquema

Agora que os artefactos foram adicionados ao esquema, é altura de publicá-lo. A publicação disponibiliza-o para atribuir a uma subscrição.

```azurecli-interactive
az blueprint publish --blueprint-name 'MyBlueprint' --version '{BlueprintVersion}'
```

O valor para `{BlueprintVersion}` é uma cadeia de letras, números e hífenes (sem espaços ou outros carateres especiais) com um comprimento máximo de 20 carateres. Utilize algo único e informativo como **v20200605-135541**.

## <a name="assign-a-blueprint"></a>Atribuir um esquema

Uma vez que uma planta é publicada usando o CLI Azure, é atribuível a uma subscrição. Atribua o esquema que criou a uma das subscrições na hierarquia do grupo de gestão. Se a planta for guardada para uma subscrição, só pode ser atribuída a essa subscrição. O parâmetro **do nome da planta** especifica a planta a atribuir. Para fornecer o nome, a localização, a identidade, a fechadura e os parâmetros de plantação, utilize os parâmetros Azure CLI correspondentes no `az blueprint assignment create` comando ou forneça-os nos **parâmetros** ficheiro JSON.

1. Execute a implementação do esquema, atribuindo-o a uma subscrição. Como os **parâmetros dos contribuintes** e **proprietários** requerem uma série de objectIds dos principais para ser concedido a atribuição de funções, use [a Azure Ative Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) para recolher os objectIds para uso nos **parâmetros** para os seus próprios utilizadores, grupos ou principais serviços.

   - Ficheiro JSON - blueprintAssignment.jsem

     ```json
     {
        "storageAccountType": {
            "value": "Standard_GRS"
        },
        "tagName": {
            "value": "CostCenter"
        },
        "tagValue": {
            "value": "ContosoIT"
        },
        "contributors": {
            "value": [
                "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                "38833b56-194d-420b-90ce-cff578296714"
            ]
        },
        "owners": {
            "value": [
                "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                "316deb5f-7187-4512-9dd4-21e7798b0ef9"
            ]
        }
     }
     ```

   - Comando Azure CLI

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

   - Identidade gerida atribuída pelo utilizador

     Uma atribuição de plantas também pode usar uma [identidade gerida atribuída pelo utilizador](../../active-directory/managed-identities-azure-resources/overview.md).
     Neste caso, o parâmetro **do tipo de identidade** é definido para _UserAssigned_ e o parâmetro **de identidades atribuídas ao utilizador** especifica a identidade. `{userIdentity}`Substitua-o pelo nome da sua identidade gerida atribuída ao utilizador.

     ```azurecli-interactive
     az blueprint assignment create \
        --name 'assignMyBlueprint' \
        --location 'westus' \
        --identity-type UserAssigned \
        --user-assigned-identities {userIdentity} \
        --resource-group-value artifact_name=storageRG name=StorageAccount location=eastus \
        --parameters blueprintAssignment.json
     ```

     A **identidade gerida atribuída pelo utilizador** pode estar em qualquer grupo de subscrição e recursos a que o utilizador que atribui a planta tem permissões.

     > [!IMPORTANT]
     > A Azure Blueprints não gere a identidade gerida atribuída pelo utilizador. Os utilizadores são responsáveis por atribuir funções e permissões suficientes ou a atribuição do projeto falhará.

## <a name="clean-up-resources"></a>Limpar os recursos

### <a name="unassign-a-blueprint"></a>Anular a atribuição de um esquema

Pode remover um esquema de uma subscrição. A remoção é, muitas vezes, feita quando os recursos de artefacto já não são precisos. Quando um esquema é removido, os artefactos atribuídos como parte desse esquema são deixados para trás. Para remover uma atribuição de planta, utilize o `az blueprint assignment delete` comando:

```azurecli-interactive
az blueprint assignment delete --name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criaste, atribuiste e removeste uma planta com o Azure CLI. Para saber mais sobre a Azure Blueprints, continue para o artigo do ciclo de vida do projeto.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](./concepts/lifecycle.md)