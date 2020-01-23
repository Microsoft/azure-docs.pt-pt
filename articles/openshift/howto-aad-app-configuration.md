---
title: Integração do Azure Active Directory para o Azure Red Hat OpenShift
description: Saiba como criar um grupo de segurança do Azure AD e um usuário para testar aplicativos em seu Microsoft Azure cluster Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: 6e2437fadb743706d4f4215bbcbab8616817de5f
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545624"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integração do Azure Active Directory para o Azure Red Hat OpenShift

Se você ainda não criou um locatário do Azure Active Directory (Azure AD), siga as instruções em [criar um locatário do Azure ad para o Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

Microsoft Azure o Red Hat OpenShift precisa de permissões para executar tarefas em nome do cluster. Se sua organização ainda não tiver um usuário do Azure AD, um grupo de segurança do Azure AD ou um registro de aplicativo do Azure AD para usar como a entidade de serviço, siga estas instruções para criá-los.

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo utilizador do Azure Active Directory

No [portal do Azure](https://portal.azure.com), verifique se seu locatário aparece sob seu nome de usuário no canto superior direito do portal:

![captura de tela do portal com locatário listado no canto superior direito](./media/howto-create-tenant/tenant-callout.png) se o locatário errado for exibido, clique no nome de usuário no canto superior direito, clique em **alternar diretório**e selecione o locatário correto na lista **todos os diretórios** .

Crie um novo usuário administrador global Azure Active Directory para entrar no cluster do Azure Red Hat OpenShift.

1. Vá para a folha [usuários – todos os usuários](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) .
2. Clique em **+ novo usuário** para abrir o painel **usuário** .
3. Insira um **nome** para este usuário.
4. Crie um **nome de usuário** com base no nome do locatário que você criou, com `.onmicrosoft.com` acrescentados no final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Anote este nome de usuário. Você precisará dela para entrar no cluster.
5. Clique em **função de diretório** para abrir o painel função de diretório e selecione **administrador global** e clique em **OK** na parte inferior do painel.
6. No painel **usuário** , clique em **Mostrar senha** e registre a senha temporária. Depois de entrar na primeira vez, você será solicitado a redefini-la.
7. Na parte inferior do painel, clique em **criar** para criar o usuário.

## <a name="create-an-azure-ad-security-group"></a>Criar um grupo de segurança do Azure AD

Para conceder acesso de administrador de cluster, as associações em um grupo de segurança do Azure AD são sincronizadas no grupo OpenShift "OSA-Customer-admins". Se não for especificado, nenhum acesso de administrador de cluster será concedido.

1. Abra a folha [grupos de Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) .
2. Clique em **+ novo grupo**.
3. Forneça um nome de grupo e uma descrição.
4. Defina o **tipo de grupo** como **segurança**.
5. Defina o **tipo de associação** como **atribuído**.

    Adicione o usuário do Azure AD que você criou na etapa anterior a esse grupo de segurança.

6. Clique em **Membros** para abrir o painel **selecionar Membros** .
7. Na lista de membros, selecione o usuário do Azure AD que você criou acima.
8. Na parte inferior do portal, clique em **selecionar** e em **criar** para criar o grupo de segurança.

    Anote o valor da ID do grupo.

9. Quando o grupo for criado, você o verá na lista de todos os grupos. Clique no novo grupo.
10. Na página exibida, copie a **ID do objeto**. Iremos nos referir a esse valor como `GROUPID` no tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

> [!IMPORTANT]
> Para sincronizar esse grupo com o grupo OSA-Customer-admins OpenShift, crie o cluster usando o CLI do Azure. O portal do Azure atualmente não tem um campo para definir esse grupo.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registro de aplicativo do Azure AD

Você pode criar automaticamente um cliente de registro de aplicativo Azure Active Directory (Azure AD) como parte da criação do cluster omitindo o sinalizador `--aad-client-app-id` para o comando `az openshift create`. Este tutorial mostra como criar o registro de aplicativo do Azure AD para fins de integridade.

Se sua organização ainda não tiver um registro de aplicativo Azure Active Directory (Azure AD) para usar como uma entidade de serviço, siga estas instruções para criar um.

1. Abra a [folha registros de aplicativo](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registro**.
2. No painel **registrar um aplicativo** , insira um nome para o registro do aplicativo.
3. Verifique se em **tipos de conta com suporte** que **contas neste diretório organizacional somente** está selecionado. Essa é a opção mais segura.
4. Adicionaremos um URI de redirecionamento mais tarde, uma vez que soubermos o URI do cluster. Clique no botão **registrar** para criar o registro do aplicativo do Azure AD.
5. Na página exibida, copie a **ID do aplicativo (cliente)** . Iremos nos referir a esse valor como `APPID` no tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Captura de tela da página de objeto do aplicativo](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Gere um segredo do cliente para autenticar seu aplicativo para Azure Active Directory.

1. Na seção **gerenciar** da página registros do aplicativo, clique em **certificados & segredos**.
2. No painel **certificados & segredos** , clique em **+ novo segredo do cliente**.  O painel **Adicionar um segredo do cliente** é exibido.
3. Forneça uma **Descrição**.
4. Set **expirará** para a duração que você preferir, por exemplo, **em 2 anos**.
5. Clique em **Adicionar** e o valor da chave será exibido na seção **segredos do cliente** da página.
6. Copie o valor da chave. Iremos nos referir a esse valor como `SECRET` no tutorial [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) .

![Captura de tela do painel certificados e segredos](./media/howto-create-tenant/create-key.png)

Para obter mais informações sobre objetos Aplicativo Azure, consulte [objetos de aplicativo e entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para obter detalhes sobre como criar um novo aplicativo do Azure AD, consulte [registrar um aplicativo com o ponto de extremidade Azure Active Directory v 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Adicionar permissões de API

1. Na seção **gerenciar** , clique em **permissões de API**.
2. Clique em **adicionar permissão** e selecione **Azure Active Directory grafo** e, em seguida, **permissões delegadas**. 
3. Expanda **usuário** na lista abaixo e habilite a permissão **User. Read** . Se o **User. Read** estiver habilitado por padrão, certifique-se de que é o usuário de permissão **Azure Active Directory Graph** **. Read**, *não* o **Microsoft Graph** permissão **User. Read**.
4. Role para cima e selecione **permissões de aplicativo**.
5. Expanda **diretório** na lista abaixo e habilite o **diretório. ReadAll**
6. Clique em **adicionar permissões** para aceitar as alterações.
7. O painel de permissões de API agora deve mostrar *User. Read* e *Directory. ReadAll*. Observe o aviso na coluna **obrigatório consentimento do administrador** ao lado de *Directory. ReadAll*.
8. Se você for o *administrador da assinatura do Azure*, clique em **conceder consentimento do administrador para o *nome da assinatura***  abaixo. Se você não for o *administrador da assinatura do Azure*, solicite o consentimento do seu administrador.
![captura de tela do painel de permissões da API. Permissões User. Read e Directory. ReadAll adicionadas, consentimento de administrador necessário para o diretório. ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A sincronização do grupo de administradores de cluster só funcionará depois que o consentimento tiver sido concedido. Você verá um círculo verde com uma marca de seleção e uma mensagem "concedida para o *nome da assinatura*" na coluna *consentimento do administrador obrigatório* .

Para obter detalhes sobre como gerenciar administradores e outras funções, consulte [Adicionar ou alterar administradores de assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Recursos

* [Aplicativos e objetos de entidade de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Início rápido: registrar um aplicativo com o ponto de extremidade do Azure Active Directory v 1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Passos seguintes

Se você tiver atendido todos os [pré-requisitos do Azure Red Hat OpenShift](howto-setup-environment.md), estará pronto para criar seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
