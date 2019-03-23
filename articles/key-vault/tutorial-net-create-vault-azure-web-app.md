---
title: Tutorial – utilizar o Azure Key Vault com uma aplicação web do Azure em .NET | Documentos da Microsoft
description: Neste tutorial, irá configurar uma aplicação ASP.NET core para ler um segredo a partir do seu Cofre de chaves.
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: rajvijan
ms.assetid: 0e57f5c7-6f5a-46b7-a18a-043da8ca0d83
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 2c2bb3e4064294bb8d4a63b009069fd6834ca31e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370895"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutorial: Utilizar o Azure Key Vault com uma aplicação web do Azure no .NET

O Azure Key Vault ajuda a proteger segredos, como chaves de API e as cadeias de ligação de base de dados. Ele fornece acesso às suas aplicações, serviços e recursos de TI.

Neste tutorial, saiba como criar uma aplicação web do Azure que pode ler as informações de um cofre de chave do Azure. O processo utiliza identidades geridas para recursos do Azure. Para obter mais informações sobre aplicações web do Azure, consulte [App Service do Azure](../app-service/overview.md).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao Cofre de chaves.
> * Obter um segredo do cofre de chaves.
> * Crie uma aplicação web do Azure.
> * Ative uma identidade gerida para a aplicação web.
> * Atribua a permissão da aplicação web.
> * Execute a aplicação web no Azure.

Antes de começar, leia [conceitos básicos do Key Vault](key-vault-whatis.md#basic-concepts). 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

* Para Windows: [SDK para .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)
* Para Mac: [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/)
* Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que execute a CLI do Azure localmente. Tem de ter a CLI do Azure versão 2.0.4 ou posterior instalado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://review.docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena as credenciais em segurança, pelo que não são apresentados no seu código. No entanto, tem de autenticar para o Azure Key Vault para recuperar as suas chaves. Para autenticar para o Key Vault, precisa de uma credencial. É um dilema bootstrap clássico. Identidade de serviço gerida (MSI) resolve este problema, fornecendo uma _inicializar identidade_ que simplifica o processo.

Quando ativar o MSI para um serviço do Azure, como máquinas virtuais do Azure, serviço de aplicações do Azure ou as funções do Azure, o Azure cria um [principal de serviço](key-vault-whatis.md#basic-concepts). MSI faz isso para a instância do serviço no Azure Active Directory (Azure AD) e injeta as credenciais do principal de serviço nessa instância.

![Diagrama MSI](media/MSI.png)

Em seguida, para obter um token de acesso, o código chama um serviço de metadados local que está disponível no recurso do Azure. O código utiliza o token de acesso que obtém a partir do ponto de final MSI local para autenticar a um serviço do Azure Key Vault.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create).

Em seguida, selecione um nome de grupo de recursos e preencha o marcador de posição. O exemplo seguinte cria um grupo de recursos na localização E.U.A Oeste:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Utilize este grupo de recursos ao longo deste tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para criar um cofre de chaves no seu grupo de recursos, forneça as seguintes informações:

* Nome do Cofre de chaves: uma cadeia de caracteres de 3 a 24, que pode conter apenas números (0-9), as letras (a-z, A-Z) e hífenes (-)
* Nome do grupo de recursos
* Localização: **E.U.A. Oeste**

Na CLI do Azure, introduza o seguinte comando:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Neste momento, a sua conta do Azure é o único que tem autorização para executar operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora pode adicionar um segredo. Pode ser uma cadeia de ligação de SQL ou outras informações que precisa para manter seguros e disponíveis para a aplicação.

Para criar um segredo no Cofre de chaves chamado **AppSecret**, introduza o seguinte comando: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret**.

Para ver o valor que está contido no segredo como texto sem formatação, introduza o seguinte comando:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Este comando apresenta as informações secretas, incluindo o URI. 

Depois de concluir estes passos, deve ter um URI para um segredo num cofre de chaves. Tome nota destas informações para utilizar mais tarde neste tutorial. 

## <a name="create-a-net-core-web-app"></a>Criar uma aplicação web .NET Core

Para criar uma aplicação web .NET Core e publicá-lo para o Azure, siga as instruções em [criar uma aplicação web ASP.NET Core no Azure](../app-service/app-service-web-get-started-dotnet.md). 

Também pode ver este vídeo:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

1. Vá para o **páginas** > **About.cshtml.cs** ficheiro.

1. Instale estes pacotes de NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importar o seguinte código para o *About.cshtml.cs* ficheiro:

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   O código na classe AboutModel deve ter este aspeto:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Executar a aplicação Web

1. No menu principal do Visual Studio 2017, selecione **depurar** > **iniciar**, com ou sem depuração. 
1. No browser, vá para o **sobre** página.  
    O valor para o **AppSecret** é apresentado.

## <a name="enable-a-managed-identity"></a>Ativar uma identidade gerida

O Azure Key Vault oferece uma forma de armazenar em segurança as credenciais e outros segredos, mas o seu código precisa para autenticar para o Key Vault para recuperá-los. [Gerido identidades para descrição geral de recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) ajuda a resolver esse problema fornecendo Azure dos serviços de uma identidade gerida automaticamente no Azure AD. Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, incluindo o Cofre de chaves, sem ter de apresentar as credenciais em seu código.

Na CLI do Azure, para criar a identidade para esta aplicação, execute o comando de atribuir-identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Substitua \<NomeAplic > com o nome da aplicação publicada no Azure.  
    Por exemplo, se o nome da sua aplicação publicada foi **MyAwesomeapp.azurewebsites.net**, substitua \<NomeAplic > com **MyAwesomeapp**.

Anote o `PrincipalId` ao publicar a aplicação no Azure. A saída do comando no passo 1 deve estar no seguinte formato:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>O comando neste procedimento é o equivalente do passa para o [portal do Azure](https://portal.azure.com) e alternar o **identidade / sistema atribuída** na definição de **no** na aplicação web Propriedades.

## <a name="assign-permissions-to-your-app"></a>Atribuir permissões à sua aplicação

Substitua \<YourKeyVaultName > com o nome do seu Cofre de chaves e substitua \<PrincipalId > com o valor da **PrincipalId** no comando seguinte:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Este comando apresenta o identity (MSI) de permissão de serviço à aplicação para fazer **Obtenha** e **lista** operações no seu Cofre de chaves.

## <a name="publish-the-web-app-to-azure"></a>Publicar a aplicação Web no Azure

Publicar a aplicação web para o Azure novamente para verificar que a sua aplicação web em direto pode obter o valor secreto.

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Selecione **Criar**.

Quando executar o aplicativo, verá que este possa obter seu valor secreto.

Agora, já criado com êxito uma aplicação web no .NET que armazena e obtém os respetivos segredos do seu Cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não são necessários, pode eliminar a máquina virtual e o seu Cofre de chaves.

## <a name="next-steps"></a>Passos Seguintes

>[!div class="nextstepaction"]
>[Guia do Programador do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-developers-guide)
