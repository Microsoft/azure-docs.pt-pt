---
title: Ativar a identidade gerida atribuída pelo sistema para a aplicação do Azure Spring Cloud
description: Como ativar a identidade gerida atribuída pelo sistema para aplicação.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.openlocfilehash: c9b9eaf7447d30dfbec6b04ff00010e0e2992c8e
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85254536"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Como permitir a identidade gerida atribuída pelo sistema para a aplicação Azure Spring Cloud
Identidades geridas para recursos Azure fornecem uma identidade gerida automaticamente no Azure Ative Directory a um recurso Azure, como a sua aplicação Azure Spring Cloud. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.

Este artigo mostra como ativar e desativar identidades geridas atribuídas pelo sistema para uma aplicação Azure Spring Cloud, utilizando o portal Azure e o CLI (disponível a partir da versão 0.2.4).

## <a name="prerequisites"></a>Pré-requisitos
Se não está familiarizado com as identidades geridas para recursos Azure, consulte [a secção de visão geral](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Vai precisar de uma instância da Nuvem de primavera de Azure. Siga o [Quickstart para implementar utilizando o Azure CLI](spring-cloud-quickstart-launch-app-cli.md).

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída ao sistema
A criação de uma app com uma identidade atribuída ao sistema requer a definição de uma propriedade adicional na aplicação.

### <a name="using-azure-portal"></a>Com o Portal do Azure
Para configurar uma identidade gerida no [portal Azure](https://portal.azure.com/), primeiro crie uma app e, em seguida, ative a funcionalidade.

1. Crie uma aplicação no portal como normalmente faria. Navegue até ele no portal.
2. Desloque-se até ao grupo **Definições** no painel de navegação esquerdo.
3. Selecione **identidade**.
4. Dentro do separador **Designado sistema,** **altere o Estado** para *ligar*. Clique em **Guardar**.

 ![Identidade gerida no portal](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Pode ativar a identidade gerida atribuída pelo sistema durante a criação de apps ou numa aplicação existente.

**Ativar a identidade gerida atribuída pelo sistema durante a criação de uma app**

O exemplo a seguir cria uma aplicação denominada *app_name* com uma identidade gerida atribuída ao sistema, conforme solicitado pelo `--assign-identity` parâmetro.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Ativar a identidade gerida atribuída pelo sistema numa aplicação existente** Utilize `az spring-cloud app identity assign` o comando para ativar a identidade atribuída ao sistema numa aplicação existente.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Obter fichas para recursos Azure
Uma aplicação pode usar a sua identidade gerida para obter fichas para aceder a outros recursos protegidos pelo Azure Ative Directory, como o Azure Key Vault. Estes tokens representam a aplicação que acede ao recurso, e não qualquer utilizador específico da aplicação.

Poderá ser necessário [configurar o recurso-alvo para permitir o acesso a partir da sua aplicação.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal) Por exemplo, se você solicitar um token para aceder ao Key Vault, certifique-se de que adicionou uma política de acesso que inclui a identidade da sua aplicação. Caso contrário, as suas chamadas para Key Vault serão rejeitadas, mesmo que incluam o símbolo. Para saber mais sobre quais os recursos que suportam tokens do Azure Ative Directory, consulte [os serviços Azure que suportam a autenticação AD da Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities#azure-services-that-support-azure-ad-authentication)

A Azure Spring Cloud partilha o mesmo ponto final para a aquisição de token com a Azure Virtual Machine. Recomendamos a utilização de Botas Java SDK ou de arranque de mola para adquirir um token.  Veja [como usar o token VM para](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token) vários exemplos de código e script e orientação sobre tópicos importantes, tais como manusear o token expiration e erros HTTP.

Recomendado: utilize botas Java SDK ou boot boot para obter fichas.  Veja as amostras nos [próximos passos.](#next-steps)

## <a name="disable-system-assigned-identity-from-an-app"></a>Desativar a identidade atribuída ao sistema a partir de uma aplicação
A remoção de uma identidade atribuída ao sistema também a eliminará do Azure AD. A eliminação do recurso da aplicação remove automaticamente as identidades atribuídas ao sistema do Azure AD.

### <a name="using-azure-portal"></a>Com o Portal do Azure
Para remover a identidade gerida atribuída pelo sistema de uma aplicação que já não precisa dela:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) usando uma conta associada à subscrição do Azure que contém a instância Azure Spring Cloud.
1. Navegue para a máquina virtual desejada e selecione **Identidade**.
1. Em **Estado atribuído ao** / **Sistema**, selecione **Off** e clique em **Guardar**:

 ![Identidade gerida no portal](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para remover a identidade gerida atribuída pelo sistema a partir de uma aplicação que já não precisa, utilize o seguinte comando:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Passos seguintes
* [Como usar identidades geridas com Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
* [Access Azure Key Vault com identidades geridas no arranque da bota de primavera](https://github.com/microsoft/azure-spring-boot/blob/master/azure-spring-boot-starters/azure-keyvault-secrets-spring-boot-starter/README.md#use-msi--managed-identities)
* [Use o cofre de chaves do serviço de aplicações com identidade de serviço gerido](https://docs.microsoft.com/samples/azure-samples/app-service-msi-keyvault-dotnet/keyvault-msi-appservice-sample/)
