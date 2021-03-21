---
title: Quickstart para a biblioteca de clientes do Certificado de Cofre de Chaves Azure - Java
description: Conheça a biblioteca do cliente do Azure Key Vault Certificate para Java com os passos neste arranque rápido.
author: msmbaldwin
ms.custom: devx-track-java, devx-track-azurecli
ms.author: mbaldwin
ms.date: 12/18/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 99b8c63060cebeffea0f3473e03b5f49a415230b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936044"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-java-certificates"></a>Quickstart: Azure Key Vault Certificate client library for Java (Certificados)
Começa com a biblioteca de clientes do Azure Key Vault Certificate para a Java. Siga os passos abaixo para instalar a embalagem e experimente o código de exemplo para tarefas básicas.

Recursos adicionais:

* [Código fonte](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)
* [Documentação de referência da API](https://azure.github.io/azure-sdk-for-java/keyvault.html)
* [Documentação do produto](index.yml)
* [Amostras](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates/src/samples/java/com/azure/security/keyvault/certificates)

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
Numa janela de consola, utilize o `mvn` comando para criar uma nova aplicação de consola Java com o nome `akv-certificates-java` .

```console
mvn archetype:generate -DgroupId=com.keyvault.certificates.quickstart
                       -DartifactId=akv-certificates-java
                       -DarchetypeArtifactId=maven-archetype-quickstart
                       -DarchetypeVersion=1.4
                       -DinteractiveMode=false
```

A saída da geração do projeto será algo parecido com isto:

```console
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: packageInPathFormat, Value: com/keyvault/quickstart
[INFO] Parameter: package, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: groupId, Value: com.keyvault.certificates.quickstart
[INFO] Parameter: artifactId, Value: akv-certificates-java
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Project created from Archetype in dir: /home/user/quickstarts/akv-certificates-java
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  38.124 s
[INFO] Finished at: 2019-11-15T13:19:06-08:00
[INFO] ------------------------------------------------------------------------
```

Mude o seu diretório para a `akv-certificates-java/` pasta recém-criada.

```console
cd akv-certificates-java
```

### <a name="install-the-package"></a>Instale o pacote
Abra o ficheiro *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-security-keyvault-certificates</artifactId>
      <version>4.1.3</version>
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
Crie uma política de acesso para o cofre-chave que concede permissões de certificados à sua conta de utilizador.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
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
A biblioteca de clientes Azure Key Vault Certificate para Java permite-lhe gerir certificados. A secção [de exemplos de Código](#code-examples) mostra como criar um cliente, criar um certificado, recuperar um certificado e apagar um certificado.

Toda a aplicação da consola está [abaixo.](#sample-code)

## <a name="code-examples"></a>Exemplos de código
### <a name="add-directives"></a>Adicionar diretivas
Adicione as seguintes diretivas ao topo do seu código:

```java
import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;
```

### <a name="authenticate-and-create-a-client"></a>Autenticar e criar um cliente
Neste arranque rápido, um utilizador registado é utilizado para autenticar o Key Vault, que é o método preferido para o desenvolvimento local. Para aplicações implantadas no Azure, uma Identidade Gerida deve ser atribuída a um Serviço de Aplicações ou Máquina Virtual. Para mais informações, consulte [a Visão Geral da Identidade Gerida.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

No exemplo abaixo, o nome do seu cofre-chave é expandido para o cofre-chave URI, no formato "https:// \<your-key-vault-name\> .vault.azure.net". Este exemplo está a usar a classe ['DefaultAzureCredential()'](https://docs.microsoft.com/java/api/com.azure.identity.defaultazurecredential) que permite usar o mesmo código em diferentes ambientes com diferentes opções para fornecer identidade. Para mais informações, consulte [a Autenticação Credencial Azure Padrão.](https://docs.microsoft.com/java/api/overview/azure/identity-readme)

```java
String keyVaultName = System.getenv("KEY_VAULT_NAME");
String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

CertificateClient certificateClient = new CertificateClientBuilder()
    .vaultUrl(keyVaultUri)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

### <a name="save-a-secret"></a>Salvar um segredo
Agora que a sua aplicação está autenticada, pode criar um certificado no seu cofre de chaves utilizando o `certificateClient.beginCreateCertificate` método. Isto requer um nome para o certificado e uma política de `certificateName` certificados.

A criação de certificados é uma operação de longa duração, para a qual pode sondar o seu progresso ou esperar que esteja concluída.

```java
SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
    certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
certificatePoller.waitForCompletion();
```

Pode obter o certificado uma vez concluída a criação através da seguinte chamada:

```java
KeyVaultCertificate createdCertificate = certificatePoller.getFinalResult();
```

### <a name="retrieve-a-certificate"></a>Recuperar um certificado
Agora pode recuperar o certificado previamente criado com o `certificateClient.getCertificate` método.

```java
KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);
 ```

Pode agora aceder aos detalhes do certificado recuperado com operações `retrievedCertificate.getName` `retrievedCertificate.getProperties` como, etc. Bem como o seu `retrievedCertificate.getCer` conteúdo.

### <a name="delete-a-certificate"></a>Apagar um certificado
Finalmente, vamos apagar o certificado do seu cofre com o `certificateClient.beginDeleteCertificate` método, que também é uma operação de longa duração.

```java
SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
deletionPoller.waitForCompletion();
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
package com.keyvault.certificates.quickstart;

import com.azure.core.util.polling.SyncPoller;
import com.azure.identity.DefaultAzureCredentialBuilder;

import com.azure.security.keyvault.certificates.CertificateClient;
import com.azure.security.keyvault.certificates.CertificateClientBuilder;
import com.azure.security.keyvault.certificates.models.CertificateOperation;
import com.azure.security.keyvault.certificates.models.CertificatePolicy;
import com.azure.security.keyvault.certificates.models.DeletedCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificate;
import com.azure.security.keyvault.certificates.models.KeyVaultCertificateWithPolicy;

public class App {
    public static void main(String[] args) throws InterruptedException, IllegalArgumentException {
        String keyVaultName = System.getenv("KEY_VAULT_NAME");
        String keyVaultUri = "https://" + keyVaultName + ".vault.azure.net";

        System.out.printf("key vault name = %s and kv uri = %s \n", keyVaultName, keyVaultUri);

        CertificateClient certificateClient = new CertificateClientBuilder()
                .vaultUrl(keyVaultUri)
                .credential(new DefaultAzureCredentialBuilder().build())
                .buildClient();

        String certificateName = "myCertificate";

        System.out.print("Creating a certificate in " + keyVaultName + " called '" + certificateName + " ... ");

        SyncPoller<CertificateOperation, KeyVaultCertificateWithPolicy> certificatePoller =
                certificateClient.beginCreateCertificate(certificateName, CertificatePolicy.getDefault());
        certificatePoller.waitForCompletion();

        System.out.print("done.");
        System.out.println("Retrieving certificate from " + keyVaultName + ".");

        KeyVaultCertificate retrievedCertificate = certificateClient.getCertificate(certificateName);

        System.out.println("Your certificate's ID is '" + retrievedCertificate.getId() + "'.");
        System.out.println("Deleting your certificate from " + keyVaultName + " ... ");

        SyncPoller<DeletedCertificate, Void> deletionPoller = certificateClient.beginDeleteCertificate(certificateName);
        deletionPoller.waitForCompletion();

        System.out.print("done.");
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Neste quickstart criou um cofre chave, criou um certificado, recuperou-o e depois apagou-o. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do cofre da chave Azure](../general/overview.md)
- Consulte o [guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Como [garantir o acesso a um cofre chave](../general/secure-your-key-vault.md)