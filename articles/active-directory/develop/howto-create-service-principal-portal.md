---
title: Criar a identidade para aplicações do Azure no portal | Documentos da Microsoft
description: Descreve como criar uma nova aplicação do Azure Active Directory e o principal de serviço que pode ser utilizado com o controlo de acesso baseado em funções no Azure Resource Manager para gerir o acesso aos recursos.
services: active-directory
documentationcenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd1534b3f966051104a3f3ee389fb047ab258fc
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482807"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como: Utilizar o portal para criar uma aplicação do Azure AD e principal de serviço que pode aceder aos recursos

Este artigo mostra-lhe como criar uma nova aplicação do Azure Active Directory (Azure AD) e o principal de serviço que pode ser utilizado com o controlo de acesso baseado em funções. Quando tiver o código que precisa de aceder ou modificar os recursos, pode criar uma identidade para a aplicação. Esta identidade é conhecida como um principal de serviço. Em seguida, pode atribuir as permissões necessárias para o principal de serviço. Este artigo mostra-lhe como utilizar o portal para criar o principal de serviço. Ele se concentra num aplicativo de inquilino único onde o aplicativo destina-se para ser executada dentro da organização apenas um. Geralmente usa aplicações de inquilino único para aplicações de linha de negócio que são executados dentro da sua organização.

> [!IMPORTANT]
> Em vez de criar um principal de serviço, considere a utilização de identidades geridas para recursos do Azure para a sua identidade da aplicação. Se o seu código é executado num serviço que oferece suporte a identidades geridas e recursos de acessos que suportam a autenticação do Azure AD, identidades geridas são uma opção melhor para. Para saber mais sobre identidades geridas para recursos do Azure, incluindo os serviços atualmente suportam, consulte [o que há de identidades geridas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Criar uma aplicação do Azure Active Directory

Vamos passar diretamente para criar a identidade. Caso se depare com um problema, verifique os [permissões obrigatórias](#required-permissions) para se certificar de que a sua conta, pode criar a identidade.

1. Inicie sessão na sua conta do Azure através da [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Selecione **Registos das aplicações**.
1. Selecione **novo registo**.
1. Nome da aplicação. Selecione uma conta suportada escreva, que determina quem pode usar o aplicativo. Sob **URI de redirecionamento**, selecione **Web** para o tipo de aplicação que pretende criar. Introduza o URI onde o token de acesso é enviado para. Não é possível criar as credenciais para uma [aplicativo nativo](../manage-apps/application-proxy-configure-native-client-application.md). Não é possível usar esse tipo para um aplicativo automatizado. Depois de definir os valores, selecione **registar**.

   ![Escreva um nome para a sua aplicação](./media/howto-create-service-principal-portal/create-app.png)

Acabou de criar a aplicação do Azure AD e principal de serviço.

## <a name="assign-the-application-to-a-role"></a>Atribuir a aplicação a uma função

Para acessar recursos na sua subscrição, tem de atribuir a aplicação a uma função. Decida que função oferece as permissões corretas para a aplicação. Para saber mais sobre as funções disponíveis, consulte o artigo [RBAC: Funções incorporadas](../../role-based-access-control/built-in-roles.md).

Pode definir o âmbito no nível da subscrição, no grupo de recursos ou ao recurso. As permissões são herdadas para níveis inferiores de âmbito. Por exemplo, adicionar uma aplicação à função do leitor para um grupo de recursos significa pode ler o grupo de recursos e todos os recursos que nele contidos.

1. Navegue para o nível de âmbito que pretende atribuir a aplicação. Por exemplo, para atribuir uma função no âmbito da subscrição, selecione **todos os serviços** e **subscrições**.

   ![Por exemplo, atribuir uma função no âmbito da subscrição](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a subscrição particular para atribuir a aplicação.

   ![Selecione a subscrição para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se não vir a subscrição que procura, selecione **filtro de subscrições global**. Certifique-se a subscrição que pretende está selecionado para o portal.

1. Selecione **controlo de acesso (IAM)** .
1. Selecione **adicionar atribuição de função**.
1. Selecione a função que pretende atribuir à aplicação. Para permitir que o aplicativo para executar ações como **reinicie**, **iniciar** e **parar** instâncias, selecionadas o **contribuinte** função. Por predefinição, as aplicações do Azure AD não são apresentadas nas opções disponíveis. Para localizar seu aplicativo, procure o nome e selecioná-lo.

   ![Selecione a função para atribuir à aplicação](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **guardar** para concluir a atribuir a função. Pode ver a sua aplicação na lista de utilizadores atribuídos a uma função para esse âmbito.

Seu principal de serviço está definida. Pode começar a usá-lo para executar as suas aplicações ou scripts. A secção seguinte mostra como obter valores que são necessários quando iniciar sessão programaticamente.

## <a name="get-values-for-signing-in"></a>Obter os valores para iniciar sessão

Quando iniciar sessão programaticamente, tem de passar o ID do inquilino com o seu pedido de autenticação. Também precisa do ID da sua aplicação e uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. Selecione **Azure Active Directory**.
1. Partir **registos das aplicações** no Azure AD, selecione a aplicação.
1. Copie o ID de diretório (inquilino) e armazená-los no código da aplicação.

    ![Copie o diretório (ID de inquilino) e armazená-lo em seu código de aplicação](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copie o **ID da Aplicação** e armazene-o no código da aplicação.

   ![Copie o ID da aplicação (cliente)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificados e segredos
Aplicações daemon podem utilizar duas formas de credenciais para autenticar com o Azure AD: certificados e segredos da aplicação.  Recomendamos que utilize um certificado, mas também pode criar um novo segredo de aplicação.

### <a name="upload-a-certificate"></a>Carregar um certificado

Pode utilizar um certificado existente se tiver uma.  Opcionalmente, pode criar um certificado autoassinado para fins de teste. Abra o PowerShell e execute [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) com os parâmetros seguintes para criar um certificado autoassinado no arquivo de certificados de utilizador no seu computador: `$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature`.  Exportar este certificado com o [gerir certificado de utilizador](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) snap-in MMC acessível a partir do painel de controle do Windows.

Para carregar o certificado:

1. Selecione **certificados e segredos**.
1. Selecione **carregar certificado** e selecione o certificado (um certificado existente ou o autoassinado de certificado que exportou).

    ![Selecione o certificado de carregamento e selecionar aquele que pretende adicionar](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecione **Adicionar**.

Depois de registar o certificado com a sua aplicação no portal de registo de aplicação, tem de ativar o código de aplicação de cliente utilizar o certificado.

### <a name="create-a-new-application-secret"></a>Criar um novo segredo de aplicação

Se optar por não utilizar um certificado, pode criar um novo segredo de aplicação.

1. Selecione **certificados e segredos**.
1. Selecione **segredos de cliente -> novo segredo do cliente**.
1. Forneça uma descrição do segredo e uma duração. Quando terminar, selecione **adicionar**.

   Depois de guardar o segredo do cliente, é apresentado o valor do segredo do cliente. Copie este valor, porque não é possível obter a chave mais tarde. Forneça o valor da chave com o ID da aplicação para iniciar sessão como o aplicativo. Armazene o valor da chave num local onde a aplicação o possa obter.

   ![Copie o valor do segredo porque não é possível obter isso mais tarde](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="required-permissions"></a>Permissões obrigatórias

Tem de ter permissões suficientes para registar uma aplicação no seu inquilino do Azure AD e atribuir a aplicação a uma função na sua subscrição do Azure.

### <a name="check-azure-ad-permissions"></a>Verifique as permissões do Azure AD

1. Selecione **Azure Active Directory**.
1. Tenha em atenção de sua função. Se tiver o **utilizador** função, deve certificar-se de que não-administradores podem registar aplicações.

   ![Encontre a sua função. Se for um utilizador, certifique-se de não-administradores podem registar aplicações](./media/howto-create-service-principal-portal/view-user-info.png)

1. Selecione **definições de utilizador**.
1. Verifique os **registos das aplicações** definição. Este valor apenas pode ser definido por um administrador. Se definido como **Sim**, qualquer utilizador no inquilino do Azure AD pode registar uma aplicação.

Se a definição dos registos da aplicação estiver definido como **não**, apenas os utilizadores com uma função de administrador podem registar esses tipos de aplicativos. Ver [funções disponíveis](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [permissões de função](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) para saber mais sobre as funções de administrador disponíveis e as permissões específicas no Azure AD, que são fornecidas para cada função. Se a sua conta está atribuída à função de utilizador, mas a definição de registo de aplicação está limitada a utilizadores de administração, peça ao seu administrador para qualquer um atribuir a uma das funções de administrador que podem criar e gerir todos os aspetos de registos de aplicações ou para permitir que os utilizadores Registe aplicações.

### <a name="check-azure-subscription-permissions"></a>Verifique as permissões de subscrição do Azure

Na sua subscrição do Azure, a conta tem de ter `Microsoft.Authorization/*/Write` acesso para atribuir uma aplicação AD a uma função. Esta ação é concedida através das funções [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso dos Utilizadores](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se a sua conta é atribuída para o **contribuinte** função, não tem permissão adequada. Receber um erro ao tentar atribuir o principal de serviço a uma função.

Para verificar as permissões de subscrição:

1. Selecione a sua conta no canto superior direito e selecione **... -> as minhas permissões**.

   ![Selecione a sua conta e as suas permissões de utilizador](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Na lista pendente, selecione a subscrição que pretende criar o serviço principal no. Em seguida, selecione **clique aqui para acesso completo de ver os detalhes para esta subscrição**.

   ![Selecione a subscrição que pretende criar o serviço principal no](./media/howto-create-service-principal-portal/view-details.png)

1. Selecione **atribuições de funções** para ver as funções atribuídas e determinar se tem as permissões adequadas para atribuir uma aplicação AD a uma função. Caso contrário, peça ao administrador de subscrição para o adicionar a função de administrador de acesso do utilizador. Na imagem seguinte, o utilizador tem atribuída à função de proprietário, o que significa que o utilizador tem as permissões adequadas.

   ![Este exemplo mostra que o utilizador tem atribuída à função de proprietário](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passos Seguintes

* Para configurar uma aplicação multi-inquilino, consulte [Guia do programador para autorização com a API do Azure Resource Manager](../../azure-resource-manager/resource-manager-api-authentication.md).
* Para saber mais sobre como especificar políticas de segurança, veja [controlo de acesso baseado em funções do Azure](../../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que pode ser concedido ou negado para os utilizadores, consulte [operações de fornecedor de recursos do Azure Resource Manager](../../role-based-access-control/resource-provider-operations.md).
