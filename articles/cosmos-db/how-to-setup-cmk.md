---
title: Configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB
description: Saiba como configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911871"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre em contato com [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Os dados armazenados em sua conta de Azure Cosmos DB são criptografados de forma automática e direta. O Azure Cosmos DB oferece duas opções para gerenciar as chaves usadas para criptografar seus dados em repouso:
- **Chaves gerenciadas pelo serviço**. Por padrão, a Microsoft gerencia as chaves usadas para criptografar sua conta de Azure Cosmos DB.
- **CMK (chaves gerenciadas pelo cliente)** . Opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com as chaves que você gerencia.

As chaves gerenciadas pelo cliente devem ser armazenadas em [Azure Key Vault](../key-vault/key-vault-overview.md). Uma chave deve ser fornecida para cada conta habilitada para CMK e é usada para criptografar todos os dados armazenados nessa conta.

## <a name="setup"></a>Configuração

Atualmente, as chaves gerenciadas pelo cliente estão disponíveis apenas para novas contas e precisam ser configuradas durante a criação da conta.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Verifique se o provedor de recursos de Azure Cosmos DB está registrado para sua assinatura do Azure

No portal do Azure, acesse sua assinatura do Azure e selecione **provedores de recursos** no menu à esquerda:

![Entrada de "provedores de recursos" no menu à esquerda](./media/how-to-setup-cmk/portal-rp.png)

Procure o provedor de recursos **Microsoft. DocumentDB** .
- Se o provedor de recursos já estiver marcado como registrado, nada precisará ser feito.
- Caso contrário, selecione-o e clique em **registrar**:

    ![Registrando o provedor de recursos Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. configurar sua instância de Azure Key Vault

O uso de chaves gerenciadas pelo cliente com Azure Cosmos DB requer que duas propriedades sejam definidas na instância de Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia: **exclusão reversível** e **não limpar**. Essas propriedades não são habilitadas por padrão, mas podem ser habilitadas usando o PowerShell ou o CLI do Azure.

Para saber como habilitar essas propriedades em uma instância existente do Azure Key Vault, consulte as seções intituladas habilitando a exclusão reversível e habilitando a proteção de limpeza em um dos seguintes artigos:
- [Como usar a exclusão reversível com o PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Como usar a exclusão reversível com CLI do Azure](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. adicionar uma política de acesso à sua instância do Azure Key Vault

Na portal do Azure, vá para a instância de Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Em seguida, selecione **políticas de acesso** no menu à esquerda:

!["Políticas de acesso" no menu à esquerda](./media/how-to-setup-cmk/portal-akv-ap.png)

- Selecione **+ Adicionar política de acesso**
- No menu **suspenso permissões de chave** , selecione **obter**, **desencapsular chave** e **encapsular chave**:

    ![Selecionando as permissões corretas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- Em **selecionar entidade de segurança**, selecione **nenhum selecionado**. Em seguida, pesquise e selecione a entidade de **Azure Cosmos DB** principal. Por fim, clique em **selecionar** na parte inferior (se o **Azure Cosmos DB** principal não puder ser encontrado, talvez seja necessário registrar novamente o provedor de recursos **Microsoft. DocumentDB** na etapa 1):

    ![Selecionando a entidade de Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Selecione **Adicionar** para adicionar a nova política de acesso

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. gerar uma chave no Azure Key Vault

No portal do Azure, acesse a instância de Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Em seguida, selecione **chaves** no menu à esquerda:

![Entrada "Keys" no menu à esquerda](./media/how-to-setup-cmk/portal-akv-keys.png)

- Selecionar **gerar/importar**
- Forneça um nome para a nova chave, selecione um tamanho de chave RSA (um mínimo de 3072 é recomendado para melhor segurança) e selecione **criar**:

    ![Criando uma nova chave](./media/how-to-setup-cmk/portal-akv-gen.png)

- Depois que a chave for criada, clique na chave recém-criada e em sua versão atual
- Copie o identificador de **chave** da chave, exceto a parte após a última barra de encaminhamento:

    ![Copiando o identificador de chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. criar uma nova conta de Azure Cosmos DB

#### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Ao criar uma nova conta de Azure Cosmos DB do portal do Azure, escolha **chave gerenciada pelo cliente** na etapa de **criptografia** . No campo **URI da chave** , passe o URI da chave de Azure Key Vault copiada da etapa 4:

![Definindo parâmetros CMK no portal do Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>Utilizar o PowerShell

Ao criar uma nova conta de Azure Cosmos DB com o PowerShell,
- Passe o URI da chave de Azure Key Vault copiada da etapa 4 na propriedade **keyVaultKeyUri** no **propertyobject**,
- Certifique-se de usar "2019-12-12" como a versão da API.

> [!IMPORTANT]
> O parâmetro `Location` precisa ser definido explicitamente para que a conta seja criada com êxito com CMK.

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

#### <a name="using-azure-resource-manager-templates"></a>Usando modelos de Azure Resource Manager

Ao criar uma nova conta de Azure Cosmos DB por meio de um modelo de Azure Resource Manager:
- Passe o URI da chave de Azure Key Vault copiada da etapa 4 na propriedade **keyVaultKeyUri** no objeto de **Propriedades**
- Certifique-se de usar "2019-12-12" como a versão da API

> [!IMPORTANT]
> O parâmetro `location` precisa ser definido explicitamente para que a conta seja criada com êxito com CMK.

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

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Há algum custo adicional ao usar chaves gerenciadas pelo cliente?

Sim. Para considerar a carga de computação adicional necessária para gerenciar a criptografia e a descriptografia de dados com chaves gerenciadas pelo cliente, todas as operações executadas na conta de Azure Cosmos DB Obtém um aumento de 25% nas [unidades de solicitação](./request-units.md) consumidas.

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Quais dados são criptografados com o CMK?

Todos os dados armazenados em sua conta de Azure Cosmos DB são criptografados com o CMK, exceto para os seguintes metadados:
- os nomes de suas contas de Azure Cosmos DB [, bancos de dados e contêineres](./account-overview.md#elements-in-an-azure-cosmos-account),
- os nomes dos [procedimentos armazenados](./stored-procedures-triggers-udfs.md),
- os caminhos de propriedade declarados em suas [políticas de indexação](./index-policy.md),
- os valores da [chave de partição](./partitioning-overview.md)de seus contêineres.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>As chaves gerenciadas pelo cliente terão suporte para as contas existentes?

Este recurso está disponível atualmente apenas para novas contas.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Há um plano para dar suporte à granularidade mais fina do que as chaves de nível de conta?

Atualmente não, no entanto, as chaves de nível de contêiner estão sendo consideradas.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Como as chaves gerenciadas pelo cliente afetam os backups?

Azure Cosmos DB usa [backups regulares e automáticos](./online-backup-and-restore.md) dos dados armazenados em sua conta. Esta operação faz backup dos dados criptografados. Para que um backup restaurado seja utilizável, a chave de criptografia usada no momento do backup ainda deve estar disponível. Isso significa que nenhuma revogação deve ter sido feita e a versão da chave que foi usada no momento do backup ainda deve estar habilitada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como fazer revogar uma chave de criptografia?

A revogação de chave é feita desabilitando a versão mais recente da chave:

![Desabilitando a versão de uma chave](./media/how-to-setup-cmk/portal-akv-rev2.png)

Como alternativa, para revogar todas as chaves de uma instância de Azure Key Vault, você pode excluir a política de acesso concedida à entidade de Azure Cosmos DB:

![Excluindo a política de acesso para a entidade de Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quais operações estão disponíveis depois que uma chave gerenciada pelo cliente é revogada?

A única operação possível quando a chave de criptografia foi revogada é a exclusão da conta.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [criptografia de dados no Azure Cosmos DB](./database-encryption-at-rest.md)
- Obtenha uma visão geral de [acesso seguro aos dados no cosmos DB](secure-access-to-data.md)