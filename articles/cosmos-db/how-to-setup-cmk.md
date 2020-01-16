---
title: Configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB
description: Saiba como configurar chaves gerenciadas pelo cliente para sua conta de Azure Cosmos DB com Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: c77e89c509f10155ddc27e92f09465959b629f67
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979209"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configurar chaves gerenciadas pelo cliente para sua conta do Azure cosmos com Azure Key Vault

> [!NOTE]
> Neste momento, você deve solicitar acesso para usar esse recurso. Para fazer isso, entre em contato com [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Os dados armazenados em sua conta do Azure cosmos são criptografados de forma automática e direta. O Azure Cosmos DB oferece duas opções para gerenciar as chaves usadas para criptografar os dados em repouso:

- **Chaves gerenciadas pelo serviço** – por padrão, a Microsoft gerencia as chaves que são usadas para criptografar os dados em sua conta do Azure Cosmos.

- **CMK (chaves gerenciadas pelo cliente)** – opcionalmente, você pode optar por adicionar uma segunda camada de criptografia com suas próprias chaves.

Você deve armazenar chaves gerenciadas pelo cliente no [Azure Key Vault](../key-vault/key-vault-overview.md) e fornecer uma chave para cada conta do Azure Cosmos habilitada com chaves gerenciadas pelo cliente. Essa chave é usada para criptografar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves gerenciadas pelo cliente só estão disponíveis para novas contas do Azure Cosmos e você deve configurá-las durante a criação da conta.

## <a id="register-resource-provider"></a>Registrar o provedor de recursos de Azure Cosmos DB para sua assinatura do Azure

1. Entre no [portal do Azure](https://portal.azure.com/), vá para sua assinatura do Azure e selecione **provedores de recursos** na guia **configurações** :

   ![Entrada de "provedores de recursos" no menu à esquerda](./media/how-to-setup-cmk/portal-rp.png)

1. Procure o provedor de recursos **Microsoft. DocumentDB** . Verifique se o provedor de recursos já está marcado como registrado. Caso contrário, escolha o provedor de recursos e selecione **registrar**:

   ![Registrando o provedor de recursos Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configurar sua instância de Azure Key Vault

O uso de chaves gerenciadas pelo cliente com o Azure Cosmos DB exige que você defina duas propriedades na instância Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Essas propriedades incluem **exclusão reversível** e **não são limpas**. Essas propriedades não são habilitadas por padrão, você pode habilitá-las usando o PowerShell ou o CLI do Azure.

Para saber como habilitar essas propriedades em uma instância existente do Azure Key Vault, consulte as seções "Habilitando a exclusão reversível" e "Habilitando a proteção de limpeza" em um dos seguintes artigos:

- [Como usar a exclusão reversível com o PowerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Como usar a exclusão reversível com CLI do Azure](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicionar uma política de acesso à sua instância do Azure Key Vault

1. Na portal do Azure, vá para a instância de Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Selecione **políticas de acesso** no menu à esquerda:

   !["Políticas de acesso" no menu à esquerda](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecione **+ Adicionar política de acesso**

1. No menu **suspenso permissões de chave** , selecione as permissões **obter**, **desencapsular chave** e **encapsular chave** :

   ![Selecionando as permissões corretas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Em **selecionar entidade de segurança**, selecione **nenhum selecionado**. Em seguida, pesquise **Azure Cosmos DB** principal e selecione-o. Por fim, clique em **selecionar** na parte inferior (se a entidade de segurança **Azure Cosmos DB** não estiver na lista, talvez seja necessário registrar novamente o provedor de recursos **Microsoft. DocumentDB** conforme descrito em [registrar a seção provedor de recursos](#register-resource-provider) deste artigo):

   ![Selecionar a entidade de Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selecione **Adicionar** para adicionar a nova política de acesso

## <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Azure Key Vault

1. No portal do Azure, acesse a instância Azure Key Vault que você planeja usar para hospedar suas chaves de criptografia. Em seguida, selecione **chaves** no menu à esquerda:

   ![Entrada "Keys" no menu à esquerda](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selecione **gerar/importar**, forneça um nome para a nova chave, selecione um tamanho de chave RSA (um mínimo de 3072 é recomendado para obter a melhor segurança) e, em seguida, selecione **criar**:

   ![Criar uma chave nova](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Depois que a chave for criada, selecione a chave recém-criada e, em seguida, em sua versão atual.

1. Copie o identificador de **chave** da chave, exceto a parte após a última barra de encaminhamento:

   ![Copiando o identificador de chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta do Azure Cosmos

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Ao criar uma nova conta de Azure Cosmos DB do portal do Azure, escolha **chave gerenciada pelo cliente** na etapa de **criptografia** . No campo **URI da chave** , Cole o identificador de URI/chave da chave de Azure Key Vault que você copiou da etapa anterior:

![Definindo parâmetros CMK no portal do Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Ao criar uma nova conta de Azure Cosmos DB com o PowerShell,

- Passe o URI da chave de Azure Key Vault copiada do anterior sob a propriedade **keyVaultKeyUri** no **propertyobject**

- Use **2019-12-12** como a versão da API.

> [!IMPORTANT]
> Você deve definir o parâmetro `Location` explicitamente para que a conta seja criada com êxito com as chaves gerenciadas pelo cliente.

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

### <a name="using-azure-resource-manager-template"></a>Com o modelo do Azure Resource Manager

Ao criar uma nova conta do Azure Cosmos por meio de um modelo de Azure Resource Manager:

- Passe o URI da chave de Azure Key Vault que você copiou anteriormente na propriedade **keyVaultKeyUri** no objeto **Properties** .

- Use **2019-12-12** como a versão da API.

> [!IMPORTANT]
> Você deve definir o parâmetro `Location` explicitamente para que a conta seja criada com êxito com as chaves gerenciadas pelo cliente.

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

Implante o modelo com o seguinte script do PowerShell:

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

Sim. Para considerar a carga de computação adicional necessária para gerenciar a criptografia e a descriptografia de dados com chaves gerenciadas pelo cliente, todas as operações executadas na conta do Azure Cosmos consomem um aumento de 25% em [unidades de solicitação](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Quais dados são criptografados com as chaves gerenciadas pelo cliente?

Todos os dados armazenados em sua conta do Azure cosmos são criptografados com as chaves gerenciadas pelo cliente, exceto para os seguintes metadados:

- Os nomes de suas contas de Azure Cosmos DB [, bancos de dados e contêineres](./account-overview.md#elements-in-an-azure-cosmos-account)

- Os nomes dos [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos de propriedade declarados em suas [políticas de indexação](./index-policy.md)

- Os valores da [chave de partição](./partitioning-overview.md) de seus contêineres

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves gerenciadas pelo cliente têm suporte para contas existentes do Azure Cosmos?

Este recurso está disponível atualmente apenas para novas contas.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Há um plano para dar suporte à granularidade mais fina do que as chaves de nível de conta?

Atualmente não, no entanto, as chaves de nível de contêiner estão sendo consideradas.

### <a name="how-does-customer-managed-keys-affect-a-backup"></a>Como as chaves gerenciadas pelo cliente afetam um backup?

Azure Cosmos DB usa [backups regulares e automáticos](./online-backup-and-restore.md) dos dados armazenados em sua conta. Esta operação faz backup dos dados criptografados. Para usar o backup restaurado, a chave de criptografia que você usou no momento do backup é necessária. Isso significa que nenhuma revogação deve ter sido feita e a versão da chave que foi usada no momento do backup ainda deve estar habilitada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como fazer revogar uma chave de criptografia?

A revogação de chave é feita desabilitando a versão mais recente da chave:

![Desabilitar a versão de uma chave](./media/how-to-setup-cmk/portal-akv-rev2.png)

Como alternativa, para revogar todas as chaves de uma instância de Azure Key Vault, você pode excluir a política de acesso concedida à entidade de Azure Cosmos DB:

![Excluindo a política de acesso para a entidade de Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Quais operações estão disponíveis depois que uma chave gerenciada pelo cliente é revogada?

A única operação possível quando a chave de criptografia foi revogada é a exclusão da conta.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre a [criptografia de dados no Azure Cosmos DB](./database-encryption-at-rest.md)
- Obtenha uma visão geral de [acesso seguro aos dados no cosmos DB](secure-access-to-data.md)