---
title: 'Tutorial: Configurar o Zoom para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para automaticamente aprovisionar e desaprovisionar contas de utilizador para aplicar Zoom.
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
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: 0a21a35e6d4a938d41e8bc11cebc1be5738d893e
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671014"
---
# <a name="tutorial-configure-zoom-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zoom para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zoom e o Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar automaticamente e os utilizadores de desaprovisionar e/ou grupos para aplicar Zoom.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino de Zoom](https://zoom.us/pricing)
* Uma conta de utilizador no Zoom com permissões de administrador

## <a name="add-zoom-from-the-gallery"></a>Adicionar Zoom a partir da Galeria

Antes de configurar o Zoom para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar Zoom a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Zoom a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Zoom**, selecione **Zoom** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Na lista de resultados de zoom](common/search-new-app.png)

## <a name="assign-users-to-zoom"></a>Atribuir utilizadores para aplicar Zoom

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso para aplicar Zoom. Depois de decidir, pode atribuir estes utilizadores e/ou grupos para aplicar Zoom ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zoom"></a>Dicas importantes para atribuir utilizadores a Zoom

* Recomenda-se que um único utilizador do Azure AD é atribuído para aplicar Zoom para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para aplicar Zoom, tem de selecionar qualquer função específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configure-automatic-user-provisioning-to-zoom"></a>Configurar o aprovisionamento automático de utilizadores para aplicar Zoom 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores ou grupos de Zoom com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Zoom, seguindo as instruções fornecidas no [Zoom único início de sessão tutorial](zoom-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="configure-automatic-user-provisioning-for-zoom-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores para Zoom no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zoom**.

    ![A ligação de Zoom na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** , digite `https://api.zoom.us/scim` no **URL de inquilino**. Para obter o **segredo de Token** da sua conta de Zoom, siga o passo a passo, conforme descrito no passo 6.

6. Inicie sessão no seu [consola de administração de Zoom](https://zoom.us/signin). Navegue para **avançadas > Zoom para os desenvolvedores** no painel de navegação esquerdo.

    ![Integrações de zoom](media/zoom-provisioning-tutorial/zoom01.png)

    Navegue para **gerir** no canto superior direito da página. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom02.png)

    Navegue para o seu criado aplicação do Azure AD. 
    
    ![Aplicação de zoom](media/zoom-provisioning-tutorial/zoom03.png)

    Selecione **aplicação credenciais** no painel de navegação esquerdo.

    ![Aplicação de zoom](media/zoom-provisioning-tutorial/zoom04.png)

    Obter o valor do Token do JWT mostrado abaixo e entrada isto para o **segredo de Token** campo no Azure AD. Se precisar de um novo token de acesso sem prazo de expiração, terá de reconfigurar a expiração do tempo que será automaticamente gerar um novo token. 

    ![Instalação de zoom](media/zoom-provisioning-tutorial/zoom05.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar para aplicar Zoom. Se a ligação falhar, certifique-se de que a conta de Zoom tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para aplicar Zoom**.

    ![Mapeamentos de utilizador de zoom](media/zoom-provisioning-tutorial/zoom-user-mapping.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para aplicar Zoom no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zoom para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.
    
     ![Mapeamentos de utilizador de zoom](media/zoom-provisioning-tutorial/zoom-user-attributes.png)

12. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para ativar o Azure AD para Zoom do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.
    
    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

14. Definir a utilizadores e/ou grupos que pretende aprovisionar para aplicar Zoom ao selecionar os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Zoom do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Zoom não suporta o aprovisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
