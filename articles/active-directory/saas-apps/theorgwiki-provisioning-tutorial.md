---
title: 'Tutorial: Configure o OrgWiki para o fornecimento automático de utilizadores com diretório ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores ao TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2b0b11ae89e1f0d150b84f49b04a8badcb44e82c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063155"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Configure o OrgWiki para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no TheOrgWiki e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos para o TheOrgWiki.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino orgWiki.](https://www.theorgwiki.com/welcome/)
* Uma conta de utilizador no OrgWiki com permissões de administrador.

## <a name="assign-users-to-theorgwiki"></a>Atribuir utilizadores ao TheOrgWiki

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao TheOrgWiki. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos ao TheOrgWiki seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Dicas importantes para atribuir utilizadores ao TheOrgWiki

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao OrgWiki para testar a configuração automática de fornecimento de utilizadores. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador ao TheOrgWiki, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurar o OrgWiki para o fornecimento

Antes de configurar o OrgWiki para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM no TheOrgWiki.

1. Inscreva-se na consola [TheOrgWiki Admin](https://www.theorgwiki.com/login/). Clique na **Consola De Administrador**.

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. Na Consola de Administração, clique no **separador Definições**. 

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navegar para **Contas de Serviço.**

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Clique em **+Conta de Serviço**. No **tipo de conta de serviço,** selecione **Token Based**. Clique em **Guardar**.

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copiar os **Tokens Ativos**. Este valor será inserido no campo Secret Token no separador de provisionamento da sua aplicação TheOrgWiki no portal Azure.
     
    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adicione o OrgWiki da galeria

Para configurar o OrgWiki para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o OrgWiki da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o OrgWiki**, selecione **TheOrgWiki** no painel de resultados. 

    ![O OrgWiki na lista de resultados](common/search-new-app.png)

5. Selecione o **'Sign-up' para o** botão OrgWiki que irá redirecioná-lo para a página de login do TheOrgWiki. 

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  No canto superior direito, selecione **Login**.

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como o OrgWiki é uma aplicação OpenIDConnect, opte por iniciar sessão no OrgWiki utilizando a sua conta de trabalho da Microsoft.

    ![Theorgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Após uma autenticação bem sucedida, a aplicação será adicionada automaticamente ao seu inquilino e será redirecionada para a sua conta TheOrgWiki.

    ![Orgwiki Adicionar SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configure o fornecimento automático de utilizadores ao TheOrgWiki 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no TheOrgWiki com base em atribuições de utilizador e/ou grupo em Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para o OrgWiki em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **TheOrgWiki**.

    ![O link OrgWiki na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. 

    Exemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> O **Valor subdomínio** só pode ser definido durante o processo inicial de inscrição para o OrgWiki.
 
6. Insera o valor simbólico no campo **Secret Token,** que recuperaste mais cedo do OrgWiki. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao OrgWiki. Se a ligação falhar, certifique-se de que a sua conta TheOrgWiki tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to TheOrgWiki**.

    ![Mapeamento de utilizadores da OrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Reveja os atributos do utilizador que são sincronizados de Azure AD para TheOrgWiki na secção **Attribute-Mapping.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no TheOrgWiki para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos do utilizador da Orgwiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o serviço de provisionamento de AD Azure para o OrgWiki, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

13. Defina os utilizadores e/ou grupos que deseja fornecer ao OrgWiki, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo demorará os utilizadores e/ou grupos a fornecer, veja [quanto tempo demorará a fornecer aos utilizadores](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Pode utilizar a secção **Current Status** para monitorizar o progresso e seguir ligações ao seu relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no TheOrgWiki. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de contas de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Aprenda a rever os registos e obtenha relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).