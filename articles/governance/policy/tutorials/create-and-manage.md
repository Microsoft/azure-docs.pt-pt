---
title: 'Tutorial: Construir políticas para impor o cumprimento'
description: Neste tutorial, você usa políticas para impor padrões, controlar custos, manter a segurança, e impor princípios de design em larga escala da empresa.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: bf3da82abcdcada1fc38df29efc988a1805c3020
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876390"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Criar e gerir políticas para impor o cumprimento

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

O primeiro passo para impor a conformidade com o Azure Policy consiste em atribuir uma definição de política. Uma definição de política define sob que condição é imposta uma política e qual o efeito a ter. Neste exemplo, atribua a definição de política incorporada chamada _Herdar uma etiqueta do grupo de recursos se faltar_ para adicionar a etiqueta especificada com o seu valor do grupo de recursos-mãe a recursos novos ou atualizados que não a etiqueta.

1. Vá ao portal Azure para atribuir políticas. Procure e selecione **Política.**

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. Selecione **Atribuições** no lado esquerdo da página Azure Policy. Uma atribuição é uma política que foi atribuída para ter lugar num âmbito específico.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. Selecione **Atribuir Política** na parte superior da página **Política - Atribuições**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. No separador **'Política de Atribuição'** e **"Básicos",** selecione o **Âmbito** selecionando a elipse e selecionando um grupo de gestão ou subscrição. Opcionalmente, selecione um grupo de recursos. Um âmbito determina quais os recursos ou agrupamento de recursos em que a atribuição de política será imposta.
   Em seguida, **selecione Selecione** na parte inferior da página **'Âmbito'.**

   Este exemplo utiliza a subscrição da **Contoso**. A sua subscrição vai ser diferente.

1. Os recursos podem ser excluídos com base no **Âmbito**. As **Exclusões** começam num nível inferior ao nível do **Âmbito**. As **Exclusões** são opcionais. Por isso, deixe-as em branco por enquanto.

1. Selecione as reticências de **Definição de política** para abrir a lista de definições disponíveis. Pode filtrar a definição de política **Tipo** a _Incorporado_ para ver tudo e ler as suas descrições.

1. **Selecione Herdar uma etiqueta do grupo de recursos se faltar**. Se não conseguir encontrá-lo imediatamente, **escreva uma etiqueta** na caixa de pesquisa e, em seguida, prima ENTER ou selecione para fora da caixa de pesquisa.
   **Selecione Selecione** na parte inferior da página **Definições disponíveis** uma vez que tenha encontrado e selecionado a definição de política.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Screenshot de procurar a Política na barra de pesquisa.":::

1. O **Nome da atribuição** é automaticamente preenchido com o nome da política que selecionou, mas pode alterá-lo. Para este exemplo, deixe _herdar uma etiqueta do grupo de recursos se faltar_. Também pode adicionar uma **Descrição** opcional. A descrição fornece detalhes sobre esta atribuição de política.

1. Deixe **a aplicação da política** conforme _habilitado_. Quando _desativada,_ esta definição permite testar o resultado da apólice sem desencadear o efeito. Para obter mais informações, consulte [o modo de execução](../concepts/assignment-structure.md#enforcement-mode).

1. **Atribuído por** é automaticamente preenchido com base em quem está iniciado. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Selecione o **separador Parâmetros** na parte superior do assistente.

1. Para **o Nome da Etiqueta,** insira _Ambiente_.

1. Selecione o **separador Remediação** na parte superior do assistente.

1. Deixar **Criar uma tarefa de remediação** sem controlo. Esta caixa permite-lhe criar uma tarefa para alterar os recursos existentes, além de recursos novos ou atualizados. Para mais informações, consulte [os recursos remediar.](../how-to/remediate-resources.md)

1. **A Criação de uma Identidade Gerida** é verificada automaticamente uma vez que esta definição de política utiliza o efeito [modificação.](../concepts/effects.md#modify) **As permissões** são definidas automaticamente para _o Contribuinte_ com base na definição de política. Para obter mais informações, veja [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione o **separador 'Rever +'** na parte superior do assistente.

1. Reveja as suas seleções e, em seguida, **selecione Criar** na parte inferior da página.

## <a name="implement-a-new-custom-policy"></a>Implementar uma nova política personalizada

Agora que atribuiu uma definição de política incorporada, pode fazer mais com o Azure Policy. Em seguida, crie uma nova política personalizada para economizar custos, validando que as máquinas virtuais criadas no seu ambiente não podem estar na série G. Desta forma, sempre que um utilizador da sua organização tenta criar uma máquina virtual na série G, o pedido é negado.

1. Selecione **Definições** em **Autoria** no lado esquerdo da página Política Azure.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. Selecione **+ Definição de política** na parte superior da página. Este botão abre para a página **de definição de Política.**

1. Introduza as seguintes informações:

   - O grupo de gestão ou a subscrição em que a definição de política é guardada. Selecione, utilizando o botão de reticências em **Localização da definição**.

     > [!NOTE]
     > Se pretender aplicar esta definição de política a diversas subscrições, a localização deverá ser um grupo de gestão que inclua as subscrições às quais atribui a política. O mesmo se aplica a uma definição de iniciativa.

   - O nome da definição de política - _Exigir SKUs VM não na série G_
   - A descrição do que a definição de política se destina a fazer – _Esta definição de política impõe que todas as máquinas virtuais criadas neste âmbito tenham SKUs diferentes da série G para reduzir custos._
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

   A propriedade de _campo_ na regra da política deve ser um valor suportado. Uma lista completa de valores encontra-se nos [campos de estrutura de definição de políticas.](../concepts/definition-structure.md#fields) Um exemplo de um alias poderá ser `"Microsoft.Compute/VirtualMachines/Size"`.

   Para ver mais amostras da Política Azure, consulte [as amostras da Política Azure](../samples/index.md).

1. Selecione **Guardar**.

## <a name="create-a-policy-definition-with-rest-api"></a>Criar uma definição de política com a API REST

Pode criar uma política com a API REST para Definições políticas Azure. A API REST permite-lhe criar e eliminar definições de política, bem como obter informações sobre as definições existentes. Para criar uma definição de política, utilize o seguinte exemplo:

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

Antes de prosseguir com o exemplo PowerShell, certifique-se de que instalou a versão mais recente do módulo Azure PowerShell Az.

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

Pode criar uma definição de política utilizando o Azure CLI com o `az policy definition` comando. Para criar uma definição de política com uma regra na mesma linha, utilize o seguinte exemplo:

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

Com uma definição de iniciativa, pode agrupar várias definições de política para alcançar um objetivo mais abrangente. Uma iniciativa avalia os recursos no âmbito da atribuição para o cumprimento das políticas incluídas. Para obter mais informações sobre as definições de iniciativa, veja [Descrição geral do Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Criar uma definição de iniciativa

1. Selecione **Definições** em **Autoria** no lado esquerdo da página Política Azure.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Screenshot de procurar a Política na barra de pesquisa.":::

1. Selecione **+ Definição** de Iniciativa no topo da página para abrir o assistente **de definição iniciativa.**

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Screenshot de procurar a Política na barra de pesquisa.":::

1. Utilize a elipse de **localização da Iniciativa** para selecionar um grupo de gestão ou subscrição para armazenar a definição. Se a página anterior foi traçada para um único grupo de gestão ou subscrição, **a localização da Iniciativa** é automaticamente povoada.

1. Introduza o **Nome** e a **Descrição** da iniciativa.

   Este exemplo valida que os recursos estão em conformidade com as definições políticas sobre a segurança. O nome da iniciativa deverá ser **Proteger-se** e a definição da descrição: **Esta iniciativa foi criada para processar todas as definições de política associadas à proteção de recursos**.

1. Em **Categoria**, escolha de entre as opções existentes ou crie uma nova categoria.

1. Desa parte de uma **versão** para a iniciativa, tal como _1.0_.

   > [!NOTE]
   > O valor da versão é estritamente metadados e não é utilizado para atualizações ou qualquer processo pelo serviço Azure Policy.

1. Selecione **Seguinte** na parte inferior da página ou no separador **Políticas** na parte superior do assistente.

1. Selecione O botão **de definição de política(s)** e navegue pela lista. Selecione a definição de política(s) que pretende adicionar a esta iniciativa. Para a iniciativa **Get Secure,** adicione as seguintes definições de política incorporadas selecionando a caixa de verificação ao lado da definição de política:

   - Localizações permitidas
   - Monitor que falta proteção de ponto final no Centro de Segurança Azure
   - Máquinas virtuais não orientadas para a Internet devem ser protegidas com grupos de segurança de rede
   - A azure Backup deve ser ativado para máquinas virtuais
   - A encriptação do disco deve ser aplicada em máquinas virtuais
   - Adicione ou substitua uma etiqueta nos recursos (adicione esta definição de política duas vezes)

   Depois de selecionar cada definição de política da lista, **selecione Adicionar** na parte inferior da lista.
   Uma vez que é adicionado duas vezes, o _Add ou substituir uma etiqueta nas_ definições de política de recursos cada um obtém um _ID de referência_diferente .

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Screenshot de procurar a Política na barra de pesquisa.":::

   > [!NOTE]
   > As definições de política selecionadas podem ser adicionadas aos grupos selecionando uma ou mais definições adicionadas e selecionando **adicionar políticas selecionadas a um grupo**. O grupo deve existir primeiro e pode ser criado no **separador Grupos** do assistente.

1. Selecione **Seguinte** na parte inferior da página ou no **separador Grupos** na parte superior do assistente. Novos grupos podem ser adicionados a partir deste separador. Para este tutorial, não vamos adicionar nenhum grupo.

1. Selecione **Seguinte** na parte inferior da página ou o separador **parâmetros Iniciativa** na parte superior do assistente. Se quiséssemos que existisse um parâmetro na iniciativa de passar para uma ou mais definições políticas incluídas, o parâmetro é definido aqui e depois utilizado no separador **parâmetros da Política.** Para este tutorial, não estamos adicionando parâmetros de iniciativa.

   > [!NOTE]
   > Uma vez guardados para uma definição de iniciativa, os parâmetros da iniciativa não podem ser eliminados da iniciativa. Se deixar de ser necessário um parâmetro de iniciativa, retire-o da utilização por quaisquer parâmetros de definição de política.

1. Selecione **Seguinte** na parte inferior da página ou no separador **parâmetros De política** na parte superior do assistente.

1. A definição de política adicionada à iniciativa que tem parâmetros é exibida numa grelha. O _tipo de valor_ pode ser 'Valor predefinido', 'Valor definido', ou 'Parâmetro de Iniciativa de Utilização'. Se for selecionado o 'Valor Definido', o valor relacionado é introduzido no _valor(s) valor(s)_. Se o parâmetro na definição de política tiver uma lista de valores permitidos, a caixa de entrada é um seletor de queda. Se for selecionado o 'Use Initiative Parameter', é fornecido um selete de drop-down com os nomes dos parâmetros de iniciativa criados no separador parâmetros da **Iniciativa.**

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Screenshot de procurar a Política na barra de pesquisa.":::

   > [!NOTE]
   > No caso de alguns parâmetros `strongType`, a lista de valores não pode ser determinada automaticamente. Nestes casos, um botão de reticências será apresentado à direita da linha do parâmetro. Selecioná-lo abre a página 'Parâmetro scope &lt; (nome do &gt; parâmetro)». Nesta página, selecione a subscrição a utilizar para fornecer as opções de valor. Este âmbito de parâmetro só é utilizado durante a criação da definição de iniciativa e não tem qualquer impacto na avaliação de políticas nem no âmbito da iniciativa quando atribuído.

   Desa estale o _valor_ 'Locais Permitidos' para 'Definir valor' e selecione 'East US 2' a partir do drop-down. Para os dois casos do _Add ou substitua uma etiqueta nas_ definições de política de recursos, desaprote os parâmetros **'Nome de Etiqueta'** e 'CostCenter' e os parâmetros **de Valor de Etiqueta** para 'Teste' e 'Lab', como mostrado abaixo. Deixe os outros como "Valor Padrão". Utilizando a mesma definição duas vezes na iniciativa, mas com parâmetros diferentes, esta configuração adiciona ou substitui uma etiqueta 'Env' pelo valor 'Test' e uma etiqueta 'CostCenter' com o valor de 'Lab' nos recursos no âmbito da atribuição.

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="Screenshot de procurar a Política na barra de pesquisa.":::

1. Selecione **Review + crie** na parte inferior da página ou na parte superior do assistente.

1. Reveja as definições e **selecione Criar**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Criar uma definição de iniciativa política com a Azure CLI

Pode criar uma definição de iniciativa política utilizando o Azure CLI com o `az policy set-definition` comando. Para criar uma definição de iniciativa política com uma definição de política existente, utilize o seguinte exemplo:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Crie uma definição de iniciativa política com a Azure PowerShell

Pode criar uma definição de iniciativa política utilizando a Azure PowerShell com o `New-AzPolicySetDefinition` cmdlet. Para criar uma definição de iniciativa política com uma definição de política existente, utilize o seguinte ficheiro de definição de iniciativa política como `VMPolicySet.json` :

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Atribuir uma definição de iniciativa

1. Selecione **Definições** em **Autoria** no lado esquerdo da página Política Azure.

1. Localize a definição de iniciativa **Proteger-se** que criou anteriormente e selecione-a. Selecione **Atribuir** na parte superior da página para abrir a página **Proteger-se: Atribuir iniciativa**.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

   Também pode clicar à direita na linha selecionada ou selecionar a elipse no final da linha para um menu contextual. Em seguida, **selecione Atribuir**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. Preencha a página **Proteger-se: Atribuir Iniciativa**, introduzindo as seguintes informações de exemplo. Pode utilizar as suas próprias informações.

   - Âmbito: o grupo de gestão ou a subscrição no qual guardou a iniciativa passa a ser a predefinição.
     Pode alterar o âmbito para atribuir a iniciativa a uma subscrição ou a um grupo de recursos na localização guardada.
   - Exclusões: configure os recursos no âmbito para evitar que a atribuição de iniciativa seja aplicada aos mesmos.
   - Definição de iniciativa e Nome da atribuição: Proteger-se (pré-preenchida como o nome da iniciativa que está a ser atribuída).
   - Descrição: esta atribuição de iniciativa está adaptada para impor este grupo de definições de política.
   - Execução da política: Deixe como o padrão _Ativado_.
   - Atribuído por: automaticamente preenchido, tendo em conta a pessoa que iniciou a sessão. Este campo é opcional e, por isso, podem ser introduzidos valores personalizados.

1. Selecione o **separador Parâmetros** na parte superior do assistente. Se configurar um parâmetro de iniciativa em etapas anteriores, desafie um valor aqui.

1. Selecione o **separador Remediação** na parte superior do assistente. Não marque o campo **Criar uma Identidade Gerida**. Esta caixa _deve_ ser verificada quando a política ou iniciativa que está a ser atribuída inclui uma política com o [deployIfNotExists](../concepts/effects.md#deployifnotexists) ou [modificar](../concepts/effects.md#modify) efeitos. Como a política usada para este tutorial não, deixe-o em branco. Para obter mais informações, veja [identidades geridas](../../../active-directory/managed-identities-azure-resources/overview.md) e [como funciona a segurança de remediação](../how-to/remediate-resources.md#how-remediation-security-works).

1. Selecione o **separador 'Rever +'** na parte superior do assistente.

1. Reveja as suas seleções e, em seguida, **selecione Criar** na parte inferior da página.

## <a name="check-initial-compliance"></a>Verificar conformidade inicial

1. Selecione **Conformidade** no lado esquerdo da página Azure Policy.

1. Localize a iniciativa **Get Secure.** Provavelmente ainda está no estado de _conformidade_ de **não ter começado.**
   Selecione a iniciativa para obter todos os detalhes da atribuição.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. Depois de concluir a atribuição da iniciativa, a página de conformidade é atualizada com o _Estado de conformidade_ de **Conforme**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

1. Selecionar qualquer política na página de conformidade da iniciativa abre a página de detalhes de conformidade para essa política. Esta página apresenta os detalhes de conformidade ao nível dos recursos.

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>Remover um recurso não conforme ou negado do âmbito com exclusão

Após a atribuição de uma iniciativa política para exigir uma localização específica, qualquer recurso criado num local diferente é negado. Nesta secção, você anda pela resolução de um pedido negado para criar um recurso criando uma exclusão em um único grupo de recursos. A exclusão impede a aplicação da política (ou iniciativa) sobre esse grupo de recursos. No exemplo seguinte, qualquer localização é permitida no grupo de recursos excluído. Uma exclusão pode aplicar-se a uma subscrição, a um grupo de recursos ou a um recurso individual.

> [!NOTE]
> Uma [isenção de política](../concepts/exemption-structure.md) também pode ser utilizada sem a avaliação de um recurso. Para obter informações adicionais, consulte [Scope in Azure Policy](../concepts/scope.md).

As implementações impedidas por uma política ou iniciativa atribuída podem ser vistas no grupo de recursos visado pela implementação: Selecione **Implementações** no lado esquerdo da página e, em seguida, selecione o Nome de **Implantação** da implementação falhada. O recurso que foi negado é apresentado com o estado _Proibido_. Para determinar a política ou iniciativa e atribuição que negou o recurso, selecione **Failed. Clique aqui para obter mais informações sobre >** na página 'Vista Geral' de Implementação. É apresentada uma janela no lado direito da página com as informações de erro. Em **Detalhes de Erro** estão os GUIDs dos objetos de política relacionados.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

Na página Política Azure: Selecione **Conformidade** no lado esquerdo da página e selecione a iniciativa **política Get Secure.** Nesta página, há um aumento na contagem **de Deny** para recursos bloqueados. No separador **Eventos** estão detalhes sobre quem tentou criar ou implementar o recurso que foi negado pela definição de política.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

Neste exemplo, Trent Baker, um dos especialistas em Virtualização sénior de Contoso, estava a fazer o trabalho necessário. Temos de dar ao Trent um espaço para uma exceção. Criei um novo grupo de recursos, **o LocationsExcluded,** e em seguida concede-lhe uma exceção a esta atribuição de políticas.

### <a name="update-assignment-with-exclusion"></a>Atualizar a atribuição com uma exclusão

1. Selecione **Atribuições** em **Criação** no lado esquerdo da página Azure Policy.

1. Navegue por todas as atribuições políticas e abra a atribuição de política _Get Secure._

1. Desaprova a **Exclusão** selecionando a elipse e selecionando o grupo de recursos para excluir, _localizaçõesExcluded_ neste exemplo. **Selecione Adicionar ao Âmbito Selecionado** e, em seguida, selecione **Guardar**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Screenshot de procurar a Política na barra de pesquisa." border="false":::

   > [!NOTE]
   > Em função da definição de política e do seu efeito, a exclusão poderia também ser concedida a recursos específicos dentro de um grupo de recursos no âmbito da atribuição. Como um efeito **Deny** foi usado neste tutorial, não faria sentido definir a exclusão em um recurso específico que já existe.

1. Selecione **Rever + guardar** e, em seguida, selecione **Guardar**.

Nesta secção, resolveu o pedido negado criando uma exclusão num único grupo de recursos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se já acabou de trabalhar com recursos deste tutorial, use os seguintes passos para eliminar qualquer uma das atribuições ou definições políticas acima criadas:

1. Selecione **Definições** (ou **Atribuições** se estiver a tentar apagar uma atribuição) em **Autoria** no lado esquerdo da página Política Azure.

1. Procure a nova definição de iniciativa ou de política (ou atribuição) que acabou de remover.

1. Clique com o botão direito do rato na linha ou selecione as reticências no fim da definição (ou atribuição) e selecione **Eliminar definição** (ou **Eliminar atribuição**).

## <a name="review"></a>Revisão

Neste tutorial conseguiu realizar com êxito as seguintes tarefas:

> [!div class="checklist"]
> - Atribuir uma política para impor uma condição para os recursos que criar no futuro
> - Criar e atribuir uma definição de iniciativa para controlar a conformidade de vários recursos
> - Resolver um recurso negado ou em não conformidade
> - Implementar uma nova política numa organização

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as estruturas de definições de política, veja este artigo:

> [!div class="nextstepaction"]
> [Estrutura de definição do Azure Policy](../concepts/definition-structure.md)