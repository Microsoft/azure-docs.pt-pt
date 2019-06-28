---
title: Gerir chaves de conta de armazenamento com o Azure Key Vault e a CLI do Azure
description: Chaves de conta de armazenamento fornecem uma integração perfeita entre o Azure Key Vault e baseada em chave de acesso a uma conta de armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 6ac054bc9750e4297080c4ab64030c9c6a5fb55a
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312839"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gerir chaves de conta de armazenamento com o Azure Key Vault e a CLI do Azure 

O Azure Key Vault gere chaves para contas de armazenamento do Azure e contas de armazenamento clássicas. Pode utilizar a funcionalidade de conta de armazenamento gerido do Key Vault para concluir várias funções de gestão de chaves para.

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) utiliza uma credencial que consiste num nome de conta e uma chave. A chave é gerado automaticamente e serve como uma palavra-passe, em vez de um como uma chave criptográfica. Key Vault gere chaves de conta de armazenamento, armazenando-os como [segredos do Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). As chaves são listadas (sincronizadas) com uma conta de armazenamento do Azure e são periodicamente regeneradas ou _girado_. 

Quando utiliza a funcionalidade de chave de conta de armazenamento gerido, considere os seguintes pontos:

- Valores de chaves nunca são retornados em resposta a um chamador.
- Cofre de chaves apenas deve gerir as chaves de conta de armazenamento. Não gerir as chaves e evitar interferências com processos de Key Vault.
- Apenas um único objeto de Cofre de chaves deve gerir chaves de conta de armazenamento. Não permitir a gestão de chaves de vários objetos.
- Pode solicitar o Key Vault para gerir a sua conta de armazenamento com um principal de utilizador, mas não com um principal de serviço.
- Voltar a gerar chaves com o Cofre de chaves apenas. Manualmente não voltar a gerar as chaves de conta de armazenamento. 

> [!NOTE]
> Integração de armazenamento do Azure com o Azure Active Directory (Azure AD) é baseado na nuvem acesso e identidade do serviço de gestão. da Microsoft
> Integração do Azure AD está disponível para [blobs do Azure e filas](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Utilize o Azure AD para autenticação e autorização.
> O Azure AD fornece acesso baseado em tokens do OAuth2 ao armazenamento do Azure, tal como o Azure Key Vault.
>
> O Azure AD permite-lhe autenticar a sua aplicação de cliente utilizando uma identidade de utilizador ou aplicação, em vez das credenciais da conta de armazenamento.
> Pode utilizar um [identidade gerida do Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando executa no Azure. Identidades geridas de remover a necessidade de autenticação de cliente e o armazenamento de credenciais no ou com a sua aplicação.
> Azure AD utiliza o controlo de acesso baseado em funções (RBAC) para gerir a autorização, que também é suportado pelo Cofre de chaves.

### <a name="service-principal-application-id"></a>ID da aplicação principal de serviço

Um inquilino do Azure AD fornece cada aplicação registada com um [principal de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object). O principal de serviço serve como a identidade da aplicação (ID). O ID da aplicação é utilizado durante a configuração de autorização de acesso a outros recursos do Azure através do RBAC.

O Key Vault é uma aplicação da Microsoft que já está registrada em inquilinos de contas do Azure AD. O Key Vault está registado sob o mesmo ID de aplicação e dentro de cada cloud do Azure.

| Inquilinos | Nuvem | ID da aplicação |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Peering público do Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Outros  | Qualquer | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Pré-requisitos

Antes de utilizar o Key Vault para gerir a sua chave de conta de armazenamento, reveja os pré-requisitos:

- Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Criar uma [conta de armazenamento do Azure](https://azure.microsoft.com/services/storage/). Siga [essas etapas](https://docs.microsoft.com/azure/storage/).
- O nome da conta de armazenamento tem de utilizar apenas letras minúsculas e números. O comprimento do nome tem de ter entre 3 e 24 carateres.        
      
## <a name="manage-storage-account-keys"></a>Gerir chaves de conta de armazenamento

Existem quatro passos básicos para utilizar o Key Vault para gerir as chaves de conta de armazenamento:

1. Obtenha uma conta de armazenamento existente.
1. Obter Cofre de chaves existente.
1. Adicione uma conta de armazenamento do Cofre de chaves geridas para o cofre. Definir `key1` como a chave do Active Directory com um ponto de nova geração de 180 dias.
1. Utilize `key1` para definir um contexto de armazenamento para a conta de armazenamento especificada.

> [!NOTE]
> Cofre de chaves, como um serviço é receber permissões de operador na sua conta de armazenamento.
> 
> Depois de configurar chaves de conta de armazenamento do Azure Key Vault geridos, apenas altere as chaves com o Key Vault.
> Para chaves de conta de armazenamento gerido, o Key Vault gerencia a rotação da chave de conta de armazenamento.

1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter o ID de recurso da conta de armazenamento para gerir:
    ```
    az storage account show -n storageaccountname
    ```

    Copie o valor de ID de recurso da saída do comando:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Exemplo de saída:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Atribua a função RBAC de "Função conta de armazenamento chave operador serviço" para o Key Vault. Esta função limita o âmbito de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássicas, utilize a função de "Clássico função conta de armazenamento chave operador Service".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` é o ID de objeto para o Cofre de chaves na cloud pública do Azure. Para obter o ID de objeto do Cofre de chaves na cloud do Azure Government, veja [ID da aplicação principal de serviço](#service-principal-application-id).
    
1. Crie uma conta de armazenamento gerida do Cofre de chave:

    Defina um período de regeneração de 90 dias. Após 90 dias, gera novamente o Key Vault `key1` e a chave Active Directory a partir de troca `key2` para `key1`. `key1` em seguida, é marcado como a chave do Active Directory. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Criar e gerar tokens

Também pode fazer o Key Vault para gerar tokens de assinatura de acesso partilhado. Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Pode conceder clientes acesso a recursos na sua conta de armazenamento sem partilhar as chaves de conta. Uma assinatura de acesso partilhado fornece uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da conta.

Os comandos nesta secção, conclua as seguintes ações:

- Definir uma definição de assinatura de acesso de conta partilhada `<YourSASDefinitionName>`. A definição está definida numa conta de armazenamento do Cofre de chaves geridas `<YourStorageAccountName>` no seu Cofre de chaves `<VaultName>`.
- Crie um token de assinatura de acesso partilhado de conta para serviços Blob, ficheiro, tabela e fila. O token é criado para tipos de recursos de serviço, o contentor e o objeto. O token é criado com todas as permissões, através de https e com as datas de início e de fim especificadas.
- Defina uma definição de assinatura de acesso do Cofre de chaves geridas armazenamento partilhado no cofre. A definição tem o modelo de URI do token de assinatura de acesso partilhado que foi criado. A definição tem o tipo de assinatura de acesso partilhado `account` e é válida por N dias.
- Obter o token de acesso real a partir do segredo do Key Vault que corresponde à definição de assinatura de acesso partilhado.

Depois de concluir os passos na secção anterior, execute os seguintes comandos para fazer o Key Vault para gerar tokens de assinatura de acesso partilhado. 

1. Crie uma definição de assinatura de acesso partilhado. Depois de criar a definição de assinatura de acesso partilhado, peça ao Cofre de chaves para gerar mais tokens de assinatura de acesso partilhado. Esta operação necessita que o `storage` e `setsas` permissões.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Para obter ajuda sobre a operação, consulte a [az storage gerar-sas de conta](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) documentação de referência.

    Depois da operação é executada com êxito, copie a saída.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Utilize o `$sasToken` gerado pelo comando anterior e criar uma definição de assinatura de acesso partilhado. Para obter mais informações sobre os parâmetros de comando, consulte a [az keyvault armazenamento sas definição criar](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) documentação de referência.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Quando o utilizador não tem permissões para a conta de armazenamento, primeiro de obter o ID de objeto do utilizador:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Obter tokens no código

Realizar operações em sua conta de armazenamento, buscando [partilhado de tokens de assinatura de acesso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do Key Vault.

Existem três formas de autenticar para o Key Vault:

- Utilize uma identidade de serviço gerido. Essa abordagem é altamente recomendada.
- Utilize um principal de serviço e o certificado. 
- Utilize um principal de serviço e a palavra-passe. Essa abordagem não é recomendada.

Para obter mais informações, consulte [Azure Key Vault: Conceitos básicos](key-vault-whatis.md#basic-concepts).

O exemplo seguinte demonstra como obter tokens de assinatura de acesso partilhado. Obter os tokens depois de criar uma definição de assinatura de acesso partilhado. 

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

Se o seu token de assinatura de acesso partilhado está prestes a expirar, obter o token de assinatura de acesso partilhado novamente a partir do Key Vault e atualizar o código.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Comandos da CLI Azure

Para obter informações sobre os comandos da CLI do Azure que são relevantes para as contas de armazenamento geridos, consulte a [armazenamento do Cofre de chaves de az](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) documentação de referência.

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/).
- Consulte artigos sobre o [blogue da equipa do Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
