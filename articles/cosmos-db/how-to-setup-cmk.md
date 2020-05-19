---
title: Configure as chaves geridas pelo cliente para a sua conta Azure Cosmos DB
description: Saiba como configurar as chaves geridas pelo cliente para a sua conta Azure Cosmos DB com o Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: thweiss
ms.openlocfilehash: 5629ddfe496ef1abd071ab579c885cbe1adeb344
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592112"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Configure as chaves geridas pelo cliente para a sua conta Azure Cosmos com o Cofre chave Azure

Os dados armazenados na sua conta Azure Cosmos são automaticamente e sem problemas encriptados com chaves geridas pela Microsoft **(chaves geridas**pelo serviço). Opcionalmente, pode optar por adicionar uma segunda camada de encriptação com chaves que gere **(chaves geridas pelo cliente).**

![Camadas de encriptação em torno dos dados do cliente](./media/how-to-setup-cmk/cmk-intro.png)

Você deve armazenar chaves geridas pelo cliente no [Cofre de Chaves Azure](../key-vault/general/overview.md) e fornecer uma chave para cada conta Azure Cosmos que está ativada com chaves geridas pelo cliente. Esta chave é usada para encriptar todos os dados armazenados nessa conta.

> [!NOTE]
> Atualmente, as chaves geridas pelo cliente estão disponíveis apenas para novas contas Azure Cosmos. Deve configurá-los durante a criação de conta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registe o fornecedor de recursos Azure Cosmos DB para a sua subscrição Azure

1. Inscreva-se no [portal Azure,](https://portal.azure.com/)vá à subscrição do Azure e selecione **fornecedores** de Recursos no separador **Definições:**

   ![Entrada "fornecedores de recursos" do menu esquerdo](./media/how-to-setup-cmk/portal-rp.png)

1. Procure o fornecedor de recursos **Microsoft.DocumentDB.** Verifique se o fornecedor de recursos já está marcado como registado. Caso contrário, escolha o fornecedor de recursos e selecione **Register:**

   ![Registar o fornecedor de recursos Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Configure a sua instância de cofre de chaves Azure

A utilização de chaves geridas pelo cliente com o Azure Cosmos DB requer que você coloque duas propriedades na instância Do Cofre chave Azure que planeia usar para alojar as suas chaves de encriptação: **Soft Delete** e **Purge Protection**.

Se criar uma nova instância do Cofre chave Azure, ative estas propriedades durante a criação:

![Permitir eliminar e purgar suavemente a proteção para uma nova instância do Cofre chave Azure](./media/how-to-setup-cmk/portal-akv-prop.png)

Se estiver a utilizar uma instância de cofre de chaves Azure existente, pode verificar se estas propriedades estão ativadas olhando para a secção **Propriedades** no portal Azure. Se alguma destas propriedades não estiver ativada, consulte as secções "Habilitar soft-delete" e "Enableing Purge Protection" num dos seguintes artigos:

- [Como utilizar soft-delete com powerShell](../key-vault/general/soft-delete-powershell.md)
- [Como utilizar soft-delete com o Azure CLI](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Adicione uma política de acesso à sua instância azure key vault

1. A partir do portal Azure, vá ao cofre de chaves Azure que planeia usar para alojar as suas chaves de encriptação. Selecione Políticas de **Acesso** a partir do menu esquerdo:

   !["Políticas de acesso" do menu esquerdo](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Selecione **+ Adicionar Política de Acesso**.

1. No menu de **permissões chave,** selecione **'Obter,** **Desembrulhar chave'** e obter permissões de chave de **embrulho:**

   ![Selecionando as permissões certas](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. Em **selecionar o diretor,** selecione **Nenhum selecionado**. Em seguida, procure o principal do **Azure Cosmos DB** e selecione-o (para facilitar a sua descoberta, também pode pesquisar pelo PRINCIPAL ID: `a232010e-820c-4083-83bb-3ace5fc29d0b` para qualquer região azure, exceto regiões do Governo Azure onde está o ID `57506a73-e302-42a9-b869-6f12d9ec29e9` principal). Por fim, escolha **Selecionar** na parte inferior. Se o diretor do **Azure Cosmos DB** não estiver na lista, poderá ter de voltar a registar o fornecedor de recursos **Microsoft.DocumentDB,** tal como descrito no Registo da secção de [fornecedor de recursos](#register-resource-provider) deste artigo.

   ![Selecione o diretor do Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Selecione **Adicionar** para adicionar a nova política de acesso.

## <a name="generate-a-key-in-azure-key-vault"></a>Gerar uma chave no Cofre de Chaves Azure

1. A partir do portal Azure, vá à instância do Cofre chave Azure que planeia usar para alojar as suas chaves de encriptação. Em seguida, selecione **Teclas** do menu esquerdo:

   ![Entrada "Chaves" do menu esquerdo](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Selecione **Generate/Import,** forneça um nome para a nova tecla e selecione um tamanho de chave RSA. Um mínimo de 3072 é recomendado para uma melhor segurança. Em seguida, selecione **Criar:**

   ![Criar uma nova chave](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Depois de criada a chave, selecione a chave recém-criada e, em seguida, a sua versão atual.

1. Copie o **identificador**chave da chave, exceto a peça após o último corte para a frente:

   ![Copiar o identificador chave da chave](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Criar uma nova conta Azure Cosmos

### <a name="using-the-azure-portal"></a>Utilizar o portal do Azure

Quando criar uma nova conta Azure Cosmos DB a partir do portal Azure, escolha a **chave gerida pelo Cliente** no passo de **Encriptação.** No campo **Key URI,** cola o identificador URI/chave da chave do Cofre de Chaves Azure que copiou do passo anterior:

![Definição de parâmetros CMK no portal Azure](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a><a id="using-powershell"></a>Usando a Azure PowerShell

Quando cria uma nova conta Azure Cosmos DB com a PowerShell:

- Passe o URI da chave Azure Key Vault copiada anteriormente sob a **propriedade keyVaultKeyUri** em **PropertyObject**.

- Utilize **2019-12-12** ou mais tarde como versão API.

> [!IMPORTANT]
> Deve definir a `locations` propriedade explicitamente para que a conta seja criada com sucesso com chaves geridas pelo cliente.

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

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo de Gestor de Recursos Azure

Quando cria uma nova conta Azure Cosmos através de um modelo de Gestor de Recursos Azure:

- Passe o URI da chave Azure Key Vault que copiou anteriormente sob a **propriedade keyVaultKeyUri** no objeto de **propriedades.**

- Utilize **2019-12-12** ou mais tarde como versão API.

> [!IMPORTANT]
> Deve definir a `locations` propriedade explicitamente para que a conta seja criada com sucesso com chaves geridas pelo cliente.

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

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a>Utilização do Azure CLI

Quando criar uma nova conta Azure Cosmos através do Azure CLI, passe o URI da chave Azure Key Vault que copiou anteriormente sob o `--key-uri` parâmetro.

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

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Existe um custo adicional para ativar as chaves geridas pelo cliente?

Não, não há nenhuma carga para ativar esta funcionalidade.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Como é que o planeamento da capacidade de impacto das chaves geridas pelo cliente?

Ao utilizar as chaves geridas pelo cliente, [as Unidades](./request-units.md) de Pedido consumidas pelas suas operações de base de dados vêem um aumento para refletir o processamento adicional necessário para realizar encriptação e desencriptação dos seus dados. Isto pode levar a uma utilização ligeiramente mais elevada da sua capacidade aprovisionada. Utilize o quadro abaixo para orientação:

| Tipo de operação | Aumento da Unidade de Pedidos |
|---|---|
| Leituras de pontos (itens de busca pelo seu ID) | + 5% por operação |
| Qualquer operação de escrita | + 6% por operação<br/>aprox. + 0,06 RU por propriedade indexada |
| Consultas, leitura de feed de mudança, ou feed de conflitos | + 15% por operação |

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

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Como posso saber se as chaves geridas pelo cliente estão ativadas na minha conta Azure Cosmos?

Pode recolher programaticamente os detalhes da sua conta Azure Cosmos e procurar a presença da `keyVaultKeyUri` propriedade. Veja acima formas de o fazer [na PowerShell](#using-powershell) e [utilizando o Azure CLI](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Como é que as chaves geridas pelo cliente afetam uma cópia de segurança?

A Azure Cosmos DB recebe cópias de [segurança regulares e automáticas](./online-backup-and-restore.md) dos dados armazenados na sua conta. Esta operação apoia os dados encriptados. Para utilizar a cópia de segurança restaurada, é necessária a chave de encriptação que utilizou no momento da cópia de segurança. Isto significa que não foi feita qualquer revogação e que a versão da chave que foi utilizada no momento da cópia de segurança ainda estará ativada.

### <a name="how-do-i-rotate-an-encryption-key"></a>Como posso rodar uma chave de encriptação?

A rotação da chave é realizada através da criação de uma nova versão da chave no Cofre chave Azure:

![Criar uma nova versão chave](./media/how-to-setup-cmk/portal-akv-rot.png)

A versão anterior pode ser desativada após 24 horas, ou depois de os registos de auditoria do [Azure Key Vault](../key-vault/general/logging.md) já não mostrarem atividade do Azure Cosmos DB nessa versão.

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
