---
title: Quickstart - Biblioteca de clientes Azure Key Vault Key para Java
description: Proporciona um arranque rápido para a biblioteca de clientes Azure Key Vault Keys para Java.
author: msmbaldwin
ms.custom: devx-track-java
ms.author: mbaldwin
ms.date: 01/05/2021
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.openlocfilehash: 124e56fad35be0f3ac5b08ee9dd66454b9d077c5
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374697"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-java"></a>Quickstart: Azure Key Vault Key Key Biblioteca de clientes para Java
Começa com a biblioteca de clientes Azure Key Vault Key para a Java. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Recursos adicionais:

* [Código fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)
* [Documentação de referência da API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentação do produto](index.yml)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys/src/samples/java/com/azure/security/keyvault/keys)

## <a name="prerequisites"></a>Pré-requisitos
- Uma subscrição Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Java Development Kit (JDK)](/java/azure/jdk/) versão 8 ou superior
- [Apache Maven](https://maven.apache.org)
- [CLI do Azure](/cli/azure/install-azure-cli)

Este quickstart assume que você está correndo [Azure CLI](/cli/azure/install-azure-cli) e [Apache Maven](https://maven.apache.org) em uma janela terminal Linux.

## <a name="setting-up"></a>Configuração
Este quickstart está a utilizar a biblioteca Azure Identity com o Azure CLI para autenticar o utilizador nos Serviços Azure. Os desenvolvedores também podem usar o Visual Studio ou Visual Studio Code para autenticar as suas chamadas, para obter mais informações, ver [Autenticar o cliente com a biblioteca de clientes da Azure Identity](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure
1. Execute o comando `login`.

    ```azurecli-interactive
    az login
    ```

   Se o CLI conseguir abrir o seu navegador predefinido, fá-lo-á e carregará uma página de inscrição do Azure.

   Caso contrário, abra uma página do navegador [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e introduza o código de autorização exibido no seu terminal.

2. Inicie sessão com as credenciais da sua conta no browser.

### <a name="create-a-new-java-console-app"></a>Criar uma nova aplicação de consola Java
Numa janela de consola, utilize o `mvn` comando para criar uma nova aplicação de consola Java com o nome `akv-keys-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.keys.quickstart
                       -DartifactId=akv-keys-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A saída da geração do projeto será algo parecido com isto:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.keys.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.keys.quickstart
[INFO] Parameter: artifactId, Value: akv-keys-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-keys-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Mude o seu diretório para a `akv-keys-java/` pasta recém-criada.

```console
cd akv-keys-java
```

### <a name="install-the-package"></a>Instale o pacote
Abra o ficheiro *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-keys</artifactId>
      <version>4.2.3</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.2.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave
[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Conceder acesso ao seu cofre chave
Crie uma política de acesso para o seu cofre-chave que concede permissões chave à sua conta de utilizador.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente
Esta aplicação está a usar o nome do cofre como uma variável ambiental chamada `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS ou Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Modelo de objeto
A biblioteca de clientes Azure Key Vault Key para Java permite-lhe gerir as chaves. A secção [de exemplos de Código](#code-examples) mostra como criar um cliente, criar uma chave, recuperar uma chave e eliminar uma chave.

Toda a aplicação da consola está [abaixo.](#sample-code)

## <a name="code-examples"></a>Exemplos de código
### <a name="add-directives"></a>Adicionar diretivas
Adicione as seguintes diretivas ao topo do seu código:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente
Neste arranque rápido, um utilizador registado é utilizado para autenticar o Key Vault, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, uma Identidade Gerida deve ser atribuída a um Serviço de Aplicações ou Máquina Virtual. Para mais informações, consulte [a Visão Geral da Identidade Gerida.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

No exemplo abaixo, o nome do seu cofre-chave é expandido para o cofre-chave URI, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

KeyClient keyClient = new KeyClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="create-a-key"></a>Criar uma chave
Agora que a sua aplicação é autenticada, pode criar uma chave no cofre da chave utilizando o `keyClient.createKey` método. Isto requer um nome para a chave e um tipo chave- atribuímos o valor "myKey" à `keyName` variável e usamos um RSA `KeyType` nesta amostra.

```java
keyClient.createKey(keyName, KeyType.RSA);
```

Pode verificar se a chave foi definida com o comando [de teclado az keyvault:](/cli/azure/keyvault/key?#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Recuperar uma chave
Agora pode recuperar a chave previamente criada com o `keyClient.getKey` método.

```java
KeyVaultKey retrievedKey = keyClient.getKey(keyName);
 ```

Pode agora aceder aos detalhes da chave recuperada com operações `retrievedKey.getProperties` `retrievedKey.getKeyOperations` como, etc.

### <a name="delete-a-key"></a>Eliminar uma chave
Finalmente, vamos apagar a chave do cofre com o `keyClient.beginDeleteKey` método.

A eliminação chave é uma operação de longa duração, para a qual pode sondar o seu progresso ou esperar que esteja concluída.

```java
SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
deletionPoller.waitForCompletion();
```

Pode verificar se a chave foi eliminada com o comando [de teclado az keyvault:](/cli/azure/keyvault/key?#az-keyvault-key-show)

```azurecli
az keyvault key show --vault-name <your-unique-key-vault-name> --name myKey
```

## <a name="clean-up-resources"></a>Limpar os recursos
Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo
```java
package com.keyvault.keys.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.keys.KeyClient;
import com.azure.security.keyvault.keys.KeyClientBuilder;
import com.azure.security.keyvault.keys.models.DeletedKey;
import com.azure.security.keyvault.keys.models.KeyType;
import com.azure.security.keyvault.keys.models.KeyVaultKey;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and key vault URI = %s \n", keyVaultName, keyVaultUri);

        KeyClient keyClient = new KeyClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String keyName = "myKey";

        System.out.print("Creating a key in " + keyVaultName + " called '" + keyName + " ... ");

        keyClient.createKey(keyName, KeyType.RSA);

        System.out.print("done.");
        System.out.println("Retrieving key from " + keyVaultName + ".");

        KeyVaultKey retrievedKey = keyClient.getKey(keyName);

        System.out.println("Your key's ID is '" + retrievedKey.getId() + "'.");
        System.out.println("Deleting your key from " + keyVaultName + " ... ");

        SyncPoller<DeletedKey, Void> deletionPoller = keyClient.beginDeleteKey(keyName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Neste quickstart criaste um cofre chave, criaste uma chave, recuperaste-a e depois apagaste-a. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Leia a [visão geral](../general/security-overview.md) da segurança do Cofre de Chaves
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Como [garantir o acesso a um cofre chave](../general/secure-your-key-vault.md)