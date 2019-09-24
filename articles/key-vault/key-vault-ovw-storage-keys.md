---
title: Gerenciar chaves de conta de armazenamento com Azure Key Vault e o CLI do Azure
description: As chaves da conta de armazenamento fornecem integração direta entre Azure Key Vault e o acesso baseado em chave a uma conta de armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 8b9478dda83b85e937faa8915fa5e9b77660f194
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203613"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gerenciar chaves de conta de armazenamento com Key Vault e o CLI do Azure

Uma conta de armazenamento do Azure usa credenciais que compreendem um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de uma como uma chave de criptografia. Key Vault gerencia chaves de conta de armazenamento armazenando-as como [segredos Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Você pode usar o Key Vault recurso chave de conta de armazenamento gerenciado para listar (sincronizar) chaves com uma conta de armazenamento do Azure e regenerar (girar) as chaves periodicamente. Você pode gerenciar chaves para contas de armazenamento e contas de armazenamento clássicas.

Ao usar o recurso chave de conta de armazenamento gerenciado, considere os seguintes pontos:

- Os valores de chave nunca são retornados em resposta a um chamador.
- Somente Key Vault deve gerenciar suas chaves de conta de armazenamento. Não gerencie as chaves por conta própria e evite interferir nos processos de Key Vault.
- Apenas um único objeto Key Vault deve gerenciar chaves de conta de armazenamento. Não permita o gerenciamento de chaves de vários objetos.
- Você pode solicitar Key Vault para gerenciar sua conta de armazenamento com uma entidade de usuário, mas não com uma entidade de serviço.
- Regenerar chaves usando apenas Key Vault. Não regenere manualmente as chaves da conta de armazenamento.

É recomendável usar a integração do armazenamento do Azure com o Azure Active Directory (Azure AD), o serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. A integração do Azure AD está disponível para [BLOBs e filas do Azure](../storage/common/storage-auth-aad.md)e fornece acesso baseado em token OAuth2 ao armazenamento do Azure (assim como Azure Key Vault).

O Azure AD permite que você autentique seu aplicativo cliente usando uma identidade de aplicativo ou de usuário, em vez de credenciais de conta de armazenamento. Você pode usar uma [identidade gerenciada do Azure ad](/azure/active-directory/managed-identities-azure-resources/) ao executar o no Azure. Identidades gerenciadas eliminam a necessidade de autenticação de cliente e armazenamento de credenciais no ou com seu aplicativo.

O Azure AD usa o RBAC (controle de acesso baseado em função) para gerenciar a autorização, que também tem suporte pelo Key Vault.

## <a name="service-principal-application-id"></a>ID do aplicativo da entidade de serviço

Um locatário do Azure AD fornece a cada aplicativo registrado uma [entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object). A entidade de serviço serve como a ID do aplicativo, que é usada durante a configuração de autorização para acesso a outros recursos do Azure via RBAC.

Key Vault é um aplicativo da Microsoft que é previamente registrado em todos os locatários do Azure AD. Key Vault é registrado sob a mesma ID de aplicativo em cada nuvem do Azure.

| locatários | Nuvem | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure público | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia, primeiro você deve fazer o seguinte:

- [Instale o CLI do Azure](/cli/azure/install-azure-cli).
- [Criar um cofre de chaves](quick-create-cli.md)
- [Criar uma conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli). O nome da conta de armazenamento deve usar apenas letras minúsculas e números. O comprimento do nome deve ter entre 3 e 24 caracteres.
      
## <a name="manage-storage-account-keys"></a>Gerenciar chaves de conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Autentique sua sessão de CLI do Azure usando os comandos [AZ login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Conceder Key Vault acesso à sua conta de armazenamento

Use o comando CLI do Azure [AZ role Assignment Create](/cli/azure/role/assignment?view=azure-cli-latest) para fornecer Key Vault acessar sua conta de armazenamento. Forneça o comando aos seguintes valores de parâmetro:

- `--role`: Passe a função RBAC "função de serviço do operador chave da conta de armazenamento". Essa função limita o escopo de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássica, passe "função de serviço do operador de chave de conta de armazenamento clássica".
- `--assignee-object-id`: Passe o valor "93c27d83-f79b-4cb2-8dd4-4aa716542e74", que é a ID de objeto para Key Vault na nuvem pública do Azure. (Para obter a ID de objeto para Key Vault na nuvem do Azure governamental, consulte [ID do aplicativo de entidade de serviço](#service-principal-application-id).)
- `--scope`: Passe a ID de recurso da conta de armazenamento, que está `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`no formato. Para localizar sua ID de assinatura, use o comando CLI do Azure [AZ Account List](/cli/azure/account?view=azure-cli-latest#az-account-list) ; para localizar o nome da conta de armazenamento e o grupo de recursos da conta de armazenamento, use o comando CLI do Azure [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Criar uma conta de armazenamento gerenciado Key Vault

 Crie uma conta de armazenamento gerenciado Key Vault usando o comando de [armazenamento CLI do Azure AZ keyvault](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) . Defina um período de regeneração de 90 dias. Após 90 dias, Key Vault regenera `key1` e troca a chave ativa de `key2` para `key1`. `key1`é marcado como a chave ativa. Forneça o comando aos seguintes valores de parâmetro:

- `--vault-name`: Passe o nome do seu cofre de chaves. Para localizar o nome do cofre de chaves, use o comando CLI do Azure [AZ keyvault List](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) .
- `-n`: Passe o nome da sua conta de armazenamento. Para localizar o nome da sua conta de armazenamento, use o comando CLI do Azure [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
- `--resource-id`: Passe a ID de recurso da conta de armazenamento, que está `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`no formato. Para localizar sua ID de assinatura, use a [lista de contas](/cli/azure/account?view=azure-cli-latest#az-account-list) do CLI do Azure AZ comando; para localizar o nome da conta de armazenamento e o grupo de recursos da conta de armazenamento, use o comando CLI do Azure [AZ Storage Account List](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Tokens de assinatura de acesso compartilhado

Você também pode solicitar Key Vault para gerar tokens de assinatura de acesso compartilhado. Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Você pode conceder aos clientes acesso aos recursos em sua conta de armazenamento sem compartilhar suas chaves de conta. Uma assinatura de acesso compartilhado fornece uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer suas chaves de conta.

Os comandos nesta seção concluem as seguintes ações:

- Defina uma definição `<YourSASDefinitionName>`de assinatura de acesso compartilhado de conta. A definição é definida em uma Key Vault conta `<YourStorageAccountName>` de armazenamento gerenciado no cofre `<YourKeyVaultName>`de chaves.
- Crie um token de assinatura de acesso compartilhado de conta para serviços BLOB, arquivo, tabela e fila. O token é criado para os tipos de recursos Service, container e Object. O token é criado com todas as permissões, via HTTPS e com as datas de início e término especificadas.
- Defina uma definição de assinatura de acesso compartilhado de armazenamento gerenciado Key Vault no cofre. A definição tem o URI do modelo do token de assinatura de acesso compartilhado que foi criado. A definição tem o tipo `account` de assinatura de acesso compartilhado e é válida por N dias.
- Verifique se a assinatura de acesso compartilhado foi salva em seu cofre de chaves como um segredo.

### <a name="create-a-shared-access-signature-token"></a>Criar um token de assinatura de acesso compartilhado

Crie uma definição de assinatura de acesso compartilhado usando o comando CLI do Azure [AZ Storage Account Generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Esta operação requer as `storage` permissões `setsas` e.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Depois que a operação for executada com êxito, copie a saída.

```console
"se=2020-01-01&sp=***"
```

Essa saída será passada para o `--template-id` parâmetro na próxima etapa.

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso compartilhado

Use o comando CLI do Azure [AZ keyvault Storage SAS-Definition Create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) , passando a saída da etapa anterior para o `--template-id` parâmetro, para criar uma definição de assinatura de acesso compartilhado.  Você pode fornecer o nome de sua escolha para o `-n` parâmetro.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificar a definição de assinatura de acesso compartilhado

Você pode verificar se a definição da assinatura de acesso compartilhado foi armazenada no cofre de chaves usando o CLI do Azure a [lista de segredos AZ keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) e [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) .

Primeiro, localize a definição de assinatura de acesso compartilhado no cofre de chaves usando o comando [AZ keyvault secreto List](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) .

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

O segredo correspondente à sua definição de SAS terá essas propriedades:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Agora você pode usar o comando [AZ keyvault secreto show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e a `id` propriedade para exibir o conteúdo desse segredo.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

A saída desse comando mostrará a cadeia de caracteres de definição`value`de SAS como.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/).
- Examine os artigos no [blog da equipe do Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Consulte a documentação de referência de [armazenamento AZ keyvault](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .
