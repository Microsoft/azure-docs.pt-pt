---
title: Integração do Azure Ative Directory para o Azure Red Hat OpenShift
description: Saiba como criar um grupo de segurança Azure AD e utilizador para testar aplicações no seu cluster Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: f0bf28d61d4c9ad95a485fb4b60e370c16ace16c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633330"
---
# <a name="azure-active-directory-integration-for-azure-red-hat-openshift"></a>Integração do Azure Ative Directory para o Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 será retirado a 30 de junho de 2022. O apoio à criação de novos clusters Azure Red Hat OpenShift 3.11 continua até 30 de novembro de 2020. Após a reforma, os restantes clusters Azure Red Hat OpenShift 3.11 serão encerrados para evitar vulnerabilidades de segurança.
> 
> Siga este guia para [criar um cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Se tiver questões específicas, [contacte-nos.](mailto:arofeedback@microsoft.com)

Se ainda não criou um inquilino do Azure Ative Directory (Azure AD), siga as instruções em [Criar um inquilino AZure AD para o Azure Red Hat OpenShift](howto-create-tenant.md) antes de continuar com estas instruções.

O Microsoft Azure Red Hat OpenShift necessita de permissões para executar tarefas em nome do seu cluster. Se a sua organização ainda não tiver um utilizador AZURE AD, um grupo de segurança Azure AD ou um registo de aplicações AD AZure para usar como diretor de serviço, siga estas instruções para criá-las.

## <a name="create-a-new-azure-active-directory-user"></a>Criar um novo utilizador do Azure Active Directory

No [portal Azure,](https://portal.azure.com)certifique-se de que o seu inquilino aparece sob o seu nome de utilizador no direito superior do portal:

![Screenshot do portal com inquilino listado em cima à direita ](./media/howto-create-tenant/tenant-callout.png) Se o inquilino errado for apresentado, clique no nome de utilizador no direito superior, clique em Switch **Directory**, e selecione o inquilino correto da lista **De Todos os Diretórios.**

Crie um novo utilizador do Azure Ative Directory 'Owner' para iniciar sessão no seu cluster Azure Red Hat OpenShift.

1. Aceda à lâmina [dos utilizadores-Todos os utilizadores.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)
2. Clique **em +Novo utilizador** para abrir o painel de **utilização.**
3. Introduza um **Nome** para este utilizador.
4. Crie um **nome de Utilizador** com base no nome do inquilino que criou, com  `.onmicrosoft.com` apêndice no final. Por exemplo, `yourUserName@yourTenantName.onmicrosoft.com`. Escreva este nome de utilizador. Vai precisar dele para se inscrever no seu agrupamento.
5. Clique na **função do Diretório** para abrir o painel de funções do diretório e selecione **Proprietário** e, em seguida, clique em **Ok** na parte inferior do painel.
6. No painel **de utilização,** clique em **Mostrar Palavra-Passe** e grave a palavra-passe temporária. Depois de assinar pela primeira vez, será solicitado para repor.
7. Na parte inferior do painel, clique em **Criar** para criar o utilizador.

## <a name="create-an-azure-ad-security-group"></a>Criar um grupo de segurança do Azure AD

Para conceder acesso a administradores de clusters, os membros de um grupo de segurança Azure AD são sincronizados no grupo OpenShift "osa-customer-admins". Se não for especificado, não será concedido qualquer acesso a administrador de cluster.

1. Abra a lâmina dos [grupos Azure Ative.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/GroupsManagementMenuBlade/AllGroups)
2. Clique **em +Novo Grupo.**
3. Forneça um nome de grupo e descrição.
4. Definir **tipo de grupo** para **segurança**.
5. Definir **o tipo de membro** para **atribuído**.

    Adicione o utilizador Azure AD que criou no passo anterior a este grupo de segurança.

6. Clique **em Membros** para abrir o painel **de membros Select.**
7. Na lista de membros, selecione o utilizador Azure AD que criou acima.
8. Na parte inferior do portal, clique em **Select** e, em seguida, **Criar** para criar o grupo de segurança.

    Anota o valor de ID do grupo.

9. Quando o grupo for criado, verá na lista de todos os grupos. Clique no novo grupo.
10. Na página que aparece, copie o ID do **objeto.** Vamos referir-nos a este valor como `GROUPID` no tutorial de cluster [OpenShift do Chapéu Vermelho Azure.](tutorial-create-cluster.md)

> [!IMPORTANT]
> Para sincronizar este grupo com o grupo Osa-customer-admins OpenShift, crie o cluster utilizando o CLI Azure. O portal Azure não tem atualmente um campo para definir este grupo.

## <a name="create-an-azure-ad-app-registration"></a>Criar um registo de aplicativo AD Azure

Pode criar automaticamente um cliente de registo de aplicações Azure Ative (Azure AD) como parte da criação do cluster, omitindo a `--aad-client-app-id` bandeira ao `az openshift create` comando. Este tutorial mostra-lhe como criar o registo da app Azure AD para a sua completude.

Se a sua organização ainda não tiver um registo de aplicações Azure Ative (Azure AD) para usar como diretor de serviço, siga estas instruções para criar uma.

1. Abra a lâmina de [registos](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) da App e clique **em +Novo registo.**
2. No **Registo de um** painel de candidatura, insira um nome para o seu registo de inscrição.
3. Certifique-se de que, nos tipos de conta **suportados, as contas neste diretório organizacional apenas** são selecionadas.  Esta é a escolha mais segura.
4. Adicionaremos um URI de redirecionamento mais tarde assim que soubermos o URI do cluster. Clique no botão **Registar** para criar o registo de inscrição Azure AD.
5. Na página que aparece, copie o **ID da Aplicação (cliente).** Vamos referir-nos a este valor como `APPID` no tutorial de cluster [OpenShift do Chapéu Vermelho Azure.](tutorial-create-cluster.md)

![Screenshot da página do objeto de aplicação](./media/howto-create-tenant/get-app-id.png)

### <a name="create-a-client-secret"></a>Criar um segredo de cliente

Gere um segredo de cliente para autenticar a sua aplicação para o Azure Ative Directory.

1. Na secção **Gerir** a página de registos da aplicação, clique em **Certificados & segredos.**
2. No **painel de segredos & Certificados,** clique **em +Novo segredo de cliente.**  O **painel secreto Add a cliente** aparece.
3. Fornecer uma **descrição**.
4. O **conjunto expira** à duração que preferir, por **exemplo, em 2 anos**.
5. Clique **em Adicionar** e o valor chave aparecerá na secção **segredos** do Cliente da página.
6. Copie o valor da chave. Vamos referir-nos a este valor como `SECRET` no tutorial de cluster [OpenShift do Chapéu Vermelho Azure.](tutorial-create-cluster.md)

![Screenshot do painel de certificados e segredos](./media/howto-create-tenant/create-key.png)

Para obter mais informações sobre os Objetos de Aplicação Azure, consulte [os objetos principais de aplicação e serviço no Diretório Ativo Azure.](../active-directory/develop/app-objects-and-service-principals.md)

Para obter mais informações sobre a criação de uma nova aplicação AD Azure, consulte [Registar uma aplicação com o ponto final Azure Ative Directory v1.0](../active-directory/develop/quickstart-register-app.md).

## <a name="add-api-permissions"></a>Adicionar permissões de APIs

[//]: # (Não altere para o Gráfico do Microsoft. Não funciona com o Microsoft Graph.)
1. Na secção **Gerir** clique nas **permissões de API**
2. Clique **em Adicionar permissão** e selecione **Azure Ative Directory Graph** e depois **permissões delegadas**.
> [!NOTE]
> Certifique-se de que selecionou o "Azure Ative Directory Graph" e não o azulejo "Microsoft Graph".

3. Expandir o **Utilizador** na lista abaixo e ativar a permissão **User.Read.** Se **o Utilizador.Ler** estiver ativado por predefinição, certifique-se de que é a permissão do **Azure Ative Directory Graph** **User.Read**.
4. Desloque-se e selecione **permissões de aplicação**.
5. Expandir o **Diretório** na lista abaixo e ativar o **Diretório.ReadAll**.
6. Clique **em Adicionar permissões** para aceitar as alterações.
7. O painel de permissões da API deve agora mostrar tanto *o Utilizador.Read* como *o Diretório.ReadAll*. Por favor, note o aviso na coluna **requerida pelo consentimento do Administrador** junto ao *Diretório.ReadAll*.
8. Se for o *Administrador de Subscrição Azure,* clique em **Grant a administrar o consentimento para o nome *de subscrição*** abaixo. Se não for o *Administrador de Subscrição da Azure,* solicite o consentimento do seu administrador.

![Screenshot do painel de permissões da API. User.Read and Directory.ReadTodas permissões adicionadas, consentimento administrativo necessário para o Diretório.ReadAll](./media/howto-aad-app-configuration/permissions-required.png)

> [!IMPORTANT]
> A sincronização do grupo de administradores de cluster só funcionará após a concessão do consentimento. Verá um círculo verde com uma marca de verificação e uma mensagem "Concedido para *Nome de Assinatura"* na coluna *de consentimento do Administrador.*

Para obter detalhes sobre administradores de gestão e outras funções, consulte [adicionar ou alterar administradores de subscrição do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="resources"></a>Recursos

* [Aplicações e objetos principais de serviço no Azure Ative Directory](../active-directory/develop/app-objects-and-service-principals.md)
* [Início rápido: Registar uma aplicação com o ponto de final do Azure Active Directory v1.0](../active-directory/develop/quickstart-register-app.md)

## <a name="next-steps"></a>Passos seguintes

Se já conheceu todos os [pré-requisitos do Azure Red Hat OpenShift,](howto-setup-environment.md)está pronto para criar o seu primeiro cluster!

Experimente o tutorial:
> [!div class="nextstepaction"]
> [Criar um cluster do Azure Red Hat OpenShift](tutorial-create-cluster.md)