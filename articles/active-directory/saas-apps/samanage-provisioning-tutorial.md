---
title: 'Tutorial: Configurar Samanage para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Samanage automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 62d0392f-37d4-436e-9aff-22f4e5b83623
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca43b62e66e3a736aa52fdd10fe36e635daba245
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280354"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar Samanage para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Samanage e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para Samanage.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem o seguinte:

* Um inquilino do Azure AD
* R [Samanage inquilino](https://www.samanage.com/pricing/) com o pacote Professional
* Uma conta de utilizador no Samanage com permissões de administrador

> [!NOTE]
> O Azure AD aprovisionamento integração depende a [Samanage Rest API](https://www.samanage.com/api/), que está disponível para programadores do Samanage para contas com o pacote Professional.

## <a name="adding-samanage-from-the-gallery"></a>Adicionando Samanage da Galeria

Antes de configurar Samanage para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Samanage a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Samanage a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Samanage**, selecione **Samanage** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-samanage"></a>Atribuir utilizadores a Samanage

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Samanage. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Samanage ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir utilizadores a Samanage

*    Funções de Samanage são automaticamente e dinamicamente preenchidas no portal do Azure da interface do Usuário hoje mesmo. Antes de atribuir funções de Samanage aos utilizadores, certifique-se de que uma sincronização inicial está concluída contra Samanage para obter as funções mais recente no seu inquilino Samanage.

*    Recomenda-se que um único utilizador do Azure AD está atribuído a Samanage para testar sua inicial configuração de aprovisionamento de utilizadores automática. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde quando os testes forem bem-sucedidos.

*   Ao atribuir um utilizador para Samanage, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-samanage"></a>Configurar o aprovisionamento automático de utilizadores para Samanage

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Samanage com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Samanage, seguindo as instruções fornecidas no [Samanage único início de sessão tutorial](samanage-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Samanage no Azure AD:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Samanage**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Samanage**.

    ![A ligação de Samanage na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **nome de utilizador administrador** e **palavra-passe de administrador** da sua conta de Samanage. Exemplos destes valores são:

   * Na **nome de utilizador administrador** campo, preencher o nome de utilizador da conta de administrador no inquilino Samanage. Exemplo: admin@contoso.com.

   * Na **palavra-passe de administrador** campo, preencher a palavra-passe da conta de administrador correspondente ao nome de utilizador administrador.

6. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se Samanage. Se a ligação falhar, certifique-se de que a conta de Samanage tem permissões de administrador e tente novamente.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Clique em **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Samanage**.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Samanage no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Samanage para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Para ativar a em mapeamentos de grupo, o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para Samanage**.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Definir **Enabled** ao **Sim** para sincronizar grupos. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para Samanage no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Samanage para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o Azure AD para Samanage do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento Samanage escolhendo os valores pretendidos na **âmbito** no **definições** secção. Ao escolher o **sincronizar todos os utilizadores e grupos** opção, considere as limitações, conforme descrito no **limitações de conector** secção abaixo.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Samanage do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Se o **sincronizar todos os utilizadores e grupos** opção está selecionada e um valor predefinido é configurado para o Samanage **funções** atributo, certifique-se de que o valor desejado sob o **valor predefinido se for nulo (é opcional)** campo é expresso no formato **{"displayName": "função"}** onde a função é o valor predefinido pretendido.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)


## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
