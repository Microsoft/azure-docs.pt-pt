---
title: Tutorial - Use O Cofre chave Azure com um webapp Azure em .NET [ Microsoft Docs
description: Neste tutorial, configura uma aplicação ASP.NET central para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.openlocfilehash: 962b738d7e5f91076d17c19daad01f8dbaf92341
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82888825"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Tutorial: Use uma identidade gerida para ligar o Cofre chave a uma Web App Azure com .NET

O Azure Key Vault fornece uma forma de armazenar credenciais e outros segredos de forma segura, mas o seu código precisa de autenticar o Cofre Chave para as recuperar. [Identidades geridas para](../../active-directory/managed-identities-azure-resources/overview.md) a visão geral dos recursos do Azure ajudam a resolver este problema, conferindo aos serviços Azure uma identidade gerida automaticamente em Azure AD. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação DaAzure AD, incluindo o Key Vault, sem ter de apresentar credenciais no seu código.

Este tutorial usa uma identidade gerida para autenticar uma Web App Azure com um Cofre chave Azure. Embora os passos utilizem a biblioteca de [clientes Azure Key Vault v4 para .NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) e o [Azure CLI,](/cli/azure/get-started-with-azure-cli)aplicam-se os mesmos princípios básicos quando se utilizam a linguagem de desenvolvimento à sua escolha, a Azure PowerShell, e/ou o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* Uma subscrição Azure - [crie uma gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* O [Núcleo .NET 3.1 SDK ou posterior](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ou [Azure PowerShell](/powershell/azure/overview)

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. Crie um grupo de recursos para alojar tanto o seu cofre chave como a sua aplicação web com o [grupo Az criar](/cli/azure/group?view=azure-cli-latest#az-group-create) comando:

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>Instale o seu cofre chave

Agora vai criar um cofre chave e colocar um segredo nele, para ser usado mais tarde neste tutorial.

Para criar um cofre chave, use o [cofre az criar](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) comando:

> [!Important]
> Cada cofre deve ter um nome único. Substitua <seu nome de cofre> com o nome do seu cofre nos seguintes exemplos.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

Tome nota do `vaultUri`devolvido , que estará no formato"https://<seu nome de cofre>.vault.azure.net/". Será utilizado na [atualização da](#update-the-code) etapa de código.

Agora pode colocar um segredo no seu cofre com o comando secreto do [cofre az keyvault.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) Deseme o nome do seu segredo para "MySecret" e o valor para "Sucesso!".

```azurecli-interactive
az keyvault secret set --vault-name "<your-keyvault-name>" --name "MySecret" --value "Success!"
```

## <a name="create-a-net-web-app"></a>Criar uma aplicação Web em .NET

### <a name="create-a-local-app"></a>Criar uma aplicação local

Numa janela de terminal no seu computador, crie um diretório com o nome `akvwebapp` e altere o diretório atual para o mesmo.

```bash
mkdir akvwebapp
cd akvwebapp
```

Agora crie uma nova aplicação .NET Core com o novo comando [web do dotnet:](/dotnet/core/tools/dotnet-new)

```bash
dotnet new web
```

Execute a aplicação localmente, para ver que aspeto deveria ter quando a implemente no Azure. 

```bash
dotnet run
```

Abra um browser e navegue para a aplicação em `http://localhost:5000`.

Verá a mensagem **Hello World** da aplicação de amostras exibida na página.

### <a name="initialize-the-git-repository"></a>Inicializar o repositório Git

Na janela do terminal, prima **Ctrl+C** para sair do servidor Web.  Inicialize um repositório de Git para o projeto .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>Configurar um utilizador de implementação

FtP e Git local podem implantar-se numa aplicação web Azure utilizando um utilizador de *implementação*. Assim que configurar o utilizador de implementação, pode utilizá-lo para todas as suas implementações Azure. O seu nome de utilizador de implementação de nível de conta e palavra-passe são diferentes das credenciais de subscrição do Azure. 

Para configurar o utilizador de implementação, execute o comando de conjunto de utilizadores de [implementação az webapp.](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) Escolha um nome de utilizador e uma senha que adere a estas diretrizes: 

- O nome de utilizador deve ser único dentro do Azure, e para os pushs git locais, não deve conter o símbolo @. 
- A palavra-passe deve ter pelo menos oito caracteres de comprimento, com dois dos seguintes três elementos: letras, números e símbolos. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

A saída JSON mostra `null`a palavra-passe como . Se obtiver o erro `'Conflict'. Details: 409`, altere o nome de utilizador. Se obtiver o `'Bad Request'. Details: 400` erro, utilize uma palavra-passe mais forte. 

Grave o seu nome de utilizador e palavra-passe para utilizar para implementar as suas aplicações web.

### <a name="create-an-app-service-plan"></a>Criar um plano do serviço de aplicações

Crie um plano de Serviço de Aplicações com o plano de appservice Azure CLI [az criar](/cli/azure/appservice/plan?view=azure-cli-latest) comando. Este exemplo seguinte cria um `myAppServicePlan` plano de Serviço de Aplicações nomeado no nível de preços **gratuitos:**

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o plano do Serviço de Aplicações tiver sido criado, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>Criar uma aplicação web remota

Crie uma [aplicação web Azure](../../app-service/containers/app-service-linux-intro.md) no plano de `myAppServicePlan` Serviço de Aplicações. 

> [!Important]
> Semelhante ao Key Vault, uma Web App Azure deve ter um nome único. Substitua \<o nome\> da sua webapp com o nome da sua aplicação web os seguintes exemplos.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Quando a aplicação Web tiver sido criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


O URL do Git remoto é apresentado na propriedade `deploymentLocalGitUrl`, com o formato `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Guarde este URL, como precisa mais tarde.

Navegue na sua aplicação recém-criada. Substitua o _ &lt;seu nome webapp>_ pelo nome da sua aplicação.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Verá a página web padrão para uma web app recém-criada.

### <a name="deploy-your-local-app"></a>Implemente a sua aplicação local

De volta à janela do terminal local, adicione um telecomando Azure ao seu repositório git local, substituindo * \<a implementaçãoLocalGitUrl-from-create-step>* com o URL do comando Git que guardou de Criar um passo remoto da [aplicação web.](#create-a-remote-web-app)

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando o Git Credential Manager lhe pedir credenciais, use as credenciais que criou na Configuração de um passo de utilizador de [implementação.](#configure-a-deployment-user)

```bash
git push azure master
```

Este comando pode demorar alguns minutos a ser executado. Ao executar, apresenta informações semelhantes ao exemplo seguinte:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

Navegue para (ou atualizar) a aplicação implementada usando o seu navegador web.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Vais ver o "Olá Mundo!" mensagem que viu anteriormente `http://localhost:5000`ao visitar.

## <a name="create-and-assign-a-managed-identity"></a>Criar e atribuir uma identidade gerida

No Azure CLI, para criar a identidade para esta aplicação, executar o comando de [atribuição de identidade webapp az:](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign)

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

A operação devolverá este corte JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Para dar permissão à sua aplicação web para **obter** e **listar** operações no seu cofre chave, passe o principalID para o comando de definição de teclado Azure CLI [az:](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>Modifique a aplicação para aceder ao seu cofre chave

### <a name="install-the-packages"></a>Instale as embalagens

A partir da janela do terminal, instale a biblioteca de clientes Azure Key Vault para pacotes .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>Atualizar o código

Encontre e abra o ficheiro Startup.cs no seu projeto akvwebapp. 

Adicione estas duas linhas ao cabeçalho:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adicione estas três `app.UseEndpoints` linhas antes da chamada, `vaultUri` atualizando o URI para refletir o seu cofre chave.

```csharp
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential());

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

Atualizar a `await context.Response.WriteAsync("Hello World!");` linha para ler:

```csharp
await context.Response.WriteAsync(secretValue);
```

Certifique-se de guardar as suas alterações antes de passar ao próximo passo.

### <a name="redeploy-your-web-app"></a>Reimplementar a sua aplicação web

Tendo atualizado o seu código, pode reimplantá-lo para Azure com os seguintes comandos Git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>Visite a sua aplicação completa na Web

```bash
http://<your-webapp-name>.azurewebsites.net
```

Onde antes de ver **hello World,** você deve agora ver o valor do seu segredo exibido: **Sucesso!**

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Saiba mais sobre [identidades geridas para o Serviço](../../app-service/overview-managed-identity.md?tabs=dotnet) de Aplicações
- Consulte a [biblioteca de clientes Azure Key Vault para referência a .NET API](/dotnet/api/overview/azure/key-vault?view=azure-dotnet)
- Consulte a [biblioteca de clientes Azure Key Vault para obter o código fonte .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)
- Consulte a [biblioteca de clientes v4 Azure Key Vault para o pacote .NET NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)


