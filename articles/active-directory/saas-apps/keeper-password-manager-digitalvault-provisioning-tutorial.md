---
title: 'Tutorial: Configure o Gestor de palavra-passe de extinção do responsável e cofre Digital para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para o Gestor de palavra-passe de extinção do responsável & Digital de cofre.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: zchia
ms.openlocfilehash: b7b096bd1ecf0a4df37ed4de0cf618216dccc2bc
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159469"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Tutorial: Configure o Gestor de palavra-passe de extinção do responsável e cofre Digital para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Gestor de palavra-passe de extinção do responsável & cofre Digital e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar os utilizadores e/ou grupos a palavra-passe de extinção do responsável Gestor de & cofre Digital.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino do Gestor de palavra-passe de extinção do responsável & Digital de cofre](https://keepersecurity.com/pricing.html?t=e)
* Uma conta de utilizador no Gestor de palavra-passe de extinção do responsável & cofre Digital com permissões de administrador.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Adicionar o Gestor de palavra-passe de extinção do responsável & Digital cofre a partir da Galeria

Antes de configurar o Gestor de palavra-passe de extinção do responsável & Digital de cofre para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar Gestor de palavra-passe de extinção do responsável & Digital cofre a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Gestor de palavra-passe de extinção do responsável & Digital cofre a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Gestor de palavra-passe de extinção do responsável e cofre Digital**, selecione **Gestor de palavra-passe de extinção do responsável e cofre Digital** no painel de resultados e, em seguida, clique o **adicionar**botão para adicionar a aplicação.

    ![Gestor de palavra-passe de extinção do responsável & Digital cofre na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Atribuir utilizadores a Gestor de palavra-passe de extinção do responsável & Digital de cofre

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso ao Gestor de palavra-passe de extinção do responsável & Digital de cofre. Depois de decidir, pode atribuir estes utilizadores e/ou grupos para o Gestor de palavra-passe de extinção do responsável & cofre Digital ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Dicas importantes para atribuir utilizadores a Gestor de palavra-passe de extinção do responsável & Digital de cofre

* Recomenda-se que um único utilizador do Azure AD é atribuído para o Gestor de palavra-passe de extinção do responsável & Digital de cofre para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para o Gestor de palavra-passe de extinção do responsável & Digital de cofre, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Configurar o aprovisionamento automático de utilizadores para o Gestor de palavra-passe de extinção do responsável & Digital de cofre 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Gestor de palavra-passe de extinção do responsável & cofre Digital com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para o Gestor de palavra-passe de extinção do responsável & cofre Digital, seguindo as instruções fornecidas no [Gestor de palavra-passe de extinção do responsável e cofre Digital único início de sessão tutorial](keeperpasswordmanager-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para o Gestor de palavra-passe de extinção do responsável & cofre Digital no Azure AD:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Gestor de palavra-passe de extinção do responsável & Digital Cofre**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Gestor de palavra-passe de extinção do responsável e cofre Digital**.

    ![A ligação de Gestor de palavra-passe de extinção do responsável & Digital cofre na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino** e **segredo de Token** do seu Gestor de palavra-passe de extinção do responsável & conta do cofre Digital, conforme descrito no passo 6.

6. Inicie sessão no seu [consola de administração de extinção do responsável](https://keepersecurity.com/console/#login). Clique em **administrador** e selecione um nó existente ou crie um novo. Navegue para o **aprovisionamento** separador e selecione **Add Method**.

    ![Consola de administração de extinção do responsável](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-admin-console.png)

    Selecione **SCIM (sistema de gestão de identidade entre domínios**.

    ![Extinção do responsável adicionar SCIM](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-add-scim.png)

    Clique em **criar Token de aprovisionamento**.

    ![Extinção do responsável criar o ponto final](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Copie os valores de **URL** e **Token** e cole-os no **URL de inquilino** e **segredo de Token** no Azure AD. Clique em **guardar** para concluir a configuração de aprovisionamento no extinção do responsável.

    ![Extinção do responsável criar Token](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-create-token.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar ao Gestor de palavra-passe de extinção do responsável & Digital de cofre. Se a ligação falhar, certifique-se de que a conta de Gestor de palavra-passe de extinção do responsável & Digital cofre tem permissões de administrador e tente novamente.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory Directory para o Gestor de palavra-passe de extinção do responsável & cofre Digital**.

    ![Mapeamentos de utilizador de extinção do responsável](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Gestor de palavra-passe de extinção do responsável & cofre Digital no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Gestor de palavra-passe de extinção do responsável & Digital de cofre para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador de extinção do responsável](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar Azure grupos do Active Directory para o Gestor de palavra-passe de extinção do responsável & cofre Digital**.

    ![Mapeamentos de grupo de extinção do responsável](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para o Gestor de palavra-passe de extinção do responsável & cofre Digital no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Gestor de palavra-passe de extinção do responsável & Digital de cofre para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos do grupo de extinção do responsável](media/keeperpasswordmanagerdigitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para o Gestor de palavra-passe de extinção do responsável & Digital cofre do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

16. Definir a utilizadores e/ou grupos que pretende aprovisionar para o Gestor de palavra-passe de extinção do responsável & cofre Digital ao selecionar os valores pretendidos no **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Gestor de palavra-passe de extinção do responsável do serviço de aprovisionamento de aprovisionamento & Cofre digital.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Gestor de palavra-passe de extinção do responsável e cofre digitais requer **e-mails** e **userName** ter o mesmo valor de origem, como todas as atualizações para qualquer um dos atributos irão modificar o outro valor.
* Gestor de palavra-passe de extinção do responsável & Digital de cofre não suportam exclusões de utilizador, apenas desativar. Os utilizadores desativados serão apresentado como bloqueado na interface de Usuário extinção do responsável da consola do administrador.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png
