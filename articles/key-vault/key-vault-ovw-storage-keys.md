---
ms.assetid: ''
title: O Azure Key Vault geridos a conta de armazenamento - CLI
description: Chaves de conta de armazenamento fornecem uma integração perfeita entre o Azure Key Vault e chave de acesso baseado em à conta de armazenamento do Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: dc743f7e8ebaebf2b253a1c2c199133bc4266dd5
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404372"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>O Azure Key Vault geridos a conta de armazenamento - CLI

> [!NOTE]
> [Integração de armazenamento do Azure com o Azure Active Directory (Azure AD) está agora em pré-visualização](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Recomendamos a utilização do Azure AD para autenticação e autorização, que fornece acesso baseado em tokens do OAuth2 ao armazenamento do Azure, tal como o Azure Key Vault. Isto permite-lhe:
> - Autenticar a sua aplicação de cliente com uma identidade de utilizador ou aplicação, em vez das credenciais da conta de armazenamento. 
> - Utilize um [identidade gerida do Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando em execução no Azure. Gerido identidades remove a necessidade de autenticação de cliente em conjunto e armazenar credenciais no ou com a sua aplicação.
> - Utilize o controlo de acesso baseado em ' (RBAC) da função para gerir a autorização, que também é suportada pelo Key Vault.

Uma [conta de armazenamento do Azure](/azure/storage/storage-create-storage-account) utiliza uma credencial que consiste num nome de conta e uma chave. A chave é gerado automaticamente e serve mais como uma "palavra-passe" em vez de uma chave criptográfica. Cofre de chaves pode gerir estas chaves de conta de armazenamento, armazenando-os como [segredos do Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Descrição geral

O Cofre de chaves geridas recurso executa várias funções de gerenciamento em seu nome de conta de armazenamento:

- Chaves de listas (sincronizações) com uma conta de armazenamento do Azure.
- Regenera (roda) as chaves periodicamente.
- Gere chaves para contas de armazenamento e as contas de armazenamento clássico.
- Valores de chaves nunca são retornados em resposta ao chamador.

Quando utiliza a funcionalidade de chave de conta de armazenamento gerido:

- **Permitir apenas o Key Vault para gerir as chaves de conta de armazenamento.** Não tente geri-los por conta própria, pois irá interferir com os processos de Key Vault.
- **Não permitir que as chaves de conta de armazenamento ser gerido por mais de um objeto do Cofre de chaves**.
- **Manualmente não voltar a gerar as chaves de conta de armazenamento**. Recomendamos que regenere-los através do Key Vault.

O exemplo seguinte mostra como permitir que o Key Vault para gerir as chaves de conta de armazenamento.

> [!IMPORTANT]
> Um inquilino do Azure AD fornece cada aplicação registada com um  **[principal de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object)**, que serve como identidade da aplicação. ID da aplicação do principal de serviço é utilizado quando dando a ele autorização para aceder a outros recursos do Azure, através do controlo de acesso baseado em funções (RBAC). Uma vez que o Key Vault é um aplicativo da Microsoft, previamente está registado em todos os inquilinos do Azure AD sob o mesmo ID de aplicação, dentro de cada cloud do Azure:
> - Utilizam o Azure AD inquilinos na cloud do Azure government ID da aplicação `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - ID da aplicação de utilizar do Azure AD inquilinos na cloud pública do Azure e todos os outros `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Pré-requisitos
--------------
1. [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) instalar a CLI do Azure   
2. [Criar uma conta de armazenamento](https://azure.microsoft.com/services/storage/)
    - Siga os passos desta [documento](https://docs.microsoft.com/azure/storage/) para criar uma conta de armazenamento  
    - **Orientações de nomenclatura:** Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Passo a passo instruções sobre como utilizar o Key Vault para gerir chaves de conta de armazenamento
--------------------------------------------------------------------------------
Conceitualmente são a lista de passos que são seguidos
- Em primeiro lugar, obtemos uma conta de armazenamento (já existente)
- Podemos, em seguida, obter um cofre de chaves (já existente)
- Em seguida, adicionamos uma conta de armazenamento gerida pelo Cofre de chaves no cofre, definição chave1 como a chave do Active Directory e com um ponto de nova geração de 180 dias
- Por último definimos um contexto de armazenamento para a conta de armazenamento especificada, com chave1

Nas instruções, abaixo, está a atribuir Key Vault como um serviço para ter permissões de operador na sua conta de armazenamento

> [!NOTE]
> . Tenha em atenção que assim que tiver configurado a cópia de segurança gerida do Azure Key Vault conta de armazenamento de chaves que eles deve **não** já é possível alterar, exceto através do Key Vault. Armazenamento de chaves de conta significa que o Key Vault iria gerir a rotação da chave de conta de armazenamento geridas

> [!IMPORTANT]
> Um inquilino do Azure AD fornece cada aplicação registada com um  **[principal de serviço](/azure/active-directory/develop/developer-glossary#service-principal-object)**, que serve como identidade da aplicação. ID da aplicação do principal de serviço é utilizado quando dando a ele autorização para aceder a outros recursos do Azure, através do controlo de acesso baseado em funções (RBAC). Uma vez que o Key Vault é um aplicativo da Microsoft, previamente está registado em todos os inquilinos do Azure AD sob o mesmo ID de aplicação, dentro de cada cloud do Azure:
> - Utilizam o Azure AD inquilinos na cloud do Azure government ID da aplicação `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - ID da aplicação de utilizar do Azure AD inquilinos na cloud pública do Azure e todos os outros `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.


1. Depois de criar uma conta de armazenamento, execute o seguinte comando para obter o ID de recurso da conta de armazenamento, que pretende gerir

    ```
    az storage account show -n storageaccountname 
    ```
    Campo de ID de cópia fora o resultado do comando acima, que é semelhante a abaixo
    ```
    /subscriptions/0xxxxxx-4310-48d9-b5ca-0xxxxxxxxxx/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    
2. Atribua função RBAC "Armazenamento de conta chave de serviço de função de operador" para o Key Vault, limitar o âmbito de acesso à sua conta de armazenamento. Para uma conta de armazenamento clássicas, utilize "Clássico conta chave operador função do serviço armazenamento."
    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    "93c27d83-f79b-4cb2-8dd4-4aa716542e74" é o ID de objeto do Cofre de chaves na nuvem pública. Para obter o ID de objeto para o Key Vault em clouds nacionais consulte a seção importante acima
    
3. Criar um cofre de chaves de conta de armazenamento gerida.     <br /><br />
   Abaixo, iremos estiver a definir um período de regeneração de 90 dias. Após 90 dias, o Cofre de chaves irá regenerar 'chave1' e trocar a chave ativa de 'chave2' para 'chave1'. Agora ele irá marcar chave1 como a chave do Active Directory. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Passo a passo instruções sobre como utilizar o Cofre de chaves para criar e gerar tokens SAS
--------------------------------------------------------------------------------
Também pode fazer o Key Vault para gerar tokens SAS (assinatura de acesso partilhado). Uma assinatura de acesso partilhado fornece acesso delegado a recursos na sua conta de armazenamento. Com uma SAS, pode conceder clientes acesso a recursos na sua conta de armazenamento sem partilhar as chaves de conta. Este é o ponto fundamental da utilização de assinaturas de acesso partilhado nas suas aplicações – uma SAS é uma forma segura de partilhar os seus recursos de armazenamento sem comprometer as chaves da conta.

Depois de concluir os passos indicados acima, podem executar os seguintes comandos para fazer o Key Vault para gerar SAS tokens para. 

A lista de coisas que deve ser realizado dos passos abaixo, são
- Define uma definição de SAS com o nome de conta "<YourSASDefinitionName>'numa conta de armazenamento geridas pelo KeyVault'<YourStorageAccountName>"no seu Cofre"<VaultName>'. 
- Cria um token SAS de conta para serviços Blob, ficheiro, tabela e fila, para tipos de recursos, serviço, o contentor e o objeto, com todas as permissões, através de https e com as datas de início e de fim especificadas
- Define uma definição de SAS de armazenamento gerida pelo Cofre de chaves no cofre, com o uri do modelo que o token SAS criado acima, de SAS. o tipo 'account' e válido por dias de N
- Obtém o token de acesso real do segredo do KeyVault correspondente para a definição de SAS

1. Neste passo, irá criar uma definição de SAS. Depois de criar essa definição de SAS, pode fazer com que o Cofre de chaves para gerar tokens SAS mais para. Esta operação requer a permissão de armazenamento/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
Pode ver a ajuda mais sobre a operação acima [aqui](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)

Quando esta operação é executada com êxito, deve ver um resultado semelhante a como mostrado abaixo. Copiá-lo

```console
   "se=2020-01-01&sp=***"
```

2. Neste passo, irá utilizar a gera ($sasToken) acima para criar uma definição de SAS. Para obter mais documentação Leia [aqui](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > No caso de que o utilizador não tem permissões para a conta de armazenamento, vamos primeiro, obtenha o Id de objeto do utilizador

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="fetch-sas-tokens-in-code"></a>Obter tokens SAS no código

Nesta seção, abordaremos como pode fazer operações na sua conta de armazenamento, buscando [SAS tokens](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) do Key Vault

No abaixo de secção, vamos demonstrar como obter tokens SAS, assim que uma definição de SAS é criada, conforme mostrado acima.

> [!NOTE] 
  Existem 3 formas de autenticar para o Key Vault, como pode ler no [conceitos básicos](key-vault-whatis.md#basic-concepts)
> - Utilizar a identidade de serviço gerida (altamente recomendado)
> - Com o Principal de serviço e certificado 
> - Com o Principal de serviço e a palavra-passe (não recomendado)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se o token SAS está prestes a expirar, em seguida, poderia obter o token SAS novamente a partir do Key Vault e atualizar o código

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

### <a name="relevant-azure-cli-commands"></a>Comandos da CLI do Azure relevantes

[Comandos da CLI de armazenamento do Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>Consulte também

- [Sobre chaves, segredos e certificados](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog da Equipe do Cofre de chaves](https://blogs.technet.microsoft.com/kv/)
