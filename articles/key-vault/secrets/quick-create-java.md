---
title: Quickstart - Biblioteca de clientes Azure Key Vault para Java
description: Fornece critérios de formato e conteúdo para a escrita Quickstarts para bibliotecas de clientes Azure SDK.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/20/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: a3e6901d41cc14ab3ea27f7c8cd9bbc4c98608a6
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901339"
---
# <a name="quickstart-azure-key-vault-client-library-for-java"></a>Quickstart: Biblioteca de clientes Azure Key Vault para Java

Começa com a biblioteca de clientes azure Key Vault para Java. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca de clientes Key Vault para Java para:

- Aumentar a segurança e o controlo sobre chaves e senhas.
- Crie e importe chaves de encriptação em minutos.
- Reduza a latência com a escala de nuvens e o despedimento global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Utilize HSMs validados de nível 2 fips 140-2.

[Documento](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault) | de | referência do código fonte[API](https://azure.github.io/azure-sdk-for-java)[Amostras de](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets/src/samples/java/com/azure/security/keyvault/secrets) [documentação do](index.yml) | produto

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição Azure - [crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versão 8 ou superior
- [Apache Maven](https://maven.apache.org)
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este quickstart assume que você está executando [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) e [Apache Maven](https://maven.apache.org) em uma janela terminal Linux.

## <a name="setting-up"></a>Configuração

### <a name="create-new-java-console-app"></a>Criar nova aplicação de consola Java

Numa janela de consola, utilize o `mvn` comando para criar `akv-java`uma nova aplicação de consola Java com o nome .

```console
mvn archetype:generate -DgroupId=com.keyvault.quickstart
                       -DartifactId=akv-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A produção da geração do projeto será algo assim:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.quickstart
[INFO] Parameter: artifactId, Value: akv-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Mude o seu diretório para a pasta akv-java recém-criada.

```console
cd akv-java
```

### <a name="install-the-package"></a>Instale o pacote

Abra o ficheiro *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-secrets</artifactId>
      <version>4.0.0</version>
    </dependency>

    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.0.0</version>
    </dependency>
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre chave

Este quickstart usa um cofre chave Azure pré-criado. Pode criar um cofre chave seguindo os passos no [quickstart Azure CLI,](quick-create-cli.md) [Azure PowerShell quickstart](quick-create-powershell.md)ou [portal Azure quickstart](quick-create-portal.md). Em alternativa, pode executar os comandos Azure CLI abaixo.

> [!Important]
> Cada cofre deve ter um nome único. Substitua <seu> de nome único com o seu cofre único com o nome do seu cofre-chave nos seguintes exemplos.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A forma mais simples de autenticar uma aplicação baseada na nuvem é com uma identidade gerida; ver Utilize um Serviço de [Aplicações gerido identidade para aceder ao Cofre de Chaves Azure](../general/managed-identity.md) para obter mais detalhes.

No entanto, por uma questão de simplicidade, este quickstart cria uma aplicação de ambiente de trabalho, que requer a utilização de um diretor de serviço e de uma política de controlo de acesso. O seu princípio de serviço requer um nome único no formato "http://<my-unique-service-principle-name>".

Crie um princípio de serviço utilizando o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli
az ad sp create-for-rbac -n "http://<my-unique-service-principle-name>" --sdk-auth
```

Esta operação devolverá uma série de pares chave/valor. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Tome nota do clienteId, clienteSecret e tenantId, pois vamos usá-los nos próximos dois passos.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Dê ao principal serviço acesso ao seu cofre chave

Crie uma política de acesso para o seu cofre chave que concede permissão ao seu diretor de serviço, passando o clienteId para o comando [de definição de teclado az keyvault.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) Dê ao diretor de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

#### <a name="set-environmental-variables"></a>Definir variáveis ambientais

O método DefaultAzureCredential na nossa aplicação baseia-se em três variáveis ambientais: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`e `AZURE_TENANT_ID`. utilizar definir estas variáveis para os valores clienteId, clientSecret e tenantId que você observou no passo [principal do serviço Criar um](#create-a-service-principal) serviço, acima. Utilize `export VARNAME=VALUE` o formato para definir as suas variáveis ambientais. (Este método apenas define as variáveis para a sua concha atual e os processos criados `/etc/environment ` a partir da concha; para adicionar permanentemente estas variáveis ao seu ambiente, editar o seu ficheiro.) 

Você também precisa guardar o seu nome chave `KEY_VAULT_NAME`do cofre como uma variável ambiental chamada .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para Java permite-lhe gerir chaves e bens relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como criar um cliente, definir um segredo, recuperar um segredo e apagar um segredo.

Toda a aplicação de consola está [abaixo](#sample-code).

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas ao topo do seu código:

```java
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente

Autenticar o seu cofre chave e criar um cliente chave vault depende das variáveis ambientais nas [variáveis ambientais definidas](#set-environmental-variables) acima. O nome do seu cofre chave é expandido para `https://<your-key-vault-name>.vault.azure.net`o cofre de chaves URI, no formato .

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String kvUri = "https://" + keyVaultName + ".vault.azure.net";

SecretClient secretClient = new SecretClientBuilder()
    .vaultUrl(kvUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação foi autenticada, pode colocar `secretClient.setSecret` um segredo no seu cofre usando o método. Isto requer um nome para o segredo. `secretName`  

```java
secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));
```

Pode verificar se o segredo foi definido com o comando secreto do [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o `secretClient.getSecret` método.

```java
KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);
 ```

Agora pode aceder ao valor do `retrievedSecret.getValue()`segredo recuperado com.

### <a name="delete-a-secret"></a>Eliminar um segredo

Finalmente, vamos apagar o segredo do seu `secretClient.beginDeleteSecret` cofre com o método.

```java
secretClient.beginDeleteSecret(secretName);
```

Pode verificar se o segredo se foi com o comando secreto do [az keyvault:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, pode utilizar o Azure CLI ou o Azure PowerShell para remover o cofre da chave e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Código de exemplo

```java
package com.keyvault.quickstart;

import java.io.Console;   

import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.azure.security.keyvault.secrets.models.KeyVaultSecret;

public class App {

    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {

        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String kvUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, kvUri);

        SecretClient secretClient = new SecretClientBuilder()
            .vaultUrl(kvUri)
            .credential(new DefaultAzureCredentialBuilder().build())
            .buildClient();


        Console con = System.console();  

        String secretName = "mySecret";

        System.out.println("Input the value of your secret > ");
        String secretValue = con.readLine();

        System.out.print("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ... ");

        secretClient.setSecret(new KeyVaultSecret(secretName, secretValue));

        System.out.println("done.");

        System.out.println("Forgetting your secret.");
        secretValue = "";
        System.out.println("Your secret is '" + secretValue + "'.");

        System.out.println("Retrieving your secret from " + keyVaultName + ".");

        KeyVaultSecret retrievedSecret = secretClient.getSecret(secretName);

        System.out.println("Your secret is '" + retrievedSecret.getValue() + "'.");
        System.out.print("Deleting your secret from " + keyVaultName + " ... ");

        secretClient.beginDeleteSecret(secretName);

        System.out.println("done.");


    }
}
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque, criaste um cofre chave, guardaste um segredo e recuperaste esse segredo. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do Cofre chave Azure](../general/overview.md)
- Consulte o guia do desenvolvedor do Cofre de [Chaves Azure](../general/developers-guide.md)
- Rever [as melhores práticas do Cofre de Chaves Azure](../general/best-practices.md)
