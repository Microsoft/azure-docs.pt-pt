---
title: Criar um aplicativo do Azure AD & entidade de serviço no portal
titleSuffix: Microsoft identity platform
description: Crie uma nova app e serviço de diretório Ativo Azure para gerir o acesso a recursos com controlo de acesso baseado em papéis no Azure Resource Manager.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: tomfitz
ms.custom: aaddev, seoapril2019, identityplatformtop40
ms.openlocfilehash: 0d3e1e10120dce404f0fdfe781661c4c169ae00a
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697222"
---
# <a name="how-to-use-the-portal-to-create-an-azure-ad-application-and-service-principal-that-can-access-resources"></a>Como: usar o portal para criar um aplicativo do Azure AD e uma entidade de serviço que pode acessar recursos

Este artigo mostra como criar um novo aplicativo Azure Active Directory (Azure AD) e uma entidade de serviço que pode ser usada com o controle de acesso baseado em função. Quando você tem um código que precisa acessar ou modificar recursos, você pode criar uma identidade para o aplicativo. Esta identidade é conhecida como um principal de serviço. Em seguida, você pode atribuir as permissões necessárias à entidade de serviço. Este artigo mostra como usar o portal para criar a entidade de serviço. Ele se concentra em um aplicativo de locatário único em que o aplicativo deve ser executado em apenas uma organização. Normalmente, você usa aplicativos de locatário único para aplicativos de linha de negócios que são executados em sua organização.

> [!IMPORTANT]
> Em vez de criar uma entidade de serviço, considere o uso de identidades gerenciadas para recursos do Azure para sua identidade de aplicativo. Se o seu código for executado em um serviço que dá suporte a identidades gerenciadas e a recursos de acesso que dão suporte à autenticação do Azure AD, as identidades gerenciadas são uma opção melhor para você. Para saber mais sobre identidades gerenciadas para recursos do Azure, incluindo quais serviços atualmente dão suporte a ele, consulte [o que são identidades gerenciadas para recursos do Azure?](../managed-identities-azure-resources/overview.md).

## <a name="create-an-azure-active-directory-application"></a>Criar um aplicativo Azure Active Directory

Vamos ir diretamente para a criação da identidade. Se você encontrar um problema, verifique as [permissões necessárias](#required-permissions) para garantir que sua conta possa criar a identidade.

1. Entre em sua conta do Azure por meio do [portal do Azure](https://portal.azure.com).
1. Selecione **Azure Active Directory**.
1. Selecione **Registos das aplicações**.
1. Selecione **novo registro**.
1. Nomeie o aplicativo. Selecione um tipo de conta com suporte, que determina quem pode usar o aplicativo. Em **URI de redirecionamento**, selecione **Web** para o tipo de aplicativo que você deseja criar. Insira o URI para o qual o token de acesso é enviado. Você não pode criar credenciais para um [aplicativo nativo](../manage-apps/application-proxy-configure-native-client-application.md). Você não pode usar esse tipo para um aplicativo automatizado. Depois de definir os valores, selecione **registrar**.

   ![Digite um nome para seu aplicativo](./media/howto-create-service-principal-portal/create-app.png)

Você criou seu aplicativo do Azure AD e a entidade de serviço.

## <a name="assign-the-application-to-a-role"></a>Atribuir o aplicativo a uma função

Para acessar recursos em sua assinatura, você deve atribuir o aplicativo a uma função. Decida qual função oferece as permissões corretas para o aplicativo. Para saber mais sobre as funções disponíveis, veja [RBAC: funções incorporadas](../../role-based-access-control/built-in-roles.md).

Você pode definir o escopo no nível da assinatura, do grupo de recursos ou do recurso. As permissões são herdadas para níveis inferiores de âmbito. Por exemplo, adicionar um aplicativo à função leitor para um grupo de recursos significa que ele pode ler o grupo de recursos e todos os recursos que ele contém.

1. Na portal do Azure, selecione o nível de escopo ao qual você deseja atribuir o aplicativo. Por exemplo, para atribuir uma função no escopo da assinatura, procure e selecione **assinaturas**, ou selecione **assinaturas** na **Home** Page.

   ![Por exemplo, atribua uma função no escopo da assinatura](./media/howto-create-service-principal-portal/select-subscription.png)

1. Selecione a assinatura específica à qual atribuir o aplicativo.

   ![Selecionar assinatura para atribuição](./media/howto-create-service-principal-portal/select-one-subscription.png)

   Se você não vir a assinatura que está procurando, selecione **filtro de assinaturas globais**. Verifique se a assinatura desejada está selecionada para o Portal.

1. Selecione **Controlo de acesso (IAM)** .
1. Selecione **adicionar atribuição de função**.
1. Selecione a função que você deseja atribuir ao aplicativo. Por exemplo, para permitir que o aplicativo execute ações como **reinicializar**, **Iniciar** e **parar** instâncias, selecione a função **colaborador** .  Leia mais sobre as [funções disponíveis](../../role-based-access-control/built-in-roles.md) por padrão, os aplicativos do Azure ad não são exibidos nas opções disponíveis. Para localizar seu aplicativo, procure o nome e selecione-o.

   ![Selecione a função a ser atribuída ao aplicativo](./media/howto-create-service-principal-portal/select-role.png)

1. Selecione **guardar** para concluir a atribuir a função. Você vê seu aplicativo na lista de usuários atribuídos a uma função para esse escopo.

Sua entidade de serviço está configurada. Você pode começar a usá-lo para executar seus scripts ou aplicativos. A próxima seção mostra como obter valores que são necessários ao entrar de forma programática.

## <a name="get-values-for-signing-in"></a>Obter valores para entrar

Ao entrar de forma programática, você precisa passar a ID de locatário com sua solicitação de autenticação. Você também precisa da ID para seu aplicativo e uma chave de autenticação. Para obter esses valores, utilize os seguintes passos:

1. Selecione **Azure Active Directory**.
1. Em **registros de aplicativo** no Azure AD, selecione seu aplicativo.
1. Copie a ID do diretório (locatário) e armazene-a no código do aplicativo.

    ![Copie o diretório (ID do locatário) e armazene-o no código do aplicativo](./media/howto-create-service-principal-portal/copy-tenant-id.png)

1. Copie o **ID da Aplicação** e armazene-o no código da aplicação.

   ![Copiar a ID do aplicativo (cliente)](./media/howto-create-service-principal-portal/copy-app-id.png)

## <a name="certificates-and-secrets"></a>Certificados e segredos
Os aplicativos daemon podem usar duas formas de credenciais para autenticar com o Azure AD: certificados e segredos do aplicativo.  É recomendável usar um certificado, mas você também pode criar um novo segredo do aplicativo.

### <a name="upload-a-certificate"></a>Carregar um certificado

Você pode usar um certificado existente se tiver um.  Opcionalmente, você pode criar um certificado autoassinado para fins de teste. Abra o PowerShell e execute [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) com os seguintes parâmetros para criar um certificado autoassinado no repositório de certificados do usuário em seu computador: 

```powershell
$cert=New-SelfSignedCertificate -Subject "CN=DaemonConsoleCert" -CertStoreLocation "Cert:\CurrentUser\My"  -KeyExportPolicy Exportable -KeySpec Signature
```

Exporte esse certificado para um arquivo usando o snap-in [gerenciar certificado do usuário](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) do MMC acessível no painel de controle do Windows.

Para carregar o certificado:

1. Selecione **certificados & segredos**.
1. Selecione **carregar certificado** e selecione o certificado (um certificado existente ou o certificado autoassinado que você exportou).

    ![Selecione carregar certificado e selecione aquele que você deseja adicionar](./media/howto-create-service-principal-portal/upload-cert.png)

1. Selecione **Adicionar**.

Depois de registrar o certificado com seu aplicativo no portal de registro de aplicativos, você precisa habilitar o código do aplicativo cliente para usar o certificado.

### <a name="create-a-new-application-secret"></a>Criar um novo segredo do aplicativo

Se você optar por não usar um certificado, poderá criar um novo segredo do aplicativo.

1. Selecione **certificados & segredos**.
1. Selecione os **segredos do cliente-> novo segredo do cliente**.
1. Forneça uma descrição do segredo e uma duração. Quando terminar, selecione **Adicionar**.

   Depois de salvar o segredo do cliente, o valor do segredo do cliente é exibido. Copie esse valor porque você não poderá recuperar a chave posteriormente. Você fornece o valor de chave com a ID do aplicativo para entrar como o aplicativo. Armazene o valor da chave num local onde a aplicação o possa obter.

   ![Copiar o valor secreto porque você não pode recuperá-lo mais tarde](./media/howto-create-service-principal-portal/copy-secret.png)

## <a name="configure-access-policies-on-resources"></a>Configurar políticas de acesso em recursos
Tenha em mente que talvez seja necessário configurar permissões de adição em recursos que seu aplicativo precisa acessar. Por exemplo, você também deve [atualizar as políticas de acesso de um cofre de chaves](/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) para dar ao aplicativo acesso a chaves, segredos ou certificados.  

1. No [portal do Azure](https://portal.azure.com), navegue até o cofre de chaves e selecione **políticas de acesso**.  
1. Selecione **Adicionar política de acesso**e, em seguida, selecione as permissões de chave, segredo e certificado que você deseja conceder ao seu aplicativo.  Selecione a entidade de serviço que você criou anteriormente.
1. Selecione **Adicionar** para adicionar a política de acesso e, em seguida, **salvar** para confirmar suas alterações.
    ![Adicionar](./media/howto-create-service-principal-portal/add-access-policy.png) de política de acesso

## <a name="required-permissions"></a>Permissões obrigatórias

Você deve ter permissões suficientes para registrar um aplicativo com seu locatário do Azure AD e atribuir o aplicativo a uma função em sua assinatura do Azure.

### <a name="check-azure-ad-permissions"></a>Verificar permissões do Azure AD

1. Selecione **Azure Active Directory**.
1. Anote sua função. Se você tiver a função de **usuário** , deverá garantir que os não administradores possam registrar aplicativos.

   ![Localize sua função. Se você for um usuário, verifique se os não-administradores podem registrar aplicativos](./media/howto-create-service-principal-portal/view-user-info.png)

1. No painel esquerdo, selecione **configurações de usuário**.
1. Verifique a configuração de **registros de aplicativo** . Esse valor só pode ser definido por um administrador. Se definido como **Sim**, qualquer usuário no locatário do Azure AD pode registrar um aplicativo.

Se a configuração de registros do aplicativo for definida como **não**, somente os usuários com uma função de administrador poderão registrar esses tipos de aplicativos. Consulte [funções](../users-groups-roles/directory-assign-admin-roles.md#available-roles) e [permissões de função](../users-groups-roles/directory-assign-admin-roles.md#role-permissions) disponíveis para saber mais sobre as funções de administrador disponíveis e as permissões específicas no Azure AD que são dadas a cada função. Se sua conta for atribuída à função de usuário, mas a configuração de registro do aplicativo for limitada a usuários administradores, peça ao administrador para atribuí-lo a uma das funções de administrador que podem criar e gerenciar todos os aspectos de registros do aplicativo ou para permitir que os usuários registrar aplicativos.

### <a name="check-azure-subscription-permissions"></a>Verificar as permissões de assinatura do Azure

Em sua assinatura do Azure, sua conta deve ter `Microsoft.Authorization/*/Write` acesso para atribuir um aplicativo do AD a uma função. Esta ação é concedida através das funções [Proprietário](../../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso dos Utilizadores](../../role-based-access-control/built-in-roles.md#user-access-administrator). Se sua conta for atribuída à função **colaborador** , você não terá a permissão adequada. Você recebe um erro ao tentar atribuir a entidade de serviço a uma função.

Para verificar suas permissões de assinatura:

1. Selecione sua conta no canto superior direito e selecione **...-> minhas permissões**.

   ![Selecione sua conta e suas permissões de usuário](./media/howto-create-service-principal-portal/select-my-permissions.png)

1. Na lista suspensa, selecione a assinatura na qual você deseja criar a entidade de serviço. Em seguida, selecione **clique aqui para exibir os detalhes de acesso completo para esta assinatura**.

   ![Selecione a assinatura na qual você deseja criar a entidade de serviço](./media/howto-create-service-principal-portal/view-details.png)

1. Selecione **atribuições de função** para exibir suas funções atribuídas e determine se você tem permissões adequadas para atribuir um aplicativo do AD a uma função. Caso contrário, peça ao administrador da assinatura para adicioná-lo à função Administrador de acesso do usuário. Na imagem a seguir, o usuário é atribuído à função proprietário, o que significa que o usuário tem as permissões adequadas.

   ![Este exemplo mostra que o usuário está atribuído à função de proprietário](./media/howto-create-service-principal-portal/view-user-role.png)

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre como especificar políticas de segurança, confira [controle de acesso baseado em função do Azure](../../role-based-access-control/role-assignments-portal.md).  
* Para obter uma lista de ações disponíveis que podem ser concedidas ou negadas a usuários, consulte [Azure Resource Manager operações do provedor de recursos](../../role-based-access-control/resource-provider-operations.md).
