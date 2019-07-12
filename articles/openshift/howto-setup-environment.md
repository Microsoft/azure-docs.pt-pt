---
title: Configurar o ambiente de desenvolvimento do Azure Red Hat OpenShift | Documentos da Microsoft
description: Seguem-se os pré-requisitos para trabalhar com o Microsoft Azure Red Hat OpenShift.
services: openshift
keywords: configurar o programa de configuração do Red hat openshift
author: jimzim
ms.author: jzim
ms.date: 05/10/2019
ms.topic: conceptual
ms.service: container-service
manager: jeconnoc
ms.openlocfilehash: a31655e8c8805505bdcc5e90bf25191590d35c18
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672525"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Configurar o seu ambiente de desenvolvimento do Azure Red Hat OpenShift

Para criar e executar aplicativos do Microsoft Azure Red Hat OpenShift, precisará:

* Comprar instâncias reservadas de máquina virtual do Azure.
* Instalar versão 2.0.65 (ou superior) da CLI do Azure (ou utilize o Azure Cloud Shell).
* Inscreva-se a `AROGA` funcionalidade e os fornecedores de recursos associados.
* Crie um inquilino do Azure Active Directory (Azure AD).
* Crie um objeto de aplicação do Azure AD.
* Crie um utilizador do Azure AD.

As instruções seguintes orientam-no através de todos estes pré-requisitos.

## <a name="purchase-azure-red-hat-openshift-application-nodes-reserved-instances"></a>Comprar instâncias reservadas do Azure Red Hat OpenShift aplicação nós

Antes de poder utilizar a Azure Red Hat OpenShift, terá de comprar um mínimo de 4 nós de aplicação do Azure Red Hat OpenShift reservado, após o qual será capaz de clusters de aprovisionamento.

Se for um cliente do Azure, [comprar instâncias reservadas do Azure Red Hat OpenShift](https://aka.ms/openshift/buy) através do portal do Azure. Após a compra, a sua subscrição será ativada dentro de 24 horas.

Se não for um cliente do Azure, [contactar as vendas](https://aka.ms/openshift/contact-sales) e preencha o formulário de vendas na parte inferior da página para iniciar o processo.

Consulte a [página de preços do Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) para obter mais informações.

## <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Azure Red Hat OpenShift requer a versão 2.0.65 ou posterior da CLI do Azure. Se já tiver instalado a CLI do Azure, pode verificar que versão tiver ao executar:

```bash
az --version
```

A primeira linha da saída terá a versão da CLI, por exemplo `azure-cli (2.0.65)`.

Aqui estão as instruções para [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) se necessitar de uma nova instalação ou de uma atualização.

Em alternativa, pode utilizar o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview). Ao utilizar o Azure Cloud Shell, certifique-se de que selecione os **Bash** ambiente se planeja seguir juntamente com o [criar e gerir um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) série de tutoriais.

## <a name="register-providers-and-features"></a>Registar fornecedores e funcionalidades

O `Microsoft.ContainerService AROGA` funcionalidade, `Microsoft.Solutions`, `Microsoft.Compute`, `Microsoft.Storage`, `Microsoft.KeyVault` e `Microsoft.Network` fornecedores têm de estar registados na sua subscrição manualmente antes de implementar o seu primeiro cluster do Azure Red Hat OpenShift.

Para registar manualmente estes fornecedores e recursos, utilize as seguintes instruções de um shell de Bash, se instalou a CLI ou da sessão do Azure Cloud Shell (Bash) no portal do Azure:

1. Se tiver várias subscrições do Azure, especifique o ID de subscrição relevantes:

    ```bash
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registre-se a funcionalidade de containerservice AROGA:

    ```bash
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registe o fornecedor de Microsoft. Storage:

    ```bash
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registe o fornecedor de Microsoft. Compute:

    ```bash
    az provider register -n Microsoft.Compute --wait
    ```

1. Registe o fornecedor de Microsoft.Solutions:

    ```bash
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registe o fornecedor de Network:

    ```bash
    az provider register -n Microsoft.Network --wait
    ```

1. Registe o fornecedor de Microsoft. keyvault:

    ```bash
    az provider register -n Microsoft.KeyVault --wait
    ```

1. Atualize o registo do fornecedor de recursos containerservice:

    ```bash
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Criar um inquilino do Azure Active Directory (Azure AD)

O serviço de Azure Red Hat OpenShift requer um inquilino do Azure Active Directory (Azure AD) associado, que representa a sua organização e sua relação à Microsoft. Inquilino do Azure AD permite-lhe registar, criar e gerir aplicações, bem como utilizar outros serviços do Azure.

Se não tiver um Azure AD para utilizar como o inquilino para o seu cluster do Azure Red Hat OpenShift ou que deseja criar um inquilino para fins de teste, siga as instruções em [criar um inquilino do Azure AD para o seu cluster do Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com este guia.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Criar do Azure AD utilizador, grupo de segurança e a aplicação objeto

Azure Red Hat OpenShift requer permissões para executar tarefas no seu cluster, como a configuração de armazenamento. Estas permissões são representadas por meio de um [principal de serviço](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object). Também desejará criar um novo utilizador do Active Directory para fins de teste de aplicações em execução no seu cluster do Azure Red Hat OpenShift.

Siga as instruções em [criar um objeto de aplicação do Azure AD e o utilizador](howto-aad-app-configuration.md) para criar um principal de serviço, gerar um URL de retorno de chamada de cliente segredo e autenticação, para a sua aplicação e crie um novo grupo de segurança do Azure AD e o utilizador para aceder a cluster.

## <a name="next-steps"></a>Passos Seguintes

Agora, está pronto para utilizar a Azure Red Hat OpenShift!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli
