---
title: Configure as chaves geridas pelo cliente para a sua conta Azure Cosmos DB
description: Saiba como configurar as chaves geridas pelo cliente para a sua conta Azure Cosmos DB com o Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 44bbd7eab80ecb1cbfef9738e42b4070dff31180
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78392413"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configure as chaves geridas pelo cliente para a sua conta Azure Cosmos com o Cofre chave Azure

> [!NOTE]
> Neste momento, deve solicitar o acesso à utilização desta capacidade. Para isso, contacte [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com).

Os dados armazenados na sua conta Azure Cosmos são encriptados automaticamente e sem problemas. A Azure Cosmos DB oferece duas opções para gerir as chaves utilizadas para encriptar os dados em repouso:

- **Chaves geridas pelo serviço**: Por padrão, a Microsoft gere as chaves que são usadas para encriptar os dados na sua conta Azure Cosmos.

- **Chaves geridas pelo cliente (CMK)** : Pode optar opcionalmente por adicionar uma segunda camada de encriptação com as suas próprias chaves.

Você deve armazenar chaves geridas pelo cliente no [Cofre de Chaves Azure](../key-vault/key-vault-overview.md) e fornecer uma chave para cada conta Azure Cosmos que está ativada com chaves geridas pelo cliente. Esta chave é usada para encriptar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves geridas pelo cliente estão disponíveis apenas para novas contas Azure Cosmos. Deve configurá-los durante a criação de conta.

## <a id="register-resource-provider"></a>Registe o fornecedor de recursos Azure Cosmos DB para a sua subscrição Azure

1. Inscreva-se no [portal Azure,](https://portal.azure.com/)vá à subscrição do Azure e selecione **fornecedores** de Recursos no separador **Definições:**

   ![Entrada "fornecedores de recursos" do menu esquerdo](./media/how-to-setup-cmk/portal-rp.png)

1. Procure o fornecedor de recursos **Microsoft.DocumentDB.** Verifique se o fornecedor de recursos já está marcado como registado. Caso contrário, escolha o fornecedor de recursos e selecione **Register:**

   ![Registar o fornecedor de recursos Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configure a sua instância de cofre de chaves Azure

A utilização de chaves geridas pelo cliente com o Azure Cosmos DB requer que você coloque duas propriedades na instância Azure Key Vault que planeia usar para hospedar as suas chaves de encriptação. Estas propriedades incluem **Soft Delete** e **Não Expurgar**. Estas propriedades não são ativadas por defeito. Pode activar-os utilizando o PowerShell ou o Azure CLI.

Para aprender a permitir estas propriedades numa instância existente do Cofre chave Azure, consulte as secções "Enableing soft-delete" e "Enableing Purge Protection" num dos seguintes artigos:

- [Como utilizar soft-delete com powerShell](../key-vault/key-vault-soft-delete-powershell.md)
- [Como utilizar soft-delete com o Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicione uma política de acesso à sua instância azure key vault

1. A partir do portal Azure, vá ao cofre de chaves Azure que planeia usar para alojar as suas chaves de encriptação. Selecione Políticas de **Acesso** a partir do menu esquerdo:

   !["Políticas de acesso" do menu esquerdo](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecione **+ Adicionar Política de Acesso**.

1. No menu de **permissões chave,** selecione **'Obter,** **Desembrulhar chave'** e obter permissões de chave de **embrulho:**

   ![Selecionando as permissões certas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Em **selecionar o diretor,** selecione **Nenhum selecionado**. Em seguida, procure o diretor do **Azure Cosmos DB** e selecione-o (para facilitar a sua descoberta, também pode pesquisar pelo PRINCIPAL ID: `a232010e-820c-4083-83bb-3ace5fc29d0b` para qualquer região azure, exceto regiões do Governo Azure onde o ID principal é `57506a73-e302-42a9-b869-6f12d9ec29e9`). Por fim, escolha **Selecionar** na parte inferior. Se o diretor do **Azure Cosmos DB** não estiver na lista, poderá ter de voltar a registar o fornecedor de recursos **Microsoft.DocumentDB,** tal como descrito no Registo da secção de [fornecedor de recursos](#register-resource-provider) deste artigo.

   ![Selecione o diretor do Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selecione **Adicionar** para adicionar a nova política de acesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Cofre de Chaves Azure

1. A partir do portal Azure, vá à instância do Cofre chave Azure que planeia usar para alojar as suas chaves de encriptação. Em seguida, selecione **Teclas** do menu esquerdo:

   ![Entrada "Chaves" do menu esquerdo](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selecione **Generate/Import,** forneça um nome para a nova tecla e selecione um tamanho de chave RSA. Um mínimo de 3072 é recomendado para uma melhor segurança. Em seguida, selecione **Criar:**

   ![Criar uma chave nova](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Depois de criada a chave, selecione a chave recém-criada e, em seguida, a sua versão atual.

1. Copie o **identificador**chave da chave, exceto a peça após o último corte para a frente:

   ![Copiar o identificador chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta Azure Cosmos

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Quando criar uma nova conta Azure Cosmos DB a partir do portal Azure, escolha a **chave gerida pelo Cliente** no passo de **Encriptação.** No campo **Key URI,** cola o identificador URI/chave da chave do Cofre de Chaves Azure que copiou do passo anterior:

![Definição de parâmetros CMK no portal Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Com o Azure PowerShell

Quando cria uma nova conta Azure Cosmos DB com a PowerShell:

- Passe o URI da chave Azure Key Vault copiada anteriormente sob a **propriedade keyVaultKeyUri** em **PropertyObject**.

- Utilize **2019-12-12** como versão API.

> [!IMPORTANT]
> Deve definir o parâmetro `Location` explicitamente para que a conta seja criada com sucesso com chaves geridas pelo cliente.

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

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Gestor de Recursos Azure

Quando cria uma nova conta Azure Cosmos através de um modelo de Gestor de Recursos Azure:

- Passe o URI da chave Azure Key Vault que copiou anteriormente sob a **propriedade keyVaultKeyUri** no objeto de **propriedades.**

- Utilize **2019-12-12** como versão API.

> [!IMPORTANT]
> Deve definir o parâmetro `Location` explicitamente para que a conta seja criada com sucesso com chaves geridas pelo cliente.

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

Desdobrar o modelo com o seguinte script PowerShell:

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

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Existe algum custo adicional para o uso de chaves geridas pelo cliente?

Sim. Para responder à carga de cálculo adicional que é necessária para gerir a encriptação de dados e a desencriptação com chaves geridas pelo cliente, todas as operações executadas contra a conta Azure Cosmos consomem um aumento de 25% nas [Unidades de Pedido.](./request-units.md)

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Que dados são encriptados com as chaves geridas pelo cliente?

Todos os dados armazenados na sua conta Azure Cosmos estão encriptados com as chaves geridas pelo cliente, com exceção dos seguintes metadados:

- Os nomes das suas contas Azure Cosmos DB, bases de [dados e contentores](./account-overview.md#elements-in-an-azure-cosmos-account)

- Os nomes dos seus [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos de propriedade declarados nas suas [políticas de indexação](./index-policy.md)

- Os valores das chaves de [partição](./partitioning-overview.md) dos seus contentores

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves geridas pelo cliente são suportadas pelas contas existentes da Azure Cosmos?

Esta funcionalidade encontra-se atualmente disponível apenas para novas contas.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existe um plano para apoiar a granularidade mais fina do que as chaves ao nível da conta?

Não atualmente, mas as chaves ao nível do contentor estão a ser consideradas.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como é que as chaves geridas pelo cliente afetam uma cópia de segurança?

A Azure Cosmos DB recebe cópias de [segurança regulares e automáticas](./online-backup-and-restore.md) dos dados armazenados na sua conta. Esta operação apoia os dados encriptados. Para utilizar a cópia de segurança restaurada, é necessária a chave de encriptação que utilizou no momento da cópia de segurança. Isto significa que não foi feita qualquer revogação e que a versão da chave que foi utilizada no momento da cópia de segurança ainda estará ativada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como revoguei uma chave de encriptação?

A revogação da chave é feita desativando a versão mais recente da chave:

![Desativar a versão de uma chave](./media/how-to-setup-cmk/portal-akv-rev2.png)

Alternativamente, para revogar todas as chaves de uma instância do Cofre chave Azure, pode eliminar a política de acesso concedida ao diretor da Azure Cosmos DB:

![Apagando a política de acesso ao diretor do Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Que operações estão disponíveis após a revogação de uma chave gerida pelo cliente?

A única operação possível quando a chave de encriptação foi revogada é a eliminação da conta.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre encriptação de [dados em Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenha uma visão geral do [acesso seguro aos dados em Cosmos DB](secure-access-to-data.md).
