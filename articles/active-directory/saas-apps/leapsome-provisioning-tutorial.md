---
title: 'Tutorial: Configurar Leapsome para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Leapsome automaticamente.
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
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: e02deaa29b40e64b63d9afb471717feef78b3cee
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672685"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Tutorial: Configurar Leapsome para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Leapsome e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para Leapsome.

> [!NOTE]
>  Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* R [Leapsome](https://www.Leapsome.com/en/pricing) inquilino.
* Uma conta de utilizador no Leapsome com permissões de administrador.

## <a name="assigning-users-to-leapsome"></a>Atribuir utilizadores a Leapsome

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Leapsome. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Leapsome ao seguir as instruções aqui:
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Dicas importantes para atribuir utilizadores a Leapsome

* Recomenda-se que um único utilizador do Azure AD está atribuído a Leapsome para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para Leapsome, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.


## <a name="setup-leapsome-for-provisioning"></a>Leapsome de configuração para o aprovisionamento

1. Inicie sessão no seu [consola de administração de Leapsome](https://www.Leapsome.com/app/#/login). Navegue para **definições > definições de administração**.

    ![Consola de administração de Leapsome](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Navegue para **integrações > aprovisionamento SCIM utilizador**.

    ![Leapsome adicionar SCIM](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Copiar o **Token de autenticação SCIM**. Este valor será introduzido no campo de segredo de Token, no separador aprovisionamento da sua aplicação Leapsome no portal do Azure.

    ![Leapsome criar Token](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Adicionar Leapsome a partir da Galeria

Antes de configurar Leapsome para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Leapsome a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Leapsome a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Leapsome**, selecione **Leapsome** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Leapsome na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Configurar o aprovisionamento automático de utilizadores para Leapsome 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Leapsome com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Leapsome, seguindo as instruções fornecidas no [tutorial de início de sessão único de Leapsome](Leapsome-tutorial.md). Início de sessão único pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Leapsome no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Leapsome**.

    ![A ligação de Leapsome na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada `https://www.leapsome.com/api/scim` no **URL de inquilino**. Entrada da **Token de autenticação do SCIM** valor obtido anteriormente no **segredo de Token**. Clique em **Testar ligação** para garantir que o Azure AD pode ligar-se Leapsome. Se a ligação falhar, certifique-se de que a conta de Leapsome tem permissões de administrador e tente novamente.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Leapsome**.

    ![Mapeamentos de utilizador de Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Leapsome no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Leapsome para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para Leapsome**.

    ![Mapeamentos de grupo Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para Leapsome no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Leapsome para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos do grupo de Leapsome](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o Azure AD para Leapsome do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

14. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento Leapsome escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Leapsome do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Requer Leapsome **nome de utilizador** de ser exclusivo.
* Leapsome só permite que os endereços de e-mail de trabalho sejam guardados.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
