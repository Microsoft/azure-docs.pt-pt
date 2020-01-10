---
title: Tutorial para usar as referências de Key Vault de configuração Azure App em um aplicativo Java Spring boot | Microsoft Docs
description: Neste tutorial, você aprenderá a usar as referências Key Vault da configuração do Azure App de um aplicativo Spring boot do Java
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
ms.openlocfilehash: 17d86f25de6eecee535d6f812f4ef0b078a4b6db
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75752516"
---
# <a name="tutorial-use-key-vault-references-in-a-java-spring-app"></a>Tutorial: usar referências de Key Vault em um aplicativo Spring Java

Neste tutorial, você aprenderá a usar o serviço de configuração do Azure App junto com Azure Key Vault. A configuração do aplicativo e os Key Vault são serviços complementares usados lado a lado na maioria das implantações de aplicativo.

A configuração de aplicativo ajuda você a usar os serviços juntos criando chaves que fazem referência a valores armazenados em Key Vault. Quando a configuração do aplicativo cria essas chaves, ela armazena os URIs de valores Key Vault em vez dos próprios valores.

Seu aplicativo usa o provedor do cliente de configuração de aplicativo para recuperar referências de Key Vault, assim como faz para qualquer outra chave armazenada na configuração do aplicativo. Nesse caso, os valores armazenados na configuração do aplicativo são URIs que fazem referência aos valores na Key Vault. Eles não são Key Vault valores ou credenciais. Como o provedor do cliente reconhece as chaves como referências Key Vault, ele usa Key Vault para recuperar seus valores.

Seu aplicativo é responsável por autenticar corretamente tanto para a configuração do aplicativo quanto para o Key Vault. Os dois serviços não se comunicam diretamente.

Este tutorial mostra como implementar referências de Key Vault em seu código. Ele se baseia no aplicativo Web introduzido nos guias de início rápido. Antes de continuar, conclua [a criação de um aplicativo Spring Java com a configuração de aplicativo](./quickstart-java-spring-app.md) primeiro.

Você pode usar qualquer editor de código para executar as etapas neste tutorial. Por exemplo, [Visual Studio Code](https://code.visualstudio.com/) é um editor de código de plataforma cruzada que está disponível para os sistemas operacionais Windows, MacOS e Linux.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie uma chave de configuração de aplicativo que faça referência a um valor armazenado em Key Vault.
> * Acesse o valor dessa chave de um aplicativo Spring Java.

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-vault"></a>Criar um cofre

1. Selecione a opção **criar um recurso** no canto superior esquerdo do portal do Azure:

    ![Saída após a conclusão da criação do Key Vault](./media/quickstarts/search-services.png)
1. Na caixa de pesquisa, digite **Key Vault**.
1. Na lista de resultados, selecione **cofres de chaves** à esquerda.
1. Em **cofres de chaves**, selecione **Adicionar**.
1. À direita em **criar cofre de chaves**, forneça as seguintes informações:
    - Selecione **assinatura** para escolher uma assinatura.
    - Em **grupo de recursos**, selecione **criar novo** e insira um nome de grupo de recursos.
    - No **nome do Key Vault**, é necessário um nome exclusivo. Para este tutorial, insira **contoso-vault2**.
    - Na lista suspensa **região** , escolha um local.
1. Deixe a outra opção **criar cofre de chaves** com seus valores padrão.
1. Selecione **Criar**.

Neste ponto, sua conta do Azure é a única autorizada a acessar esse novo cofre.

![Saída após a conclusão da criação do Key Vault](./media/quickstarts/vault-properties.png)

## <a name="add-a-secret-to-key-vault"></a>Adicionar um segredo ao Key Vault

Para adicionar um segredo ao cofre, você precisa executar apenas algumas etapas adicionais. Nesse caso, adicione uma mensagem que você pode usar para testar Key Vault recuperação. A mensagem é chamada de **mensagem**e você armazena o valor "olá de Key Vault".

1. Nas páginas de propriedades do Key Vault, selecione **segredos**.
1. Selecione **gerar/importar**.
1. No painel **criar um segredo** , insira os seguintes valores:
    - **Opções de carregamento**: insira **manual**.
    - **Nome**: Insira a **mensagem**.
    - **Valor**: insira **Olá de Key Vault**.
1. Deixe o outro **criar as propriedades secretas** com seus valores padrão.
1. Selecione **Criar**.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Adicionar uma referência de Key Vault à configuração do aplicativo

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **todos os recursos**e, em seguida, selecione a instância do repositório de configuração de aplicativo que você criou no guia de início rápido.

1. Selecione **Configuration Explorer**.

1. Selecione **+ criar** > **referência do cofre de chaves**e especifique os seguintes valores:
    - **Chave**: selecione **/Application/config.keyvaultmessage**
    - **Rótulo**: Deixe esse valor em branco.
    - **Assinatura**, **grupo de recursos**e **cofre de chaves**: Insira os valores correspondentes aos valores no cofre de chaves que você criou na seção anterior.
    - **Segredo**: selecione a **mensagem** nomeada do segredo que você criou na seção anterior.

## <a name="connect-to-key-vault"></a>Conectar-se ao Key Vault

1. Neste tutorial, você usa uma entidade de serviço para autenticação para Key Vault. Para criar essa entidade de serviço, use o comando CLI do Azure [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

    ```azurecli
    az ad sp create-for-rbac -n "http://mySP" --sdk-auth
    ```

    Esta operação retorna uma série de pares de chave/valor:

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

1. Execute o seguinte comando para permitir que a entidade de serviço acesse o cofre de chaves:

    ```
    az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
    ```

1. Crie as seguintes variáveis de ambiente, usando os valores para a entidade de serviço que foram exibidos na etapa anterior:

    * **AZURE_CLIENT_ID**: *clientId*
    * **AZURE_CLIENT_SECRET**: *clientSecret*
    * **AZURE_TENANT_ID**: *tenantid*

> [!NOTE]
> Essas credenciais de Key Vault são usadas somente dentro de seu aplicativo. Seu aplicativo é autenticado diretamente para Key Vault com essas credenciais. Eles nunca são passados para o serviço de configuração do aplicativo.

## <a name="update-your-code-to-use-a-key-vault-reference"></a>Atualizar seu código para usar uma referência de Key Vault

1. Abra *MessageProperties. java*. Adicione uma nova variável chamada *keyVaultMessage*:

    ```java
    private String keyVaultMessage;

    public String getKeyVaultMessage() {
        return keyVaultMessage;
    }

    public void setKeyVaultMessage(String keyVaultMessage) {
        this.keyVaultMessage = keyVaultMessage;
    }
    ```

1. Abra *HelloController. java*. Atualize o método *GetMessage* para incluir a mensagem recuperada de Key Vault.

    ```java
    @GetMapping
    public String getMessage() {
        return "Message: " + properties.getMessage() + "\nKey Vault message: " + properties.getKeyVaultMessage();
    }
    ```

1. Crie seu aplicativo Spring boot com o Maven e execute-o, por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
1. Depois que o aplicativo estiver em execução, use a *rotação* para testar seu aplicativo, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Você verá a mensagem inserida no repositório de configuração de aplicativo. Você também verá a mensagem que você inseriu em Key Vault.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou uma chave de configuração de aplicativo que faz referência a um valor armazenado em Key Vault. Para saber como usar os sinalizadores de recurso em seu aplicativo Java Spring, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./quickstart-feature-flag-spring-boot.md)
