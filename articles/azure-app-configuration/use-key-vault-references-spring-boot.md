---
title: Tutorial para usar referências do Cofre da Chave de Configuração de Aplicações Azure numa aplicação Java Spring Boot / Microsoft Docs
description: Neste tutorial, você aprende a usar as referências key vault da Azure App Configuration a partir de uma aplicação Java Spring Boot
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: alkemper
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: ede8203078a3d496975e208622ef61018997cf8d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929225"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Tutorial: Use referências do Cofre de Chaves numa aplicação java Spring

Neste tutorial, aprende a utilizar o serviço de Configuração de Aplicações Azure juntamente com o Azure Key Vault. Configuração de aplicativos e Cofre de Chaves são serviços complementares utilizados lado a lado na maioria das implementações da aplicação.

A Configuração da Aplicação ajuda-o a utilizar os serviços em conjunto, criando teclas que referenciam valores armazenados no Key Vault. Quando a Configuração da Aplicação cria tais teclas, armazena os URIs dos valores do Cofre chave em vez dos próprios valores.

A sua aplicação utiliza o fornecedor de clientes de Configuração de Aplicações para recuperar referências do Key Vault, tal como acontece com quaisquer outras teclas armazenadas na Configuração da Aplicação. Neste caso, os valores armazenados na Configuração da Aplicação são URIs que referenciam os valores no Cofre de Chaves. Não são valores ou credenciais do Cofre-Chave. Como o fornecedor de clientes reconhece as chaves como referências do Key Vault, utiliza o Key Vault para recuperar os seus valores.

A sua aplicação é responsável pela autenticação adequada tanto na Configuração de Aplicações como no Cofre de Chaves. Os dois serviços não comunicam diretamente.

Este tutorial mostra-lhe como implementar referências do Key Vault no seu código. Baseia-se na aplicação web introduzida nos quickstarts. Antes de continuar, preencha [criar uma aplicação Java Spring com configuração de aplicação](./quickstart-java-spring-app.md) primeiro.

Você pode usar qualquer editor de código para fazer os passos neste tutorial. Por exemplo, [o Visual Studio Code](https://code.visualstudio.com/) é um editor de códigos de plataforma cruzada que está disponível para os sistemas operativos Windows, macOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma chave de configuração de aplicativos que faz referência a um valor armazenado no Key Vault.
> * Aceda ao valor desta chave a partir de uma aplicação Java Spring.

## <a name="prerequisites"></a>Pré-requisitos

* Azure subscrição - [crie uma gratuitamente](https://azure.microsoft.com/free/)
* Um Kit de [Desenvolvimento java suportado (JDK)](/java/azure/jdk) com a versão 8.
* [Apache Maven](https://maven.apache.org/download.cgi) versão 3.0 ou superior.

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **de recurso Criar** no canto superior esquerdo do portal Azure:

    ![A screenshot mostra a opção de recurso Criar no portal Azure.](./media/quickstarts/search-services.png)
1. Na caixa de pesquisa, introduza **o Cofre de Chaves.**
1. Na lista de resultados, selecione **cofres chave** à esquerda.
1. Em **cofres chave**, selecione **Add**.
1. À direita no **cofre de chaves Create,** forneça as seguintes informações:
    * **Selecione Subscrição** para escolher uma subscrição.
    * No **Grupo de Recursos,** selecione **Criar novo** e introduza um nome de grupo de recursos.
    * Em **key vault name**, um nome único é necessário. Para este tutorial, **insira Contoso-vault2.**
    * Na lista de drop-down da **Região,** escolha um local.
1. Deixe as outras **Opções de cofre de chave** com os seus valores padrão.
1. Selecione **Criar**.

Neste momento, a sua conta Azure é a única autorizada a aceder a este novo cofre.

![A imagem mostra o cofre da chave.](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, precisa dar apenas alguns passos adicionais. Neste caso, adicione uma mensagem que pode usar para testar a recuperação do Cofre de Chaves. A mensagem chama-se **Mensagem** e armazena o valor "Olá do Cofre de Chaves" nela.

1. A partir das páginas de propriedades do Cofre chave, selecione **Secrets**.
1. **Selecione Gerar/Importar.**
1. No painel Criar um painel **secreto,** insira os seguintes valores:
    * **Opções de upload**: Introduza **manual**.
    * **Nome**: **Introdução Mensagem**.
    * **Valor**: **Insira Olá a partir do Cofre da Chave**.
1. Deixar o outro **Criar propriedades secretas** com os seus valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicione uma referência do Cofre de Chaves à Configuração de Aplicações

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **todos os recursos** e, em seguida, selecione a instância da loja de Configuração de Aplicações que criou no arranque rápido.

1. Selecione **O Explorador de Configuração**.

1. Selecione **+ Criar**  >  **referência de cofre de chave** e, em seguida, especificar os seguintes valores:
    * **Chave**: Selecione **/application/config.keyvaultmessage**
    * **Etiqueta**: Deixe este valor em branco.
    * **Subscrição**, **Grupo de Recursos** e Cofre **chave**: Introduza os valores correspondentes aos valores no cofre-chave que criou na secção anterior.
    * **Segredo**: Selecione o segredo chamado **Mensagem** que criou na secção anterior.

## <a name="connect-to-key-vault"></a>Ligue-se ao cofre de chaves

1. Neste tutorial, você usa um principal de serviço para autenticação para Key Vault. Para criar este principal serviço, utilize o comando Azure CLI [ad sp create-for-rbac:](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Esta operação devolve uma série de pares chave/valor:

    ```json
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

1. Executar o seguinte comando para permitir que o principal de serviço aceda ao seu cofre chave:

    ```azurecli
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get
    ```

1. Executar o seguinte comando para obter o seu id de objeto e, em seguida, adicioná-lo à Configuração de Aplicação.

    ```azurecli
    az ad sp show --id <clientId-of-your-service-principal>
    az role assignment create --role "App Configuration Data Reader" --assignee-object-id <objectId-of-your-service-principal> --resource-group <your-resource-group>
    ```

1. Crie as variáveis ambientais **AZURE_CLIENT_ID,** **AZURE_CLIENT_SECRET** e **AZURE_TENANT_ID.** Utilize os valores para o principal de serviço que foram apresentados nos passos anteriores. Na linha de comando, executar os seguintes comandos e reiniciar o pedido de comando para permitir que a alteração produza efeitos:

    ```cmd
    setx AZURE_CLIENT_ID "clientId"
    setx AZURE_CLIENT_SECRET "clientSecret"
    setx AZURE_TENANT_ID "tenantId"
    ```

    Se utilizar o Windows PowerShell, executar o seguinte comando:

    ```azurepowershell
    $Env:AZURE_CLIENT_ID = "clientId"
    $Env:AZURE_CLIENT_SECRET = "clientSecret"
    $Env:AZURE_TENANT_ID = "tenantId"
    ```

    Se utilizar o macOS ou o Linux, executar o seguinte comando:

    ```cmd
    export AZURE_CLIENT_ID ='clientId'
    export AZURE_CLIENT_SECRET ='clientSecret'
    export AZURE_TENANT_ID ='tenantId'
    ```


> [!NOTE]
> Estas credenciais key vault são usadas apenas dentro da sua aplicação.  A sua aplicação autentica-se diretamente com o Key Vault utilizando estas credenciais sem envolver o serviço de Configuração de Aplicações.  O Cofre-Chave fornece autenticação tanto para a sua aplicação como para o seu serviço de Configuração de Aplicações sem partilhar ou expor chaves.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualize o seu código para utilizar uma referência do Cofre de Chaves

1. Crie uma variável ambiental chamada **APP_CONFIGURATION_ENDPOINT.** Desaponte o seu valor para o ponto final da sua loja de Configuração de Aplicações. Pode encontrar o ponto final na lâmina **'Chaves de acesso'** no portal Azure. Reinicie o pedido de comando para permitir que a alteração entre em vigor. 


1. Abrir *botastrap.propriedades* na pasta *de recursos.* Atualize este ficheiro para utilizar o valor **APP_CONFIGURATION_ENDPOINT.** Remova quaisquer referências a uma cadeia de ligação neste ficheiro. 

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].endpoint= ${APP_CONFIGURATION_ENDPOINT}
    ```

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

1. Abra *o HelloController.java*. Atualize o método *getMessage* para incluir a mensagem recuperada do Cofre de Chaves.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Crie um novo ficheiro chamado *AzureCredentials.java* e adicione o código abaixo.

    ```java
    package com.example.demo;

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

1. Criar um novo ficheiro chamado *AppConfiguration.java*. E adicione o código abaixo.

    ```java
    package com.example.demo;

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

1. Crie um novo ficheiro nos seus recursos o diretório META-INF chamado *spring.factorys* e adicione o código abaixo.

    ```factories
    org.springframework.cloud.bootstrap.BootstrapConfiguration=\
    com.example.demo.AppConfiguration
    ```

1. Construa a sua aplicação Boot de primavera com a Maven e execute-a, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Depois da sua aplicação estar em execução, use *o curl* para testar a sua aplicação, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```

    Vê a mensagem que inseriu na loja de Configuração de Aplicações. Também vê a mensagem que introduziu no Cofre de Chaves.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma chave de Configuração de Aplicações que faz referência a um valor armazenado no Key Vault. Para aprender a usar bandeiras de recurso na sua aplicação Java Spring, continue até ao próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerida](./quickstart-feature-flag-spring-boot.md)
