---
title: 'Tutorial: Configurar o Dropbox for Business para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para o Dropbox for Business.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: d7a7a76c86100041b544916c7d10e43bf3aaa44d
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672914"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Tutorial: Configurar o Dropbox for Business para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Dropbox para empresas e o Azure Active Directory (Azure AD) configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para o Dropbox for Business.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um Dropbox para o inquilino de negócios](https://www.dropbox.com/business/pricing)
* Uma conta de utilizador no Dropbox para empresas com permissões de administrador.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Adicionar Dropbox para a empresa a partir da Galeria

Antes de configurar o Dropbox for Business para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Dropbox para a empresa a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Dropbox para a empresa a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Dropbox para empresas**, selecione **Dropbox para empresas** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Dropbox para a empresa na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Atribuir utilizadores a Dropbox para empresas

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso para a Dropbox para empresas. Depois de decidir, pode atribuir estes utilizadores e/ou grupos para o Dropbox for Business, seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Dicas importantes para atribuir utilizadores a Dropbox para empresas

* Recomenda-se que um único utilizador do Azure AD é atribuído ao Dropbox para empresas testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para o Dropbox for Business, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Configurar o aprovisionamento automático de utilizadores para o Dropbox for Business 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Dropbox para empresas com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para o Dropbox for Business, seguindo as instruções fornecidas no [Dropbox para negócios único início de sessão tutorial](dropboxforbusiness-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para o Dropbox for Business no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Dropbox para empresas**.

    ![O Dropbox para ligação de negócios na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, clique em **autorizar**. É aberto um Dropbox para caixa de diálogo de início de sessão de negócios numa nova janela do browser.

    ![Aprovisionamento ](common/provisioning-oauth.png)

6. Sobre o **iniciar sessão no Dropbox para empresas ligar com o Azure AD** caixa de diálogo, inicie sessão no seu Dropbox para o inquilino de negócios e verificar a sua identidade.

    ![Dropbox for Business início de sessão](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. Após concluir os passos 5 e 6, clique em **Testar ligação** para garantir que o Azure AD pode ligar o Dropbox for Business. Se a ligação falhar, certifique-se de que seu Dropbox conta com permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-oauth.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para o Dropbox**.

    ![Mapeamentos de utilizador do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para a Dropbox no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de usuário no Dropbox para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para o Dropbox**.

    ![Mapeamentos de grupo do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para a Dropbox no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Dropbox para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de grupo do Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para o Dropbox, o serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

16. Definir a utilizadores e/ou grupos que pretende aprovisionar para o Dropbox ao selecionar os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Dropbox, o serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector
 
* Dropbox não suporta a suspender os utilizadores convidados. Se um utilizador convidado é suspenso, esse utilizador será eliminado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

