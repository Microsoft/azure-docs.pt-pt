---
title: Quickstart - Biblioteca de clientes Azure Key Vault para .NET (SDK v3)
description: Saiba como criar, recuperar e apagar segredos de um cofre de chaves Azure utilizando a biblioteca de clientes .NET (v3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.topic: quickstart
ms.openlocfilehash: 9b92796e477ea0dd6795015edd3f400dd2cc9aa7
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773748"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Quickstart: Biblioteca de clientes Azure Key Vault para .NET (SDK v3)

Começa com a biblioteca de clientes azure Key Vault para .NET. Siga as etapas abaixo para instalar o pacote e experimentar o código de exemplo para tarefas básicas.

> [!NOTE]
> Este quickstart utiliza a versão v3.0.4 da biblioteca de clientes Microsoft.Azure.KeyVault. Para utilizar a versão mais atualizada da biblioteca de clientes Key Vault, consulte a [biblioteca de clientes Azure Key Vault para .NET (SDK v4)](quick-create-net.md). 

O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Utilize a biblioteca de clientes Key Vault para .NET para:

- Aumente a segurança e o controle sobre chaves e senhas.
- Crie e importe chaves de criptografia em minutos.
- Reduza a latência com escala de nuvem e redundância global.
- Simplificar e automatizar tarefas para certificados TLS/SSL.
- Use os HSMs validados pelo FIPS 140-2 nível 2.

[Documentação de referência da API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) | Pacote fonte de código | da [Biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) [(NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua < seu-Unique-keyvault-Name > pelo nome do seu cofre de chaves nos exemplos a seguir.


## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure- [crie uma gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* O [Núcleo .NET 2.1 SDK ou posterior](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

Este quickstart pressupõe que está a executar `dotnet`, [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), e comandos Windows num terminal Windows (como [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-6), [Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6), ou o [Azure Cloud Shell).](https://shell.azure.com/)

## <a name="setting-up"></a>Configurando

### <a name="create-new-net-console-app"></a>Criar uma nova aplicação de consola .NET

Numa janela de consola, utilize o comando `dotnet new` para criar uma nova aplicação de consola .NET com o nome `akv-dotnet`.


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

A partir da janela da consola, instale a biblioteca de clientes Azure Key Vault para .NET:

```console
dotnet add package Microsoft.Azure.KeyVault
```

Para este arranque rápido, terá de instalar também os seguintes pacotes:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e um cofre de chaves

Este guia de início rápido usa um cofre de chaves do Azure criado previamente. Você pode criar um cofre de chaves seguindo as etapas na guia de início rápido [CLI do Azure](quick-create-cli.md), guia de início rápido [Azure PowerShell](quick-create-powershell.md)ou [portal do Azure início rápido](quick-create-portal.md). Em alternativa, pode simplesmente executar os comandos Azure CLI abaixo.

> [!Important]
> Cada cofre de chaves deve ter um nome exclusivo. Substitua < seu-Unique-keyvault-Name > pelo nome do seu cofre de chaves nos exemplos a seguir.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Criar um principal de serviço

A maneira mais simples de autenticar um aplicativo .NET baseado em nuvem é com uma identidade gerenciada; consulte [usar uma identidade gerenciada do serviço de aplicativo para acessar Azure Key Vault](managed-identity.md) para obter detalhes. Para simplificar, no entanto, este guia de início rápido cria um aplicativo de console .NET. A autenticação de um aplicativo de área de trabalho com o Azure requer o uso de uma entidade de serviço e uma política de controle de acesso.

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

Tome nota do clienteId e clienteSecret, pois vamos usá-los no [Authenticate para](#authenticate-to-your-key-vault) o seu passo de cofre chave abaixo.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Fornecer acesso à entidade de serviço ao cofre de chaves

Crie uma política de acesso para o cofre de chaves que concede permissão para sua entidade de serviço passando o clientId para o comando [AZ keyvault Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) . Conceda à entidade de serviço obter, listar e definir permissões para chaves e segredos.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --secret-permissions delete get list set --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

## <a name="object-model"></a>Modelo de objeto

A biblioteca de clientes Azure Key Vault para .NET permite-lhe gerir chaves e bens relacionados, tais como certificados e segredos. As amostras de código abaixo mostrar-lhe-ão como definir um segredo e recuperar um segredo.

Toda a aplicação de consola está disponível em https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet.

## <a name="code-examples"></a>Exemplos de código

### <a name="add-directives"></a>Adicionar diretivas

Adicione as seguintes diretivas à parte superior do seu código:

[!code-csharp[Directives](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=directives)]

### <a name="authenticate-to-your-key-vault"></a>Autenticar o seu cofre chave

Este arranque rápido .NET baseia-se em variáveis ambientais para armazenar credenciais que não devem ser colocadas em código. 

Antes de construir e executar a sua aplicação, use o comando `setx` para definir as variáveis `akvClientId`, `akvClientSecret`, `akvTenantId`e `akvSubscriptionId` ambiente para os valores acima referidos.

```console
setx akvClientId <your-clientID>

setx akvClientSecret <your-clientSecret>
````

Cada vez que chama `setx`, deve obter uma resposta de "SUCESSO: Valor especificado foi guardado".

Atribuir estas variáveis ambientais a cordas no seu código e, em seguida, autenticar a sua aplicação passando-as para a [classe KeyVaultClient:](/dotnet/api/microsoft.azure.keyvault.keyvaultclient)

[!code-csharp[Authentication](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=authentication)]

### <a name="save-a-secret"></a>Salvar um segredo

Agora que a sua aplicação é autenticada, pode colocar um segredo no seu cofre utilizando o [método SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) Isto requer o URL do seu cofre chave, que está na forma `https://<your-unique-keyvault-name>.vault.azure.net/secrets/`. Também requer um nome para o segredo. 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Você pode verificar se o segredo foi definido com o comando [AZ keyvault segredo show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Recuperar um segredo

Agora pode recuperar o valor previamente definido com o [método GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

O teu segredo está agora guardado como `keyvaultSecret.Value;`.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o CLI do Azure ou Azure PowerShell para remover o cofre de chaves e o grupo de recursos correspondente.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, você criou um cofre de chaves, armazenou um segredo e recuperou esse segredo. Veja toda a aplicação de [consola no GitHub.](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/akvdotnet)

Para saber mais sobre Key Vault e como integrá-lo a seus aplicativos, continue nos artigos abaixo.

- Implementar [a autenticação serviço-a-serviço para o Cofre chave Azure usando .NET](service-to-service-authentication.md)
- Leia uma [visão geral do Azure Key Vault](key-vault-overview.md)
- Consulte o [Guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)
- Saiba mais sobre [chaves, segredos e certificados](about-keys-secrets-and-certificates.md)
- Examinar [Azure Key Vault práticas recomendadas](key-vault-best-practices.md)
