---
title: Criar um Principal de serviço para o Azure Stack | Documentos da Microsoft
description: Descreve como criar um principal de serviço que pode ser utilizado com o controlo de acesso baseado em funções no Azure Resource Manager para gerir o acesso aos recursos.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 9f0ccb85fa4b42ef3ca90c6d4ca2d80113b93a0c
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733704"
---
# <a name="give-applications-access-to-azure-stack-resources-by-creating-service-principals"></a>Conceder acesso de aplicações para recursos do Azure Stack através da criação de principais de serviço

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Pode dar uma aplicação acesso aos recursos do Azure Stack através da criação de um serviço principal, que utiliza o Azure Resource Manager. Um principal de serviço permite-lhe permissões específicas de delegado usando [controlo de acesso baseado em funções](azure-stack-manage-permissions.md).

Como melhor prática, deve utilizar principais de serviço para as suas aplicações. Principais de serviço são preferíveis a executar uma aplicação com as suas próprias credenciais pelos seguintes motivos:

* Pode atribuir permissões ao principal de que são diferentes de suas próprias permissões de conta de serviço. Normalmente, as permissões de um principal de serviço são restritas a exatamente o que a aplicação precisa de fazer.
* Não é necessário que alterar credenciais da aplicação, se alterar a sua função ou responsabilidades.
* Pode utilizar um certificado para automatizar a autenticação ao executar um script automático.

## <a name="example-scenario"></a>Cenário de exemplo

Tem uma aplicação de gestão de configuração que precisa de inventário de recursos do Azure com o Azure Resource Manager. Pode criar um serviço principal e atribuí-lo à função do leitor. Esta função proporciona o acesso só de leitura da aplicação aos recursos do Azure.

## <a name="getting-started"></a>Introdução

Utilize os passos neste artigo como guia para:

* Crie um principal de serviço para a sua aplicação.
* Registar a sua aplicação e crie uma chave de autenticação.
* Atribua a aplicação a uma função.

A forma como tiver configurado o Active Directory para o Azure Stack determina como criar um principal de serviço. Escolha uma das seguintes opções:

* Criar um serviço principal para [do Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Criar um serviço principal para [serviços de Federação do Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Os passos para atribuir um principal de serviço a uma função o mesmo para o Azure AD e AD FS. Depois de criar o principal de serviço, poderá [delegar permissões](azure-stack-create-service-principals.md) ao atribuí-lo a uma função.

## <a name="create-service-principal-for-azure-ad"></a>Criar principal de serviço para o Azure AD

Se a sua pilha do Azure utiliza o Azure AD como o repositório de identidades, pode criar um serviço principal com os mesmos passos como no Azure, através do portal do Azure.

>[!NOTE]
Verifique que tem o [do Azure AD permissões obrigatórias](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) antes de começar a criar um principal de serviço.

### <a name="create-service-principal"></a>Criar um principal de serviço

Para criar um principal de serviço para a sua aplicação:

1. Inicie sessão na sua conta do Azure através da [portal do Azure](https://portal.azure.com).
2. Selecione **do Azure Active Directory** > **registos das aplicações** > **adicionar**.
3. Indique um nome e um URL para a aplicação. Selecione **aplicação Web / API** ou **nativo** para o tipo de aplicação que pretende criar. Depois de definir os valores, selecione **criar**.

### <a name="get-credentials"></a>Obter credenciais

Quando iniciar sessão programaticamente, utilize o ID da sua aplicação e uma chave de autenticação. Para obter estes valores:

1. Partir **registos das aplicações** no Active Directory, selecione a aplicação.

2. Copie o **ID da Aplicação** e armazene-o no código da aplicação. As aplicações nas aplicações de exemplo utilizam **id de cliente** quando nos Referimos ao **ID de aplicação**.

     ![ID da aplicação para a aplicação](./media/azure-stack-create-service-principals/image12.png)
3. Para gerar uma chave de autenticação, selecione **Chaves**.

4. Indique uma descrição e uma duração para a chave. Quando terminar, selecione **Guardar**.

>[!IMPORTANT]
Depois de guardar a chave, a chave **valor** é apresentado. Anote este valor, porque não é possível obter a chave mais tarde. Armazene o valor da chave num local onde a aplicação o possa obter.

![Aviso de chave-valor para a chave guardada.](./media/azure-stack-create-service-principals/image15.png)

A etapa final é [atribuir uma função de seu aplicativo](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Criar principal de serviço para o AD FS

Se tiver implementado o Azure Stack através do AD FS como o repositório de identidades, pode utilizar o PowerShell para as seguintes tarefas:

* Crie um principal de serviço.
* Atribua o principal de serviço a uma função.
* Inicie sessão com a identidade do principal de serviço.

Para obter detalhes sobre como criar o principal de serviço, consulte [criar principal de serviço para o AD FS](../azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Atribuir o principal de serviço a uma função

Para acessar recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida que função representa as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte o artigo [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md).

>[!NOTE]
Pode definir o âmbito de uma função no nível de uma subscrição, um grupo de recursos ou um recurso. As permissões são herdadas para níveis inferiores de âmbito. Por exemplo, uma aplicação com a função de leitor para um grupo de recursos significa que a aplicação pode ler qualquer um dos recursos no grupo de recursos.

Utilize os passos seguintes como guia para atribuir uma função a um principal de serviço.

1. No portal do Azure Stack, navegue para o nível de âmbito que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito da subscrição, selecione **subscrições**.

2. Selecione a subscrição para atribuir a aplicação. Neste exemplo, a subscrição é o Visual Studio Enterprise.

     ![Selecione a subscrição do Visual Studio Enterprise para atribuição](./media/azure-stack-create-service-principals/image16.png)

3. Selecione **controlo de acesso (IAM)** para a subscrição.

4. Selecione **adicionar atribuição de função**.

5. Selecione a função que pretende atribuir à aplicação.

6. Procure a sua aplicação e selecioná-lo.

7. Selecione **OK** para concluir a atribuir a função. Pode ver a sua aplicação na lista de utilizadores atribuídos a uma função para esse âmbito.

Agora que já criou um principal de serviço e uma função atribuída, seu aplicativo pode acessar recursos do Azure Stack.

## <a name="next-steps"></a>Passos Seguintes

[Gerir permissões de utilizador](azure-stack-manage-permissions.md)
