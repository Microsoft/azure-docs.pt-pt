---
title: Ativar a identidade gerida atribuída pelo sistema para a aplicação do Azure Spring Cloud
description: Como ativar a identidade gerida atribuída pelo sistema para aplicação.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878234"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Como permitir a identidade gerida atribuída pelo sistema para a aplicação Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C #

Identidades geridas para recursos Azure fornecem uma identidade gerida automaticamente no Azure Ative Directory a um recurso Azure, como a sua aplicação Azure Spring Cloud. Pode utilizar esta identidade para autenticar qualquer serviço que suporte a autenticação AZure AD, sem ter credenciais no seu código.

Este artigo mostra como ativar e desativar identidades geridas atribuídas pelo sistema para uma aplicação Azure Spring Cloud, utilizando o portal Azure e o CLI (disponível a partir da versão 0.2.4).

## <a name="prerequisites"></a>Pré-requisitos
Se não está familiarizado com as identidades geridas para recursos Azure, consulte [a secção de visão geral](../active-directory/managed-identities-azure-resources/overview.md).
Vai precisar de uma instância da Nuvem de primavera de Azure. Siga o [Quickstart para implementar utilizando o Azure CLI](spring-cloud-quickstart.md).

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

Poderá ser necessário [configurar o recurso-alvo para permitir o acesso a partir da sua aplicação.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md) Por exemplo, se você solicitar um token para aceder ao Key Vault, certifique-se de que adicionou uma política de acesso que inclui a identidade da sua aplicação. Caso contrário, as suas chamadas para Key Vault serão rejeitadas, mesmo que incluam o símbolo. Para saber mais sobre quais os recursos que suportam tokens do Azure Ative Directory, consulte [os serviços Azure que suportam a autenticação AD da Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

A Azure Spring Cloud partilha o mesmo ponto final para a aquisição de token com a Azure Virtual Machine. Recomendamos a utilização de Botas Java SDK ou de arranque de mola para adquirir um token.  Veja [como usar o token VM para](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) vários exemplos de código e script e orientação sobre tópicos importantes, tais como manusear o token expiration e erros HTTP.

Recomendado: utilize botas Java SDK ou boot boot para obter fichas.  Veja as amostras nos [próximos passos.](#next-steps)

## <a name="disable-system-assigned-identity-from-an-app"></a>Desativar a identidade atribuída ao sistema a partir de uma aplicação
A remoção de uma identidade atribuída ao sistema também a eliminará do Azure AD. A eliminação do recurso da aplicação remove automaticamente as identidades atribuídas ao sistema do Azure AD.

### <a name="using-azure-portal"></a>Com o Portal do Azure
Para remover a identidade gerida atribuída pelo sistema de uma aplicação que já não precisa dela:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) usando uma conta associada à subscrição do Azure que contém a instância Azure Spring Cloud.
1. Navegue para a máquina virtual desejada e selecione **Identidade**.
1. Em **Estado atribuído ao** / **Sistema**, selecione **Off** e clique em **Guardar**:

 ![Identidade gerida](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure
Para remover a identidade gerida atribuída pelo sistema a partir de uma aplicação que já não precisa, utilize o seguinte comando:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Passos seguintes

* [Access Azure Key Vault com identidades geridas no arranque da bota de primavera](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Saiba mais sobre identidades geridas para recursos Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Como usar identidades geridas com Java SDK](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
