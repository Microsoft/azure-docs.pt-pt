---
title: Tutorial - Identidade gerida para ligar o Key Vault à app Azure Spring Cloud
description: Configurar identidade gerida para ligar o Key Vault a uma aplicação Azure Spring Cloud
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 07/08/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c091241a4928b3f498be7944559aa5b91c6fecf0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101705063"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-spring-cloud-app"></a>Tutorial: Use uma identidade gerida para ligar o Key Vault a uma aplicação Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java

Este artigo mostra-lhe como criar uma identidade gerida para uma aplicação Azure Spring Cloud e usá-la para aceder ao Azure Key Vault.

O Azure Key Vault pode ser usado para armazenar e controlar de forma segura o acesso a fichas, senhas, certificados, chaves API e outros segredos para a sua aplicação. Pode criar uma identidade gerida no Azure Ative Directory (AAD) e autenticar qualquer serviço que suporte a autenticação AAD, incluindo o Key Vault, sem ter de apresentar credenciais no seu código.

## <a name="prerequisites"></a>Pré-requisitos

* [Inscreva-se para uma subscrição do Azure](https://azure.microsoft.com/free/)
* [Instale a versão Azure CLI 2.0.67 ou superior](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Instalar Maven 3.0 ou superior](https://maven.apache.org/download.cgi)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos para conter tanto o Cofre de Chaves como a Nuvem de primavera utilizando o grupo de comando [az criar:](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-create)

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Configurar o seu Cofre de Chaves
Para criar um Cofre de Chaves, utilize o teclado de comando [az create](/cli/azure/keyvault?view=azure-cli-latest&preserve-view=true#az-keyvault-create):

> [!Important]
> Cada Cofre-Chave deve ter um nome único. Substitua <o seu nome de chave-> pelo nome do seu Cofre de Chaves nos seguintes exemplos.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Tome nota da devolvida `vaultUri` , que estará no formato "https://<o seu nome keyvault>.vault.azure.net". Será usado no passo seguinte.

Agora pode colocar um segredo no seu Cofre de Chaves com o conjunto secreto de comando [az keyvault](/cli/azure/keyvault/secret?view=azure-cli-latest&preserve-view=true#az-keyvault-secret-set):

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" \
    --name "connectionString" \
    --value "jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;"
```

## <a name="create-azure-spring-cloud-service-and-app"></a>Criar serviço e app Azure Spring Cloud
Depois de instalar a extensão correspondente, crie uma instância Azure Spring Cloud com o comando Azure CLI `az spring-cloud create` . 

```azurecli-interactive
az extension add --name spring-cloud
az spring-cloud create -n "myspringcloud" -g "myResourceGroup"
```
O exemplo a seguir cria uma aplicação com o nome `springapp` de uma identidade gerida atribuída ao sistema, conforme solicitado pelo `--assign-identity` parâmetro.

```azurecli
az spring-cloud app create -n "springapp" -s "myspringcloud" -g "myResourceGroup" --is-public true --assign-identity
export SERVICE_IDENTITY=$(az spring-cloud app show --name "springapp" -s "myspringcloud" -g "myResourceGroup" | jq -r '.identity.principalId')
```

Tome nota do `url` devolvido, que estará no formato `https://<your-app-name>.azuremicroservices.io` . Será usado no passo seguinte.


## <a name="grant-your-app-access-to-key-vault"></a>Conceda à sua aplicação acesso ao Key Vault
Use `az keyvault set-policy` para conceder acesso adequado no Key Vault para a sua aplicação.
```azurecli
az keyvault set-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY} --secret-permissions set get list
```
> [!NOTE]
> Utilize `az keyvault delete-policy --name "<your-keyvault-name>" --object-id ${SERVICE_IDENTITY}` para remover o acesso à sua aplicação depois de a identidade gerida atribuída pelo sistema ser desativada.

## <a name="build-a-sample-spring-boot-app-with-spring-boot-starter"></a>Construa uma aplicação de boot de mola de amostra com arranque de Bota de primavera
Esta aplicação terá acesso para obter segredos do Azure Key Vault. Utilize a aplicação inicial: Arranque de [arranque Azure Key Vault Secrets Spring](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets).  O Cofre da Chave Azure é adicionado como um exemplo de Spring **PropertySource**.  Os segredos armazenados no Cofre da Chave Azure podem ser convenientemente acedidos e utilizados como qualquer propriedade de configuração externa, como propriedades em ficheiros. 

1. Gere um projeto de amostra a partir de start.spring.io com o Azure Key Vault Spring Starter. 
    ```azurecli
    curl https://start.spring.io/starter.tgz -d dependencies=web,azure-keyvault-secrets -d baseDir=springapp -d bootVersion=2.3.1.RELEASE -d javaVersion=1.8 | tar -xzvf -
    ```

2. Especifique o seu Cofre-Chave na sua aplicação. 

    ```azurecli
    cd springapp
    vim src/main/resources/application.properties
    ```

    Para utilizar a identidade gerida para aplicações Azure Spring Cloud, adicione propriedades com o conteúdo abaixo ao src/main/resources/application.properties.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```
    > [!Note] 
    > Deve adicionar o url do cofre chave `application.properties` como acima. Caso contrário, o url do cofre-chave pode não ser capturado durante o tempo de funcionação.

3. Adicione o exemplo de código ao src/main/java/com/exemplo/demo/DemoApplication.java. Recupera a cadeia de ligação do Cofre da Chave. 

    ```Java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.boot.CommandLineRunner;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @SpringBootApplication
    @RestController
    public class DemoApplication implements CommandLineRunner {

        @Value("${connectionString}")
        private String connectionString;

        public static void main(String[] args) {
          SpringApplication.run(DemoApplication.class, args);
        }

        @GetMapping("get")
        public String get() {
          return connectionString;
        }

        public void run(String... varl) throws Exception {
          System.out.println(String.format("\nConnection String stored in Azure Key Vault:\n%s\n",connectionString));
        }
      }
    ```

    Se abrir pom.xml, verá a dependência `azure-keyvault-secrets-spring-boot-starter` de. Adicione esta dependência ao seu projeto em pom.xml. 

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-keyvault-secrets-spring-boot-starter</artifactId>
    </dependency>
    ```

4. Embale o seu aplicativo de amostra. 

    ```azurecli
    mvn clean package
    ```

5. Agora pode implementar a sua aplicação para Azure com o comando Azure CLI  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/demo-0.0.1-SNAPSHOT.jar
    ```

6.  Para testar a sua aplicação, aceda ao ponto final público ou ao ponto final do teste.

    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/get
    ```

    Verá a mensagem "Obteve com sucesso o valor da conexão secreta A partir de Key Vault https://<o seu nome keyvault>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

## <a name="build-sample-spring-boot-app-with-java-sdk"></a>Construa a aplicação De Boot de primavera de amostra com Java SDK

Esta amostra pode definir e obter segredos do Azure Key Vault. A [biblioteca de clientes Azure Key Vault Secret para a Java](/java/api/overview/azure/security-keyvault-secrets-readme?preserve-view=true&view=azure-java-stablelibrary) fornece suporte de autenticação simbólica Azure Ative Directory em todo o Azure SDK. Fornece um conjunto de implementações **TokenCredential** que podem ser usadas para construir clientes Azure SDK para apoiar a autenticação simbólica da AAD.

A biblioteca de clientes Azure Key Vault Secret permite-lhe armazenar e controlar de forma segura o acesso a fichas, senhas, chaves API e outros segredos. A biblioteca oferece operações para criar, recuperar, atualizar, eliminar, purgar, recuar, restaurar e listar os segredos e as suas versões.

1. Clone um projeto de amostra. 

    ```azurecli
    git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
    ```

2. Especifique o seu Cofre-Chave na sua aplicação. 

    ```azurecli
    cd Azure-Spring-Cloud-Samples/managed-identity-keyvault
    vim src/main/resources/application.properties
    ```

    Para utilizar a identidade gerida para aplicações Azure Spring Cloud, adicione propriedades com os seguintes conteúdos para *src/main/resources/application.properties*.

    ```
    azure.keyvault.enabled=true
    azure.keyvault.uri=https://<your-keyvault-name>.vault.azure.net
    ```

3. Inclua [ManagedIdentityCredentialBuilder](/java/api/com.azure.identity.managedidentitycredentialbuilder?preserve-view=true&view=azure-java-stable) para obter ficha do Azure Ative Directory e [SecretClientBuilder](/java/api/com.azure.security.keyvault.secrets.secretclientbuilder?preserve-view=true&view=azure-java-stable) para definir ou obter segredos do Key Vault no seu código.

    Obtenha o exemplo da [MainController.java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/src/main/java/com/microsoft/azure/MainController.java#L28) do projeto de amostra clonada.

    Inclua `azure-identity` também e como dependência no seu `azure-security-keyvault-secrets` pom.xml. Obtenha o exemplo de [pom.xml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/managed-identity-keyvault/pom.xml#L21) do projeto de amostra clonada. 

4. Embale o seu aplicativo de amostra. 

    ```azurecli
    mvn clean package
    ```

5. Agora implemente a aplicação para Azure com o comando Azure CLI  `az spring-cloud app deploy` . 

    ```azurecli
    az spring-cloud app deploy -n "springapp" -s "myspringcloud" -g "myResourceGroup" --jar-path target/asc-managed-identity-keyvault-sample-0.1.0.jar
    ```

6. Aceda ao ponto final público ou ao ponto final de teste para testar a sua aplicação. 

    Primeiro, obtenha o valor do seu segredo que estabeleceu no Cofre da Chave Azure. 
    ```azurecli
    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/connectionString
    ```

    Verá a mensagem "Obteve com sucesso o valor da conexão secreta A partir de Key Vault https://<o seu nome keyvault>.vault.azure.net/: jdbc:sqlserver://SERVER.database.windows.net:1433;database=DATABASE;".

    Agora cria um segredo e depois recupera-o usando o SDK Java. 
    ```azurecli
    curl -X PUT https://myspringcloud-springapp.azuremicroservices.io/secrets/test?value=success

    curl https://myspringcloud-springapp.azuremicroservices.io/secrets/test
    ```

    Você verá a mensagem "Conseguiu o valor do teste secreto do Key Vault https://<o seu nome keyvault>.vault.azure.net: sucesso". 

## <a name="next-steps"></a>Passos seguintes

* [Como aceder ao blob de armazenamento com identidade gerida em Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Como permitir a identidade gerida atribuída pelo sistema para a aplicação Azure Spring Cloud](./spring-cloud-howto-enable-system-assigned-managed-identity.md)
* [Saiba mais sobre identidades geridas para recursos Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Autenticar nuvem de primavera azure com cofre chave em ações do GitHub](./spring-cloud-github-actions-key-vault.md)
