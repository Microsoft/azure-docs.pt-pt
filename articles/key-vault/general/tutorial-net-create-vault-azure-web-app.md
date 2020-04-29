---
title: Tutorial - Use O Cofre de Chaves Azure com uma aplicação web Azure em .NET [ Microsoft Docs
description: Neste tutorial, configura uma aplicação ASP.NET central para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: bb907c809d411128ee799d5057379a2022144882
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422880"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Tutorial: Use o Cofre chave Azure com uma aplicação web Azure em .NET

O Azure Key Vault ajuda-o a proteger segredos como chaves API e cordas de ligação de base de dados. Fornece-lhe acesso às suas aplicações, serviços e recursos de TI.

Neste tutorial, você aprende a criar uma aplicação web Azure que pode ler informações a partir de um cofre chave Azure. O processo utiliza identidades geridas para recursos Azure. Para mais informações sobre aplicações web Azure, consulte [o Azure App Service](../../app-service/overview.md).

Este tutorial mostra-lhe como:

> [!div class="checklist"]
> * Criar um cofre de chaves.
> * Adicione um segredo ao cofre da chave.
> * Obter um segredo do cofre de chaves.
> * Crie uma aplicação web Azure.
> * Ativar uma identidade gerida para a aplicação web.
> * Atribuir permissão para a aplicação web.
> * Execute a aplicação web no Azure.

Antes de começar, leia [os conceitos básicos do Key Vault.](basic-concepts.md) 

Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="prerequisites"></a>Pré-requisitos

* Para Windows: [.NET Core 2.1 SDK ou posterior](https://www.microsoft.com/net/download/windows)
* For Mac: [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)
* Para Windows, Mac e Linux:
  * [Git](https://git-scm.com/downloads)
  * Este tutorial requer que você dirija o Azure CLI localmente. Tem de ter a versão 2.0.4 do Azure CLI ou posteriormente instalada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Sobre a Identidade de Serviço Gerida

O Azure Key Vault armazena credenciais de forma segura, por isso não são exibidos no seu código. No entanto, precisa autenticar o Cofre de Chaves Azure para recuperar as suas chaves. Para autenticar o Cofre chave, precisa de uma credencial. É um dilema clássico de botas. A Identidade de Serviço Gerida (MSI) resolve este problema fornecendo uma identidade de _bootstrap_ que simplifica o processo.

Quando ativa o MSI para um serviço Azure, como máquinas virtuais Azure, Serviço de Aplicações Azure ou Funções Azure, o Azure cria um diretor de [serviço.](basic-concepts.md) A MSI faz isto por exemplo do serviço no Azure Ative Directory (Azure AD) e injeta as credenciais principais do serviço nesse caso.

![Diagrama de MSI](../media/MSI.png)

Em seguida, para obter um sinal de acesso, o seu código chama um serviço de metadados local que está disponível no recurso Azure. O seu código usa o sinal de acesso que obtém do ponto final local da MSI para autenticar um serviço Azure Key Vault.

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

Você usa este grupo de recursos em todo este tutorial.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

Para criar um cofre chave no seu grupo de recursos, forneça as seguintes informações:

* Nome do cofre chave: uma cadeia de 3 a 24 caracteres que pode conter apenas números (0-9), letras (a-z, A-Z) e hífenes (-)
* Nome do grupo de recursos
* Localização: **Oeste DOS EUA**

No Azure CLI, insira o seguinte comando:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Neste momento, a sua conta Azure é a única que está autorizada a realizar operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Agora pode acrescentar um segredo. Pode ser uma cadeia de ligação SQL ou qualquer outra informação que você precisa para manter ambos seguros e disponíveis para a sua aplicação.

Para criar um segredo no cofre de chaves chamado **AppSecret,** insira o seguinte comando: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Este segredo armazena o valor **MySecret.**

Para ver o valor contido no segredo como texto simples, insira o seguinte comando:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Este comando exibe as informações secretas, incluindo o URI. 

Depois de concluir estes passos, deve ter um URI para um segredo num cofre de chaves. Tome nota desta informação para posterior utilização neste tutorial. 

## <a name="create-a-net-core-web-app"></a>Criar uma aplicação web .NET Core

Para criar uma aplicação web .NET Core e publicá-la no Azure, siga as instruções em [Criar uma ASP.NET aplicação web Core em Azure.](../../app-service/app-service-web-get-started-dotnet.md) 

Também pode ver este vídeo:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

1. Vá ao arquivo **de páginas** > **About.cshtml.cs.**

1. Instale estes pacotes NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Importar o seguinte código para o ficheiro *About.cshtml.cs:*

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   O seu código na classe AboutModel deve ser assim:

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

1. No menu principal do Visual Studio 2019, selecione **Debug** > **Start,** com ou sem depuração. 
1. No navegador, vá à página **Sobre.**  
    O valor para o **AppSecret** é apresentado.

## <a name="enable-a-managed-identity"></a>Ativar uma identidade gerida

O Azure Key Vault fornece uma forma de armazenar credenciais e outros segredos de forma segura, mas o seu código precisa de autenticar o Cofre Chave para as recuperar. [Identidades geridas para](../../active-directory/managed-identities-azure-resources/overview.md) a visão geral dos recursos do Azure ajudam a resolver este problema, conferindo aos serviços Azure uma identidade gerida automaticamente em Azure AD. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaAzure AD, incluindo o Key Vault, sem ter de apresentar credenciais no seu código.

No Azure CLI, para criar a identidade para esta aplicação, executar o comando de atribuição-identidade:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Substitua \<o Nome do Seu Nome de Aplicação> com o nome da aplicação publicada no Azure.  
    Por exemplo, se o **MyAwesomeapp.azurewebsites.net**nome da \<sua aplicação publicada foi MyAwesomeapp.azurewebsites.net , substitua o seu nome de> por **MyAwesomeapp**.

Tome nota `PrincipalId` da publicação da aplicação no Azure. A saída do comando na etapa 1 deve estar no seguinte formato:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>O comando neste procedimento é o equivalente a ir ao [portal Azure](https://portal.azure.com) e a mudar a **definição Identidade/Sistema atribuída** a **On** nas propriedades da aplicação web.

## <a name="assign-permissions-to-your-app"></a>Atribuir permissões à sua aplicação

Substitua \<o Seu Nome-> do YourKeyVaultName com o nome do seu cofre chave e substitua \<o DirectorId> pelo valor do **DirectorId** no seguinte comando:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

Este comando dá a identidade (MSI) do serviço de aplicações para **obter** e **listar** operações no seu cofre chave.

## <a name="publish-the-web-app-to-azure"></a>Publicar a aplicação Web no Azure

Publique a sua aplicação web no Azure mais uma vez para verificar se a sua aplicação ao vivo pode obter o valor secreto.

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. **Selecione Publicar** > **Iniciar**.
3. Selecione **Criar**.

Quando executar a aplicação, deve ver que pode recuperar o seu valor secreto.

Agora, você criou com sucesso uma aplicação web em .NET que armazena e rebusca os seus segredos do seu cofre chave.

## <a name="clean-up-resources"></a>Limpar recursos
Quando já não forem necessários, pode eliminar a máquina virtual e o cofre da chave.

## <a name="next-steps"></a>Passos seguintes

>[!div class="nextstepaction"]
>[Guia do Programador do Azure Key Vault](developers-guide.md)
