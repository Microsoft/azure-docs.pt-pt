---
title: Configurar o público anónimo ler acesso a contentores e bolhas
titleSuffix: Azure Storage
description: Saiba como ativar ou desativar o acesso anónimo aos dados blob para a conta de armazenamento. Defina a definição de acesso público do contentor para disponibilizar contentores e bolhas para acesso anónimo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209936"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>Configurar o público anónimo ler acesso a contentores e bolhas

O Azure Storage suporta o acesso anónimo ao público para ler o acesso a contentores e bolhas. Por predefinição, todos os pedidos a um contentor e às suas bolhas devem ser autorizados utilizando a autorização do Azure Ative Directory (Azure AD) ou da Chave Partilhada. Ao configurar a definição de nível de acesso público de um contentor para permitir o acesso anónimo, os clientes podem ler dados nesse recipiente sem autorizar o pedido.

> [!WARNING]
> Quando um recipiente é configurado para acesso público, qualquer cliente pode ler dados nesse recipiente. O acesso público apresenta um potencial risco de segurança, por isso, se o seu cenário não o exigir, a Microsoft recomenda que o desative para a conta de armazenamento. Para obter mais informações, consulte [Prevent anonymous public read access to containers and blobs](anonymous-read-access-prevent.md).

Para configurar o acesso público a um contentor, deve realizar dois passos:

1. Permitir o acesso público à conta de armazenamento
1. Configure a definição de acesso público do contentor

Este artigo descreve como configurar o público anónimo ler o acesso a um contentor e às suas bolhas. Para obter informações sobre como aceder a dados blob de forma anónima a partir de uma aplicação do cliente, consulte [access public containers e blobs anonimamente com .NET](anonymous-read-access-client.md).

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>Permitir ou desativar o acesso de leitura pública para uma conta de armazenamento

Por predefinição, o acesso público está habilitado para uma conta de armazenamento. A desativação do acesso público impede todo o acesso anónimo a contentores e bolhas nessa conta. Para uma maior segurança, a Microsoft recomenda que desative o acesso público às suas contas de armazenamento, a menos que o seu cenário exija que os utilizadores acedam a recursos blob de forma anónima.

> [!WARNING]
> A desativação do acesso público a uma conta de armazenamento sobrepõe-se às definições de acesso público a todos os contentores dessa conta de armazenamento. Quando o acesso público é desativado para a conta de armazenamento, quaisquer futuros pedidos anónimos a essa conta falharão.

Para ativar ou desativar o acesso público a uma conta de armazenamento, utilize o portal Azure ou o Azure CLI para configurar a propriedade **blobPublicAccess** da conta. Esta propriedade está disponível para todas as contas de armazenamento que são criadas com o modelo de implementação do Gestor de Recursos Azure. Para mais informações, consulte [a visão geral da conta de Armazenamento.](../common/storage-account-overview.md)

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para ativar ou desativar o acesso do público a uma conta de armazenamento no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Localizar a **definição de configuração** em **Definições**.
1. Descreva **o acesso público blob** a **deficientes** ou **ativados**.

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="Screenshot mostrando como ativar ou desativar o acesso público blob para conta":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para ativar ou desativar o acesso público a uma conta de armazenamento com o Azure CLI, obtenha primeiro o ID de recursos para a sua conta de armazenamento, ligando para o comando [de show de recursos az.](/cli/azure/resource#az-resource-show) Em seguida, ligue para o comando [de atualização de recursos az](/cli/azure/resource#az-resource-update) para definir a propriedade **allowBlobPublicAccess** para a conta de armazenamento. Para permitir o acesso do público, descreva a propriedade **allowBlobPublicAccess** para ser verdadeira; para desativar, defini-lo como **falso**.

O exemplo a seguir desativa o acesso público à conta de armazenamento. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

Para verificar se o acesso público está ativado com o Azure CLI, ligue para o comando de [show de recursos az](/cli/azure/resource#az-resource-show) e consulte a propriedade **allowBlobPublicAccess:**

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> A desativação do acesso público a uma conta de armazenamento não afeta quaisquer sites estáticos alojados nessa conta de armazenamento. O **recipiente $web** é sempre acessível ao público.

## <a name="check-the-public-access-setting-for-a-storage-account"></a>Verifique a definição de acesso público para uma conta de armazenamento

Para verificar a definição de acesso público para uma conta de armazenamento, obtenha o valor da propriedade **allowBlobPublicAccess.** Para verificar esta propriedade para obter uma grande número de contas de armazenamento de uma só vez, utilize o Azure Resource Graph Explorer.

A propriedade **allowBlobPublicAccess** não é definida por padrão e não devolve um valor até que o descreva explicitamente. A conta de armazenamento não permite o acesso do público quando o valor da propriedade é nulo.

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>Verifique a definição de acesso público para uma única conta de armazenamento

Para verificar a definição de acesso público para uma única conta de armazenamento utilizando o Azure CLI, ligue para o comando de [exibição de recursos az](/cli/azure/resource#az-resource-show) e consulte a propriedade **allowBlobPublicAccess:**

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>Consulte a definição de acesso público para um conjunto de contas de armazenamento

Para verificar a definição de acesso público através de um conjunto de contas de armazenamento com o melhor desempenho, pode utilizar o Azure Resource Graph Explorer no portal Azure. Para saber mais sobre a utilização do Explorador de Gráficos de Recurso, consulte [Quickstart: Execute a sua primeira consulta de Gráfico de Recursos utilizando o Azure Resource Graph Explorer](/azure/governance/resource-graph/first-query-portal).

Executando a seguinte consulta no Explorador de Gráficos de Recurso devolve uma lista de contas de armazenamento e exibe o valor da propriedade **allowBlobPublicAccess** para cada conta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>Definir o nível de acesso público para um recipiente

Para conceder aos utilizadores anónimos o acesso a um contentor e às suas bolhas, primeiro permitir o acesso público à conta de armazenamento, em seguida, definir o nível de acesso público do contentor. Se o acesso público for desativado para a conta de armazenamento, não poderá configurar o acesso público a um contentor.

Quando o acesso público estiver habilitado para uma conta de armazenamento, pode configurar um recipiente com as seguintes permissões:

- **Sem acesso público:** O recipiente e as suas bolhas só podem ser acedidos com um pedido autorizado. Esta opção é o padrão para todos os novos recipientes.
- **O público lê o acesso apenas para bolhas:** As bolhas dentro do recipiente podem ser lidas por pedido anónimo, mas os dados do contentor não estão disponíveis anonimamente. Os clientes anónimos não podem enumerar as bolhas dentro do contentor.
- **O público lê o acesso ao contentor e às suas bolhas:** Os dados do contentor e do blob podem ser lidos por pedido anónimo, exceto para as definições de permissão do contentor e metadados de contentores. Os clientes podem enumerar bolhas dentro do contentor por pedido anónimo, mas não podem enumerar contentores dentro da conta de armazenamento.

Não é possível alterar o nível de acesso público a uma bolha individual. O nível de acesso público é definido apenas ao nível do contentor.

Para definir o nível de acesso público de um contentor, utilize o portal Azure ou o Azure CLI. Pode definir o nível de acesso público do recipiente quando criar o recipiente ou atualizar esta definição num recipiente existente.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para atualizar o nível de acesso público a um ou mais contentores existentes no portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal Azure.
1. Sob o **serviço Blob** na lâmina do menu, selecione **Recipientes**.
1. Selecione os recipientes para os quais pretende definir o nível de acesso público.
1. Utilize o botão **de nível de acesso Alterar** para visualizar as definições de acesso do público.
1. Selecione o nível de acesso público desejado a partir do **nível** de acesso do Público e clique no botão OK para aplicar a alteração nos recipientes selecionados.

    ![Screenshot mostrando como definir o nível de acesso público no portal](./media/anonymous-read-access-configure/configure-public-access-container.png)

Quando o acesso público é desativado para a conta de armazenamento, o nível de acesso público de um contentor não pode ser definido. Se tentar definir o nível de acesso público do contentor, verá que a definição está desativada porque o acesso público é proibido para a conta.

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="Screenshot mostrando que definir o nível de acesso público do contentor é bloqueado quando o acesso público é desativado":::

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para atualizar o nível de acesso público para um ou mais contentores com O Azure CLI, ligue para o comando [de autorização do recipiente de armazenamento AZ.](/cli/azure/storage/container#az-storage-container-set-permission) Autorize esta operação passando na sua chave de conta, uma cadeia de ligação ou uma assinatura de acesso partilhado (SAS). A operação [set Container ACL](/rest/api/storageservices/set-container-acl) que define o nível de acesso público do contentor não suporta a autorização com a Azure AD. Para obter mais informações, consulte [permissões para ligar para as operações de dados de blob e fila.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

O exemplo a seguir define a definição de acesso público a um recipiente para permitir o acesso anónimo ao contentor e às suas bolhas. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

Quando o acesso público é desativado para a conta de armazenamento, o nível de acesso público de um contentor não pode ser definido. Se tentar definir o nível de acesso público do contentor, ocorre um erro que indica que o acesso público não é permitido na conta de armazenamento.

---

## <a name="check-the-container-public-access-setting"></a>Verifique a definição de acesso público do contentor

Para verificar a definição de acesso público para um ou mais contentores, pode utilizar o portal Azure, PowerShell, Azure CLI, uma das bibliotecas do cliente do Azure Storage ou o fornecedor de recursos de armazenamento Azure. As seguintes secções oferecem alguns exemplos.  

### <a name="check-the-public-access-setting-for-a-single-container"></a>Verifique a definição de acesso público para um único recipiente

Para obter o nível de acesso público para um ou mais contentores com Azure CLI, ligue para o comando de autorização do [contentor de armazenamento az.](/cli/azure/storage/container#az-storage-container-show-permission) Autorize esta operação passando na sua chave de conta, uma cadeia de ligação ou uma assinatura de acesso partilhado (SAS). A operação [Get Container ACL](/rest/api/storageservices/get-container-acl) que devolve o nível de acesso público de um contentor não suporta a autorização com a Azure AD. Para obter mais informações, consulte [permissões para ligar para as operações de dados de blob e fila.](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)

O exemplo a seguir lê-se na definição de acesso público a um contentor. Lembre-se de substituir os valores de espaço reservado nos parênteses pelos seus próprios valores:

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>Verifique a definição de acesso público para um conjunto de contentores

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