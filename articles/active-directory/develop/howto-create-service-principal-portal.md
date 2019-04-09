---
title: Criar a identidade para aplicações do Azure no portal | Documentos da Microsoft
description: Descreve como criar uma nova aplicação do Azure Active Directory e o principal de serviço que pode ser utilizado com o controlo de acesso baseado em funções no Azure Resource Manager para gerir o acesso aos recursos.
services: active-directory
documentationcenter: na
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee12cb2a0fdd4547ffae8dfce4fddee03167e539
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257421"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como: Utilizar o portal para criar uma aplicação do Azure AD e principal de serviço que pode aceder aos recursos

Este artigo mostra-lhe como criar uma nova aplicação do Azure Active Directory e o serviço principal que pode ser utilizado com o controlo de acesso baseado em funções. Quando tiver o código que precisa de aceder ou modificar os recursos, pode criar uma identidade para a aplicação. Esta identidade é conhecida como um principal de serviço. Em seguida, pode atribuir as permissões necessárias para o principal de serviço. Este artigo mostra-lhe como utilizar o portal para criar o principal de serviço. Ele se concentra num aplicativo de inquilino único onde o aplicativo destina-se para ser executada dentro da organização apenas um. Geralmente usa aplicações de inquilino único para aplicações de linha de negócio que são executados dentro da sua organização.

> [!IMPORTANT]
> Em vez de criar um principal de serviço, considere a utilização de identidades geridas para recursos do Azure para a sua identidade da aplicação. Se o seu código é executado num serviço que suporta recursos de acessos que suportem a autenticação do Azure Active Directory (Azure AD) e de identidades geridas, identidades geridas são uma opção melhor para. Para saber mais sobre identidades geridas para recursos do Azure, incluindo os serviços atualmente suportam, consulte [o que há de identidades geridas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

Vamos passar diretamente para criar a identidade. Caso se depare com um problema, verifique os [permissões obrigatórias](#required-permissions) para se certificar de que a sua conta, pode criar a identidade.

1. Inicie sessão na sua conta do Azure através da [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Selecione **Registos das aplicações**.

   ![selecionar registos das aplicações](./media/howto-create-service-principal-portal/select-app-registrations.png)

1. Selecione **Novo registo de aplicação**.

   ![Adicionar a aplicação](./media/howto-create-service-principal-portal/select-add-app.png)

1. Indique um nome e um URL para a aplicação. Selecione **aplicação / API Web** no tipo de aplicação que quer criar. Não é possível criar as credenciais para uma [aplicativo nativo](../manage-apps/application-proxy-configure-native-client-application.md). Não é possível usar esse tipo para um aplicativo automatizado. Depois de definir os valores, selecione **criar**.

   ![dar nome à aplicação](./media/howto-create-service-principal-portal/create-app.png)

Acabou de criar a aplicação do Azure AD e principal de serviço.

## <a name="assign-the-application-to-a-role"></a>Atribuir a aplicação a uma função

Para acessar recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida que função oferece as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte o artigo [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md).

Pode definir o âmbito no nível da subscrição, no grupo de recursos ou ao recurso. As permissões são herdadas para níveis inferiores de âmbito. Por exemplo, adicionar uma aplicação à função do leitor para um grupo de recursos significa pode ler o grupo de recursos e todos os recursos que nele contidos.

1. Navegue para o nível de âmbito que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito da subscrição, selecione **todos os serviços** e **subscrições**.

   ![Selecionar subscrição](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a subscrição particular para atribuir a aplicação.

   ![Selecione a subscrição para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se não vir a subscrição que procura, selecione **filtro de subscrições global**. Certifique-se a subscrição que pretende está selecionado para o portal. 

1. Selecione **controlo de acesso (IAM)**.
1. Selecione **adicionar atribuição de função**.

   ![Selecione adicionar atribuição de função](./media/howto-create-service-principal-portal/select-add.png)

1. Selecione a função que pretende atribuir à aplicação. Para permitir que o aplicativo para executar ações como **reinicie**, **iniciar** e **parar** instâncias, selecionadas o **contribuinte** função. Por predefinição, as aplicações do Azure AD não são apresentadas nas opções disponíveis. Para localizar seu aplicativo, procure o nome e selecioná-lo.

   ![Selecionar função](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **guardar** para concluir a atribuir a função. Pode ver a sua aplicação na lista de utilizadores atribuídos a uma função para esse âmbito.

Seu principal de serviço está definida. Pode começar a usá-lo para executar as suas aplicações ou scripts. A secção seguinte mostra como obter valores que são necessários quando iniciar sessão programaticamente.

## <a name="get-values-for-signing-in"></a>Obter os valores para iniciar sessão

### <a name="get-tenant-id"></a>Obter o ID de inquilino

Quando iniciar sessão programaticamente, tem de passar o ID do inquilino com o seu pedido de autenticação.

1. Selecione **Azure Active Directory**.
1. Selecione **propriedades**.

   ![selecionar propriedades do Azure AD](./media/howto-create-service-principal-portal/select-ad-properties.png)

1. Copiar o **ID de diretório** para obter sua ID do inquilino.

   ![ID do Inquilino](./media/howto-create-service-principal-portal/copy-directory-id.png)

### <a name="get-application-id-and-authentication-key"></a>Obter a chave de ID e a autenticação da aplicação

Também precisa do ID da sua aplicação e uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. Partir **registos das aplicações** no Azure AD, selecione a aplicação.

   ![Selecionar aplicação](./media/howto-create-service-principal-portal/select-app.png)

1. Copie o **ID da Aplicação** e armazene-o no código da aplicação.

   ![ID de cliente](./media/howto-create-service-principal-portal/copy-app-id.png)

1. Selecione **definições**.

   ![Selecionar definições](./media/howto-create-service-principal-portal/select-settings.png)

1. Selecione **Chaves**.
1. Indique uma descrição e uma duração para a chave. Quando terminar, selecione **Guardar**.

   ![Guardar chave](./media/howto-create-service-principal-portal/save-key.png)

   Depois de guardar a chave, o valor da mesma é apresentado. Copie este valor, porque não é possível obter a chave mais tarde. Forneça o valor da chave com o ID da aplicação para iniciar sessão como o aplicativo. Armazene o valor da chave num local onde a aplicação o possa obter.

   ![chave guardada](./media/howto-create-service-principal-portal/copy-key.png)

## <a name="required-permissions"></a>Permissões obrigatórias

Tem de ter permissões suficientes para registar uma aplicação no seu inquilino do Azure AD e atribuir a aplicação a uma função na sua subscrição do Azure.

### <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

1. Selecione **Azure Active Directory**.
1. Tenha em atenção de sua função. Se tiver o **utilizador** função, deve certificar-se de que não-administradores podem registar aplicações.

   ![Localizar utilizador](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecione **definições de utilizador**.

   ![Selecione as definições de utilizador](./media/howto-create-service-principal-portal/select-user-settings.png)

1. Verifique os **registos das aplicações** definição. Este valor apenas pode ser definido por um administrador. Se definido como **Sim**, qualquer utilizador no inquilino do Azure AD pode registar uma aplicação.

   ![ver registos de aplicações](./media/howto-create-service-principal-portal/view-app-registrations.png)

Se a definição dos registos da aplicação estiver definido como **não**, apenas os utilizadores com uma função de administrador podem registar esses tipos de aplicativos. Ver [funções disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [permissões de função](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) para saber mais sobre as funções de administrador disponíveis e as permissões específicas no Azure AD, que são fornecidas para cada função. Se a sua conta está atribuída à função de utilizador, mas a definição de registo de aplicação está limitada a utilizadores de administração, peça ao seu administrador para qualquer um atribuir a uma das funções de administrador que podem criar e gerir todos os aspetos de registos de aplicações ou para permitir que os utilizadores Registe aplicações.

### <a name="check-azure-subscription-permissions"></a>Verifique as permissões de subscrição do Azure

Na sua subscrição do Azure, a conta tem de ter `Microsoft.Authorization/*/Write` acesso para atribuir uma aplicação AD a uma função. Esta ação é concedida através das funções [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso dos Utilizadores](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se a sua conta é atribuída para o **contribuinte** função, não tem permissão adequada. Receber um erro ao tentar atribuir o principal de serviço a uma função.

Para verificar as permissões de subscrição:

1. Selecione a sua conta no canto superior direito e selecione **as minhas permissões**.

   ![Selecione as permissões de utilizador](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Na lista pendente, selecione a subscrição que pretende criar o serviço principal no. Em seguida, selecione **clique aqui para acesso completo de ver os detalhes para esta subscrição**.

   ![Localizar utilizador](./media/howto-create-service-principal-portal/view-details.png)

1. Ver as funções atribuídas e determinar se tem as permissões adequadas para atribuir uma aplicação AD a uma função. Caso contrário, peça ao administrador de subscrição para o adicionar a função de administrador de acesso do utilizador. Na imagem seguinte, o utilizador tem atribuída à função de proprietário, o que significa que o utilizador tem as permissões adequadas.

   ![Mostrar permissões](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passos Seguintes

* Para configurar uma aplicação multi-inquilino, consulte [Guia do programador para autorização com a API do Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Para saber mais sobre como especificar políticas de segurança, veja [controlo de acesso baseado em funções do Azure](../../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que pode ser concedido ou negado para os utilizadores, consulte [operações de fornecedor de recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
