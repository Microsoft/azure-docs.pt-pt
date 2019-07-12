---
title: 'Tutorial: Configurar o teclado para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com o teclado automaticamente.
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
ms.author: zhchia
ms.openlocfilehash: 32e634bc089417aaa8080b30a5f77f663a3d8b33
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2019
ms.locfileid: "67611768"
---
# <a name="tutorial-configure-dialpad-for-automatic-user-provisioning"></a>Tutorial: Configurar o teclado para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no teclado e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para teclado.

> [!NOTE]
>  Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).

> Este conector está atualmente em pré-visualização. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino de teclado](https://www.dialpad.com/pricing/).
* Uma conta de utilizador no teclado de com permissões de administrador.

## <a name="assign-users-to-dialpad"></a>Atribuir utilizadores a teclado
O Azure Active Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso ao teclado. Depois de decidir, pode atribuir estes utilizadores e/ou grupos para teclado de seguindo as instruções aqui:
 
* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md) 

 ## <a name="important-tips-for-assigning-users-to-dialpad"></a>Dicas importantes para atribuir utilizadores a teclado

 * Recomenda-se que um único utilizador do Azure AD está atribuído a teclado para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para o teclado, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com a função de acesso predefinida são excluídos desde o aprovisionamento.

## <a name="setup-dialpad-for-provisioning"></a>Teclado de configuração para o aprovisionamento

Antes de configurar o teclado para automático de utilizadores de aprovisionamento com o Azure AD, terá de obter algumas informações de aprovisionamento do teclado.

1. Inicie sessão no seu [consola de administração do teclado](https://dialpadbeta.com/login) e selecione **definições de administração**. Certifique-se de que **minha empresa** está selecionada na lista pendente. Navegue para **Authentication > chaves de API**.

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad01.png)

2. Gerar uma nova chave clicando **adicionar uma chave** e configurar as propriedades de seu token secreto.

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad02.png)

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad03.png)

3. Clique nas **clique aqui para mostrar o valor** botão para a sua chave de API recentemente criada e copie o valor mostrado. Este valor será introduzido na **segredo de Token** campo no separador aprovisionamento da sua aplicação de teclado no portal do Azure. 

    ![Teclado de criar Token](media/dialpad-provisioning-tutorial/dialpad04.png)

## <a name="add-dialpad-from-the-gallery"></a>Adicionar teclado da partir da Galeria

Para configurar o teclado para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar teclado da partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o teclado da partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **teclado**, selecione **teclado de** no painel de resultados.
    ![Teclado de na lista de resultados](common/search-new-app.png)

5. Navegue para o **URL** realçado em baixo num browser separado. 

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad05.png)

6. No canto superior direito, selecione **sessão > teclado de uso online**.

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/dialpad06.png)

7. Como o teclado de é uma aplicação de OpenIDConnect, optar por iniciar sessão com a conta de trabalho da Microsoft de teclado.

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/loginpage.png)

8. Após uma autenticação com êxito, aceite a solicitação de consentimento para a página de consentimento. O aplicativo, em seguida, irá ser automaticamente adicionado ao seu inquilino e será redirecionado para a sua conta do teclado.

    ![Teclado de adicionar SCIM](media/dialpad-provisioning-tutorial/redirect.png)

 ## <a name="configure-automatic-user-provisioning-to-dialpad"></a>Configurar o aprovisionamento automático de utilizadores de teclado

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no teclado de com base em atribuições de utilizador e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-dialpad-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores de teclado no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **teclado de**.

    ![A ligação de teclado na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada `https://dialpad.com/scim` no **URL de inquilino**. O valor que obtidos e guardado anteriormente de teclado de entrada **segredo de Token**. Clique em **Testar ligação** para garantir que o Azure AD pode ligar-se precisar. Se a ligação falhar, certifique-se de que a conta de teclado tem permissões de administrador e tente novamente.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para teclado de**.

    ![Mapeamentos de utilizador de teclado](media/dialpad-provisioning-tutorial/dialpad-user-mappings-new.png)

9. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o teclado no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no teclado para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador de teclado](media/dialpad-provisioning-tutorial/dialpad07.png)

10. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o Azure AD para o teclado do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

12. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento teclado de escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no teclado do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md)
##  <a name="connector-limitations"></a>Limitações de conector
* Teclado de não suporta atualmente muda de grupo. Isso significa que todas as alterações para o **displayName** de um grupo no Azure AD não vai ser atualizado e refletido no teclado.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
