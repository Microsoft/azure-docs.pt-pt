---
title: 'Tutorial: Configurar o Zendesk para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de utilizador ao Zendesk automaticamente.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant
ms.collection: M365-identity-device-management
ms.openlocfilehash: f559d2c2398998ba590419758de559f21d9b65f5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114670"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zendesk para aprovisionamento automático de utilizadores

Este tutorial demonstra os passos para executar no Zendesk e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e grupos ao Zendesk automaticamente.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de aprovisionamento de utilizador do Azure AD. Para obter informações sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações de software-como-serviço (SaaS) com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que tem:

* Um inquilino do Azure AD.
* Um inquilino de Zendesk com o [Enterprise](https://www.zendesk.com/product/pricing/) planear ou mais bem habilitados.
* Uma conta de utilizador no Zendesk com permissões de administrador.

> [!NOTE]
> Conta com o Azure AD, integração de aprovisionamento a [API do Rest de Zendesk](https://developer.zendesk.com/rest_api/docs/core/introduction). Esta API está disponível para as equipes do Zendesk no plano de Enterprise ou melhor.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicionar Zendesk no Azure Marketplace

Antes de configurar o Zendesk para automático de utilizadores de aprovisionamento com o Azure AD, adicione Zendesk no Azure Marketplace à sua lista de aplicativos gerenciados de SaaS.

Para adicionar o Zendesk a partir do Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Zendesk** e selecione **Zendesk** do painel de resultados. Para adicionar a aplicação, selecione **adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir utilizadores à Zendesk

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, decida quais os utilizadores ou grupos no Azure AD precisam de aceder ao Zendesk. Para atribuir estes utilizadores ou grupos ao Zendesk, siga as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir utilizadores a Zendesk

* Hoje em dia, funções de Zendesk são automaticamente e dinamicamente preenchidas no portal do Azure da interface do Usuário. Antes de atribuir funções de Zendesk para os utilizadores, certifique-se de que uma sincronização inicial foi concluída em relação a Zendesk para obter as funções mais recente no seu inquilino do Zendesk.

* Recomendamos que atribua um único utilizador do Azure AD ao Zendesk para testar sua inicial configuração de aprovisionamento de utilizadores automática. Pode atribuir utilizadores adicionais ou grupos mais tarde após os testes forem bem-sucedidos.

* Quando atribui um utilizador ao Zendesk, selecione qualquer função específicas da aplicação válida, se estiver disponível, na caixa de diálogo de atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configurar o aprovisionamento automático de utilizadores ao Zendesk 

Esta secção orienta-o pelos passos para configurar o serviço de aprovisionamento do AD do Azure. Usá-lo para criar, atualizar e desativar a utilizadores ou grupos no Zendesk com base em atribuições de utilizador ou grupo no Azure AD.

> [!TIP]
> Também pode ativar baseado em SAML início de sessão único para Zendesk. Siga as instruções no [Zendesk único início de sessão tutorial](zendesk-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementam uma à outra.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores para Zendesk no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais** > **todas as aplicações** > **Zendesk**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zendesk**.

    ![A ligação de Zendesk na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Modo de aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Sob o **credenciais de administrador** secção, introduza o nome de utilizador administrador, o token secreto e o domínio da sua conta do Zendesk. Exemplos destes valores são:

   * Na **nome de utilizador administrador** caixa, preencha o nome de utilizador de conta de administrador do seu inquilino do Zendesk. Um exemplo é admin@contoso.com.

   * Na **segredo de Token** caixa, preencha o token SECRETO, conforme descrito no passo 6.

   * Na **domínio** caixa, preencha o subdomínio do seu inquilino do Zendesk. Por exemplo, para uma conta com um URL de inquilino do `https://my-tenant.zendesk.com`, é o subdomínio **meu inquilino**.

6. O token secreto para a sua conta do Zendesk está localizado em **administrador** > **API** > **definições**. Certifique-se de que **Token de acesso** está definida como **ativado**.

    ![Definições de administrador do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Token secreto do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas de mostrado no passo 5, selecione **Testar ligação** certificar-se de que o Azure AD pode ligar ao Zendesk. Se a ligação falhar, certifique-se de que a sua conta do Zendesk com permissões de administrador e tente novamente.

    ![Ligação de teste da Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Na **notificação por E-Mail** caixa, introduza o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de aprovisionamento. Selecione o **enviar uma notificação por e-mail quando ocorre uma falha** caixa de verificação.

    ![Zendesk Notification Email](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecione **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory ao Zendesk**.

    ![Sincronização de utilizador do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD ao Zendesk no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zendesk para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Atributos de utilizador correspondentes do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory ao Zendesk**.

    ![Sincronização de grupo do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD ao Zendesk no **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Zendesk para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Atributos de grupo correspondentes do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de âmbito, siga as instruções a [tutorial âmbito do filtro](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para Zendesk, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

    ![Estado de aprovisionamento do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os utilizadores ou grupos que pretende fazer o aprovisionamento do Zendesk. Na **definições** secção, selecione os valores que pretende na **âmbito**.

    ![Âmbito do Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para aprovisionar, selecione **guardar**.

    ![Zendesk Save](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos na **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações posteriores. Que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure é executado. 

Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade de aprovisionamento. O relatório descreve todas as ações executadas pelo Azure AD no Zendesk do serviço de aprovisionamento.

Para obter informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Zendesk suporta a utilização de grupos para os utilizadores com **agente** apenas funções. Para obter mais informações, consulte a [documentação de Zendesk](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Quando uma função personalizada é atribuída a um utilizador ou grupo, o utilizador automático do Azure AD também o serviço de aprovisionamento atribui a função predefinida **agente**. Apenas os agentes podem ser atribuídos uma função personalizada. Para obter mais informações, consulte a [documentação da API do Zendesk](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
