---
title: Crie o seu ambiente de desenvolvimento openshift do chapéu vermelho Azure
description: Aqui estão os pré-requisitos para trabalhar com o Microsoft Azure Red Hat OpenShift.
keywords: configuração de openshift chapéu vermelho configuração
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
ms.custom: devx-track-azurecli
ms.openlocfilehash: b468f967e68b72e3c9da276dc2077fc09256c895
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89470039"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar o seu ambiente de desenvolvimento do Azure Red Hat OpenShift

Para construir e executar aplicações Microsoft Azure Red Hat OpenShift, terá de:

* Instale a versão 2.0.65 (ou superior) do Azure CLI (ou utilize a Concha da Nuvem Azure).
* Registe-se para os `AROGA` fornecedores de recursos associados.
* Crie um inquilino Azure Ative Directory (Azure AD).
* Crie um objeto de aplicação AD Azure.
* Crie um utilizador AD Azure.

As seguintes instruções irão acompanhá-lo através de todos estes pré-requisitos.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

O Azure Red Hat OpenShift requer a versão 2.0.65 ou superior do Azure CLI. Se já instalou o Azure CLI, pode verificar qual a versão que tem executando:

```azurecli
az --version
```

A primeira linha de saída terá a versão CLI, por `azure-cli (2.0.65)` exemplo.

Aqui estão as instruções para [instalar o Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) se necessitar de uma nova instalação ou de uma atualização.

Alternadamente, pode utilizar a [Azure Cloud Shell](../cloud-shell/overview.md). Ao utilizar a Azure Cloud Shell, não se esqueça de selecionar o ambiente **Bash** se planeia seguir juntamente com o Create e gerir uma série tutorial [de cluster Azure Red Hat OpenShift.](tutorial-create-cluster.md)

## <a name="register-providers-and-features"></a>Registar fornecedores e funcionalidades

A `Microsoft.ContainerService AROGA` funcionalidade, `Microsoft.Solutions` , e os `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` fornecedores devem estar registados manualmente na sua subscrição antes de implementar o seu primeiro cluster Azure Red Hat OpenShift.

Para registar estes fornecedores e funcionalidades manualmente, utilize as seguintes instruções de uma concha Bash se tiver instalado o CLI, ou a partir da sessão Azure Cloud Shell (Bash) no seu portal Azure:

1. Se tiver várias subscrições do Azure, especifique o ID de subscrição relevante:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registe a função Microsoft.ContainerService AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registe-se o fornecedor Microsoft.Storage:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registe-se o fornecedor Microsoft.Compute:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. Registe-se o fornecedor Microsoft.Solutions:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registe-se o fornecedor Microsoft.Network:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registe-se o fornecedor Microsoft.KeyVault:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Atualizar o registo do fornecedor de recursos Microsoft.ContainerService:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um inquilino Azure Ative (Azure AD)

O serviço Azure Red Hat OpenShift requer um inquilino associado do Azure Ative Directory (Azure AD) que representa a sua organização e a sua relação com a Microsoft. O seu inquilino Azure AD permite-lhe registar, construir e gerir aplicações, bem como utilizar outros serviços Azure.

Se você não tem um AD Azure para usar como inquilino para o seu cluster Azure Red Hat OpenShift, ou você deseja criar um inquilino para testes, siga as instruções em [Criar um inquilino AZURE AD para o seu cluster Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com este guia.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Criar um utilizador AD Azure, grupo de segurança e objeto de aplicação

O Azure Red Hat OpenShift requer permissões para executar tarefas no seu cluster, como configurar o armazenamento. Estas permissões são representadas através de um [principal serviço.](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) Também vai querer criar um novo utilizador do Ative Directory para testar aplicações em execução no seu cluster Azure Red Hat OpenShift.

Siga as instruções na [Criar um objeto de aplicação AD AZure e utilizador](howto-aad-app-configuration.md) para criar um principal de serviço, gerar um URL de retorno secreto e de autenticação do cliente para a sua aplicação, e criar um novo grupo de segurança AD AD E um utilizador para aceder ao cluster.

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para usar o Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli