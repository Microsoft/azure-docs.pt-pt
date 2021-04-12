---
title: Configure as chaves geridas pelo cliente para a sua conta DB Azure Cosmos
description: Saiba como configurar chaves geridas pelo cliente para a sua conta DB Azure Cosmos com Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/01/2021
ms.author: thweiss
ms.openlocfilehash: 1b1fc0b51c1cd2a99ec97bec9f588699a893ceca
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222627"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configure chaves geridas pelo cliente para a sua conta do Azure Cosmos com o Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os dados armazenados na sua conta Azure Cosmos são automaticamente e sem problemas encriptados com teclas geridas pela Microsoft **(teclas geridas** pelo serviço). Opcionalmente, pode optar por adicionar uma segunda camada de encriptação com as teclas geridas **(teclas geridas pelo cliente).**

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Camadas de encriptação em torno dos dados do cliente":::

Tem de armazenar chaves geridas pelo cliente no [Azure Key Vault](../key-vault/general/overview.md) e fornecer uma chave para cada conta Azure Cosmos que esteja ativada com chaves geridas pelo cliente. Esta chave é usada para encriptar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves geridas pelo cliente estão disponíveis apenas para novas contas Azure Cosmos. Deve configurá-los durante a criação de conta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registe o fornecedor de recursos DB da Azure Cosmos para a sua subscrição Azure

1. Inscreva-se no [portal Azure,](https://portal.azure.com/)aceda à subscrição do Azure e selecione **fornecedores de Recursos** no separador **Definições:**

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Entrada de fornecedores de recursos a partir do menu esquerdo":::

1. Procure o fornecedor de recursos **Microsoft.DocumentDB.** Verifique se o fornecedor de recursos já está marcado como registado. Caso contrário, escolha o fornecedor de recursos e **selecione Registar-se:**

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Registo do Microsoft.Docfornecedor de recursos umentDB":::

## <a name="configure-your-azure-key-vault-instance"></a>Configure o seu exemplo de Cofre de Chave Azure

A utilização de chaves geridas pelo cliente com a Azure Cosmos DB requer que você descreva duas propriedades no abobadador Azure Key Vault que planeia utilizar para hospedar as suas chaves de encriptação: **Soft Delete** and **Purpur Protection**.

Se criar uma nova instância Azure Key Vault, ative estas propriedades durante a criação:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Permitir a eliminação suave e a proteção de purga para uma nova instância Azure Key Vault":::

Se estiver a utilizar uma instância Azure Key Vault existente, pode verificar se estas propriedades estão ativadas olhando para a secção **Propriedades** no portal Azure. Se alguma destas propriedades não estiver ativada, consulte as secções "Habilitar a eliminação suave" e "Habilitar a proteção da purga" num dos seguintes artigos:

- [Como utilizar soft-delete com PowerShell](../key-vault/general/key-vault-recovery.md)
- [Como utilizar soft-delete com Azure CLI](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a><a id="add-access-policy"></a> Adicione uma política de acesso ao seu Azure Key Vault

1. A partir do portal Azure, vá ao Azure Key Vault que planeia usar para hospedar as suas chaves de encriptação. Selecione Políticas de **Acesso** a partir do menu esquerdo:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Políticas de acesso a partir do menu à esquerda":::

1. Selecione **+ Adicionar Política de Acesso**.

1. No menu de permissões de perguntas à **chave,** selecione **Obter,** **Desembrulhar a chave** e as permissões de Tecla de **Embrulho:**

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Selecionando as permissões certas":::

1. Em **Select principal**, selecione Nenhum **selecionado**.

1. Procure o principal da **Azure Cosmos DB** e selecione-o (para facilitar a sua posição, pode também pesquisar por ID principal: `a232010e-820c-4083-83bb-3ace5fc29d0b` para qualquer região de Azure, exceto regiões do Governo Azure onde está o ID `57506a73-e302-42a9-b869-6f12d9ec29e9` principal). Se o diretor da **Azure Cosmos** não estiver na lista, poderá ter de voltar a registar o fornecedor de recursos **Microsoft.DocumentDB,** conforme descrito no [Registo da](#register-resource-provider) secção fornecedora de recursos deste artigo.

   > [!NOTE]
   > Isto regista a primeira identidade do Azure Cosmos DB na sua política de acesso ao Cofre da Chave Azure. Para substituir esta identidade de primeira parte pela sua conta DB Azure Cosmos gerida, consulte [utilizando uma identidade gerida na política de acesso ao Cofre da Chave Azure](#using-managed-identity).

1. Escolha **Selecione** na parte inferior. 

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Selecione o principal da Azure Cosmos DB":::

1. **Selecione Adicionar** para adicionar a nova política de acesso.

1. **Selecione Guardar** na instância key Vault para guardar todas as alterações.

## <a name="generate-a-key-in-azure-key-vault"></a>Gere uma chave no Cofre da Chave Azure

1. A partir do portal Azure, vá ao Azure Key Vault que planeia usar para hospedar as suas chaves de encriptação. Em seguida, selecione **Chaves** do menu esquerdo:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Entrada de chaves do menu esquerdo":::

1. **Selecione Gerar/Importar,** forneça um nome para a nova chave e selecione um tamanho da chave RSA. Recomenda-se um mínimo de 3072 para a melhor segurança. Em seguida, **selecione Criar**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Criar uma nova chave":::

1. Após a criação da chave, selecione a chave recém-criada e, em seguida, a sua versão atual.

1. Copie o **identificador** chave da chave, exceto a parte após o último corte dianteiro:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Copiar o identificador chave da chave":::

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta Azure Cosmos

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Quando criar uma nova conta DB Azure Cosmos a partir do portal Azure, escolha a **chave gerida pelo Cliente** no passo de **Encriptação.** No campo **Key URI,** cole o identificador URI/chave da chave Azure Key Vault que copiou do passo anterior:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Definição de parâmetros CMK no portal Azure":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Usando a Azure PowerShell

Quando crias uma nova conta DB da Azure Cosmos com a PowerShell:

- Passe o URI da chave Azure Key Vault copiada anteriormente sob a propriedade **KeyVaultKeyUri** em **PropertyObject**.

- Utilize **2019-12-12** ou mais tarde como versão API.

> [!IMPORTANT]
> Tem de definir `locations` a propriedade explicitamente para que a conta seja criada com sucesso com as teclas geridas pelo cliente.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

Após a criação da conta, pode verificar se as chaves geridas pelo cliente foram ativadas através da obtenção do URI da chave Azure Key Vault:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de gestor de recursos Azure

Quando cria uma nova conta Azure Cosmos através de um modelo de Gestor de Recursos Azure:

- Passe o URI da chave Azure Key Vault que copiou anteriormente sob a propriedade **KeyVaultKeyUri** chave no objeto de **propriedades.**

- Utilize **2019-12-12** ou mais tarde como versão API.

> [!IMPORTANT]
> Tem de definir `locations` a propriedade explicitamente para que a conta seja criada com sucesso com as teclas geridas pelo cliente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Implemente o modelo com o seguinte script PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Utilização do CLI Azure

Quando criar uma nova conta Azure Cosmos através do CLI Azure, passe o URI da chave Azure Key Vault que copiou anteriormente sob o `--key-uri` parâmetro.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Após a criação da conta, pode verificar se as chaves geridas pelo cliente foram ativadas através da obtenção do URI da chave Azure Key Vault:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="using-a-managed-identity-in-the-azure-key-vault-access-policy"></a><a id="using-managed-identity"></a> Usando uma identidade gerida na política de acesso ao Cofre da Chave Azure

Esta política de acesso garante que as suas chaves de encriptação podem ser acedidas pela sua conta DB Azure Cosmos. Isto é feito através da concessão de acesso a uma identidade específica do Azure Ative Directory (AD). São apoiados dois tipos de identidades:

- A identidade de primeira parte da Azure Cosmos DB pode ser usada para garantir o acesso ao serviço DB da Azure Cosmos.
- A [identidade gerida](how-to-setup-managed-identity.md) da sua conta Azure Cosmos DB pode ser usada para permitir o acesso à sua conta especificamente.

Como uma identidade gerida atribuída pelo sistema só pode ser recuperada após a criação da sua conta, ainda precisa de criar a sua conta utilizando a identidade do primeiro partido, como [descrito acima.](#add-access-policy) Em seguida:

1. Se isso não foi feito durante a criação de conta, [ative uma identidade gerida atribuída pelo sistema](how-to-setup-managed-identity.md) na sua conta e copie o que foi `principalId` atribuído.

1. Adicione uma nova política de acesso à sua conta Azure Key Vault, tal como [acima](#add-access-policy)descrita, mas utilizando o `principalId` que copiou no passo anterior em vez da identidade de primeira parte da Azure Cosmos DB.

1. Atualize a sua conta DB Azure Cosmos para especificar que pretende utilizar a identidade gerida atribuída ao sistema ao aceder às suas chaves de encriptação no Cofre da Chave Azure. Pode fazê-lo especificando esta propriedade no modelo Azure Resource Manager da sua conta:

   ```json
   {
       "type": " Microsoft.DocumentDB/databaseAccounts",
       "properties": {
           "defaultIdentity": "SystemAssignedIdentity",
           // ...
       },
       // ...
   }
   ```

1. Opcionalmente, pode remover a identidade de primeira parte da Azure Cosmos da sua política de acesso ao Cofre da Chave Azure.

## <a name="key-rotation"></a>Rotação de chaves

A rotação da chave gerida pelo cliente utilizada pela sua conta Azure Cosmos pode ser feita de duas formas.

- Criar uma nova versão da chave atualmente utilizada a partir do Cofre da Chave Azure:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Criar uma nova versão chave":::

- Troque a chave atualmente utilizada por uma totalmente diferente, atualizando a chave URI na sua conta. A partir do portal Azure, aceda à sua conta Azure Cosmos e selecione Encriptação de **Dados** a partir do menu esquerdo:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="A entrada do menu de encriptação de dados":::

    Em seguida, substitua o **Key URI** pela nova chave que pretende utilizar e selecione **Guardar**:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Atualizar a chave URI":::

    Aqui está como fazer para obter o mesmo resultado em PowerShell:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

A versão chave ou chave anterior pode ser desativada após 24 horas, ou depois dos registos de auditoria do [Azure Key Vault](../key-vault/general/logging.md) já não mostrarem atividade a partir de Azure Cosmos DB nessa chave ou versão chave.
    
## <a name="error-handling"></a>Processamento de erros

Ao utilizar Customer-Managed Keys (CMK) em Azure Cosmos DB, se houver algum erro, a Azure Cosmos DB devolve os detalhes de erro juntamente com um código de sub-estado HTTP na resposta. Pode utilizar este código de sub-estado para depurar a causa principal do problema. Consulte os códigos de estado HTTP para o artigo [DB da Azure Cosmos](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) para obter a lista de códigos de sub-estado HTTP suportados.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Existe um custo adicional para ativar as chaves geridas pelo cliente?

Não, não há nenhuma taxa para ativar esta funcionalidade.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Como é que as chaves geridas pelo cliente impactam o planeamento da capacidade?

Ao utilizar as teclas geridas pelo cliente, [as Unidades de Pedido consumidas](./request-units.md) pelas suas operações de base de dados vêem um aumento para refletir o processamento adicional necessário para a encriptação e desencriptação dos seus dados. Isto pode levar a uma utilização ligeiramente maior da sua capacidade a provisionada. Utilize a tabela abaixo para obter orientação:

| Tipo de operação | Aumento da Unidade de Pedido |
|---|---|
| Leituras de pontos (buscar itens pelo seu ID) | + 5% por operação |
| Qualquer operação de escrita | + 6% por operação<br/>aprox. + 0,06 RU por propriedade indexada |
| Consultas, leitura de feed de mudança, ou feed de conflitos | + 15% por operação |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Que dados são encriptados com as chaves geridas pelo cliente?

Todos os dados armazenados na sua conta Azure Cosmos estão encriptados com as teclas geridas pelo cliente, com exceção dos seguintes metadados:

- Os nomes das suas contas, bases de [dados e contentores](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account) da Azure Cosmos

- Os nomes dos seus [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos da propriedade declarados nas suas [políticas de indexação](./index-policy.md)

- Os valores das chaves de [partição](./partitioning-overview.md) dos seus contentores

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves geridas pelo cliente são suportadas para as contas existentes da Azure Cosmos?

Esta funcionalidade encontra-se atualmente disponível apenas para novas contas.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>É possível utilizar chaves geridas pelo cliente em conjunto com a [loja analítica](analytical-store-introduction.md)Azure Cosmos DB?

Sim, mas tem de [usar a identidade gerida da sua conta Azure Cosmos](#using-managed-identity) na sua política de acesso ao Cofre da Chave Azure antes de ativar a loja analítica.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existe um plano para suportar uma granularidade mais fina do que as chaves de nível de conta?

Atualmente não, mas as chaves ao nível do contentor estão a ser consideradas.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Como posso saber se as chaves geridas pelo cliente estão ativadas na minha conta Azure Cosmos?

A partir do portal Azure, vá à sua conta Azure Cosmos e assista à entrada de Encriptação de **Dados** no menu esquerdo; se esta entrada existir, as chaves geridas pelo cliente estão ativadas na sua conta:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="A entrada do menu de encriptação de dados":::

Você também pode buscar programáticamente os detalhes da sua conta Azure Cosmos e procurar a presença da `keyVaultKeyUri` propriedade. Veja acima as formas de o fazer [em PowerShell](#using-powershell) e [utilizando o Azure CLI](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como é que as chaves geridas pelo cliente afetam uma cópia de segurança?

A Azure Cosmos DB faz [cópias de segurança regulares e automáticas dos dados armazenados](./online-backup-and-restore.md) na sua conta. Esta operação confirma os dados encriptados. Para utilizar a cópia de segurança restaurada, é necessária a chave de encriptação que utilizou no momento da cópia de segurança. Isto significa que não foi feita nenhuma revogação e a versão da chave que foi utilizada no momento da cópia de segurança continuará ativada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como revogo uma chave de encriptação?

A revogação da chave é feita desativando a versão mais recente da chave:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Desativar a versão de uma chave":::

Em alternativa, para revogar todas as chaves de uma instância do Cofre da Chave Azure, pode eliminar a política de acesso concedida ao diretor da Azure Cosmos DB:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Eliminação da política de acesso ao diretor da Azure Cosmos DB":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Que operações estão disponíveis após a revogação de uma chave gerida pelo cliente?

A única operação possível quando a chave de encriptação foi revogada é a eliminação da conta.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [encriptação de dados em Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenha uma visão geral do [acesso seguro aos dados em Cosmos DB](secure-access-to-data.md).
