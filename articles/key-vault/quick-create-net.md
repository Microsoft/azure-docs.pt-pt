---
title: Início rápido-biblioteca de cliente Azure Key Vault para .NET
description: Fornece critérios de formato e conteúdo para escrever guias de início rápido para bibliotecas de cliente do SDK do Azure.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 05/20/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 8bc1e4d5eae76796e82195b4ef34ddefc54302b9
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910330"
---
# <a name="quickstart-azure-key-vault-client-library-for-net"></a>Início rápido: Biblioteca de cliente Azure Key Vault para .NET

Introdução à biblioteca de cliente do Azure Key Vault para .NET. Siga as etapas abaixo para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Use a biblioteca de cliente do Key Vault para .NET para:

- Aumente a segurança e o controle sobre chaves e senhas.
- Crie e importe chaves de criptografia em minutos.
- Reduza a latência com escala de nuvem e redundância global.
- Simplifique e automatize tarefas para certificados SSL/TLS.
- Use os HSMs validados pelo FIPS 140-2 nível 2.

[](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | [Código](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/KeyVault)[](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/) -fonte da biblioteca de documentação de referência de API (NuGet) | 


## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [SDK do .NET Core 2,1 ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este início rápido pressupõe que você `dotnet`esteja executando, [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)e comandos do Windows em um terminal do Windows (como [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6)ou o [Azure cloud Shell](https://shell.azure.com/)).

## <a name="setting-up"></a>Configurando

### <a name="create-new-net-console-app"></a>Criar novo aplicativo de console .NET

Crie um novo aplicativo .NET Core em seu editor ou IDE preferido.

Em uma janela de console, use `dotnet new` o comando para criar um novo aplicativo de console com `akv-dotnet`o nome.


```console
dotnet new console -n akvdotnet
```

Altere o diretório para a pasta de aplicativos recém-criada. Você pode criar o aplicativo com:

```console
dotnet build
```

A saída da compilação não deve conter nenhum aviso ou erro.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Instalar o pacote

Na janela do console, instale a biblioteca de cliente do Azure Key Vault para .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Para este guia de início rápido, você precisará instalar os seguintes pacotes também:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

Este guia de início rápido usa um cofre de chaves do Azure criado previamente. Você pode criar um cofre de chaves seguindo as etapas na guia de início rápido [CLI do Azure](quick-create-cli.md), guia de início rápido [Azure PowerShell](quick-create-powershell.md)ou [portal do Azure início rápido](quick-create-portal.md). Como alternativa, você pode simplesmente executar os comandos de CLI do Azure abaixo.

> [!Important]
> Cada Key Vault deve ter um nome exclusivo. O exemplo a seguir cria um Key Vault chamado *myKV*, mas você deve nomear algo diferente e usar esse nome em todo este guia de início rápido.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A maneira mais simples de autenticar um aplicativo .NET baseado em nuvem é com uma identidade gerenciada; consulte [autenticação de serviço a serviço para Azure Key Vault usando .net](service-to-service-authentication.md) para obter detalhes. Para simplificar, no entanto, este guia de início rápido cria um aplicativo de console .NET. A autenticação de um aplicativo de área de trabalho com o Azure requer o uso de uma entidade de serviço.
Crie uma entidade de serviço usando o comando CLI do Azure [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) :

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Essa operação retornará uma série de pares de chave/valor. 

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

Anote o clientId, clientSecret, SubscriptionId e tenantid, pois usaremos-os na etapa [autenticar para o cofre de chaves](#authenticate-to-your-key-vault) abaixo.

Você também precisará da appID da entidade de serviço. Você pode encontrá-lo executando [AZ ad SP List](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) com o `--show-mine` parâmetro:

```azurecli
az ad sp list --show-mine
```

O `appID` é exibido no JSON retornado:

```json
    "appId": "2cf5aa18-0100-445a-9438-0b93e577a3ed",
```

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que concede permissão para sua entidade de serviço. Você faz isso com o comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Vamos dar à entidade de serviço Get, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <appid-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de cliente do Azure Key Vault para .NET permite que você gerencie chaves e ativos relacionados, como certificados e segredos. Os exemplos de código abaixo mostrarão como definir um segredo e recuperar um segredo.

Todo o aplicativo de console está disponível https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet em.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior do seu código:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Autenticar no cofre de chaves

Este início rápido do .NET depende de variáveis de ambiente para armazenar credenciais que não devem ser colocadas no código. 

Antes de compilar e executar seu aplicativo, use o `setx` comando para definir as `akvClientId`variáveis `akvClientSecret`de `akvTenantId`ambiente, `akvSubscriptionId` , e para os valores que você anotou acima.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>

setx akvTenantId <your-tentantId>

setx akvSubscriptionId <your-subscriptionId>
````

Sempre que você chamar `setx`, deverá obter uma resposta de "êxito: O valor especificado foi salvo. "

Atribua essas variáveis de ambiente a cadeias de caracteres em seu código e, em seguida, autentique seu aplicativo passando-as para a [classe KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient):

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que seu aplicativo está autenticado, você pode colocar um segredo em seu keyvault usando o [método SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) isso requer a URL do cofre de chaves, que está no formato `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Ele também requer um nome para o segredo – estamos usando "MySecret".  Talvez você queira atribuir essas cadeias de caracteres a variáveis para resue.

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Você pode verificar se o segredo foi definido com o comando [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora você pode recuperar o valor definido anteriormente com o [método GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Seu segredo agora está salvo como `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o CLI do Azure ou Azure PowerShell para remover o cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup" -l "EastUS" 
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Consulte [todo o aplicativo de console no GitHub](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet).

Para saber mais sobre Key Vault e como integrá-lo a seus aplicativos, continue nos artigos abaixo.

- Implementar [a autenticação de serviço a serviço para Azure Key Vault usando o .net](service-to-service-authentication.md)
- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Consulte o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)
