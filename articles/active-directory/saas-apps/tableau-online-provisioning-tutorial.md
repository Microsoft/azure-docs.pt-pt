---
title: 'Tutorial: Configurar o Tableau Online para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com o Tableau Online automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2018
ms.author: v-wingf-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ed8bae32cb8d0680b1f9aa66f26407c3313ea06
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077824"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar o Tableau Online para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Tableau Online e o Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e/ou grupos a Tableau Online automaticamente.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem o seguinte:

*   Um inquilino do Azure AD
*   A [inquilino Tableau Online](https://www.tableau.com/)
*   Uma conta de utilizador no Tableau Online com permissões de administrador

> [!NOTE]
> O Azure AD aprovisionamento integração depende a [Tableau Online Rest API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm), que está disponível para os desenvolvedores Tableau Online.

## <a name="adding-tableau-online-from-the-gallery"></a>Adicionar Tableau Online a partir da Galeria
Antes de configurar o Tableau Online para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar Tableau Online da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Tableau Online a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]

3. Para adicionar Tableau Online, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Tableau Online**.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/AppSearch.png)

5. No painel de resultados, selecione **Tableau Online**e, em seguida, clique nas **Add** botão para adicionar o Tableau Online à sua lista de aplicações SaaS.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/AppSearchResults.png)

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/AppCreation.png)

## <a name="assigning-users-to-tableau-online"></a>Atribuir utilizadores a Tableau Online

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Tableau Online. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Tableau Online ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir utilizadores a Tableau Online

*   Recomenda-se que um único utilizador do Azure AD está atribuído a Tableau Online para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Quando atribuir um utilizador a Tableau Online, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-tableau-online"></a>Configurar o aprovisionamento automático de utilizadores para o Tableau Online

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Tableau Online com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para o Tableau Online, siga as instruções fornecidas no [Tableau único início de sessão tutorial Online](tableauonline-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para o Tableau Online no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione o Tableau Online da sua lista de aplicações SaaS.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/AppInstanceSearch.png)

3. Selecione o **aprovisionamento** separador.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **domínio**, **nome de utilizador administrador**, **palavra-passe de administrador**, e **conteúdo URL** da sua conta Tableau Online:

   * Na **domínio** campo, preencher o subdomínio com base no passo 6.

   * Na **nome de utilizador administrador** campo, preencher o nome de utilizador da conta de administrador no inquilino Clarizen. Exemplo: admin@contoso.com.

   * Na **palavra-passe de administrador** campo, preencher a palavra-passe da conta de administrador correspondente ao nome de utilizador administrador.

   * Na **URL de conteúdo** campo, preencher o subdomínio com base no passo 6.

6. Depois de iniciar sessão sua conta administrativa para o Tableau Online, os valores para **domínio** e **URL de conteúdo** podem ser extraídos da URL da página Administração.

    *   O **domínio** sua Tableau Online para a conta pode ser copiada desta parte do URL: ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    *   O **URL de conteúdo** sua Tableau Online para a conta pode ser copiada nesta secção, e é um valor é definido durante a configuração de conta. Neste exemplo, o valor for "contoso": ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Sua **domínio** pode ser diferente da apresentada aqui. 


7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar a Tableau Online. Se a ligação falhar, certifique-se de que sua conta Tableau Online tem permissões de administrador e tente novamente.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

10. Clique em **Guardar**.

11. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Tableau**.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/UserMappings.png)

12. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Tableau Online no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Tableau Online para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

13. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para Tableau**.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

14. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para o Tableau Online no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Tableau Online para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

15. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

16. Para ativar o Azure AD para o Tableau Online do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

17. Definir a utilizadores e/ou grupos que pretende fazer o aprovisionamento Tableau Online ao selecionar os valores pretendidos na **âmbito** no **definições** secção.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

18. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Tableau Online aprovisionamento](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Tableau Online do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
