---
title: 'Tutorial: Build policies to enforce compliance'
description: In this tutorial, you use policies to enforce standards, control costs, maintain security, and impose enterprise wide design principles.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 75a1d892a88f2b5bbdbec2a1b8d525245bb1e86f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482325"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Create and manage policies to enforce compliance

Compreender como criar e gerir políticas no Azure é importante para manter a conformidade com os seus padrões empresariais e contratos de nível de serviço. Neste tutorial, vai aprender a utilizar o Azure Policy para realizar algumas das tarefas mais comuns relacionadas com a criação, atribuição e gestão de políticas na sua organização, tais como:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que criar no futuro
> - Criar e atribuir uma definição de iniciativa para controlar a conformidade de vários recursos
> - Resolver um recurso negado ou em não conformidade
> - Implementar uma nova política numa organização

Se gostaria de atribuir uma política para identificar o estado atual de conformidade dos seus recursos existentes, os artigos de início rápido mostram como fazê-lo.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="assign-a-policy"></a>Atribuir uma política

O primeiro passo para impor a conformidade com o Azure Policy consiste em atribuir uma definição de política. Uma definição de política define sob que condição é imposta uma política e qual o efeito a ter. Neste exemplo, atribua uma definição de política incorporada chamada *Requer a versão do SQL Server 12.0*, para impor a condição de que todas as bases de dados SQL Server têm de ser v12.0 para estarem em conformidade.

1. Go to the Azure portal to assign policies. Search for and select **Policy**.

   ![Search for Policy in the search bar](../media/create-and-manage/search-policy.png)

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   ![Select Assignments from Policy Overview page](../media/create-and-manage/select-assignments.png)

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Assign a policy definition from Assignments page](../media/create-and-manage/select-assign-policy.png)

1. On the **Assign Policy** page, select the **Scope** by selecting the ellipsis and selecting either a management group or subscription. Opcionalmente, selecione um grupo de recursos. Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Then select **Select** at the bottom of the **Scope** page.

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais, por isso deixe este campo em branco, por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. Pode filtrar o **Tipo** de definição de política para *Incorporado* para ver todas e ler as respetivas descrições.

1. Selecione **Requer a versão do SQL Server 12.0**. If you can't find it right away, type **require sql server** into the search box and then press ENTER or select out of the search box. Select **Select** at the bottom of the **Available Definitions** page once you have found and selected the policy definition.

   ![Use search filter to locate a policy](../media/create-and-manage/select-available-definition.png)

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Para este exemplo, deixe *Requer a versão do SQL Server 12.0*. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Assigned by** is automatically filled based on who is logged in. Este campo é opcional,por isso pode introduzir valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. This box _must_ be checked when the policy or initiative being assigned includes a policy with the [deployIfNotExists](../concepts/effects.md#deployifnotexists) effect. As the policy used for this tutorial doesn't, leave it blank. Para obter mais informações, consulte [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione **Atribuir**.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que atribuiu uma definição de política incorporada, pode fazer mais com o Azure Policy. Next, create a new custom policy to save costs by validating that VMs created in your environment can't be in the G series. Desta forma, sempre que um utilizador na sua organização tenta criar uma VM na série G, o pedido é negado.

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Definition page under Authoring group](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+ Definição de política** na parte superior da página. This button opens to the **Policy definition** page.

1. Introduza as seguintes informações:

   - O grupo de gestão ou a subscrição em que a definição de política é guardada. Selecione, utilizando o botão de reticências em **Localização da definição**.

     > [!NOTE]
     > Se pretender aplicar esta definição de política a diversas subscrições, a localização deverá ser um grupo de gestão que inclua as subscrições às quais atribui a política. O mesmo se aplica a uma definição de iniciativa.

   - O nome da definição de política - *Necessitar de SKUs de VM mais pequenas que a série G*
   - A descrição do que a definição de política é suposto fazer: *Esta definição de política impõe que todas as VMs criadas neste âmbito tenham SKUs mais pequenas que a série G para reduzir os custos.*
   - Escolha de entre as opções existentes (tais como _Computação_) ou crie uma nova categoria para esta definição de política.
   - Copie o seguinte código JSON e atualize-o de acordo com as suas necessidades com:
      - Os parâmetros de política.
      - As regras/condições de política, neste caso: o tamanho da SKU de VM igual à série G
      - O efeito de política, neste caso: **Negar**.

   Veja a seguir o aspeto que o JSON deverá ter. Cole o seu código revisto no portal do Azure.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   The *field* property in the policy rule must be one of the following values: Name, Type, Location, Tags, or an alias. Um exemplo de um alias poderá ser `"Microsoft.Compute/VirtualMachines/Size"`.

   Para ver mais exemplos do Azure Policy, veja [Exemplos do Azure Policy](../samples/index.md).

1. Selecione **Guardar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Criar uma definição de política com a API REST

You can create a policy with the REST API for Azure Policy Definitions. A API REST permite-lhe criar e eliminar definições de política, bem como obter informações sobre as definições existentes. Para criar uma definição de política, utilize o seguinte exemplo:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Incluir um corpo de pedido semelhante ao exemplo seguinte:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Criar uma definição de política com o PowerShell

Before proceeding with the PowerShell example, make sure you've installed the latest version of the Azure PowerShell Az module.

Pode criar uma definição de política ao utilizar o cmdlet `New-AzPolicyDefinition`.

Para criar uma definição de política a partir de um ficheiro, passe o caminho para o ficheiro. Para um ficheiro externo, utilize o seguinte exemplo:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Para um ficheiro local, utilize o seguinte exemplo:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Para criar uma definição de política com uma regra na mesma linha, utilize o seguinte exemplo:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

O resultado é armazenado num objeto `$definition`, que é utilizado durante a atribuição de política. O exemplo seguinte cria uma definição de política que inclui os parâmetros:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Ver definições de política com o PowerShell

Para ver todas as definições de política na sua subscrição, utilize o seguinte comando:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Devolve todas as definições de política disponíveis, incluindo políticas incorporadas. Cada política é devolvida no seguinte formato:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Criar uma definição de política com a CLI do Azure

Pode criar uma definição de política ao utilizar a CLI do Azure com o comando de definição de política. Para criar uma definição de política com uma regra na mesma linha, utilize o seguinte exemplo:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Ver definições de política com a CLI do Azure

Para ver todas as definições de política na sua subscrição, utilize o seguinte comando:

```azurecli-interactive
az policy definition list
```

Devolve todas as definições de política disponíveis, incluindo políticas incorporadas. Cada política é devolvida no seguinte formato:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Criar e atribuir uma definição de iniciativa

Com uma definição de iniciativa, pode agrupar várias definições de política para alcançar um objetivo mais abrangente. An initiative evaluates resources within scope of the assignment for compliance to the included policies. Para obter mais informações sobre as definições de iniciativa, veja [Descrição geral do Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Criar uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Select definition from the Definitions page](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+ Definição de Iniciativa** na parte superior da página para abrir a página **Definição de iniciativa**.

   ![Review initiative definition page](../media/create-and-manage/initiative-definition.png)

1. Utilize as reticências de **Localização da definição** para selecionar um grupo de gestão ou uma subscrição na qual armazenar a definição. Se a página anterior se destinava a um único grupo de gestão ou subscrição, a **Localização da definição** será automaticamente preenchida.

1. Introduza o **Nome** e a **Descrição** da iniciativa.

   This example validates that resources are in compliance with policy definitions about getting secure. O nome da iniciativa deverá ser **Proteger-se** e a definição da descrição: **Esta iniciativa foi criada para processar todas as definições de política associadas à proteção de recursos**.

1. Em **Categoria**, escolha de entre as opções existentes ou crie uma nova categoria.

1. Procure na lista de **Definições Disponíveis** (lado direito da página **Definição de iniciativa**) e selecione as definições de política que gostaria de adicionar a esta iniciativa. For the **Get secure** initiative, add the following built-in policy definitions by selecting the **+** next to the policy definition information or selecting a policy definition row and then the **+ Add** option in the details page:

   - Requer a Versão do SQL Server 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   After selecting the policy definition from the list, it's added under **Policies and Parameters**.

   ![Review initiative definition parameters](../media/create-and-manage/initiative-definition-2.png)

1. If a policy definition being added to the initiative has parameters, they're shown under the policy name in the **Policies and Parameters** area. O _valor_ pode ser definido como "Definir o valor' (hard-coded para todas as atribuições dessa iniciativa) ou "Usar parâmetro de iniciativa" (definido durante cada atribuição de iniciativa). If 'Set value' is selected, the drop-down to the right of _Values_ allows entering or selecting the value(s). Se “Usar Parâmetro de Iniciativa” estiver selecionado, será apresentada uma nova secção **Parâmetros de iniciativa** que lhe permite definir o parâmetro que será definido durante a atribuição de iniciativa. Os valores permitidos neste parâmetro de iniciativa podem restringir mais o que pode ser definido durante a atribuição de iniciativa.

   ![Change initiative definition parameters from allowed values](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > No caso de alguns parâmetros `strongType`, a lista de valores não pode ser determinada automaticamente. Nestes casos, um botão de reticências será apresentado à direita da linha do parâmetro. Selecting it opens the 'Parameter scope (&lt;parameter name&gt;)' page. Nesta página, selecione a subscrição a utilizar para fornecer as opções de valor. Este âmbito de parâmetro só é utilizado durante a criação da definição de iniciativa e não tem qualquer impacto na avaliação de políticas nem no âmbito da iniciativa quando atribuído.

1. Selecione **Guardar**.

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

1. Localize a definição de iniciativa **Proteger-se** que criou anteriormente e selecione-a. Selecione **Atribuir** na parte superior da página para abrir a página **Proteger-se: Atribuir iniciativa**.

   ![Assign a definition from Initiative definition page](../media/create-and-manage/assign-definition.png)

   You can also right-click on the selected row or select the ellipsis at the end of the row for a contextual menu. Em seguida, selecione **Atribuir**.

   ![Alternative options for an initiative](../media/create-and-manage/select-right-click.png)

1. Preencha a página **Proteger-se: Atribuir Iniciativa**, introduzindo as seguintes informações de exemplo. Pode utilizar as suas próprias informações.

   - Âmbito: o grupo de gestão ou a subscrição no qual guardou a iniciativa passa a ser a predefinição.
     Pode alterar o âmbito para atribuir a iniciativa a uma subscrição ou a um grupo de recursos na localização guardada.
   - Exclusões: configure os recursos no âmbito para evitar que a atribuição de iniciativa seja aplicada aos mesmos.
   - Definição de iniciativa e Nome da atribuição: Proteger-se (pré-preenchida como o nome da iniciativa que está a ser atribuída).
   - Descrição: esta atribuição de iniciativa está adaptada para impor este grupo de definições de política.
   - Atribuído por: automaticamente preenchido, tendo em conta a pessoa que iniciou a sessão. Este campo é opcional,por isso pode introduzir valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. This box _must_ be checked when the policy or initiative being assigned includes a policy with the [deployIfNotExists](../concepts/effects.md#deployifnotexists) effect. As the policy used for this tutorial doesn't, leave it blank. Para obter mais informações, consulte [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione **Atribuir**.

## <a name="check-initial-compliance"></a>Verificar conformidade inicial

1. Selecione **Conformidade** no lado esquerdo da página Azure Policy.

1. Locate the **Get Secure** initiative. It's likely still in _Compliance state_ of **Not started**.
   Select the initiative to get full details on the progress of the assignment.

   ![Initiative compliance page - evaluations not started](../media/create-and-manage/compliance-status-not-started.png)

1. Depois de concluir a atribuição da iniciativa, a página de conformidade é atualizada com o _Estado de conformidade_ de **Conforme**.

   ![Initiative compliance page- resources compliant](../media/create-and-manage/compliance-status-compliant.png)

1. Selecting on any policy on the initiative compliance page opens the compliance details page for the policy. Esta página apresenta os detalhes de conformidade ao nível dos recursos.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Excluir um recurso negado ou em não conformidade ao utilizar a Exclusão

Ao seguir o exemplo acima, depois de atribuir a definição de política para requerer a versão 12.0 do SQL Server, um servidor SQL criado com uma versão diferente da 12.0 seria negado. In this section, you walk through resolving a denied request to create a SQL server by creating an exclusion on a single resource group. A exclusão impede a imposição da política (ou iniciativa) nesse recurso.
No exemplo seguinte, qualquer versão do SQL Server é permitida num único grupo de recursos. Uma exclusão pode aplicar-se a uma subscrição ou a um grupo de recursos. Pode ainda restringir a exclusão a recursos individuais.

A deployment prevented by an assigned policy or initiative can be viewed in two locations:

- On the resource group targeted by the deployment: Select **Deployments** in the left side of the page, then select the **Deployment Name** of the failed deployment. O recurso que foi negado é apresentado com o estado _Proibido_. To determine the policy or initiative and assignment that denied the resource, select **Failed. Click here for details ->** on the Deployment Overview page.
  É apresentada uma janela no lado direito da página com as informações de erro. Under **Error Details** are the GUIDs of the related policy objects.

  ![Implementação negada pela atribuição de política](../media/create-and-manage/rg-deployment-denied.png)

- On the Azure Policy page: Select **Compliance** in the left side of the page and select the **Require SQL Server version 12.0** policy. Na página que abre, verá um aumento na contagem de **Negar**. Under the **Events** tab, you would also see who tried the deployment that was denied by the policy.

  ![Descrição geral de conformidade de uma política atribuída](../media/create-and-manage/compliance-overview.png)

In this example, Trent Baker, one of Contoso's Sr. Virtualization specialists, was doing required work. We need to grant Trent an exception, but we don't want the non-version 12.0 SQL servers in just any resource group. Criámos um novo grupo de recursos, **SQLServers_Excluded** e irá agora conceder uma exceção a esta atribuição de política.

### <a name="update-assignment-with-exclusion"></a>Atualizar a atribuição com uma exclusão

1. Selecione **Atribuições** em **Criação** no lado esquerdo da página Azure Policy.

1. Procure por todas as atribuições de política e abra a atribuição *Requer a Versão do SQL Server 12.0*.

1. Set the **Exclusion** by selecting the ellipsis and selecting the resource group to exclude, *SQLServers_Excluded* in this example.

   ![Add an excluded resource group to the policy assignment](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Consoante a política e o respetivo efeito, a exclusão também pode ser concedida a recursos específicos dentro de um grupo de recursos no âmbito da atribuição. Como neste tutorial foi utilizado um efeito **Negar**, não faria sentido definir a exclusão num recurso específico já existente.

1. Select **Select** and then select **Save**.

In this section, you resolved the denied request by creating an exclusion on a single resource group.

## <a name="clean-up-resources"></a>Limpar recursos

If you're done working with resources from this tutorial, use the following steps to delete any of the assignments or definitions created above:

1. Select **Definitions** (or **Assignments** if you're trying to delete an assignment) under **Authoring** in the left side of the Azure Policy page.

1. Procure a nova definição de iniciativa ou de política (ou atribuição) que acabou de remover.

1. Clique com o botão direito do rato na linha ou selecione as reticências no fim da definição (ou atribuição) e selecione **Eliminar definição** (ou **Eliminar atribuição**).

## <a name="review"></a>Rever

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que criar no futuro
> - Criar e atribuir uma definição de iniciativa para controlar a conformidade de vários recursos
> - Resolver um recurso negado ou em não conformidade
> - Implementar uma nova política numa organização

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Azure Policy definition structure](../concepts/definition-structure.md) (Estrutura de definição do Azure Policy)