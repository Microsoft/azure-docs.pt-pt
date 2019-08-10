---
title: Tutorial-usar Azure Key Vault com um aplicativo Web do Azure no .NET | Microsoft Docs
description: Neste tutorial, você configura um aplicativo ASP.NET Core para ler um segredo do cofre de chaves.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: c27c787eeac9bbf68b512b55b9ceab11074a81d8
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934362"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutorial: Usar Azure Key Vault com um aplicativo Web do Azure no .NET

Azure Key Vault ajuda a proteger segredos como chaves de API e cadeias de conexão de banco de dados. Ele fornece acesso a seus aplicativos, serviços e recursos de ti.

Neste tutorial, você aprenderá a criar um aplicativo Web do Azure que pode ler informações de um cofre de chaves do Azure. O processo usa identidades gerenciadas para recursos do Azure. Para obter mais informações sobre os aplicativos Web do Azure, consulte [Azure app Service](../app-service/overview.md).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre de chaves.
> * Obter um segredo do cofre de chaves.
> * Crie um aplicativo Web do Azure.
> * Habilite uma identidade gerenciada para o aplicativo Web.
> * Atribuir permissão para o aplicativo Web.
> * Execute o aplicativo Web no Azure.

Antes de começar, leia [Key Vault conceitos básicos](key-vault-whatis.md#basic-concepts). 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

* Para Windows: [SDK do .NET Core 2,1 ou posterior](https://www.microsoft.com/net/download/windows)
* Para Mac: [Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/)
* Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você execute o CLI do Azure localmente. Você deve ter o CLI do Azure versão 2.0.4 ou posterior instalado. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena as credenciais com segurança, portanto, elas não são exibidas no seu código. No entanto, você precisa autenticar-se no Azure Key Vault para recuperar suas chaves. Para autenticar no Key Vault, você precisa de uma credencial. É um dilema de Bootstrap clássico. O Identidade de Serviço Gerenciada (MSI) resolve esse problema fornecendo uma _identidade de Bootstrap_ que simplifica o processo.

Quando você habilita o MSI para um serviço do Azure, como máquinas virtuais do Azure, serviço Azure App ou Azure Functions, o Azure cria uma [entidade de serviço](key-vault-whatis.md#basic-concepts). O MSI faz isso para a instância do serviço no Azure Active Directory (Azure AD) e injeta as credenciais da entidade de serviço nessa instância.

![Diagrama MSI](media/MSI.png)

Em seguida, para obter um token de acesso, seu código chama um serviço de metadados local que está disponível no recurso do Azure. Seu código usa o token de acesso que obtém do ponto de extremidade MSI local para autenticar em um serviço de Azure Key Vault.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create).

Em seguida, selecione um nome de grupo de recursos e preencha o espaço reservado. O exemplo seguinte cria um grupo de recursos na localização E.U.A Oeste:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Você usa este grupo de recursos ao longo deste tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para criar um cofre de chaves em seu grupo de recursos, forneça as seguintes informações:

* Nome do cofre de chaves: uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hifens (-)
* Nome do grupo de recursos
* Localização: **E.U.A. Oeste**

Na CLI do Azure, digite o seguinte comando:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Neste ponto, sua conta do Azure é a única que está autorizada a executar operações nesse novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora você pode adicionar um segredo. Pode ser uma cadeia de conexão SQL ou qualquer outra informação que você precise manter segura e disponível para seu aplicativo.

Para criar um segredo no cofre de chaves chamado **AppSecret**, insira o seguinte comando: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Esse segredo armazena o valor **MySecret**.

Para exibir o valor contido no segredo como texto sem formatação, digite o seguinte comando:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Esse comando exibe as informações secretas, incluindo o URI. 

Depois de concluir estes passos, deve ter um URI para um segredo num cofre de chaves. Anote essas informações para uso posterior neste tutorial. 

## <a name="create-a-net-core-web-app"></a>Criar um aplicativo Web .NET Core

Para criar um aplicativo Web do .NET Core e publicá-lo no Azure, siga as instruções em [criar um aplicativo web ASP.NET Core no Azure](../app-service/app-service-web-get-started-dotnet.md). 

Também pode ver este vídeo:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

1. Vá para o arquivo **pages** > **about.cshtml.cs** .

1. Instale estes pacotes NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importe o seguinte código para o arquivo *about.cshtml.cs* :

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Seu código na classe AboutModel deve ser assim:

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

1. No menu principal do Visual Studio 2019, selecione **depurar** > **Iniciar**, com ou sem depuração. 
1. No navegador, vá para a página **sobre** .  
    O valor para o **AppSecret** é apresentado.

## <a name="enable-a-managed-identity"></a>Habilitar uma identidade gerenciada

O Azure Key Vault fornece uma maneira de armazenar credenciais e outros segredos com segurança, mas seu código precisa autenticar-se no Key Vault para recuperá-los. [Identidades gerenciadas para recursos do Azure visão geral](../active-directory/managed-identities-azure-resources/overview.md) ajuda a resolver esse problema fornecendo aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD. Você pode usar essa identidade para autenticar em qualquer serviço que ofereça suporte à autenticação do Azure AD, incluindo Key Vault, sem precisar exibir credenciais em seu código.

No CLI do Azure, para criar a identidade para este aplicativo, execute o comando assign-Identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Substitua \<nomedoseuaplicativo > pelo nome do aplicativo publicado no Azure.  
    Por exemplo, se o nome do aplicativo publicado foi **MyAwesomeapp.azurewebsites.net**, \<substitua nomedoseuaplicativo > por **MyAwesomeapp**.

Anote o `PrincipalId` ao publicar o aplicativo no Azure. A saída do comando na etapa 1 deve estar no seguinte formato:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>O comando neste procedimento é o equivalente de ir para a [portal do Azure](https://portal.azure.com) e alternar a configuração de **Identity/System atribuída** para **on** nas propriedades do aplicativo Web.

## <a name="assign-permissions-to-your-app"></a>Atribuir permissões ao seu aplicativo

Substitua \<YourKeyVaultName > pelo nome do cofre de chaves e substitua \<o > PrincipalId pelo valor da **entidade de segurança** no seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Esse comando fornece a identidade (MSI) da permissão do serviço de aplicativo para realizar operações **Get** e **list** no cofre de chaves.

## <a name="publish-the-web-app-to-azure"></a>Publicar a aplicação Web no Azure

Publique seu aplicativo Web no Azure novamente para verificar se o seu aplicativo Web em tempo real pode buscar o valor secreto.

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Selecione **Criar**.

Ao executar o aplicativo, você verá que ele pode recuperar o valor secreto.

Agora, você criou com êxito um aplicativo Web no .NET que armazena e busca seus segredos do cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos
Quando eles não forem mais necessários, você poderá excluir a máquina virtual e o cofre de chaves.

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
>[Guia do Programador do Azure Key Vault](key-vault-developers-guide.md)
