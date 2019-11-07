---
title: Configurar seu ambiente de desenvolvimento do Azure Red Hat OpenShift | Microsoft Docs
description: Aqui estão os pré-requisitos para trabalhar com Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: configuração da instalação do Red Hat openshift
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: fa09ed90258a62d37dafeea5f4760e1fabdc210b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581603"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar o seu ambiente de desenvolvimento do Azure Red Hat OpenShift

Para compilar e executar Microsoft Azure aplicativos Red Hat OpenShift, você precisará:

* Instale a versão 2.0.65 (ou superior) do CLI do Azure (ou use o Azure Cloud Shell).
* Registre-se para o recurso `AROGA` e provedores de recursos associados.
* Crie um locatário do Azure Active Directory (AD do Azure).
* Crie um objeto de aplicativo do Azure AD.
* Crie um usuário do Azure AD.

As instruções a seguir mostrarão todos esses pré-requisitos.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

O Azure Red Hat OpenShift requer a versão 2.0.65 ou superior do CLI do Azure. Se você já tiver instalado o CLI do Azure, poderá verificar qual versão você tem executando:

```bash
az --version
```

A primeira linha de saída terá a versão da CLI, por exemplo `azure-cli (2.0.65)`.

Aqui estão as instruções para [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se você precisar de uma nova instalação ou uma atualização.

Como alternativa, você pode usar o [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ao usar o Azure Cloud Shell, certifique-se de selecionar o ambiente **bash** se você planeja acompanhar o tutorial [criar e gerenciar uma série de tutoriais de cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

## <a name="register-providers-and-features"></a>Registrar provedores e recursos

O recurso `Microsoft.ContainerService AROGA`, `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` e `Microsoft.Network` provedores devem ser registrados em sua assinatura manualmente antes de implantar seu primeiro cluster do Azure Red Hat OpenShift.

Para registrar esses provedores e recursos manualmente, use as seguintes instruções de um shell bash se você instalou a CLI ou da sessão Azure Cloud Shell (bash) em seu portal do Azure:

1. Se você tiver várias assinaturas do Azure, especifique a ID de assinatura relevante:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registre o recurso Microsoft. ContainerService AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registre o provedor Microsoft. Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registre o provedor Microsoft. Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registre o provedor Microsoft. Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registre o provedor Microsoft. Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registre o provedor Microsoft. keyvault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Atualize o registro do provedor de recursos Microsoft. ContainerService:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um locatário do Azure Active Directory (Azure AD)

O serviço do Azure Red Hat OpenShift requer um locatário associado do Azure Active Directory (Azure AD) que representa sua organização e sua relação com a Microsoft. Seu locatário do Azure AD permite que você registre, crie e gerencie aplicativos, bem como use outros serviços do Azure.

Se você não tiver um Azure AD para usar como o locatário para seu cluster do Azure Red Hat OpenShift ou se desejar criar um locatário para teste, siga as instruções em [criar um locatário do Azure ad para o cluster do Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com este guia .

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Criar um usuário do Azure AD, um grupo de segurança e um objeto de aplicativo

O Azure Red Hat OpenShift requer permissões para executar tarefas no cluster, como configurar o armazenamento. Essas permissões são representadas por meio de uma [entidade de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Você também desejará criar um novo usuário Active Directory para testar aplicativos em execução no cluster do Azure Red Hat OpenShift.

Siga as instruções em [criar um objeto de aplicativo do Azure AD e um usuário](howto-aad-app-configuration.md) para criar uma entidade de serviço, gerar um segredo do cliente e uma URL de retorno de chamada de autenticação para seu aplicativo e criar um novo grupo de segurança do Azure AD e usuário para acessar o cluster.

## <a name="next-steps"></a>Passos seguintes

Agora você está pronto para usar o Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
