---
title: Gerir as chaves da conta de armazenamento com o Cofre de Chaves Azure e o Azure CLI
description: As chaves da conta de armazenamento proporcionam uma integração perfeita entre o Cofre chave azure e o acesso baseado em chaves a uma conta de armazenamento Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 1125bafa43ce1752c58d1cce0bba66a6bbd32c32
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685424"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gerir as chaves da conta de armazenamento com o Key Vault e o Azure CLI

Uma conta de armazenamento Azure usa credenciais que incluem um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de uma chave criptográfica. Key Vault gere as chaves da conta de armazenamento armazenando-as como segredos do Cofre Chave. 

Pode utilizar a função de chave de armazenamento gerida pelo Key Vault para listar (sincronizar) chaves com uma conta de armazenamento Azure e regenerar (rodar) as teclas periodicamente. Você pode gerir chaves para contas de armazenamento e contas de armazenamento clássicas.

Quando utilizar a função chave da conta de armazenamento gerida, considere os seguintes pontos:

- Os valores-chave nunca são devolvidos em resposta a um ouvinte.
- Só o Cofre chave deve gerir as chaves da sua conta de armazenamento. Não gereas as chaves sozinho e evita interferir nos processos do Cofre chave.
- Apenas um único objeto key vault deve gerir as chaves da conta de armazenamento. Não permita a gestão da chave a partir de vários objetos.
- Pode solicitar ao Key Vault que gere a sua conta de armazenamento com um diretor de utilizador, mas não com um diretor de serviço.
- Regenerar as teclas usando apenas o Cofre chave. Não regenerar manualmente as chaves da sua conta de armazenamento.

Recomendamos a utilização da integração do Armazenamento Azure com o Azure Ative Directory (Azure AD), o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A integração da Azure AD está disponível para [blobs e filas Azure,](../../storage/common/storage-auth-aad.md)e fornece acesso baseado em token OAuth2 ao Armazenamento Azure (tal como o Azure Key Vault).

A Azure AD permite-lhe autenticar a sua aplicação cliente utilizando uma aplicação ou identidade de utilizador, em vez de credenciais de conta de armazenamento. Pode usar uma identidade gerida pelo [Azure](/azure/active-directory/managed-identities-azure-resources/) Quando correr em Azure. As identidades geridas removem a necessidade de autenticação do cliente e armazenam credenciais dentro ou com a sua aplicação.

A Azure AD utiliza o controlo de acesso baseado em funções (RBAC) para gerir a autorização, que também é apoiada pela Key Vault.

## <a name="service-principal-application-id"></a>ID de aplicação principal de serviço

Um inquilino da AD Azure fornece cada pedido registado com um [diretor de serviço.](/azure/active-directory/develop/developer-glossary#service-principal-object) O diretor de serviço serve como id de aplicação, que é usado durante a configuração de autorização para acesso a outros recursos Azure via RBAC.

Key Vault é uma aplicação da Microsoft que está pré-registada em todos os inquilinos da AD Azure. O Cofre chave está registado sob o mesmo ID de aplicação em cada nuvem Azure.

| Inquilinos | Nuvem | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure público | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, primeiro deve fazer o seguinte:

- [Instale o Azure CLI](/cli/azure/install-azure-cli).
- [Criar um cofre chave](quick-create-cli.md)
- [Crie uma conta de armazenamento Azure.](../../storage/common/storage-account-create.md?tabs=azure-cli) O nome da conta de armazenamento deve utilizar apenas letras minúsculas e números. O comprimento do nome deve ser entre 3 e 24 caracteres.
      
## <a name="manage-storage-account-keys"></a>Gerir as chaves da conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Autenticar a sua sessão Azure CLI utilizando os comandos [de login az.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Dê acesso ao Cofre chave à sua conta de armazenamento

Utilize a atribuição de funções Azure CLI [az criar](/cli/azure/role/assignment?view=azure-cli-latest) comando para dar acesso ao Cofre Chave na sua conta de armazenamento. Fornecer ao comando os seguintes valores de parâmetro:

- `--role`: Passe a função RBAC "Chave de Depósito" RBAC. Esta função limita o âmbito de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássica, passe "Classic Storage Account Key Operator Service Role".
- `--assignee`: Passe ohttps://vault.azure.netvalor ", que é a url para Key Vault na nuvem pública de Azure. (Para a nuvem Azure Goverment utilize '--asinge-object-id', consulte o ID da [aplicação principal do serviço](#service-principal-application-id).)
- `--scope`: Passe o ID do recurso da `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`sua conta de armazenamento, que está no formulário . Para encontrar o seu ID de subscrição, utilize o comando da lista de contas Azure CLI [az;](/cli/azure/account?view=azure-cli-latest#az-account-list) para encontrar o nome da sua conta de armazenamento e o grupo de recursos da conta de armazenamento, utilize o comando da lista de conta de armazenamento Azure CLI [az.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê permissão à sua conta de utilizador para gerir contas de armazenamento

Utilize o cmdlet de política de teclado Azure CLI [az](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) para atualizar a política de acesso ao Cofre chave e conceder permissões de conta de armazenamento na sua conta de utilizador.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Note que as permissões para contas de armazenamento não estão disponíveis na página da conta de armazenamento "Políticas de acesso" no portal Azure.
### <a name="create-a-key-vault-managed-storage-account"></a>Criar uma conta de armazenamento gerida por cofre chave

 Crie uma conta de armazenamento gerida pela Key Vault utilizando o comando de armazenamento do cofre de keyvault Azure CLI [az.](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) Estabeleça um período de regeneração de 90 dias. Após 90 dias, o `key1` Cofre chave regenera-se `key2` `key1`e troca a chave ativa de . `key1`é então marcado como a chave ativa. Fornecer ao comando os seguintes valores de parâmetro:

- `--vault-name`: Passe o nome do seu cofre chave. Para encontrar o nome do seu cofre chave, use o comando da lista de cofres Azure CLI [az.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list)
- `-n`: Passe o nome da sua conta de armazenamento. Para encontrar o nome da sua conta de armazenamento, utilize o comando da lista de armazenamento Azure CLI [az.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
- `--resource-id`: Passe o ID do recurso da `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`sua conta de armazenamento, que está no formulário . Para encontrar o seu ID de subscrição, utilize o comando da lista de contas Azure CLI [az;](/cli/azure/account?view=azure-cli-latest#az-account-list) para encontrar o nome da sua conta de armazenamento e o grupo de recursos da conta de armazenamento, utilize o comando da lista de conta de armazenamento Azure CLI [az.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Fichas de assinatura de acesso partilhado

Também pode pedir ao Key Vault que gere fichas de assinatura de acesso partilhado. Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode conceder aos clientes acesso aos recursos na sua conta de armazenamento sem partilhar as chaves da sua conta. Uma assinatura de acesso partilhado fornece-lhe uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da sua conta.

Os comandos desta secção completam as seguintes ações:

- Definir uma definição `<YourSASDefinitionName>`de assinatura de acesso partilhado de conta. A definição está definida numa conta `<YourStorageAccountName>` de armazenamento `<YourKeyVaultName>`gerida pelo Key Vault no seu cofre chave.
- Crie um símbolo de assinatura de acesso partilhado de conta para os serviços Blob, File, Table e Queue. O símbolo é criado para tipos de recursos Serviço, Contentor e Objeto. O símbolo é criado com todas as permissões, em https, e com as datas de início e fim especificadas.
- Delineie uma definição de assinatura de acesso compartilhado de armazenamento no cofre. A definição tem o modelo URI do símbolo de assinatura de acesso partilhado que foi criado. A definição tem o `account` tipo de assinatura de acesso partilhado e é válida para os dias N.
- Verifique se a assinatura de acesso partilhado foi guardada no seu cofre como segredo.

### <a name="create-a-shared-access-signature-token"></a>Criar um símbolo de assinatura de acesso partilhado

Crie uma definição de assinatura de acesso partilhado utilizando o comando de armazenamento da conta Azure CLI [az generate-sas.](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) Esta operação requer `storage` `setsas` as permissões.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Depois de a operação funcionar com sucesso, copie a saída.

```console
"se=2020-01-01&sp=***"
```

Esta saída será a `--template-id` passada para o parâmetro no próximo passo.

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso partilhado

Utilize o cofre de armazenamento Azure CLI [az definição de sas](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) criar `--template-id` comando, passando a saída do passo anterior para o parâmetro, para criar uma definição de assinatura de acesso partilhado.  Pode fornecer o nome da `-n` sua escolha ao parâmetro.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifique a definição de assinatura de acesso partilhado

Pode verificar se a definição de assinatura de acesso partilhado foi armazenada no seu cofre de chaves utilizando a [lista secreta](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) do cofre do cofre do Cofre de Chaves Azure CLI e comandos secretos do [keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

Primeiro, encontre a definição de assinatura de acesso partilhado no seu cofre usando o comando secreto da lista de [cofres az.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

O segredo correspondente à sua definição SAS terá estas propriedades:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

Você pode agora usar o comando secreto `id` do [az keyvault show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e a propriedade para ver o conteúdo desse segredo.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

A saída deste comando mostrará a`value`sua cadeia de definição SAS como .


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [chaves, segredos e certificados.](https://docs.microsoft.com/rest/api/keyvault/)
- Reveja artigos no blog da [equipa Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Consulte a documentação de referência de armazenamento de [cofre az.](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)
