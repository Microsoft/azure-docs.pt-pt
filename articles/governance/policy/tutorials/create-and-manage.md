---
title: Criar e gerir políticas para impor a conformidade
description: Utilize o Azure Policy para impor normas, corresponder a requisitos de conformidade regulamentar e de auditoria, controlar custos, manter a segurança e consistência de desempenho, bem como impor princípios de design a nível empresarial.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
ms.openlocfilehash: 82c56a01d7f14f1eeae26dfa784f3545552bf6b9
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981389"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Criar e gerir políticas para impor a conformidade

Compreender como criar e gerir políticas no Azure é importante para manter a conformidade com os seus padrões empresariais e contratos de nível de serviço. Neste tutorial, vai aprender a utilizar o Azure Policy para realizar algumas das tarefas mais comuns relacionadas com a criação, atribuição e gestão de políticas na sua organização, tais como:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que criar no futuro
> - Criar e atribuir uma definição de iniciativa para controlar a conformidade de vários recursos
> - Resolver um recurso negado ou em não conformidade
> - Implementar uma nova política numa organização

Se gostaria de atribuir uma política para identificar o estado atual de conformidade dos seus recursos existentes, os artigos de início rápido mostram como fazê-lo. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="assign-a-policy"></a>Atribuir uma política

O primeiro passo para impor a conformidade com o Azure Policy consiste em atribuir uma definição de política. Uma definição de política define sob que condição é imposta uma política e qual o efeito a ter. Neste exemplo, atribua uma definição de política incorporada chamada *Requer a versão do SQL Server 12.0*, para impor a condição de que todas as bases de dados SQL Server têm de ser v12.0 para estarem em conformidade.

1. Inicie o serviço Azure Policy no portal do Azure ao clicar em **Todos os serviços** e, em seguida, ao pesquisar e selecionar **Policy**.

   ![Pesquisar política em todos os serviços](../media/create-and-manage/search-policy.png)

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   ![Selecionar atribuições da página Visão geral da política](../media/create-and-manage/select-assignments.png)

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   ![Atribuir uma definição de política da página atribuições](../media/create-and-manage/select-assign-policy.png)

1. Na página **Atribuir Política**, selecione **Âmbito** ao clicar no botão de reticências e ao selecionar um grupo de gestão ou uma subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina que recursos ou agrupamento de recursos em que a atribuição de política é imposta. Em seguida, clique em **Selecionar** na parte inferior da página **Âmbito**.

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais. Por isso, deixe-as em branco por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. Pode filtrar o **Tipo** de definição de política para *Incorporado* para ver todas e ler as respetivas descrições.

1. Selecione **Requer a versão do SQL Server 12.0**. Se não é possível encontrá-lo imediatamente, escreva **necessita do sql server** para a pesquisa caixa e, em seguida, prima ENTER ou clicar fora da caixa de pesquisa. Clique em **Selecionar** na parte inferior da página **Definições Disponíveis** depois de ter encontrado e selecionado a definição de política.

   ![Usar o filtro de pesquisa para localizar uma política](../media/create-and-manage/select-available-definition.png)

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Para este exemplo, deixe *Requer a versão do SQL Server 12.0*. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.
   **Atribuído por** é preenchida automaticamente com base em quem está conectado. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. Esta caixa _tem_ verificado quando a política ou iniciativa que está sendo atribuído inclui uma política com o [deployIfNotExists](../concepts/effects.md#deployifnotexists) efeito. Como a política utilizada para este tutorial não, deixe em branco. Para obter mais informações, veja [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que atribuiu uma definição de política incorporada, pode fazer mais com o Azure Policy. Em seguida, crie uma nova política personalizada para reduzir os custos ao validar que as VMs criadas no seu ambiente não podem estar na série G. Desta forma, sempre que um utilizador na sua organização tenta criar uma VM na série G, o pedido é negado.

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Página definição em grupo de criação](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+ Definição de política** na parte superior da página. Este botão abre-se para o **definição de política** página.

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

   A propriedade *Field* na regra de política deve ser um dos seguintes valores: Nome, tipo, local, marcas ou um alias. Um exemplo de um alias poderá ser `"Microsoft.Compute/VirtualMachines/Size"`.

   Para ver mais exemplos do Azure Policy, veja [Exemplos do Azure Policy](../samples/index.md).

1. Selecione **Guardar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Criar uma definição de política com a API REST

Você pode criar uma política com a API REST para definições de Azure Policy. A API REST permite-lhe criar e eliminar definições de política, bem como obter informações sobre as definições existentes. Para criar uma definição de política, utilize o seguinte exemplo:

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

Antes de prosseguir com o exemplo do PowerShell, verifique se você instalou a versão mais recente do módulo Azure PowerShell AZ.

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

Com uma definição de iniciativa, pode agrupar várias definições de política para alcançar um objetivo mais abrangente. Uma iniciativa avalia os recursos dentro do escopo da atribuição para conformidade com as políticas incluídas. Para obter mais informações sobre as definições de iniciativa, veja [Descrição geral do Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Criar uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

   ![Selecione a definição na página Definições](../media/create-and-manage/definition-under-authoring.png)

1. Selecione **+ Definição de Iniciativa** na parte superior da página para abrir a página **Definição de iniciativa**.

   ![Página de definição da iniciativa de revisão](../media/create-and-manage/initiative-definition.png)

1. Utilize as reticências de **Localização da definição** para selecionar um grupo de gestão ou uma subscrição na qual armazenar a definição. Se a página anterior se destinava a um único grupo de gestão ou subscrição, a **Localização da definição** será automaticamente preenchida.

1. Introduza o **Nome** e a **Descrição** da iniciativa.

   Neste exemplo valida que recursos estão em conformidade com as definições de política sobre a segurança. Nomeie a iniciativa como **segura** e defina a descrição como: **Essa iniciativa foi criada para lidar com todas as definições de política associadas à proteção de recursos**.

1. Em **Categoria**, escolha de entre as opções existentes ou crie uma nova categoria.

1. Procure na lista de **Definições Disponíveis** (lado direito da página **Definição de iniciativa**) e selecione as definições de política que gostaria de adicionar a esta iniciativa. Para a iniciativa **Proteger-se**, adicione as seguintes definições de política incorporadas, clicando em **+** junto às informações de definição de política ou clicando numa linha de definição de política e, em seguida, na opção **+ Adicionar** na página de detalhes:

   - Requer a Versão do SQL Server 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Depois de selecionar a definição de política na lista, é adicionada sob **políticas e parâmetros**.

   ![Examinar parâmetros de definição de iniciativa](../media/create-and-manage/initiative-definition-2.png)

1. Se uma definição de política que está a ser adicionada a iniciativa tiver parâmetros, estão mostrados sob o nome da política no **políticas e parâmetros** área. O _valor_ pode ser definido como "Definir o valor' (hard-coded para todas as atribuições dessa iniciativa) ou "Usar parâmetro de iniciativa" (definido durante cada atribuição de iniciativa). Se ' set value ' for selecionado, a lista suspensa à direita de _valores_ permitirá inserir ou selecionar os valores. Se “Usar Parâmetro de Iniciativa” estiver selecionado, será apresentada uma nova secção **Parâmetros de iniciativa** que lhe permite definir o parâmetro que será definido durante a atribuição de iniciativa. Os valores permitidos neste parâmetro de iniciativa podem restringir mais o que pode ser definido durante a atribuição de iniciativa.

   ![Alterar parâmetros de definição de iniciativa de valores permitidos](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > No caso de alguns parâmetros `strongType`, a lista de valores não pode ser determinada automaticamente. Nestes casos, um botão de reticências será apresentado à direita da linha do parâmetro. Ao clicar nele, abrirá a página “Âmbito do parâmetro (&lt;nome do parâmetro&gt;)”. Nesta página, selecione a subscrição a utilizar para fornecer as opções de valor. Este âmbito de parâmetro só é utilizado durante a criação da definição de iniciativa e não tem qualquer impacto na avaliação de políticas nem no âmbito da iniciativa quando atribuído.

1. Clique em **Guardar**.

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Selecione **Definições** em **Criação** no lado esquerdo da página Azure Policy.

1. Localize a definição de iniciativa **Proteger-se** que criou anteriormente e clique nela. Selecione **atribuir** na parte superior da página para abrir para o **Get seguro: Atribuir a página iniciativa @ no__t-0.

   ![Atribuir uma definição da página de definição de iniciativa](../media/create-and-manage/assign-definition.png)

   Pode também com o botão direito na linha selecionada ou left-click nas reticências no final da linha para um menu contextual. Em seguida, selecione **Atribuir**.

   ![Opções alternativas para uma iniciativa](../media/create-and-manage/select-right-click.png)

1. Preencha o **Get Secure: Atribua a página Initiative @ no__t-0 inserindo as informações de exemplo a seguir. Pode utilizar as suas próprias informações.

   - Âmbito: O grupo de gerenciamento ou a assinatura para a qual você salvou a iniciativa se torna o padrão.
     Pode alterar o âmbito para atribuir a iniciativa a uma subscrição ou a um grupo de recursos na localização guardada.
   - Exclusões Configure todos os recursos dentro do escopo para impedir que a atribuição de iniciativa seja aplicada a eles.
   - Definição da iniciativa e nome da atribuição: Obtenha segurança (preenchida previamente como nome da iniciativa que está sendo atribuída).
   - Descrição: Essa atribuição de iniciativa é adaptada para impor esse grupo de definições de política.
   - Atribuído por: Preenchido automaticamente com base em quem está conectado. Este campo é opcional,por isso pode introduzir valores personalizados.

1. Não marque o campo **Criar uma Identidade Gerida**. Esta caixa _tem_ verificado quando a política ou iniciativa que está sendo atribuído inclui uma política com o [deployIfNotExists](../concepts/effects.md#deployifnotexists) efeito. Como a política utilizada para este tutorial não, deixe em branco. Para obter mais informações, veja [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../how-to/remediate-resources.md#how-remediation-security-works).

1. Clique em **Atribuir**.

## <a name="check-initial-compliance"></a>Verificar conformidade inicial

1. Selecione **Conformidade** no lado esquerdo da página Azure Policy.

1. Localize a iniciativa **Obter Origem**. É provável que ainda na _estado de conformidade_ dos **não iniciado**.
   Clique na iniciativa para obter todos os detalhes sobre o progresso da atribuição.

   ![Página de conformidade da iniciativa-avaliações não iniciadas](../media/create-and-manage/compliance-status-not-started.png)

1. Depois de concluir a atribuição da iniciativa, a página de conformidade é atualizada com o _Estado de conformidade_ de **Conforme**.

   ![Página de conformidade da iniciativa-conformidade de recursos](../media/create-and-manage/compliance-status-compliant.png)

1. Se clicar em qualquer política na página de conformidade da iniciativa, será apresentada a página dos detalhes de conformidade referentes à política. Esta página apresenta os detalhes de conformidade ao nível dos recursos.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Excluir um recurso negado ou em não conformidade ao utilizar a Exclusão

Ao seguir o exemplo acima, depois de atribuir a definição de política para requerer a versão 12.0 do SQL Server, um servidor SQL criado com uma versão diferente da 12.0 seria negado. Nesta secção, é necessário percorrer resolver uma solicitação sem permissão para criar um SQL server através da criação de uma exclusão num grupo de recursos. A exclusão impede a imposição da política (ou iniciativa) nesse recurso.
No exemplo seguinte, qualquer versão do SQL Server é permitida num único grupo de recursos. Uma exclusão pode aplicar-se a uma subscrição ou a um grupo de recursos. Pode ainda restringir a exclusão a recursos individuais.

Uma implementação impedida por uma política atribuída ou iniciativa pode ser visualizada em duas localizações:

- No grupo de recursos de destino da implantação: Selecione **implantações** no lado esquerdo da página e clique no **nome** da implantação da implantação com falha. O recurso que foi negado é apresentado com o estado _Proibido_. Para determinar a política ou iniciativa e a atribuição que negou o recurso, clique em **Falhou. Clique aqui para obter detalhes ->** na página de Descrição Geral da Implementação. É apresentada uma janela no lado direito da página com as informações de erro. Sob **detalhes do erro** são os GUIDs dos objetos de política relacionada.

  ![Implementação negada pela atribuição de política](../media/create-and-manage/rg-deployment-denied.png)

- Na página Azure Policy: Selecione **conformidade** no lado esquerdo da página e clique na política **exigir SQL Server versão 12,0** . Na página que abre, verá um aumento na contagem de **Negar**. Sob o **eventos** guia, irá também ver quem tentou a implementação que foi negada pela política.

  ![Descrição geral de conformidade de uma política atribuída](../media/create-and-manage/compliance-overview.png)

Neste exemplo, Trent Baker, um dos especialistas em Sr. Virtualization da Contoso, estava fazendo o trabalho necessário. Precisamos conceder Trent uma exceção, mas não Queremos que os servidores SQL não - a versão 12.0 apenas qualquer grupo de recursos. Criámos um novo grupo de recursos, **SQLServers_Excluded** e irá agora conceder uma exceção a esta atribuição de política.

### <a name="update-assignment-with-exclusion"></a>Atualizar a atribuição com uma exclusão

1. Selecione **Atribuições** em **Criação** no lado esquerdo da página Azure Policy.

1. Procure por todas as atribuições de política e abra a atribuição *Requer a Versão do SQL Server 12.0*.

1. Defina a **Exclusão**, clicando nas reticências e selecionando o grupo de recursos a excluir, *SQLServers_Excluded* neste exemplo.

   ![Adicionar um grupo de recursos excluídos à atribuição de política](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Consoante a política e o respetivo efeito, a exclusão também pode ser concedida a recursos específicos dentro de um grupo de recursos no âmbito da atribuição. Como neste tutorial foi utilizado um efeito **Negar**, não faria sentido definir a exclusão num recurso específico já existente.

1. Clique em **Selecionar** e em **Guardar**.

Nesta secção, resolveu o pedido negado através da criação de uma exclusão num grupo de recursos.

## <a name="clean-up-resources"></a>Limpar recursos

Se já está a trabalhar com os recursos neste tutorial, utilize os passos seguintes para eliminar quaisquer atribuições ou definições criadas acima:

1. Selecione **definições** (ou **atribuições** se estiver a tentar eliminar uma atribuição) sob **criação** no lado esquerdo da página política do Azure.

1. Procure a nova definição de iniciativa ou de política (ou atribuição) que acabou de remover.

1. Clique com o botão direito do rato na linha ou selecione as reticências no fim da definição (ou atribuição) e selecione **Eliminar definição** (ou **Eliminar atribuição**).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que criar no futuro
> - Criar e atribuir uma definição de iniciativa para controlar a conformidade de vários recursos
> - Resolver um recurso negado ou em não conformidade
> - Implementar uma nova política numa organização

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)