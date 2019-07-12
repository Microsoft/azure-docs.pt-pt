---
title: 'Tutorial: Configurar o Tableau Online para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de utilizador para o Tableau Online automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e203e88de8d806f489e5a7ab9bfd227c8232f84
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670904"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para aprovisionamento automático de utilizadores

Este tutorial demonstra os passos para executar em Tableau Online e o Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e grupos com o Tableau Online automaticamente.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de aprovisionamento de utilizador do Azure AD. Para obter informações sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações de software-como-serviço (SaaS) com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que tem:

*   Um inquilino do Azure AD.
*   R [inquilino Tableau Online](https://www.tableau.com/).
*   Uma conta de utilizador no Tableau Online com permissões de administrador.

> [!NOTE]
> O Azure AD aprovisionamento integração depende do [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Esta API está disponível para os desenvolvedores Tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicionar o Tableau Online no Azure Marketplace
Antes de configurar o Tableau Online para utilizadores automático de aprovisionamento com o Azure AD, adicione Tableau Online do Azure Marketplace à sua lista de aplicações de SaaS geridas.

Para adicionar o Tableau Online do Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Tableau Online** e selecione **Tableau Online** do painel de resultados. Para adicionar a aplicação, selecione **adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir utilizadores a Tableau Online

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, decida quais os utilizadores ou grupos no Azure AD precisam de acesso a Tableau Online. Para atribuir estes utilizadores ou grupos a Tableau Online, siga as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir utilizadores a Tableau Online

*   Recomendamos que atribua um único utilizador do Azure AD para o Tableau Online para testar o configuração de aprovisionamento automático de utilizadores. Pode atribuir utilizadores adicionais ou grupos mais tarde.

*   Quando atribui um utilizador a Tableau Online, selecione qualquer função específicas da aplicação válida, se estiver disponível, na caixa de diálogo de atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configurar o aprovisionamento automático de utilizadores para o Tableau Online

Esta secção orienta-o pelos passos para configurar o serviço de aprovisionamento do AD do Azure. Utilize-o para criar, atualizar e desativar a utilizadores ou grupos no Tableau Online com base em atribuições de utilizador ou grupo no Azure AD.

> [!TIP]
> Também pode ativar baseado em SAML início de sessão único para o Tableau Online. Siga as instruções no [Tableau único início de sessão tutorial Online](tableauonline-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementam uma à outra.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores para o Tableau Online no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais** > **todas as aplicações** > **Tableau Online**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Tableau Online**.

    ![A ligação Tableau Online na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Modo de aprovisionamento Online tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** secção, o domínio, o nome de utilizador administrador, a palavra-passe de administrador e o URL de conteúdo da sua conta Tableau Online de entrada:

   * Na **domínio** caixa, preencha o subdomínio com base no passo 6.

   * Na **nome de utilizador administrador** caixa, preencha o nome de utilizador da conta de administrador no inquilino Clarizen. Um exemplo é admin@contoso.com.

   * Na **palavra-passe de administrador** caixa, preencha a palavra-passe da conta de administrador que corresponde ao nome de utilizador administrador.

   * Na **URL de conteúdo** caixa, preencha o subdomínio com base no passo 6.

6. Depois de iniciar sessão para a sua conta administrativa para o Tableau Online, pode obter os valores para **domínio** e **URL de conteúdo** da URL da página Administração.

    * O **domínio** sua Tableau Online para a conta pode ser copiada desta parte do URL:

        ![Domínio de tableau Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * O **URL de conteúdo** sua Tableau Online para a conta pode ser copiada nesta secção. É um valor que é definido durante a configuração de conta. Neste exemplo, o valor for "contoso":

        ![URL de conteúdo Online tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Sua **domínio** poderá ser diferente da apresentada aqui.

7. Depois de preencher as caixas de mostrado no passo 5, selecione **Testar ligação** certificar-se de que o Azure AD pode ligar a Tableau Online. Se a ligação falhar, certifique-se de que a sua conta Tableau Online tem permissões de administrador e tente novamente.

    ![Ligação de teste Online tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na **notificação por E-Mail** caixa, introduza o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de aprovisionamento. Selecione o **enviar uma notificação por e-mail quando ocorre uma falha** caixa de verificação.

    ![E-Mail de notificação Online tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecione **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Tableau**.

    ![Sincronização de utilizador Online tableau](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Tableau Online no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Tableau Online para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Atributos de utilizador de correspondentes Online de tableau](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para Tableau**.

    ![Sincronização de grupo Online tableau](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para o Tableau Online no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Tableau Online para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Tableau Online correspondentes atributos do grupo](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de âmbito, siga as instruções a [tutorial âmbito do filtro](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para o Tableau Online, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

    ![Estado de aprovisionamento Online tableau](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os utilizadores ou grupos que pretende fazer o aprovisionamento Tableau Online. Na **definições** secção, selecione os valores que pretende na **âmbito**.

    ![Âmbito de tableau Online](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para aprovisionar, selecione **guardar**.

    ![Tableau Online guardar](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos na **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações posteriores. Que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure é executado. 

Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade de aprovisionamento. O relatório descreve todas as ações executadas pelo Azure AD no Tableau Online do serviço de aprovisionamento.

Para obter informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
