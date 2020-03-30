---
title: 'Tutorial: Configure Matrix Prioritária para o fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador à Priority Matrix.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: a4598a99-3c98-4c14-86c2-95cc562e2439
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: 80ffaba6713027d216958e0be2cd4ae35a8d2d70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063446"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Tutorial: Configure A Matriz Prioritária para o fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na Priority Matrix e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer automaticamente e desfornecer utilizadores e/ou grupos para a Priority Matrix.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização Pública. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino da Matriz Prioritária](https://appfluence.com/pricing/)
* Uma conta de utilizador numa Matriz Prioritária com permissões de Administrador.

## <a name="assign-users-to-priority-matrix"></a>Atribuir utilizadores à Matriz Prioritária

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à Matriz Prioritária. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à Priority Matrix seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Dicas importantes para atribuir utilizadores à Priority Matrix

* Recomenda-se que um único utilizador da AD Azure seja atribuído à Priority Matrix para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à Priority Matrix, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-priority-matrix-for-provisioning"></a>Criar matriz prioritária para o provisionamento

Antes de configurar a Priority Matrix para o fornecimento automático de utilizadores com o Azure AD, terá de obter algumas informações de fornecimento da Priority Matrix.

1. Inscreva-se na sua Consola de [Administração Priority Matrix](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Clique em ficha de **login oauth** para a Matriz Prioritária

    ![Matriz prioritária Adicionar SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Clique no botão **GET NEW TOKEN.** Copie a **Corda token.** Este valor será inserido no campo **Secret Token** no separador de provisionamento da sua aplicação Priority Matrix no portal Azure. 

    ![Matriz prioritária Criar Token](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Adicione A Matriz Prioritária da galeria

Para configurar a Priority Matrix para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar a Priority Matrix da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza A **Matriz Prioritária**, selecione **Priority Matrix** no painel de resultados. 

    ![Matriz Prioritária na lista de resultados](common/search-new-app.png)

5. Selecione o **botão 'Sign-up' para A Matriz Prioritária** que irá redirecioná-lo para a página de login da Priority Matrix. 

    ![Adcção de Matriz Prioritária OIDC](media/priority-matrix-provisioning-tutorial/signup.png)

6. Como a Priority Matrix é uma aplicação OpenIDConnect, opte por iniciar sessão na Priority Matrix utilizando a sua conta de trabalho da Microsoft.

    ![Login De Matriz Prioritária OIDC](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Após uma autenticação bem sucedida, aceite o pedido de consentimento para a página de consentimento. O pedido será adicionado automaticamente ao seu inquilino e será redirecionado para a sua conta Priority Matrix.

    ![Consentimento da Matriz Prioritária OIDC](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Configure o fornecimento automático de utilizadores à Matriz Prioritária 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos em Priority Matrix com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto final do SCIM da Priority Matrix, consulte o [fornecimento do Utilizador e a Matriz Prioritária](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para a Matriz Prioritária em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Priority Matrix**.

    ![O link Matriz Prioritária na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de `https://sync.appfluence.com/scim/v2/` **Credenciais de Administrador,** insere-se no URL do **Arrendatário**. Insera o valor que recuperou e salvou mais cedo da Priority Matrix em **Secret Token**. Clique na **ligação de teste** para garantir que o Azure AD pode ligar-se à Matriz Prioritária. Se a ligação falhar, certifique-se de que a sua conta Priority Matrix tem permissões de Administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Priority Matrix**.

    ![Mapeamento de utilizador de matriz prioritária](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para Priority Matrix na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas do utilizador na Priority Matrix para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador da matriz prioritária](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o serviço de provisionamento de AD Azure para a Matriz Prioritária, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que deseja fornecer à Priority Matrix, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na Priority Matrix.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)


