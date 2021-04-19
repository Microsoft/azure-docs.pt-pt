---
title: Gerencie o seu Azure Ative Directory B2C
titleSuffix: Azure Active Directory B2C
description: Saiba como gerir o seu inquilino Azure Ative Directory B2C. Saiba quais as funcionalidades AD AD do Azure suportadas no Azure AD B2C, como usar funções de administrador para gerir recursos, e como adicionar contas de trabalho e utilizadores convidados ao seu inquilino Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c3ea17a4f6dc2fb5134c6ceb1ae37d25e0881365
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715520"
---
# <a name="manage-your-azure-active-directory-b2c-tenant"></a>Gerencie o seu inquilino Azure Ative Directory B2C

No Azure Ative Directory B2C (Azure AD B2C), um inquilino representa o seu diretório de utilizadores de consumidores. Cada inquilino Azure AD B2C é distinto e separado de qualquer outro inquilino Azure AD B2C. Um inquilino Azure AD B2C é diferente de um inquilino do Azure Ative Directory, que você já pode ter. Neste artigo, você aprende a gerir o seu inquilino Azure AD B2C.

## <a name="supported-azure-ad-features"></a>Funcionalidades AD suportadas

O Azure AD B2C conta com a plataforma AD AZure. As seguintes funcionalidades AZURE AD podem ser usadas no seu inquilino Azure AD B2C.

|Funcionalidade  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [Grupos](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | Os grupos podem ser usados para gerir contas administrativas e de utilizadores.| Os grupos podem ser utilizados para gerir contas administrativas. [As contas dos consumidores](user-overview.md#consumer-user) não apoiam grupos. |
| [Convidando os hóspedes de identidades externas](../active-directory//external-identities/add-users-administrator.md)| Pode convidar utilizadores convidados e configurar funcionalidades de identidades externas, como federação e iniciar sação nas contas do Facebook e Google. | Você pode convidar apenas uma conta Microsoft ou um utilizador AZure AD como um hóspede para o seu inquilino AZure AD para aceder a aplicações ou gerir inquilinos. Para [contas de consumo,](user-overview.md#consumer-user)utiliza fluxos de utilizadores AD B2C e políticas personalizadas para gerir utilizadores e iniciar súbs ou iniciar seduposições com fornecedores de identidade externos, como o Google ou o Facebook. |
| [Funções e administradores](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| Totalmente suportado para contas administrativas e de utilizador. | As funções não são suportadas nas [contas dos consumidores.](user-overview.md#consumer-user) As contas dos consumidores não têm acesso a recursos Azure.|
| [Nomes de domínio personalizados](../active-directory/roles/permissions-reference.md#) |  Você pode usar domínios personalizados Azure AD apenas para contas administrativas. | [As contas do consumidor](user-overview.md#consumer-user) podem iniciar scontabilidade com um nome de utilizador, número de telefone ou qualquer endereço de e-mail. Pode utilizar [domínios personalizados](custom-domain.md) nos seus URLs de redirecionamento.|
| [Conditional Access](../active-directory/roles/permissions-reference.md#) | Totalmente suportado para contas administrativas e de utilizador. | Um subconjunto de funcionalidades de acesso condicionado AD Ad É suportado com [contas de consumidores](user-overview.md#consumer-user) Lean como configurar [o domínio personalizado](conditional-access-user-flow.md)Azure AD B2C .|

## <a name="other-azure-resources-in-your-tenant"></a>Outros recursos Azure no seu inquilino

Num inquilino Azure AD B2C, não é possível a provisionar outros recursos Azure, tais como máquinas virtuais, aplicações web Azure ou funções Azure. Você deve criar estes recursos no seu inquilino AZure AD.

## <a name="azure-ad-b2c-accounts-overview"></a>Visão geral das contas do Azure AD B2C

Os seguintes tipos de contas podem ser criados num inquilino Azure AD B2C:

Num inquilino Azure AD B2C, existem vários tipos de contas que podem ser criadas como descrito na visão geral das contas de utilizador no artigo [Azure Ative Directory B2C.](user-overview.md)

- **Conta de trabalho** - Uma conta de trabalho pode aceder a recursos num inquilino, e com uma função de administrador, pode gerir inquilinos.
- **Conta de hóspedes** - Uma conta de hóspedes só pode ser uma conta Microsoft ou um utilizador do Azure Ative Directory que pode ser usado para aceder a aplicações ou gerir inquilinos.
- **Conta de consumo** - Uma conta de consumo é utilizada por um utilizador das aplicações que registou no Azure AD B2C.

Para obter mais informações sobre estes tipos de conta, consulte [a visão geral das contas dos utilizadores no Azure Ative Directory B2C](user-overview.md). Qualquer utilizador que seja designado para gerir o seu inquilino Azure AD B2C deve ter uma conta de utilizador Azure AD para que possa aceder aos serviços relacionados com o Azure. Pode adicionar tal utilizador [criando uma conta](#add-an-administrator-work-account) (conta de trabalho) no seu inquilino Azure AD B2C, ou [convidando-o](#invite-an-administrator-guest-account) ao seu inquilino Azure AD B2C como utilizador convidado.

## <a name="use-roles-to-control-resource-access"></a>Utilizar funções para controlar o acesso aos recursos

Ao planear a sua estratégia de controlo de acesso, o melhor é atribuir aos utilizadores a função menos privilegiada necessária para aceder aos recursos. A tabela seguinte descreve os recursos primários no seu inquilino Azure AD B2C e as funções administrativas mais adequadas para os utilizadores que os gerem.

|Recurso  |Descrição  |Função  |
|---------|---------|---------|
|[Inscrições de candidatura](tutorial-register-applications.md) | Crie e gere todos os aspetos da sua web, mobile e registos de aplicações nativas dentro do Azure AD B2C.|[Administrador de Aplicações](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Fornecedores de identidade](add-identity-provider.md)| Configure o [fornecedor local](identity-provider-local.md) de identidade e os fornecedores externos de identidade social ou empresarial. | [Administrador de fornecedor de identidade externa](../active-directory/roles/permissions-reference.md#external-identity-provider-administrator)|
|[Conectores de API](add-api-connector.md)| Integre os fluxos dos seus utilizadores com APIs web para personalizar a experiência do utilizador e integrar-se com sistemas externos.|[Administrador de atributo de fluxo de id do utilizador externo](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Imagem corporativa](customize-ui.md#configure-company-branding)| Personalize as páginas de fluxo do utilizador.| [Administrador Global](../active-directory/roles/permissions-reference.md#global-administrator)|
|[Atributos do utilizador](user-flow-custom-attributes.md)| Adicione ou elimine os atributos personalizados disponíveis para todos os fluxos do utilizador.| [Administrador de atributo de fluxo de id do utilizador externo](../active-directory/roles/permissions-reference.md#external-id-user-flow-attribute-administrator)|
|Gerir utilizadores| Gerir [as contas dos consumidores](manage-users-portal.md) e as contas administrativas, tal como descrito neste artigo.| [Administrador do Utilizador](../active-directory/roles/permissions-reference.md#user-administrator)|
|Funções e administradores| Gerir atribuições de funções no diretório Azure AD B2C. Criar e gerir grupos que podem ser atribuídos a funções Azure AD B2C. |[Administrador Global,](../active-directory/roles/permissions-reference.md#global-administrator) [Administrador privilegiado](../active-directory/roles/permissions-reference.md#privileged-role-administrator)|
|[Fluxos de utilizadores](user-flow-overview.md)|Para uma configuração rápida e ativação de tarefas de identidade comuns, como inscrição, inscrição e edição de perfis.| [Administrador de atributo de fluxo de id do utilizador externo](../active-directory/roles/permissions-reference.md#external-id-user-flow-administrator)|
|[Políticas personalizadas](user-flow-overview.md)| Crie, leia, atualize e elimine todas as políticas personalizadas em Azure AD B2C.| [Administrador de Política B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)|
|[Chaves da política](policy-keys-overview.md)|Adicione e gere chaves de encriptação para assinar e validar fichas, segredos de clientes, certificados e senhas usadas em políticas personalizadas.|[Administrador do keyset B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-keyset-administrator)|


## <a name="add-an-administrator-work-account"></a>Adicionar um administrador (conta de trabalho)

Para criar uma nova conta administrativa, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com permissões de Administrador Global ou Administrador privilegiado.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione **Novo utilizador**.
1. Na página **do Utilizador,** introduza informações para este utilizador:

   - **Nome**. Obrigatório. O primeiro e último nome do novo utilizador. Por exemplo, *Mary Parker.*
   - **Nome do utilizador**. Obrigatório. O nome de utilizador do novo utilizador. Por exemplo, `mary@contoso.com`.
     A parte de domínio do nome de utilizador deve utilizar o nome de domínio inicial, *\<yourdomainname> .onmicrosoft.com*.
   - **Grupos**. Opcionalmente, pode adicionar o utilizador a um ou mais grupos existentes. Também pode adicionar o utilizador a grupos mais tarde. 
   - **Função diretório**: Se necessitar de permissões administrativas Azure AD para o utilizador, pode adicioná-las a uma função AZure AD. Pode atribuir ao utilizador um administrador global ou uma ou mais das funções de administrador limitada no AZure AD. Para obter mais informações sobre a atribuição de funções, consulte [utilizar funções para controlar o acesso a recursos.](#use-roles-to-control-resource-access)
   - **Informações de trabalho**: Pode adicionar mais informações sobre o utilizador aqui, ou fazê-lo mais tarde. 

1. Copie a palavra-passe autogerada fornecida na **caixa de senha.** Terá de dar esta palavra-passe ao utilizador para iniciar sação pela primeira vez.
1. Selecione **Criar**.

O utilizador é criado e adicionado ao seu inquilino Azure AD B2C. É preferível ter pelo menos uma conta de trabalho nativa do seu inquilino Azure AD B2C atribuído ao papel de Administrador Global. Esta conta pode ser considerada uma conta de break-glass.

## <a name="invite-an-administrator-guest-account"></a>Convidar um administrador (conta de hóspedes)

Você também pode convidar um novo utilizador convidado para gerir o seu inquilino. A conta de hóspedes é a opção preferida quando a sua organização também tem Azure AD porque o ciclo de vida desta identidade pode ser gerido externamente. 

Para convidar um utilizador, siga estes passos:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com permissões de Administrador Global ou Administrador privilegiado.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione **Nova conta de hóspedes**.
1. Na página **do Utilizador,** introduza informações para este utilizador:

   - **Nome**. Obrigatório. O primeiro e último nome do novo utilizador. Por exemplo, *Mary Parker.*
   - **Endereço de e-mail**. Obrigatório. O endereço de e-mail do utilizador que pretende convidar. Por exemplo, `mary@contoso.com`.   
   - **Mensagem pessoal**: Adicione uma mensagem pessoal que será incluída no e-mail de convite.
   - **Grupos**. Opcionalmente, pode adicionar o utilizador a um ou mais grupos existentes. Também pode adicionar o utilizador a grupos mais tarde.
   - **Função diretório**: Se necessitar de permissões administrativas Azure AD para o utilizador, pode adicioná-las a uma função AZure AD. Pode atribuir ao utilizador um administrador global ou uma ou mais das funções de administrador limitada no AZure AD. Para obter mais informações sobre a atribuição de funções, consulte [utilizar funções para controlar o acesso a recursos.](#use-roles-to-control-resource-access)
   - **Informações de trabalho**: Pode adicionar mais informações sobre o utilizador aqui, ou fazê-lo mais tarde.

1. Selecione **Criar**.

Um e-mail de convite é enviado ao utilizador. O utilizador precisa de aceitar o convite para poder entrar.

### <a name="resend-the-invitation-email"></a>Reencaende o e-mail do convite

Se o hóspede não recebeu o e-mail do convite, ou o convite expirou, pode reensirar o convite. Como alternativa ao e-mail do convite, você pode dar a um hóspede um link direto para aceitar o convite. Para reensundir o convite e obter o link direto:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Procure e selecione o utilizador para o quais pretende reenso o convite.
1. No **| do Utilizador Página de perfil,** em **Identidade,** **selecione (Gerir)**.
    
    ![A screenshot mostra como reenressar o e-mail do convite da conta do hóspede.](./media/tenant-management/guest-account-resend-invite.png)

1. Para **resend convite?**  Quando **tem certeza de que deseja reencasse um convite?** 
1. Azure AD B2C envia o convite. Também pode copiar o URL do convite e forneca-lo diretamente ao hóspede.
    
    ![Screenshot mostra como obter o URL de convite.](./media/tenant-management/guest-account-invitation-url.png)  
 
## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

Pode atribuir uma função quando [criar um utilizador](#add-an-administrator-work-account) ou convidar um utilizador [convidado.](#invite-an-administrator-guest-account) Pode adicionar uma função, alterar o papel ou remover uma função para um utilizador:

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com permissões de Administrador Global ou Administrador privilegiado.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Em **Gerir**, selecione **Utilizadores**.
1. Selecione o utilizador para o quais pretende alterar as funções. Em seguida, **selecione Funções atribuídas**.
1. Selecione **atribuir atribuições,** selecione a função a atribuir (por exemplo, *administrador de aplicação)* e, em seguida, escolha **Adicionar**.

## <a name="remove-a-role-assignment"></a>Remover uma atribuição de função

Se precisar de remover uma atribuição de função de um utilizador, siga estes passos:

1. Selecione **Azure AD B2C**, selecione **Utilizadores** e, em seguida, procure e selecione o utilizador.
1. Selecione **funções atribuídas**. Selecione a função que pretende remover, por exemplo administrador *de aplicação,* e, em seguida, selecione **Remover a atribuição**.

## <a name="review-administrator-account-role-assignments"></a>Rever atribuições de funções de conta de administrador

Como parte de um processo de auditoria, você normalmente revê quais os utilizadores que são atribuídos a funções específicas no diretório Azure AD B2C. Utilize as seguintes etapas para a auditoria das funções privilegiadas atribuídas atualmente aos utilizadores.

1. Inscreva-se no [portal Azure](https://portal.azure.com/) com permissões de Administrador Global ou Administrador privilegiado.
1. Selecione o filtro **de subscrição Diretório +** no menu superior e, em seguida, selecione o diretório que contém o seu inquilino Azure AD B2C.
1. Sob **os serviços Azure**, selecione **Azure AD B2C**. Ou utilize a caixa de pesquisa para encontrar e selecionar **Azure AD B2C**.
1. Em **Gestão**, **selecione Funções e administradores**.
1. Selecione uma função, como **administrador global.** O **Papel | A** página de atribuições lista os utilizadores com essa função.

## <a name="delete-an-administrator-account"></a>Eliminar uma conta de administrador

Para eliminar um utilizador existente, tem de ter uma atribuição de função *de administrador Global.* Os administradores globais podem eliminar qualquer utilizador, incluindo outros administradores. *Os administradores do utilizador* podem eliminar qualquer utilizador não administrativo.

1. No seu diretório Azure AD B2C, selecione **Utilizadores** e, em seguida, selecione o utilizador que pretende eliminar.
1. **Selecione Eliminar**, e depois **Sim** para confirmar a eliminação.

O utilizador é eliminado e já não aparece na página **dos Utilizadores - Todos os utilizadores.** O utilizador pode ser visto na página de **utilizadores eliminados** durante os próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre a restauração de um utilizador, consulte [Restaurar ou remover um utilizador recentemente eliminado utilizando o Azure Ative Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="protect-administrative-accounts"></a>Proteger contas administrativas

Recomenda-se que proteja todas as contas do administrador com autenticação de vários fatores (MFA) para maior segurança. O MFA é um processo de verificação de identidade durante a entrada de informação que incita o utilizador a uma forma mais de identificação, como um código de verificação no seu dispositivo móvel ou um pedido na sua aplicação Microsoft Authenticator.

![Métodos de autenticação utilizados na imagem de inscrição](./media/tenant-management/sing-in-with-multi-factor-authentication.png)

Pode permitir que os [incumprimentos de segurança da Azure AD](../active-directory/fundamentals/concept-fundamentals-security-defaults.md) forcem todas as contas administrativas a utilizar em MFA.



## <a name="next-steps"></a>Passos seguintes

- [Criar um inquilino do Azure Active Directory B2C no portal do Azure](tutorial-create-tenant.md)

