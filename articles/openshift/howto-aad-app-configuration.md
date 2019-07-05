---
title: Integração do Active Directory do Azure para a Azure Red Hat OpenShift | Documentos da Microsoft
description: Saiba como criar um grupo de segurança do Azure AD e o utilizador para fins de teste de aplicações no seu cluster do Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/13/2019
ms.openlocfilehash: b79efa6ee1f4c052a0037a971fc36d8a9ae0ce58
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67458709"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integração do Active Directory do Azure para a Azure Red Hat OpenShift

Se ainda não tiver criado um inquilino do Azure Active Directory (Azure AD), siga as indicações [criar um inquilino do Azure AD para a Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

Microsoft Azure Red Hat OpenShift necessita de permissões para executar tarefas em nome do seu cluster. Se sua organização ainda não incluir um utilizador, grupo de segurança do Azure AD ou um registo de aplicação do Azure AD para utilizar como o principal de serviço, siga estas instruções para criá-los.

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo utilizador do Azure Active Directory

Na [portal do Azure](https://portal.azure.com), certifique-se de que o seu inquilino é apresentado em seu nome de utilizador no canto superior direito do portal:

![Captura de ecrã do portal com o inquilino listado no canto superior direito](./media/howto-create-tenant/tenant-callout.png) se o inquilino errado for apresentado, clique em seu nome de utilizador no canto superior direito, em seguida, clique em **trocar diretório**e selecione o inquilino correto do **todos os Diretórios** lista.

Crie um novo utilizador de administrador global do Azure Active Directory para iniciar sessão no seu cluster do Azure Red Hat OpenShift.

1. Vá para o [os usuários de todos os utilizadores](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) painel.
2. Clique em **+ novo utilizador** para abrir o **utilizador** painel.
3. Introduza um **nome** para este utilizador.
4. Criar uma **nome de utilizador** com base no nome do inquilino que criou, com `.onmicrosoft.com` anexada ao final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Anote este nome de utilizador. Precisará das mesmas para iniciar sessão no seu cluster.
5. Clique em **função de diretório** para abrir o painel de função de diretório e selecione **Administrador Global** e, em seguida, clique em **Ok** na parte inferior do painel.
6. Na **usuário** painel, clique em **mostrar palavra-passe** e registe a palavra-passe temporária. Depois de iniciar sessão pela primeira vez, será solicitado a repô-lo.
7. Na parte inferior do painel, clique em **criar** para criar o utilizador.

## <a name="create-an-azure-ad-security-group"></a>Criar um grupo de segurança do Azure AD

Para conceder acesso de administrador de cluster, as associações num grupo de segurança do Azure AD são sincronizadas nos OpenShift grupo "osa--administradores de clientes". Se não for especificado, sem acesso de administrador de cluster vai ser concedido.

1. Abra o [grupos do Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups) painel.
2. Clique em **+ novo grupo**.
3. Forneça um nome de grupo e a descrição.
4. Definir **tipo de grupo** ao **segurança**.
5. Definir **tipo de associação** ao **atribuído**.

    Adicione o utilizador do Azure AD que criou no passo anterior para este grupo de segurança.

6. Clique em **membros** para abrir o **selecionar membros do** painel.
7. Na lista de membros, selecione o utilizador do Azure AD que criou acima.
8. Na parte inferior do portal, clique em **selecionar** e, em seguida **criar** para criar o grupo de segurança.

    Anote o valor de ID de grupo.

9. Quando o grupo é criado, verá-lo na lista de todos os grupos. Clique no novo grupo.
10. Na página que aparece, copie os **ID de objeto**. Nós nos referiremos a este valor como `GROUPID` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registo de aplicação do Azure AD

Pode criar automaticamente um cliente de registo de aplicações do Azure Active Directory (Azure AD) como parte da criação do cluster omitindo os `--aad-client-app-id` Sinalizar para o `az openshift create` comando. Este tutorial mostra-lhe como criar o registo de aplicações do Azure AD para fornecer uma visão completa.

Se sua organização ainda não tiver um registo de aplicações do Azure Active Directory (Azure AD) para utilizar como um principal de serviço, siga estas instruções para criar um.

1. Abra o [painel de registos de aplicação](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) e clique em **+ novo registo**.
2. Na **registar uma aplicação** painel, introduza um nome para o registo de aplicação.
3. Certifique-se de que, em **tipos de conta suportados** que **contas neste diretório organizacional apenas** está selecionada. Esta é a opção mais segura.
4. Adicionaremos um URI de redirecionamento mais tarde quando soubermos o URI do cluster. Clique nas **registar** botão para criar o registo de aplicação do Azure AD.
5. Na página que aparece, copie os **ID da aplicação (cliente)** . Nós nos referiremos a este valor como `APPID` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

![Captura de ecrã da página de objeto de aplicação](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Criar um segredo do cliente

Gere um segredo do cliente para autenticar a sua aplicação ao Azure Active Directory.

1. Na **Manage** secção da página de registos da aplicação, clique em **certificados e segredos**.
2. Sobre o **certificados e segredos** painel, clique em **+ novo segredo do cliente**.  O **adicionar um segredo de cliente** é apresentado o painel.
3. Fornecer um **Descrição**.
4. Definir **Expires** para a duração de sua preferência, por exemplo **dentro de 2 anos**.
5. Clique em **Add** e o valor da chave aparecerá na **segredos de cliente** secção da página.
6. Copie o valor da chave. Nós nos referiremos a este valor como `SECRET` no [criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md) tutorial.

![Captura de ecrã do painel de certificados e segredos](./media/howto-create-tenant/create-key.png)

Para obter mais informações sobre objetos de aplicação do Azure, consulte [aplicativos e objetos de principal de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para obter detalhes sobre como criar uma nova aplicação do Azure AD, consulte [registar uma aplicação com o ponto de final do Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Adicionar permissões de API

1. Na **Manage** secção clique **permissões de API**.
2. Clique em **adicionar permissão** e selecione **Azure Active Directory Graph** , em seguida, **permissões delegadas**
3. Expanda **usuário** na lista abaixo e certifique-se **User.Read** está ativada.
4. Role e selecione **permissões de aplicação**.
5. Expanda **diretório** na lista abaixo e ativar **Directory.ReadAll**
6. Clique em **adicionar permissões** para aceitar as alterações.
7. O painel de permissões de API deve agora mostrar ambos *User.Read* e *Directory.ReadAll*. Tenha em atenção o aviso no **consentimento de administrador necessário** coluna junto a *Directory.ReadAll*.
8. Se estiver a *administrador da subscrição do Azure*, clique em **conceder autorização de administrador para *nome da subscrição***  abaixo. Se não for o *administrador da subscrição do Azure*, solicitar o consentimento do seu administrador.
![Captura de ecrã do painel de permissões de API. Permissões User.Read e Directory.ReadAll adicionadas, o consentimento de administrador necessária para Directory.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> Sincronização do grupo de administradores de cluster irá funcionar apenas depois de consentimento foi concedido. Verá um círculo verde com uma marca de verificação e uma mensagem "para o *nome da subscrição*" no *consentimento de administrador necessário* coluna.

Para obter detalhes sobre a gestão de administradores e outras funções, consulte [adicionar ou alterar os administradores de subscrição do Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Recursos

* [Aplicativos e objetos de principal de serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Quickstart: Registar uma aplicação com o ponto de final de versão 1.0 do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Passos Seguintes

Se tiver cumprido todos os [pré-requisitos do Azure Red Hat OpenShift](howto-setup-environment.md), está pronto para criar seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
