---
title: 'Tutorial: Configurar BlueJeans para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para BlueJeans automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d8fedb372fb245b7bc35cb440bd758336ab2a68
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057630"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Tutorial: Configurar BlueJeans para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no BlueJeans e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para BlueJeans.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem o seguinte:

* Um inquilino do Azure AD
* Um inquilino BlueJeans com o [minha empresa](https://www.BlueJeans.com/pricing) planear ou melhor ativada
* Uma conta de utilizador no BlueJeans com permissões de administrador

> [!NOTE]
> O Azure AD aprovisionamento integração depende a [BlueJeans API](https://BlueJeans.github.io/developer), que está disponível para as equipes de BlueJeans no plano Standard ou superior.

## <a name="adding-bluejeans-from-the-gallery"></a>Adicionando BlueJeans da Galeria

Antes de configurar BlueJeans para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar BlueJeans a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar BlueJeans a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **BlueJeans**, selecione **BlueJeans** no painel de resultados e, em seguida, selecione o **Add** botão para adicionar a aplicação.

    ![BlueJeans na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bluejeans"></a>Atribuir utilizadores a BlueJeans

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a BlueJeans. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a BlueJeans ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-bluejeans"></a>Dicas importantes para atribuir utilizadores a BlueJeans

* Recomenda-se que um único utilizador do Azure AD está atribuído a BlueJeans para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para BlueJeans, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-bluejeans"></a>Configurar o aprovisionamento automático de utilizadores para BlueJeans

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no BlueJeans com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para BlueJeans, seguindo as instruções fornecidas no [BlueJeans único início de sessão tutorial](bluejeans-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para BlueJeans no Azure AD:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **BlueJeans**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **BlueJeans**.

    ![A ligação de BlueJeans na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/Bluejeans1.png)

5. Sob o **credenciais de administrador** secção, de entrada a **nome de utilizador administrador**, e **palavra-passe de administrador** da sua conta de BlueJeans. Exemplos destes valores são:

   * Na **nome de utilizador administrador** campo, preencher o nome de utilizador da conta de administrador no inquilino BlueJeans. Exemplo: admin@contoso.com.

   * Na **palavra-passe de administrador** campo, preencher a palavra-passe correspondente ao nome de utilizador administrador.

6. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se BlueJeans. Se a ligação falhar, certifique-se de que a conta de BlueJeans tem permissões de administrador e tente novamente.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/BluejeansTestConnection.png)

7. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/BluejeansNotificationEmail.png)

8. Clique em **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para BlueJeans**.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/BluejeansMapping.png)

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para BlueJeans no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no BlueJeans para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/BluejeansUserMappingAtrributes.png)

11. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para ativar o Azure AD para BlueJeans do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/BluejeansProvisioningStatus.png)

13. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento BlueJeans escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/UserGroupSelection.png)

14. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![BlueJeans aprovisionamento](./media/bluejeans-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no BlueJeans do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Bluejeans não permite que os nomes de utilizador que exceda 30 carateres.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[1]: ./media/bluejeans-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
