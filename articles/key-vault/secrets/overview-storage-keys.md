---
title: Gerir chaves de conta de armazenamento com Azure Key Vault e o Azure CLI
description: As chaves da conta de armazenamento proporcionam uma integração perfeita entre o Cofre da Chave Azure e o acesso baseado em chaves a uma conta de armazenamento Azure.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 573e4c9d8db3f07f223826ab648f2ef57e1d9c58
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766322"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gerir chaves de conta de armazenamento com o Key Vault e o Azure CLI
> [!IMPORTANT]
> Recomendamos a utilização da integração do Azure Storage com o Azure Ative Directory (Azure AD), o serviço de gestão de identidade e acesso baseado na nuvem da Microsoft. A integração AD AD está disponível para [bolhas e filas Azure,](../../storage/common/storage-auth-aad.md)e fornece acesso baseado em símbolos OAuth2 ao Azure Storage (tal como o Azure Key Vault). O Azure AD permite-lhe autenticar a sua aplicação de cliente utilizando uma aplicação ou identidade de utilizador, em vez de credenciais de conta de armazenamento. Você pode usar uma [identidade gerida Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) quando você correr em Azure. Identidades geridas removem a necessidade de autenticação do cliente e armazenam credenciais dentro ou com a sua aplicação. Utilize a solução abaixo apenas quando a autenticação AZure AD não for possível.

Uma conta de armazenamento Azure utiliza credenciais que incluem um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma palavra-passe, em vez de uma chave criptográfica. Key Vault gere as chaves da conta de armazenamento regenerando-as periodicamente na conta de armazenamento e fornece fichas de assinatura de acesso partilhado para acesso delegado a recursos na sua conta de armazenamento.

Pode utilizar a função de conta de armazenamento gerida do Key Vault para listar as teclas (sincronização) com uma conta de armazenamento Azure e regenerar (rodar) as teclas periodicamente. Pode gerir as chaves tanto para contas de armazenamento como para contas de armazenamento clássicas.

Quando utilizar a função chave da conta de armazenamento gerida, considere os seguintes pontos:

- Os valores-chave nunca são devolvidos em resposta a um chamador.
- Só o Key Vault deve gerir as chaves da sua conta de armazenamento. Não maneie as chaves sozinho e evite interferir com os processos do Key Vault.
- Apenas um único objeto do Key Vault deve gerir as chaves da conta de armazenamento. Não permita a gestão de chaves a partir de vários objetos.
- Regenerar as chaves usando apenas o Cofre de Chaves. Não regenerar manualmente as chaves da conta de armazenamento.

## <a name="service-principal-application-id"></a>ID de aplicação do principal de serviço

Um inquilino da AD Azure fornece a cada pedido registado um [principal de serviço.](../../active-directory/develop/developer-glossary.md#service-principal-object) O diretor de serviço serve como iD de aplicação, que é usado durante a instalação de autorização para acesso a outros recursos Azure via Azure RBAC.

Key Vault é uma aplicação da Microsoft pré-registada em todos os inquilinos da AD Azure. O Key Vault está registado sob o mesmo ID de aplicação em cada nuvem Azure.

| Inquilinos | Cloud | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure público | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outro  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, deve primeiro fazer o seguinte:

- [Instale o Azure CLI](/cli/azure/install-azure-cli).
- [Criar um cofre de chaves](quick-create-cli.md)
- [Criar uma conta de armazenamento Azure](../../storage/common/storage-account-create.md?tabs=azure-cli). O nome da conta de armazenamento deve utilizar apenas letras e números minúsculos. O comprimento do nome deve ter entre 3 e 24 caracteres.
      
## <a name="manage-storage-account-keys"></a>Gerir chaves de conta de armazenamento

### <a name="connect-to-your-azure-account"></a>Ligar à sua conta do Azure

Autenticar a sua sessão Azure CLI utilizando os comandos [de login az.](/powershell/module/az.accounts/connect-azaccount)

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Dê ao Key Vault acesso à sua conta de armazenamento

Utilize a atribuição de função Azure CLI [az criar](/cli/azure/role/assignment) comando para dar ao Key Vault acesso à sua conta de armazenamento. Fornecer ao comando os seguintes valores de parâmetro:

- `--role`: Passe a função Azure "Função de Serviço do Operador da Chave de Armazenamento" . Esta função limita o âmbito de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássica, passe "Classic Storage Account Operator Operator Service Role" em vez disso.
- `--assignee`: Passe o valor " https://vault.azure.net ", que é o url para Key Vault na nuvem pública Azure. (Para a nuvem de Azure Goverment use '--asingee-object-id' em vez disso, consulte [iD de aplicação principal do serviço](#service-principal-application-id).)
- `--scope`: Passe o ID do seu recurso de conta de armazenamento, que está no formulário `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Para encontrar o seu ID de subscrição, utilize o comando da lista de conta Azure CLI [az;](/cli/azure/account?#az_account_list) para encontrar o nome da sua conta de armazenamento e grupo de recursos de conta de armazenamento, use o comando da lista de conta de armazenamento Azure CLI [az.](/cli/azure/storage/account?#az_storage_account_list)

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Dê permissão à sua conta de utilizador para contas de armazenamento geridas

Utilize o cmdlet [de política de chave-tevault-set-keyvault-set-set do](/cli/azure/keyvault?#az_keyvault_set_policy) Azure CLI para atualizar a política de acesso ao Cofre-chave e conceder permissões de conta de armazenamento na sua conta de utilizador.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Note que as permissões para contas de armazenamento não estão disponíveis na página "Políticas de acesso" da conta de armazenamento no portal Azure.
### <a name="create-a-key-vault-managed-storage-account"></a>Criar uma conta de armazenamento gerido por cofre chave

 Crie uma conta de armazenamento gerida pelo Key Vault utilizando o comando de [armazenamento de keyvault](/cli/azure/keyvault/storage?#az_keyvault_storage_add) Azure CLI. Desabar um período de regeneração de 90 dias. Quando é hora de rodar, o KeyVault regenera a chave que não está ativa e, em seguida, define a chave recém-criada como ativa. Apenas uma das chaves é usada para emitir fichas SAS a qualquer momento, esta é a chave ativa. Fornecer ao comando os seguintes valores de parâmetro:

- `--vault-name`: Passe o nome do seu cofre chave. Para encontrar o nome do seu cofre chave, use o comando da lista de [keyvault](/cli/azure/keyvault?#az_keyvault_list) Azure CLI.
- `-n`: Passe o nome da sua conta de armazenamento. Para encontrar o nome da sua conta de armazenamento, utilize o comando da lista de conta de armazenamento Azure CLI [az.](/cli/azure/storage/account?#az_storage_account_list)
- `--resource-id`: Passe o ID do seu recurso de conta de armazenamento, que está no formulário `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Para encontrar o seu ID de subscrição, utilize o comando da lista de conta Azure CLI [az;](/cli/azure/account?#az_account_list) para encontrar o nome da sua conta de armazenamento e grupo de recursos de conta de armazenamento, use o comando da lista de conta de armazenamento Azure CLI [az.](/cli/azure/storage/account?#az_storage_account_list)
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Fichas de assinatura de acesso partilhado

Também pode pedir ao Key Vault para gerar fichas de assinatura de acesso partilhado. Uma assinatura de acesso partilhado proporciona acesso delegado aos recursos na sua conta de armazenamento. Pode conceder aos clientes acesso a recursos na sua conta de armazenamento sem partilhar as chaves da sua conta. Uma assinatura de acesso partilhado proporciona-lhe uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da sua conta.

Os comandos nesta secção completam as seguintes ações:

- Desa cos para definir uma assinatura de acesso partilhado `<YourSASDefinitionName>` por conta. A definição está definida numa conta de armazenamento gerida pelo Key Vault `<YourStorageAccountName>` no cofre da `<YourKeyVaultName>` chave.
- Crie um token de assinatura de acesso partilhado de conta para os serviços Blob, File, Table e Queue. O token é criado para tipos de recursos Serviço, Contentor e Objeto. O token é criado com todas as permissões, mais https, e com as datas de início e fim especificadas.
- Desafine uma definição de assinatura de acesso partilhado de armazenamento no cofre. A definição tem o modelo URI do token de assinatura de acesso partilhado que foi criado. A definição tem o tipo de assinatura de acesso partilhado `account` e é válida para os dias N.
- Verifique se a assinatura de acesso partilhado foi guardada no cofre como segredo.

### <a name="create-a-shared-access-signature-token"></a>Criar um token de assinatura de acesso partilhado

Crie uma definição de assinatura de acesso partilhado utilizando o comando [de confli de armazenamento](/cli/azure/storage/account?#az_storage_account_generate_sas) Azure CLI. Esta operação requer as `storage` `setsas` permissões.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Depois de a operação ter sido executado com sucesso, copie a saída.

```console
"se=2020-01-01&sp=***"
```

Esta saída será passada para o `--template-uri` parâmetro no passo seguinte.

### <a name="generate-a-shared-access-signature-definition"></a>Gerar uma definição de assinatura de acesso partilhado

Utilize o azure CLI [az keyvault de armazenamento sas-definição criar](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_create) comando, passando a saída do passo anterior para o `--template-uri` parâmetro, para criar uma definição de assinatura de acesso partilhado.  Pode fornecer o nome da sua escolha ao `-n` parâmetro.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verifique a definição de assinatura de acesso partilhado

Pode verificar se a definição de assinatura de acesso partilhado foi armazenada no seu cofre de chaves utilizando o comando de [exibição de armazenamento sas-definição de teclado](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) Azure CLI.

Você pode agora usar o [az keyvault armazenamento sas-definição de comando](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) e a `id` propriedade para ver o conteúdo desse segredo.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [chaves, segredos e certificados.](/rest/api/keyvault/)
- Reveja artigos no blog da [equipa do Azure Key Vault](/archive/blogs/kv/).
- Consulte a documentação de referência de [armazenamento de teclado az.](/cli/azure/keyvault/storage)
