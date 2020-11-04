---
title: Configurar o público anónimo ler acesso a contentores e bolhas
titleSuffix: Azure Storage
description: Saiba como permitir ou proibir o acesso anónimo aos dados blob para a conta de armazenamento. Defina a definição de acesso público do contentor para disponibilizar contentores e bolhas para acesso anónimo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: a808a6bb0d4dc75f02147bbfdd74d17b34e96f6e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313983"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurar o público anónimo ler acesso a contentores e bolhas

O Azure Storage suporta o acesso público anónimo opcional para recipientes e bolhas. Por padrão, o acesso anónimo aos seus dados nunca é permitido. A menos que você ative explicitamente o acesso anónimo, todos os pedidos para um recipiente e suas bolhas devem ser autorizados. Ao configurar a definição de nível de acesso público de um contentor para permitir o acesso anónimo, os clientes podem ler dados nesse recipiente sem autorizar o pedido.

> [!WARNING]
> Quando um recipiente é configurado para acesso público, qualquer cliente pode ler dados nesse recipiente. O acesso público apresenta um potencial risco de segurança, por isso, se o seu cenário não o exigir, a Microsoft recomenda que o desapromia para a conta de armazenamento. Para obter mais informações, consulte [Prevent anonymous public read access to containers and blobs](anonymous-read-access-prevent.md).

Este artigo descreve como configurar o público anónimo ler o acesso a um contentor e às suas bolhas. Para obter informações sobre como aceder a dados blob de forma anónima a partir de uma aplicação do cliente, consulte [access public containers e blobs anonimamente com .NET](anonymous-read-access-client.md).

## <a name="about-anonymous-public-read-access"></a>Sobre o acesso ao público anónimo

O acesso do público aos seus dados é sempre proibido por defeito. Existem duas configurações distintas que afetam o acesso do público:

1. **Permitir o acesso público à conta de armazenamento.** Por predefinição, uma conta de armazenamento permite a um utilizador com as permissões adequadas para permitir o acesso do público a um contentor. Os dados blob não estão disponíveis para acesso público, a menos que o utilizador tome o passo adicional para configurar explicitamente a configuração de acesso público do recipiente.
1. **Configure a definição de acesso público do contentor.** Por predefinição, a definição de acesso público de um contentor é desativada, o que significa que é necessária autorização para cada pedido ao contentor ou aos seus dados. Um utilizador com as permissões adequadas pode modificar a definição de acesso público de um contentor para permitir o acesso anónimo apenas se for permitido acesso anónimo à conta de armazenamento.

A tabela que se segue resume como ambas as configurações em conjunto afetam o acesso público a um contentor.

| Cenário de acesso público | O acesso público é desativado para um contentor (definição predefinição) | O acesso público a um contentor está definido para Contentor | Acesso público a um contentor está definido para Blob |
|--|--|--|--|
| O acesso público é proibido para a conta de armazenamento | Não há acesso público a nenhum contentor na conta de armazenamento. | Não há acesso público a nenhum contentor na conta de armazenamento. A definição da conta de armazenamento substitui a definição do recipiente. | Não há acesso público a nenhum contentor na conta de armazenamento. A definição da conta de armazenamento substitui a definição do recipiente. |
| O acesso público é permitido para a conta de armazenamento (definição predefinição) | Não há acesso público a este recipiente (configuração predefinida). | O acesso público é permitido a este contentor e às suas bolhas. | O acesso público é permitido a bolhas neste recipiente, mas não ao próprio contentor. |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>Permitir ou proibir o acesso público a uma conta de armazenamento

Por predefinição, uma conta de armazenamento é configurada para permitir a um utilizador com as permissões adequadas para permitir o acesso do público a um recipiente. Quando o acesso público é permitido, um utilizador com as permissões adequadas pode modificar a definição de acesso público de um contentor para permitir o acesso público anónimo aos dados nesse contentor. Os dados blob nunca estão disponíveis para acesso público, a menos que o utilizador tome o passo adicional para configurar explicitamente a definição de acesso público do recipiente.

Tenha em mente que o acesso do público a um recipiente é sempre desligado por defeito e deve ser explicitamente configurado para permitir pedidos anónimos. Independentemente da configuração da conta de armazenamento, os seus dados nunca estarão disponíveis para acesso público, a menos que um utilizador com permissões adequadas dê este passo adicional para permitir o acesso do público no recipiente.

A não permitir o acesso público à conta de armazenamento impede o acesso anónimo a todos os contentores e bolhas nessa conta. Quando o acesso público é proibido para a conta, não é possível configurar a definição de acesso público para um contentor para permitir o acesso anónimo. Para uma maior segurança, a Microsoft recomenda que não seja permitido o acesso do público às suas contas de armazenamento, a menos que o seu cenário exija que os utilizadores acedam a recursos blob de forma anónima.

> [!IMPORTANT]
> A não permitir o acesso do público a uma conta de armazenamento sobrepõe-se às definições de acesso público a todos os contentores dessa conta de armazenamento. Quando o acesso público for proibido para a conta de armazenamento, quaisquer futuros pedidos anónimos a essa conta falharão. Antes de alterar esta definição, certifique-se de compreender o impacto nas aplicações do cliente que podem estar a aceder a dados na sua conta de armazenamento de forma anónima. Para obter mais informações, consulte [Prevent anonymous public read access to containers and blobs](anonymous-read-access-prevent.md).

Para permitir ou proibir o acesso público a uma conta de armazenamento, configurar a propriedade **AllowBlobPublicAccess** da conta. Esta propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Gestor de Recursos Azure. Para mais informações, consulte [a visão geral da conta de Armazenamento.](../common/storage-account-overview.md)

A propriedade **AllowBlobPublicAccess** não é definida por padrão e não devolve um valor até que o descreva explicitamente. A conta de armazenamento permite o acesso público quando o valor da propriedade é **nulo** ou **verdadeiro.**

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para permitir ou proibir o acesso do público a uma conta de armazenamento no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Localizar a **definição de configuração** em **Definições**.
1. Descreva **o acesso público blob** a **Ativação** ou **Desativação**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot mostrando como permitir ou desativar o acesso público blob para conta":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para permitir ou proibir o acesso público a uma conta de armazenamento com a PowerShell, instale [a versão 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) ou posterior da Azure PowerShell. Em seguida, configurar a propriedade **AllowBlobPublicAccess** para uma conta de armazenamento nova ou existente.

O exemplo a seguir cria uma conta de armazenamento e define explicitamente a propriedade **AllowBlobPublicAccess** para **verdadeira**. Em seguida, atualiza a conta de armazenamento para definir a propriedade **AllowBlobPublicAccess** para **falso**. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para permitir ou proibir o acesso público a uma conta de armazenamento com o Azure CLI, instale a versão 2.9.0 ou posterior do Azure CLI. Para mais informações, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli). Em seguida, configurar a propriedade **allowBlobPublicAccess** para uma conta de armazenamento nova ou existente.

O exemplo a seguir cria uma conta de armazenamento e define explicitamente a propriedade **allowBlobPublicAccess** para **verdade**. Em seguida, atualiza a conta de armazenamento para definir a propriedade **allowBlobPublicAccess** para **falso**. O exemplo também recupera o valor da propriedade em cada caso. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[Modelo](#tab/template)

Para permitir ou proibir o acesso público a uma conta de armazenamento com um modelo, crie um modelo com a propriedade **AllowBlobPublicAccess** definida como **verdadeira** ou **falsa**. Os passos seguintes descrevem como criar um modelo no portal Azure.

1. No portal Azure, escolha **Criar um recurso.**
1. Em **Search the Marketplace** , **digitar a implementação do modelo** e, em seguida, premir **ENTER**.
1. Escolha a **implementação do modelo (implementar usando modelos personalizados) (pré-visualização)** , escolha **Criar** e, em seguida, escolha **Construir o seu próprio modelo no editor**.
1. No editor de modelo, cole no JSON seguinte para criar uma nova conta e definir a propriedade **AllowBlobPublicAccess** para **verdadeiro** ou **falso**. Lembre-se de substituir os espaços reservados em suportes angulares pelos seus próprios valores.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. Guarde o modelo.
1. Especifique o parâmetro do grupo de recursos, em seguida, escolha o botão **Review + criar** para implementar o modelo e criar uma conta de armazenamento com a propriedade **allowBlobPublicAccess** configurada.

---

> [!NOTE]
> A não permitir o acesso público a uma conta de armazenamento não afeta quaisquer sites estáticos alojados nessa conta de armazenamento. O **recipiente $web** é sempre acessível ao público.
>
> Depois de atualizar a definição de acesso público para a conta de armazenamento, pode demorar até 30 segundos antes de a alteração ser totalmente propagada.

Permitir ou não permitir o acesso público blob requer a versão 2019-04-01 ou mais tarde do fornecedor de recursos de armazenamento Azure. Para obter mais informações, consulte [a Azure Storage Resource Provider REST API](/rest/api/storagerp/).

Os exemplos desta secção mostraram como ler a propriedade **AllowBlobPublicAccess** para a conta de armazenamento para determinar se o acesso público é atualmente permitido ou proibido. Para saber mais sobre como verificar se a configuração de acesso público de uma conta está configurada para impedir o acesso anónimo, consulte [Remediar o acesso público anónimo](anonymous-read-access-prevent.md#remediate-anonymous-public-access).

## <a name="set-the-public-access-level-for-a-container"></a>Definir o nível de acesso público para um recipiente

Para conceder aos utilizadores anónimos o acesso a um contentor e às suas bolhas, primeiro permitir o acesso público à conta de armazenamento, em seguida, definir o nível de acesso público do contentor. Se o acesso público for negado para a conta de armazenamento, não poderá configurar o acesso público a um contentor.

Quando é permitido o acesso público a uma conta de armazenamento, pode configurar um recipiente com as seguintes permissões:

- **Sem acesso público:** O recipiente e as suas bolhas só podem ser acedidos com um pedido autorizado. Esta opção é o padrão para todos os novos recipientes.
- **O público lê o acesso apenas para bolhas:** As bolhas dentro do recipiente podem ser lidas por pedido anónimo, mas os dados do contentor não estão disponíveis anonimamente. Os clientes anónimos não podem enumerar as bolhas dentro do contentor.
- **O público lê o acesso ao contentor e às suas bolhas:** Os dados do contentor e do blob podem ser lidos por pedido anónimo, exceto para as definições de permissão do contentor e metadados de contentores. Os clientes podem enumerar bolhas dentro do contentor por pedido anónimo, mas não podem enumerar contentores dentro da conta de armazenamento.

Não é possível alterar o nível de acesso público a uma bolha individual. O nível de acesso público é definido apenas ao nível do contentor. Pode definir o nível de acesso público do recipiente quando criar o recipiente, ou pode atualizar a definição num recipiente existente.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para atualizar o nível de acesso público a um ou mais contentores existentes no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal Azure.
1. Sob o **serviço Blob** na lâmina do menu, selecione **Recipientes**.
1. Selecione os recipientes para os quais pretende definir o nível de acesso público.
1. Utilize o botão **de nível de acesso Alterar** para visualizar as definições de acesso do público.
1. Selecione o nível de acesso público desejado a partir do **nível** de acesso do Público e clique no botão OK para aplicar a alteração nos recipientes selecionados.

    ![Screenshot mostrando como definir o nível de acesso público no portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Quando o acesso público é proibido para a conta de armazenamento, o nível de acesso público de um contentor não pode ser definido. Se tentar definir o nível de acesso público do contentor, verá que a definição está desativada porque o acesso público é proibido para a conta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Screenshot mostrando que a definição do nível de acesso público do contentor é bloqueada quando o acesso público é proibido":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para atualizar o nível de acesso público para um ou mais contentores com PowerShell, ligue para o comando [Set-AzStorageContainerAcl.](/powershell/module/az.storage/set-azstoragecontaineracl) Autorize esta operação passando na sua chave de conta, uma cadeia de ligação ou uma assinatura de acesso partilhado (SAS). A operação [set Container ACL](/rest/api/storageservices/set-container-acl) que define o nível de acesso público do contentor não suporta a autorização com a Azure AD. Para obter mais informações, consulte [permissões para ligar para as operações de dados de blob e fila.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

O exemplo a seguir cria um recipiente com acesso público desativado e, em seguida, atualiza a definição de acesso público do contentor para permitir o acesso anónimo ao contentor e às suas bolhas. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

Quando o acesso público é proibido para a conta de armazenamento, o nível de acesso público de um contentor não pode ser definido. Se tentar definir o nível de acesso público do contentor, o Azure Storage retorna o erro indicando que o acesso público não é permitido na conta de armazenamento.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar o nível de acesso público para um ou mais contentores com O Azure CLI, ligue para o comando [de autorização do recipiente de armazenamento AZ.](/cli/azure/storage/container#az-storage-container-set-permission) Autorize esta operação passando na sua chave de conta, uma cadeia de ligação ou uma assinatura de acesso partilhado (SAS). A operação [set Container ACL](/rest/api/storageservices/set-container-acl) que define o nível de acesso público do contentor não suporta a autorização com a Azure AD. Para obter mais informações, consulte [permissões para ligar para as operações de dados de blob e fila.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

O exemplo a seguir cria um recipiente com acesso público desativado e, em seguida, atualiza a definição de acesso público do contentor para permitir o acesso anónimo ao contentor e às suas bolhas. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

Quando o acesso público é proibido para a conta de armazenamento, o nível de acesso público de um contentor não pode ser definido. Se tentar definir o nível de acesso público do contentor, o Azure Storage retorna o erro indicando que o acesso público não é permitido na conta de armazenamento.

# <a name="template"></a>[Modelo](#tab/template)

N/D.

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Verifique a definição de acesso público para um conjunto de contentores

É possível verificar quais os contentores de uma ou mais contas de armazenamento configurados para acesso público, enumerando os contentores e verificando a definição de acesso público. Esta abordagem é uma opção prática quando uma conta de armazenamento não contém um grande número de contentores, ou quando você está verificando a definição através de um pequeno número de contas de armazenamento. No entanto, o desempenho pode sofrer se tentar enumerar um grande número de contentores.

O exemplo a seguir utiliza o PowerShell para obter a definição de acesso público a todos os contentores numa conta de armazenamento. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>Passos seguintes

- [Impedir que o público anónimo leia o acesso a contentores e bolhas](anonymous-read-access-prevent.md)
- [Aceda a contentores públicos e bolhas de forma anónima com .NET](anonymous-read-access-client.md)
- [Autorizar o acesso ao Armazenamento Azure](../common/storage-auth.md)
