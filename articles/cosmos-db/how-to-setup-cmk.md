---
title: Configure as chaves geridas pelo cliente para a sua conta DB Azure Cosmos
description: Saiba como configurar chaves geridas pelo cliente para a sua conta DB Azure Cosmos com Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: thweiss
ms.openlocfilehash: 31681397961045da02add7ccb37f29f6c835c08d
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659892"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configure chaves geridas pelo cliente para a sua conta do Azure Cosmos com o Azure Key Vault

Os dados armazenados na sua conta Azure Cosmos são automaticamente e sem problemas encriptados com teclas geridas pela Microsoft **(teclas geridas**pelo serviço). Opcionalmente, pode optar por adicionar uma segunda camada de encriptação com as teclas geridas **(teclas geridas pelo cliente).**

![Camadas de encriptação em torno dos dados do cliente](./media/how-to-setup-cmk/cmk-intro.png)

Tem de armazenar chaves geridas pelo cliente no [Azure Key Vault](../key-vault/general/overview.md) e fornecer uma chave para cada conta Azure Cosmos que esteja ativada com chaves geridas pelo cliente. Esta chave é usada para encriptar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves geridas pelo cliente estão disponíveis apenas para novas contas Azure Cosmos. Deve configurá-los durante a criação de conta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registe o fornecedor de recursos DB da Azure Cosmos para a sua subscrição Azure

1. Inscreva-se no [portal Azure,](https://portal.azure.com/)aceda à subscrição do Azure e selecione **fornecedores de Recursos** no separador **Definições:**

   ![Entrada de "fornecedores de recursos" a partir do menu esquerdo](./media/how-to-setup-cmk/portal-rp.png)

1. Procure o fornecedor de recursos **Microsoft.DocumentDB.** Verifique se o fornecedor de recursos já está marcado como registado. Caso contrário, escolha o fornecedor de recursos e **selecione Registar-se:**

   ![Registo do Microsoft.Docfornecedor de recursos umentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configure o seu exemplo de Cofre de Chave Azure

A utilização de chaves geridas pelo cliente com a Azure Cosmos DB requer que você descreva duas propriedades no abobadador Azure Key Vault que planeia utilizar para hospedar as suas chaves de encriptação: **Soft Delete** and **Purpur Protection**.

Se criar uma nova instância Azure Key Vault, ative estas propriedades durante a criação:

![Permitir a eliminação suave e a proteção de purga para uma nova instância Azure Key Vault](./media/how-to-setup-cmk/portal-akv-prop.png)

Se estiver a utilizar uma instância Azure Key Vault existente, pode verificar se estas propriedades estão ativadas olhando para a secção **Propriedades** no portal Azure. Se alguma destas propriedades não estiver ativada, consulte as secções "Habilitar a eliminação suave" e "Habilitar a proteção da purga" num dos seguintes artigos:

- [Como utilizar soft-delete com PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Como utilizar soft-delete com Azure CLI](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicione uma política de acesso ao seu Azure Key Vault

1. A partir do portal Azure, vá ao Azure Key Vault que planeia usar para hospedar as suas chaves de encriptação. Selecione Políticas de **Acesso** a partir do menu esquerdo:

   !["Políticas de acesso" do menu à esquerda](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecione **+ Adicionar Política de Acesso**.

1. No menu de permissões de perguntas à **chave,** selecione **Obter,** **Desembrulhar a chave**e as permissões de Tecla de **Embrulho:**

   ![Selecionando as permissões certas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Em **Select principal**, selecione Nenhum **selecionado**. Em seguida, procure o diretor da **Azure Cosmos DB** e selecione-o (para facilitar a sua posição, também pode pesquisar por ID principal: `a232010e-820c-4083-83bb-3ace5fc29d0b` para qualquer região de Azure, exceto regiões do Governo Azure onde o ID principal `57506a73-e302-42a9-b869-6f12d9ec29e9` é). Por fim, escolha **Selecione** na parte inferior. Se o diretor da **Azure Cosmos** não estiver na lista, poderá ter de voltar a registar o fornecedor de recursos **Microsoft.DocumentDB,** conforme descrito no [Registo da](#register-resource-provider) secção fornecedora de recursos deste artigo.

   ![Selecione o principal da Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. **Selecione Adicionar** para adicionar a nova política de acesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Gere uma chave no Cofre da Chave Azure

1. A partir do portal Azure, vá ao Azure Key Vault que planeia usar para hospedar as suas chaves de encriptação. Em seguida, selecione **Chaves** do menu esquerdo:

   ![Entrada "Chaves" do menu esquerdo](./media/how-to-setup-cmk/portal-akv-keys.png)

1. **Selecione Gerar/Importar,** forneça um nome para a nova chave e selecione um tamanho da chave RSA. Recomenda-se um mínimo de 3072 para a melhor segurança. Em seguida, **selecione Criar**:

   ![Criar uma nova chave](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Após a criação da chave, selecione a chave recém-criada e, em seguida, a sua versão atual.

1. Copie o **identificador**chave da chave, exceto a parte após o último corte dianteiro:

   ![Copiar o identificador chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta Azure Cosmos

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Quando criar uma nova conta DB Azure Cosmos a partir do portal Azure, escolha a **chave gerida pelo Cliente** no passo de **Encriptação.** No campo **Key URI,** cole o identificador URI/chave da chave Azure Key Vault que copiou do passo anterior:

![Definição de parâmetros CMK no portal Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a><a id="using-powershell"></a>Usando a Azure PowerShell

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

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a>Utilização do CLI Azure

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

## <a name="key-rotation"></a>Rotação de chaves

A rotação da chave gerida pelo cliente utilizada pela sua conta Azure Cosmos pode ser feita de duas formas.

- Criar uma nova versão da chave atualmente utilizada a partir do Cofre da Chave Azure:

  ![Criar uma nova versão chave](./media/how-to-setup-cmk/portal-akv-rot.png)

- Troque a chave atualmente utilizada por uma totalmente diferente, atualizando a `keyVaultKeyUri` propriedade da sua conta. Eis como fazê-lo no PowerShell:

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

Ao utilizar chaves geridas pelo cliente (CMK) em Azure Cosmos DB, se houver algum erro, a Azure Cosmos DB devolve os detalhes de erro juntamente com um código de sub-estado HTTP na resposta. Pode utilizar este código de sub-estado para depurar a causa principal do problema. Consulte os códigos de estado HTTP para o artigo [DB da Azure Cosmos](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) para obter a lista de códigos de sub-estado HTTP suportados.

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

- Os nomes das suas contas, bases de [dados e contentores](./account-overview.md#elements-in-an-azure-cosmos-account) da Azure Cosmos

- Os nomes dos seus [procedimentos armazenados](./stored-procedures-triggers-udfs.md)

- Os caminhos da propriedade declarados nas suas [políticas de indexação](./index-policy.md)

- Os valores das chaves de [partição](./partitioning-overview.md) dos seus contentores

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>As chaves geridas pelo cliente são suportadas para as contas existentes da Azure Cosmos?

Esta funcionalidade encontra-se atualmente disponível apenas para novas contas.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existe um plano para suportar uma granularidade mais fina do que as chaves de nível de conta?

Atualmente não, mas as chaves ao nível do contentor estão a ser consideradas.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Como posso saber se as chaves geridas pelo cliente estão ativadas na minha conta Azure Cosmos?

Você pode programáticamente buscar os detalhes da sua conta Azure Cosmos e procurar a presença da `keyVaultKeyUri` propriedade. Veja acima as formas de o fazer [em PowerShell](#using-powershell) e [utilizando o Azure CLI](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como é que as chaves geridas pelo cliente afetam uma cópia de segurança?

A Azure Cosmos DB faz [cópias de segurança regulares e automáticas dos dados armazenados](./online-backup-and-restore.md) na sua conta. Esta operação confirma os dados encriptados. Para utilizar a cópia de segurança restaurada, é necessária a chave de encriptação que utilizou no momento da cópia de segurança. Isto significa que não foi feita nenhuma revogação e a versão da chave que foi utilizada no momento da cópia de segurança continuará ativada.

### <a name="how-do-i-revoke-an-encryption-key"></a>Como revogo uma chave de encriptação?

A revogação da chave é feita desativando a versão mais recente da chave:

![Desativar a versão de uma chave](./media/how-to-setup-cmk/portal-akv-rev2.png)

Em alternativa, para revogar todas as chaves de uma instância do Cofre da Chave Azure, pode eliminar a política de acesso concedida ao diretor da Azure Cosmos DB:

![Eliminação da política de acesso ao diretor da Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Que operações estão disponíveis após a revogação de uma chave gerida pelo cliente?

A única operação possível quando a chave de encriptação foi revogada é a eliminação da conta.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [encriptação de dados em Azure Cosmos DB](./database-encryption-at-rest.md).
- Obtenha uma visão geral do [acesso seguro aos dados em Cosmos DB](secure-access-to-data.md).
