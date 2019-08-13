---
title: 'Início rápido: Definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET-Azure Key Vault | Microsoft Docs'
description: Neste guia de início rápido, você define e recupera um segredo de Azure Key Vault usando um aplicativo Web .NET
services: key-vault
author: msmbaldwin
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 01/02/2019
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 9ddb1db9b39ac942a3476f50aad39c98198b2a18
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68958599"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-net-web-app"></a>Início rápido: Definir e recuperar um segredo de Azure Key Vault usando um aplicativo Web .NET

Neste guia de início rápido, você seguirá as etapas para obter um aplicativo Web do Azure para ler informações de Azure Key Vault usando identidades gerenciadas para recursos do Azure. O uso de Key Vault ajuda a manter as informações seguras. Saiba como:

* Criar um cofre de chaves.
* Armazene um segredo no cofre de chaves.
* Obter um segredo do cofre de chaves.
* Criar uma aplicação Web do Azure.
* Ative uma [identidade de serviço gerida](../active-directory/managed-identities-azure-resources/overview.md) para a aplicação Web.
* Conceda as permissões exigidas para a aplicação ler dados do cofre de chaves.

Antes de continuarmos, leia os [conceitos básicos para Key Vault](key-vault-whatis.md#basic-concepts).

>[!NOTE]
>O Key Vault é um repositório central para armazenar segredos de forma programática. Mas para tal, primeiro as aplicações e os utilizadores têm de autenticar para o Key Vault, ou seja, apresentar um segredo. Ao manter as práticas recomendadas de segurança, esse primeiro segredo precisa ser girado periodicamente. 
>
>Com [identidades de serviço gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md), os aplicativos executados no Azure obtêm uma identidade que o Azure gerencia automaticamente. Isto ajuda a resolver o *problema de introdução do segredo*, para que os utilizadores e as aplicações possam seguir as melhores práticas e não precisem de se preocupar sobre trocar o primeiro segredo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

* No Windows:
  * [Visual Studio 2019](https://www.microsoft.com/net/download/windows) com as seguintes cargas de trabalho:
    * Desenvolvimento ASP.NET e Web
    * Desenvolvimento de várias plataformas .NET core
  * [SDK .NET Core 2.1 ou posterior](https://www.microsoft.com/net/download/windows)

* No Mac:
  * Veja [Novidades do Visual Studio para Mac](https://visualstudio.microsoft.com/vs/mac/).

* Todas as plataformas:
  * Git ([transferir](https://git-scm.com/downloads)).
  * Uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
  * [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) versão 2.0.4 ou posterior. Está disponível para Windows, Mac e Linux.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos.

Selecione um nome do grupo de recursos e preencha o marcador de posição.
O exemplo seguinte cria um grupo de recursos na localização E.U.A Leste:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

O grupo de recursos que acabou de criar é utilizado ao longo deste artigo.

## <a name="create-a-key-vault"></a>Criar um cofre de chaves

A seguir, vai criar um cofre de chaves no grupo de recursos criado no passo anterior. Forneça as seguintes informações:

* Nome do cofre de chaves: O nome deve ser uma cadeia de caracteres de 3-24 e deve conter apenas 0-9, a-z, A-Z e um hífen (-).
* Nome do grupo de recursos.
* Localização: **Leste dos EUA**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Nesta altura, a sua conta do Azure é a única autorizada a realizar quaisquer operações neste novo cofre.

## <a name="add-a-secret-to-the-key-vault"></a>Adicionar um segredo ao cofre de chaves

Estamos a adicionar um segredo para ajudar a ilustrar a forma como isto funciona. Pode armazenar uma cadeia de ligação do SQL ou outras informações que precise de manter em segurança, mas mantenha disponível para a sua aplicação.

Escreva os seguintes comandos para criar um segredo no cofre de chaves designado **AppSecret**. Este segredo irá armazenar o valor **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Para ver o valor contido no segredo como texto simples:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Este comando apresenta informações do segredo, incluindo o URI. Depois de concluir estes passos, deve ter um URI para um segredo num cofre de chaves. Tome nota destas informações. Irá precisar deles noutro passo.

## <a name="clone-the-repo"></a>Clonar o repositório

Clone o repositório para fazer uma cópia local onde pode editar a origem. Execute o seguinte comando:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Abrir e editar a solução

Edite o ficheiro program.cs para executar o exemplo com o nome do cofre de chaves específico:

1. Procure a pasta key-vault-dotnet-core-quickstart.
2. Abra o arquivo Key-Vault-dotnet-Core-QuickStart. sln no Visual Studio 2019.
3. Abra o arquivo Program.cs e atualize o espaço reservado *YourKeyVaultName* com o nome do cofre de chaves que você criou anteriormente.

Esta solução utiliza as bibliotecas NuGet [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

## <a name="run-the-app"></a>Executar a aplicação

No menu principal do Visual Studio 2019, selecione **depurar** > **Iniciar sem depuração**. Quando o browser é apresentado, aceda à página **Sobre**. O valor para o **AppSecret** é apresentado.

## <a name="publish-the-web-application-to-azure"></a>Publicar a aplicação Web no Azure

Publique esta aplicação no Azure para vê-la em direto como uma aplicação Web e veja que também pode obter o valor secreto:

1. No Visual Studio, selecione o projeto **key-vault-dotnet-core-quickstart**.
2. Selecione **Publicar** > **Iniciar**.
3. Crie um novo **Serviço de Aplicações** e, em seguida, selecione **Publicar**.
4. Altere o nome da aplicação para **keyvaultdotnetcorequickstart**.
5. Selecione **Criar**.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-a-managed-identity-for-the-web-app"></a>Ativar uma identidade gerida para a aplicação Web

O Azure Key Vault oferece uma forma de armazenar credenciais e outras chaves e segredos em segurança, mas o código tem de se autenticar no Key Vault para poder obtê-los. A [descrição geral das identidades geridas para os recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) simplifica a resolução deste problema ao conferir aos serviços do Azure uma identidade gerida automaticamente no Microsoft Azure Active Directory (Microsoft Azure AD). Pode utilizar esta identidade para autenticar em qualquer serviço que suporte a autenticação do Azure AD, incluindo o Key Vault, sem ser necessário ter credenciais no seu código.

No CLI do Azure, execute o comando assign-Identity para criar a identidade para este aplicativo:

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
>O comando neste procedimento é o equivalente a aceder ao portal e mudar a definição **Identidade/Sistema atribuído** para **Ativado** nas propriedades da aplicação Web.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Atribuir permissões à sua aplicação para ler segredos do Cofre de Chaves

Tome nota da saída quando publicar a aplicação no Azure. Deverá estar no formato:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Em seguida, execute este comando com o nome do seu cofre de chaves e o valor do **PrincipalId**:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list

```

Agora quando executar a aplicação, deverá ver o seu valor secreto obtido. No comando anterior, você está dando a identidade das permissões do serviço de aplicativo para realizar operações de **obtenção** e **lista** no cofre de chaves.

## <a name="clean-up-resources"></a>Limpar recursos
Exclua o grupo de recursos, a máquina virtual e todos os recursos relacionados quando você não precisar mais deles. Para fazer isso, selecione o grupo de recursos para o cofre de chaves e selecione **excluir**.

Exclua o cofre de chaves usando o comando [AZ keyvault Delete](https://docs.microsoft.com/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-delete) :

```azurecli
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Key Vault](key-vault-whatis.md)
