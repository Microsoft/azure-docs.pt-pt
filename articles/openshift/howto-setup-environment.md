---
title: Instale o seu ambiente de desenvolvimento OpenShift do seu chapéu vermelho azul
description: Aqui estão os pré-requisitos para trabalhar com o Microsoft Azure Red Hat OpenShift.
keywords: conjunto de turno de chapéu vermelho configuração
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.openlocfilehash: e7396ce9fbed46688d59b582f246e5454d063fb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477039"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar o seu ambiente de desenvolvimento do Azure Red Hat OpenShift

Para construir e executar aplicações Abertas do Chapéu Vermelho microsoft Azure, terá de:

* Instale a versão 2.0.65 (ou superior) do Azure CLI (ou utilize a Casca de Nuvem Azure).
* Registe-se na `AROGA` funcionalidade e nos fornecedores de recursos associados.
* Crie um inquilino azure Ative Directory (Azure AD).
* Crie um objeto de aplicação Azure AD.
* Crie um utilizador de Anúncios Azure.

As seguintes instruções irão acompanhá-lo através de todos estes pré-requisitos.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

O Azure Red Hat OpenShift requer a versão 2.0.65 ou superior ao Azure CLI. Se já instalou o Azure CLI, pode verificar qual a versão que tem executando:

```azurecli
az --version
```

A primeira linha de saída terá a `azure-cli (2.0.65)`versão CLI, por exemplo.

Aqui estão as instruções para [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se necessitar de uma nova instalação ou de uma atualização.

Alternadamente, pode utilizar a [Casca de Nuvem Azure.](https://docs.microsoft.com/azure/cloud-shell/overview) Ao utilizar a Concha azure Cloud, certifique-se de selecionar o ambiente **Bash** se planeia seguir juntamente com a Create e gerir uma série tutorial de cluster OpenShift do [Chapéu Vermelho Azure.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Registar fornecedores e funcionalidades

A `Microsoft.ContainerService AROGA` `Microsoft.Solutions`funcionalidade, `Microsoft.Compute` `Microsoft.Storage`e `Microsoft.KeyVault` `Microsoft.Network` os fornecedores devem estar registados manualmente na sua subscrição antes de implantar o seu primeiro cluster OpenShift do Chapéu Vermelho Azure.

Para registar estes fornecedores e funcionalidades manualmente, utilize as seguintes instruções de uma concha Bash se tiver instalado o CLI, ou a partir da sessão Azure Cloud Shell (Bash) no seu portal Azure:

1. Se tiver várias subscrições do Azure, especifique o ID de subscrição relevante:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registe a funcionalidade Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registe o fornecedor microsoft.Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registe o fornecedor Microsoft.Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registe o fornecedor Microsoft.Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registe o fornecedor Microsoft.Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registe o fornecedor Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Atualizar o registo do fornecedor de recursos Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um inquilino azure Ative Directory (Azure AD)

O serviço Azure Red Hat OpenShift requer um inquilino associado do Azure Ative Directory (Azure AD) que represente a sua organização e a sua relação com a Microsoft. O seu inquilino Azure AD permite-lhe registar, construir e gerir aplicações, bem como utilizar outros serviços Azure.

Se não tiver um Anúncio Azure para usar como inquilino do seu cluster Azure Red Hat OpenShift, ou se pretender criar um inquilino para testes, siga as instruções em [Create a Azure AD tenant para o seu cluster Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com este guia.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Criar um utilizador, grupo de segurança e objeto de aplicação Azure AD

O Azure Red Hat OpenShift requer permissões para executar tarefas no seu cluster, como configurar o armazenamento. Estas permissões são representadas através de um diretor de [serviço.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) Também vai querer criar um novo utilizador ative diretório para testar aplicações em execução no seu cluster OpenShift do Chapéu Vermelho Azure.

Siga as instruções em [Create a Azure AD object and user](howto-aad-app-configuration.md) to create a service principal, generate a client secret and authentication callback URL for your app, and create a new Azure AD security group and user to access the cluster.

## <a name="next-steps"></a>Passos seguintes

Está pronto para usar o Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
