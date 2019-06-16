---
title: 'Tutorial: Configurar Samanage para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de utilizador para Samanage automaticamente.
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
ms.openlocfilehash: d474d9bfd6016885eaa21afcea5d44d39c624084
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730595"
---
# <a name="tutorial-configure-samanage-for-automatic-user-provisioning"></a>Tutorial: Configurar Samanage para aprovisionamento automático de utilizadores

Este tutorial demonstra os passos para executar no Samanage e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e grupos para Samanage automaticamente.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de aprovisionamento de utilizador do Azure AD. Para obter informações sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações de software-como-serviço (SaaS) com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que tem:

* Um inquilino do Azure AD.
* R [Samanage inquilino](https://www.samanage.com/pricing/) com o pacote Professional.
* Uma conta de utilizador no Samanage com permissões de administrador.

> [!NOTE]
> O Azure AD aprovisionamento integração depende do [Samanage Rest API](https://www.samanage.com/api/). Esta API está disponível para os desenvolvedores de Samanage para contas com o pacote Professional.

## <a name="add-samanage-from-the-azure-marketplace"></a>Adicionar Samanage no Azure Marketplace

Antes de configurar Samanage para automático de utilizadores de aprovisionamento com o Azure AD, adicione Samanage no Azure Marketplace à sua lista de aplicativos gerenciados de SaaS.

Para adicionar Samanage do Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Samanage** e selecione **Samanage** do painel de resultados. Para adicionar a aplicação, selecione **adicionar**.

    ![Samanage na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-samanage"></a>Atribuir utilizadores a Samanage

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, decida quais os utilizadores ou grupos no Azure AD precisam de acesso a Samanage. Para atribuir estes utilizadores ou grupos a Samanage, siga as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-samanage"></a>Dicas importantes para atribuir utilizadores a Samanage

*    Hoje em dia, Samanage funções são automaticamente e dinamicamente preenchidas no portal do Azure da interface do Usuário. Antes de atribuir funções de Samanage aos utilizadores, certifique-se de que uma sincronização inicial está concluída contra Samanage para obter as funções mais recente no seu inquilino Samanage.

*    Recomendamos que atribua um único utilizador do Azure AD para Samanage para testar sua inicial configuração de aprovisionamento de utilizadores automática. Pode atribuir utilizadores e grupos adicionais mais tarde após os testes forem bem-sucedidos.

*    Quando atribui um utilizador a Samanage, selecione qualquer função específicas da aplicação válida, se estiver disponível, na caixa de diálogo de atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configure-automatic-user-provisioning-to-samanage"></a>Configurar o aprovisionamento automático de utilizadores para Samanage

Esta secção orienta-o pelos passos para configurar o serviço de aprovisionamento do AD do Azure. Usá-lo para criar, atualizar e desativar os utilizadores ou grupos no Samanage com base em atribuições de utilizador ou grupo no Azure AD.

> [!TIP]
> Também pode ativar baseado em SAML início de sessão único para Samanage. Siga as instruções no [Samanage único início de sessão tutorial](samanage-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementam uma à outra.

### <a name="configure-automatic-user-provisioning-for-samanage-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores para Samanage no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais** > **todas as aplicações** > **Samanage**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Samanage**.

    ![A ligação de Samanage na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento Samanage](./media/samanage-provisioning-tutorial/ProvisioningTab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Modo de aprovisionamento de Samanage](./media/samanage-provisioning-tutorial/ProvisioningCredentials.png)

5. Sob o **credenciais de administrador** , digite o nome de utilizador administrador e a palavra-passe de administrador da sua conta de Samanage. Exemplos destes valores são:

   * Na **nome de utilizador administrador** caixa, preencha o nome de utilizador da conta de administrador no inquilino Samanage. Um exemplo é admin@contoso.com.

   * Na **palavra-passe de administrador** caixa, preencha a palavra-passe da conta de administrador que corresponde ao nome de utilizador administrador.

6. Depois de preencher as caixas de mostrado no passo 5, selecione **Testar ligação** certificar-se de que o Azure AD pode ligar-se Samanage. Se a ligação falhar, certifique-se de que a conta de Samanage tem permissões de administrador e tente novamente.

    ![Ligação de teste de Samanage](./media/samanage-provisioning-tutorial/TestConnection.png)

7. Na **notificação por E-Mail** caixa, introduza o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de aprovisionamento. Selecione o **enviar uma notificação por e-mail quando ocorre uma falha** caixa de verificação.

    ![E-Mail de notificação de Samanage](./media/samanage-provisioning-tutorial/EmailNotification.png)

8. Selecione **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Samanage**.

    ![Sincronização de utilizador Samanage](./media/samanage-provisioning-tutorial/UserMappings.png)

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Samanage no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Samanage para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Atributos de utilizador correspondentes Samanage](./media/samanage-provisioning-tutorial/UserAttributeMapping.png)

11. Para ativar a em mapeamentos de grupo, o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para Samanage**.

    ![Sincronização do grupo Samanage](./media/samanage-provisioning-tutorial/GroupMappings.png)

12. Definir **Enabled** ao **Sim** para sincronizar grupos. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para Samanage no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Samanage para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Atributos de grupo correspondentes Samanage](./media/samanage-provisioning-tutorial/GroupAttributeMapping.png)

13. Para configurar filtros de âmbito, siga as instruções a [tutorial âmbito do filtro](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o Azure AD para Samanage, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

    ![Estado de aprovisionamento de Samanage](./media/samanage-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os utilizadores ou grupos que pretende fazer o aprovisionamento Samanage. Na **definições** secção, selecione os valores que pretende na **âmbito**. Quando seleciona a **sincronizar todos os utilizadores e grupos** opção, considere as limitações, conforme descrito na seção a seguir "Limitações de conector".

    ![Âmbito de Samanage](./media/samanage-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para aprovisionar, selecione **guardar**.

    ![Guardar Samanage](./media/samanage-provisioning-tutorial/SaveProvisioning.png)


Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos na **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações posteriores. Que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure é executado. 

Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade de aprovisionamento. O relatório descreve todas as ações executadas pelo Azure AD no Samanage do serviço de aprovisionamento.

Para obter informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

Se selecionar a **sincronizar todos os utilizadores e grupos** opção e configurar um valor para o Samanage **funções** atributo, o valor sob a **valor predefinido se for nulo (é opcional)** caixa tem de ser expresso no seguinte formato:

- {"displayName": "função"}, em que a função é o valor predefinido que pretende.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/samanage-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/samanage-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/samanage-provisioning-tutorial/tutorial_general_03.png
