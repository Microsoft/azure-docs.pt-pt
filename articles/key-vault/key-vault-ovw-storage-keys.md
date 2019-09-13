---
title: Gerenciar chaves de conta de armazenamento com Azure Key Vault e o CLI do Azure
description: As chaves da conta de armazenamento fornecem integração direta entre Azure Key Vault e o acesso baseado em chave a uma conta de armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/01/2019
ms.openlocfilehash: 86ac58aea174ca05580da99b6bde0cc3823ab151
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883057"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gerenciar chaves de conta de armazenamento com Azure Key Vault e o CLI do Azure 

Azure Key Vault gerencia chaves para contas de armazenamento do Azure e contas de armazenamento clássicas. Você pode usar o recurso Key Vault conta de armazenamento gerenciado para concluir várias funções de gerenciamento de chaves para você.

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) usa uma credencial que consiste em um nome de conta e uma chave. A chave é gerada automaticamente e serve como uma senha, em vez de uma como uma chave de criptografia. Key Vault gerencia chaves de conta de armazenamento armazenando-as como [segredos Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). As chaves são listadas (sincronizadas) com uma conta de armazenamento do Azure e regeradas periodicamente ou _giradas_. 

Ao usar o recurso chave de conta de armazenamento gerenciado, considere os seguintes pontos:

- Os valores de chave nunca são retornados em resposta a um chamador.
- Somente Key Vault deve gerenciar suas chaves de conta de armazenamento. Não gerencie as chaves por conta própria e evite interferir nos processos de Key Vault.
- Apenas um único objeto Key Vault deve gerenciar chaves de conta de armazenamento. Não permita o gerenciamento de chaves de vários objetos.
- Você pode solicitar Key Vault para gerenciar sua conta de armazenamento com uma entidade de usuário, mas não com uma entidade de serviço.
- Regenerar chaves usando apenas Key Vault. Não regenere manualmente as chaves da conta de armazenamento. 

> [!NOTE]
> A integração do armazenamento do Azure com o Azure Active Directory (AD do Azure) é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft.
> A integração do Azure AD está disponível para [BLOBs e filas do Azure](../storage/common/storage-auth-aad.md).
> Use o Azure AD para autenticação e autorização.
> O Azure AD fornece acesso baseado em token OAuth2 ao armazenamento do Azure, assim como Azure Key Vault.
>
> O Azure AD permite que você autentique seu aplicativo cliente usando uma identidade de aplicativo ou de usuário, em vez de credenciais de conta de armazenamento.
> Você pode usar uma [identidade gerenciada do Azure ad](/azure/active-directory/managed-identities-azure-resources/) ao executar o no Azure. Identidades gerenciadas eliminam a necessidade de autenticação de cliente e armazenamento de credenciais no ou com seu aplicativo.
> O Azure AD usa o RBAC (controle de acesso baseado em função) para gerenciar a autorização, que também tem suporte pelo Key Vault.

### <a name="service-principal-application-id"></a>ID do aplicativo da entidade de serviço

Um locatário do Azure AD fornece a cada aplicativo registrado uma [entidade de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object). A entidade de serviço serve como a identidade do aplicativo (ID). A ID do aplicativo é usada durante a configuração de autorização para acesso a outros recursos do Azure por meio do RBAC.

Key Vault é um aplicativo da Microsoft que é previamente registrado em todos os locatários do Azure AD. O Key Vault é registrado na mesma ID de aplicativo e em cada nuvem do Azure.

| locatários | Nuvem | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure público | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar Key Vault para gerenciar sua chave de conta de armazenamento, examine os pré-requisitos:

- Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Crie uma [conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Siga [estas etapas](../storage/index.yml).
- O nome da conta de armazenamento deve usar apenas letras minúsculas e números. O comprimento do nome deve ter entre 3 e 24 caracteres.        
      
## <a name="manage-storage-account-keys"></a>Gerenciar chaves de conta de armazenamento

Há quatro etapas básicas para usar Key Vault gerenciar chaves de conta de armazenamento:

1. Obter uma conta de armazenamento existente.
1. Busque um cofre de chaves existente.
1. Adicione uma Key Vault conta de armazenamento gerenciado ao cofre. Defina `key1` como a chave ativa com um período de regeneração de 90 dias.
1. Use `key1` para definir um contexto de armazenamento para a conta de armazenamento especificada.

> [!NOTE]
> Key Vault como um serviço são atribuídas permissões de operador em sua conta de armazenamento.
> 
> Depois de configurar Azure Key Vault chaves de conta de armazenamento gerenciado, altere apenas as chaves usando Key Vault.
> Para chaves de conta de armazenamento gerenciado, o Key Vault gerencia a rotação da chave da conta de armazenamento.

1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter a ID de recurso da conta de armazenamento a ser gerenciada:
    ```
    az storage account show -n storageaccountname
    ```

    Copie o valor da ID do recurso da saída do comando:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Exemplo de saída:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Atribua a função RBAC "função de serviço do operador chave da conta de armazenamento" para Key Vault. Essa função limita o escopo de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássico, use a função "função de serviço do operador chave de conta de armazenamento clássica".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`é a ID de objeto para Key Vault na nuvem pública do Azure. Para obter a ID de objeto para Key Vault na nuvem do Azure governamental, consulte [ID do aplicativo de entidade de serviço](#service-principal-application-id).
    
1. Crie uma conta de armazenamento gerenciada Key Vault:

    Defina um período de regeneração de 90 dias. Após 90 dias, Key Vault regenera `key1` e troca a chave ativa de `key2` para `key1`. `key1`é marcado como a chave ativa. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Criar e gerar tokens

Você também pode solicitar Key Vault para gerar tokens de assinatura de acesso compartilhado. Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Você pode conceder aos clientes acesso aos recursos em sua conta de armazenamento sem compartilhar suas chaves de conta. Uma assinatura de acesso compartilhado fornece uma maneira segura de compartilhar seus recursos de armazenamento sem comprometer suas chaves de conta.

Os comandos nesta seção concluem as seguintes ações:

- Defina uma definição `<YourSASDefinitionName>`de assinatura de acesso compartilhado de conta. A definição é definida em uma Key Vault conta `<YourStorageAccountName>` de armazenamento gerenciado no cofre `<VaultName>`de chaves.
- Crie um token de assinatura de acesso compartilhado de conta para serviços BLOB, arquivo, tabela e fila. O token é criado para os tipos de recursos Service, container e Object. O token é criado com todas as permissões, via HTTPS e com as datas de início e término especificadas.
- Defina uma definição de assinatura de acesso compartilhado de armazenamento gerenciado Key Vault no cofre. A definição tem o URI do modelo do token de assinatura de acesso compartilhado que foi criado. A definição tem o tipo `account` de assinatura de acesso compartilhado e é válida por N dias.
- Recupere o token de acesso real do segredo de Key Vault que corresponde à definição de assinatura de acesso compartilhado.

Depois de concluir as etapas na seção anterior, execute os comandos a seguir para solicitar Key Vault para gerar tokens de assinatura de acesso compartilhado. 

1. Crie uma definição de assinatura de acesso compartilhado. Depois que a definição de assinatura de acesso compartilhado for criada, peça Key Vault para gerar mais tokens de assinatura de acesso compartilhado. Esta operação requer as `storage` permissões `setsas` e.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Para obter ajuda sobre a operação, consulte a documentação de referência do [AZ Storage Account Generate-SAS](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) .

    Depois que a operação for executada com êxito, copie a saída.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Use o `$sasToken` gerado pelo comando anterior e crie uma definição de assinatura de acesso compartilhado. Para obter mais informações sobre os parâmetros de comando, consulte a documentação [AZ keyvault Storage SAS-Definition Create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) Reference.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Quando o usuário não tiver permissões para a conta de armazenamento, primeiro obtenha a ID de objeto do usuário:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Buscar tokens no código

Execute operações em sua conta de armazenamento buscando [tokens de assinatura de acesso compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) do Key Vault.

Há três maneiras de se autenticar no Key Vault:

- Use uma identidade de serviço gerenciada. Essa abordagem é altamente recomendada.
- Use uma entidade de serviço e um certificado. 
- Use uma entidade de serviço e uma senha. Essa abordagem não é recomendada.

Para obter mais informações, [consulte Azure Key Vault: Conceitos](key-vault-whatis.md#basic-concepts)básicos.

O exemplo a seguir demonstra como buscar tokens de assinatura de acesso compartilhado. Você busca os tokens depois de criar uma definição de assinatura de acesso compartilhado. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se o token de assinatura de acesso compartilhado estiver prestes a expirar, busque novamente o token de assinatura de acesso compartilhado de Key Vault e atualize o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Comandos da CLI Azure

Para obter informações sobre os comandos de CLI do Azure que são relevantes para contas de armazenamento gerenciadas, consulte a documentação de referência de [armazenamento AZ keyvault](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/).
- Examine os artigos no [blog da equipe do Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
