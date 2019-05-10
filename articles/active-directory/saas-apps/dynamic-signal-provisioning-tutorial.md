---
title: 'Tutorial: Configurar o sinal de dinâmico para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com o sinal dinâmico.
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
ms.date: 05/07/2019
ms.author: zchia
ms.openlocfilehash: f79bc083105f997b08f7cfa6f8e08a8f4f455455
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470564"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Tutorial: Configurar o sinal de dinâmico para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no sinal dinâmico e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e/ou grupos para sinal dinâmica automaticamente.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino de sinal dinâmico](https://dynamicsignal.com/)
* Uma conta de utilizador no sinal dinâmico com permissões de administrador.

## <a name="add-dynamic-signal-from-the-gallery"></a>Adicionar sinal dinâmico a partir da Galeria

Antes de configurar o sinal de dinâmico para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar sinal dinâmico a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o sinal dinâmico a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **sinal dinâmica**, selecione **sinal dinâmico** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Sinal dinâmico na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Atribuir utilizadores a dinâmica de sinal

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a dinâmica de sinal. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a dinâmica de sinal ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Dicas importantes para atribuir utilizadores a dinâmica de sinal

* Recomenda-se que um único utilizador do Azure AD é atribuído a sinal dinâmico para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para o sinal dinâmico, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Configurar o aprovisionamento automático de utilizadores para sinal dinâmico 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no sinal dinâmica com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para sinal dinâmica, seguindo as instruções fornecidas no [sinal dinâmico único início de sessão tutorial](dynamicsignal-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores por sinal dinâmico no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **sinal dinâmica**.

    ![A ligação de sinal dinâmico na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino** e **segredo de Token** da conta de seu sinal dinâmica, conforme descrito no passo 6.

6. Na consola de administração do sinal dinâmica, navegue até **administrador > Avançadas > API**.

    ![Aprovisionamento dinâmico sinal](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Cópia a **URL de API SCIM** ao **URL de inquilino**. Clique em **gerar novo Token** para gerar um **Token de portador** e copie o valor de **segredo de Token**.

    ![Aprovisionamento dinâmico sinal](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se dinâmico sinal. Se a ligação falhar, certifique-se de que a conta de sinal dinâmico tem permissões de administrador e tente novamente.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users para sinal dinâmica**.

    ![Mapeamentos de utilizador de sinal dinâmico](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para sinal dinâmico no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no sinal de dinâmico para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador de sinal dinâmico](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o Azure AD para sinal dinâmico do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

14. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento dinâmico de sinal ao selecionar os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no sinal de dinâmico do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Sinal dinâmico não suporta exclusões de permanente de utilizador do Azure AD. Para eliminar um utilizador permanentemente no sinal dinâmico, a operação tem de ser feitas através da consola de administração de sinal dinâmica da interface do Usuário. 
* Atualmente, o sinal dinâmico não suporta grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

