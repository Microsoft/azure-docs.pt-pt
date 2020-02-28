---
title: Tutorial para usar referências chave de configuração de aplicativos Azure numa aplicação Java Spring Boot  Microsoft Docs
description: Neste tutorial, você aprende a usar referências chave de cofre de configuração de aplicativos da App Azure a partir de uma aplicação Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/16/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: d1fb963753577e9518d93262f9c9c7a1cf984005
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77656012"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Tutorial: Use referências chave vault em uma aplicação Java Spring

Neste tutorial, você aprende a usar o serviço de configuração de aplicações Azure juntamente com o Cofre chave Azure. Configuração de aplicativos e cofre de chaves são serviços complementares utilizados lado a lado na maioria das implementações de aplicações.

A Configuração da Aplicação ajuda-o a utilizar os serviços em conjunto, criando chaves que referenciam valores armazenados no Key Vault. Quando a Configuração da Aplicação cria tais chaves, armazena os URIs dos valores do Cofre Chave em vez dos próprios valores.

A sua aplicação utiliza o fornecedor de clientes de Configuração de Aplicações para recuperar referências do Cofre chave, tal como acontece com quaisquer outras chaves armazenadas na Configuração de Aplicações. Neste caso, os valores armazenados na Configuração de Aplicações são URIs que referenciam os valores no Cofre chave. Não são valores ou credenciais do Cofre Chave. Como o fornecedor de clientes reconhece as chaves como referências chave vault, usa o Key Vault para recuperar os seus valores.

A sua aplicação é responsável por autenticar adequadamente tanto a Configuração de Aplicações como a Chave Vault. Os dois serviços não comunicam diretamente.

Este tutorial mostra-lhe como implementar referências do Cofre chave no seu código. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, complete a Create a Java Spring app com a Configuração da [Aplicação](./quickstart-java-spring-app.md) primeiro.

Pode usar qualquer editor de código para fazer os passos neste tutorial. Por exemplo, o [Visual Studio Code](https://code.visualstudio.com/) é um editor de código seletiva que está disponível para os sistemas operativos Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma chave de configuração de aplicações que refira um valor armazenado no Cofre chave.
> * Aceda ao valor desta chave a partir de uma aplicação Java Spring.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura Azure - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Um Kit de Desenvolvimento Java suportado [(JDK)](https://docs.microsoft.com/java/azure/jdk) com a versão 8.
* [Apache Maven](https://maven.apache.org/download.cgi) versão 3.0 ou superior.

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção Criar uma opção **de recurso** no canto superior esquerdo do portal Azure:

    ![A saída após a criação do cofre chave está completa](./media/quickstarts/search-services.png)
1. Na caixa de pesquisa, introduza o **Cofre chave.**
1. Da lista de resultados, selecione **cofres chave** à esquerda.
1. Nos **cofres chave,** selecione **Adicionar**.
1. À direita no **cofre de chaves Create,** forneça as seguintes informações:
    * Selecione **Subscrição** para escolher uma subscrição.
    * No **Grupo de Recursos,** selecione **Criar novo** e insira um nome de grupo de recursos.
    * Em **nome do cofre chave,** é necessário um nome único. Para este tutorial, entre **contoso-abóbada2**.
    * Na lista de abandono da **Região,** escolha um local.
1. Deixe a outra Criar opções **de cofre chave** com os seus valores padrão.
1. Selecione **Criar**.

Neste ponto, a sua conta Azure é a única autorizada a aceder a este novo cofre.

![A saída após a criação do cofre chave está completa](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, precisa de dar apenas alguns passos adicionais. Neste caso, adicione uma mensagem que pode usar para testar a recuperação do Cofre chave. A mensagem chama-se **Mensagem**, e guarda-se o valor "Olá do Cofre Chave" nela.

1. A partir das páginas de propriedades do Cofre chave, selecione **Segredos**.
1. **Selecione Generate/Import**.
1. No **Painel Criar um** painel secreto, introduza os seguintes valores:
    * **Opções de upload**: Entrar **manual**.
    * **Nome**: Introduzir **mensagem**.
    * **Valor**: **Introduza olá a partir do cofre da chave**.
1. Deixe o outro **Criar propriedades secretas** com os seus valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicione uma referência chave vault à configuração da aplicação

1. Inicie sessão no [portal do Azure](https://portal.azure.com). **Selecione Todos os recursos**e, em seguida, selecione a instância da loja de configuração de aplicações que criou no arranque rápido.

1. Selecione Explorador de **Configuração**.

1. Selecione **+ Criar** > **referência do cofre chave,** e depois especificar os seguintes valores:
    * **Chave**: Selecione **/application/config.keyvaultmessage**
    * **Etiqueta**: Deixe este valor em branco.
    * **Subscrição,** **Grupo de Recursos**e Cofre **chave**: Introduza os valores correspondentes aos valores no cofre chave que criou na secção anterior.
    * **Segredo**: Selecione o segredo chamado **Mensagem** que criou na secção anterior.

## <a name="connect-to-key-vault"></a>Ligar ao cofre da chave

1. Neste tutorial, você usa um diretor de serviço para autenticação no Cofre chave. Para criar este diretor de serviço, utilize o comando Azure CLI [az ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Esta operação devolve uma série de pares chave/valor:

    ```console
    {
    "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
    "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
    "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
    "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "activeDirectoryGraphResourceId": "https://graph.windows.net/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
    }
    ```

1. Execute o seguinte comando para permitir que o diretor de serviço aceda ao seu cofre chave:

    ```console
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Execute o seguinte comando para obter o seu object-id e, em seguida, adicione-o à Configuração da Aplicação.

    ```console
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Crie as seguintes variáveis ambientais, utilizando os valores para o diretor de serviço que foram apresentados na etapa anterior:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clienteSecret*
    * **AZURE_TENANT_ID**: *inquilinoId*

> [!NOTE]
> Estas credenciais key vault são usadas apenas dentro da sua aplicação.  A sua aplicação autentica-se diretamente com o Key Vault utilizando estas credenciais sem envolver o serviço de Configuração de Aplicações.  O Key Vault fornece autenticação tanto para a sua aplicação como para o seu serviço de configuração de aplicações sem partilhar ou expor chaves.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualize o seu código para utilizar uma referência do Cofre de Chaves

1. Open *MessageProperties.java*. Adicione uma nova variável chamada *keyVaultMessage:*

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Abra *HelloController.java.* Atualize o método *getMessage* para incluir a mensagem obtida a partir do Cofre chave.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Crie um novo ficheiro chamado *AzureCredentials.java* e adicione o código abaixo.

    ```java
    package com.example;

    import com.azure.core.credential.TokenCredential;
    import com.azure.identity.EnvironmentCredentialBuilder;
    import com.microsoft.azure.spring.cloud.config.AppConfigurationCredentialProvider;
    import com.microsoft.azure.spring.cloud.config.KeyVaultCredentialProvider;

    public class AzureCredentials implements AppConfigurationCredentialProvider, KeyVaultCredentialProvider{

        @Override
        public TokenCredential getKeyVaultCredential(String uri) {
            return getCredential();
        }

        @Override
        public TokenCredential getAppConfigCredential(String uri) {
            return getCredential();
        }

        private TokenCredential getCredential() {
            return new EnvironmentCredentialBuilder().build();
        }

    }
    ```

1. Crie um novo ficheiro chamado *AppConfiguration.java*. E adicione o código abaixo.

    ```java
    package com.example;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;

    @Configuration
    public class AppConfiguration {

        @Bean
        public AzureCredentials azureCredentials() {
            return new AzureCredentials();
        }
    }
    ```

1. Crie um novo ficheiro no seu diretório meta-INF de recursos chamado *spring.fábricas* e adicione.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.AppConfiguration
    ```

1. Construa a sua aplicação Spring Boot com a Maven e execute-a, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Depois de a sua aplicação estar em execução, utilize *caracóis* para testar a sua aplicação, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Vê a mensagem que inseriu na loja de configuração de aplicações. Também vê a mensagem que inseriu no Cofre chave.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma chave de configuração de aplicações que refere um valor armazenado no Key Vault. Para aprender a usar bandeiras de recurso na sua aplicação Java Spring, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./quickstart-feature-flag-spring-boot.md)
