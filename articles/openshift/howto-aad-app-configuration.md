---
title: Integração do Diretório Ativo Azure para o Azure Red Hat OpenShift
description: Aprenda a criar um grupo de segurança e utilizador de AD Azure para testar aplicações no seu cluster OpenShift do Microsoft Azure Red Hat.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: a2eade6c5a9c826d28d435a09861ba58463ae8c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280537"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integração do Diretório Ativo Azure para o Azure Red Hat OpenShift

Se ainda não criou um inquilino azure Ative Directory (Azure AD), siga as instruções em [Create a Azure AD tenant for Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

O Microsoft Azure Red Hat OpenShift necessita de permissões para executar tarefas em nome do seu cluster. Se a sua organização ainda não tiver um utilizador de Anúncios Azure, um grupo de segurança Azure AD ou um registo de aplicações Azure AD para usar como diretor de serviço, siga estas instruções para criá-las.

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo utilizador do Azure Active Directory

No [portal Azure,](https://portal.azure.com)certifique-se de que o seu inquilino aparece sob o seu nome de utilizador no canto superior direito do portal:

![Screenshot do portal com inquilino](./media/howto-create-tenant/tenant-callout.png) listado no topo direito Se o inquilino errado for apresentado, clique no seu nome de utilizador no direito superior, em seguida, clique em **Switch Diretório**, e selecione o inquilino correto da lista **de Todos os Diretórios.**

Crie um novo utilizador de administrador global do Azure Ative Diretório para iniciar sessão no seu cluster Azure Red Hat OpenShift.

1. Vá para a lâmina [utilizadores-todos os utilizadores.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)
2. Clique em **+Novo utilizador** para abrir o painel **do Utilizador.**
3. Introduza um **Nome** para este utilizador.
4. Crie um **nome de Utilizador** com base `.onmicrosoft.com` no nome do inquilino que criou, com anexação no final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Escreva este nome de utilizador. Vai precisar dele para se inscrever no seu agrupamento.
5. Clique na **função de Diretório** para abrir o painel de papéis de diretório e selecione **administrador global** e, em seguida, clique em **Ok** na parte inferior do painel.
6. No painel **do Utilizador,** clique em **Mostrar Palavra-passe** e grave a palavra-passe temporária. Depois de assinar pela primeira vez, será solicitado a reset-lo.
7. Na parte inferior do painel, clique em **Criar** para criar o utilizador.

## <a name="create-an-azure-ad-security-group"></a>Criar um grupo de segurança Azure AD

Para conceder acesso ao cluster admin, os membros de um grupo de segurança Azure AD estão sincronizados no grupo OpenShift "osa-customer-admins". Se não for especificado, não será concedido acesso à administração do cluster.

1. Abra a lâmina dos [grupos de diretórios Ativos Azure.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)
2. Clique **em +Novo Grupo**.
3. Forneça um nome e descrição de grupo.
4. Definir **o tipo de grupo** para a **segurança**.
5. Definir **o tipo de membro** para **atribuído**.

    Adicione o utilizador Azure AD que criou no passo anterior a este grupo de segurança.

6. Clique em **Membros** para abrir o painel de **membros Select.**
7. Na lista de membros, selecione o utilizador Azure AD que criou acima.
8. Na parte inferior do portal, clique em **Select** **e,** em seguida, Criar para criar o grupo de segurança.

    Escreva o valor de identificação do grupo.

9. Quando o grupo for criado, você vai vê-lo na lista de todos os grupos. Clique no novo grupo.
10. Na página que aparece, copie o ID do **Objeto**. Vamos referir-nos a `GROUPID` este valor como no tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

> [!IMPORTANT]
> Para sincronizar este grupo com o grupo osa-cliente-administradoropens OpenShift, crie o cluster utilizando o Azure CLI. O portal Azure carece atualmente de um campo para definir este grupo.

## <a name="create-an-azure-ad-app-registration"></a>Criar uma inscrição de aplicação Azure AD

Pode criar automaticamente um cliente de registo de aplicações Azure Ative Directory (Azure `--aad-client-app-id` AD) como parte da criação do cluster, omitindo a bandeira ao `az openshift create` comando. Este tutorial mostra-lhe como criar o registo da aplicação Azure AD para a completude.

Se a sua organização ainda não tiver um registo de aplicação Azure Ative Directory (Azure AD) para usar como diretor de serviço, siga estas instruções para criar uma.

1. Abra a lâmina de [registos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) da App e clique **em +Nova inscrição.**
2. No **Registo de um** painel de candidatura, insira um nome para o seu registo de candidatura.
3. Certifique-se de que, nos **tipos de conta suportadas,** as contas neste diretório organizacional são selecionadas **apenas.** Esta é a escolha mais segura.
4. Adicionaremos um URI redirecionado mais tarde assim que soubermos o URI do cluster. Clique no botão **Register** para criar o registo de aplicação Azure AD.
5. Na página que aparece, copie o ID do **Pedido (cliente).** Vamos referir-nos a `APPID` este valor como no tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

![Screenshot da página de objeto de aplicativo](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Criar um segredo de cliente

Gere um segredo de cliente para autenticar a sua app ao Azure Ative Directory.

1. Na secção **Gerir** a página de registos da aplicação, clique em **Certificados & segredos.**
2. No painel de **certificados & segredos,** clique em **+Novo segredo de cliente.**  O **Add um painel secreto do cliente** aparece.
3. Forneça uma **descrição**.
4. O conjunto **expira** à duração que preferir, por exemplo **em 2 anos.**
5. Clique em **Adicionar** e o valor chave aparecerá na secção de **segredos** do Cliente da página.
6. Copie o valor-chave. Vamos referir-nos a `SECRET` este valor como no tutorial de [cluster Create a Azure Red Hat OpenShift.](tutorial-create-cluster.md)

![Screenshot dos certificados e segredos painel](./media/howto-create-tenant/create-key.png)

Para mais informações sobre objetos de aplicação Azure, consulte [os objetos principais de aplicação e serviço no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals).

Para mais detalhes sobre a criação de uma nova aplicação Azure AD, consulte Registar uma aplicação com o Ponto final do [Azure Ative Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app).

## <a name="add-api-permissions"></a>Adicionar permissões de APIs

[//]: # (Não mude para o Microsoft Graph. Não funciona com o Microsoft Graph.)
1. Na secção **Gerir** clique em **permissões API**
2. Clique em **Adicionar permissão** e selecione **Azure Ative Directory Graph** e, em seguida, **permissões delegadas**.
> [!NOTE]
> Certifique-se de que selecionou o "Azure Ative Directory Graph" e não o azulejo "Microsoft Graph".

3. Expandir o **Utilizador** na lista abaixo e ativar a permissão **User.Read.** Se **o Utilizador.Ler** estiver ativado por defeito, certifique-se de que é a permissão do **Gráfico de Diretório Ativo Azure** **User.Read**.
4. Percorra e selecione **permissões**de aplicação .
5. Expandir **o Diretório** na lista abaixo e ativar **o Diretório.ReadAll**.
6. Clique em **Adicionar permissões** para aceitar as alterações.
7. O painel de permissões DaPI deve agora mostrar tanto *user.Read* como *Diretório.ReadAll*. Por favor, note o aviso em **consentimento da Administração requer uma** coluna próxima ao *Diretório.ReadAll*.
8. Se for o Administrador de *Subscrição Azure,* clique no consentimento do **Administrador grant para o Nome de *Subscrição* ** abaixo. Se não for o Administrador de *Assinatura Azure,* solicite o consentimento do seu administrador.

![Screenshot do painel de permissões DaPI. User.Read and Directy.ReadTodas as permissões adicionadas, consentimento administrativo necessário para O Diretório.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A sincronização do grupo de administradores de clusters só funcionará após a concessão do consentimento. Você verá um círculo verde com uma marca de verificação e uma mensagem "Granted for *Subscription Name"* na coluna *de consentimento do Administrador.*

Para mais informações sobre a gestão de administradores e outras funções, consulte Adicionar ou alterar administradores de [subscrição do Azure](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).

## <a name="resources"></a>Recursos

* [Aplicações e objetos principais de serviço no Diretório Ativo azure](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)
* [Início rápido: Registar uma aplicação com o ponto de final do Azure Active Directory v1.0](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)

## <a name="next-steps"></a>Passos seguintes

Se já conheceu todos os [pré-requisitos do Azure Red Hat OpenShift,](howto-setup-environment.md)está pronto para criar o seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)
