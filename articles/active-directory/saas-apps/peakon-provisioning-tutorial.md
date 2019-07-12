---
title: 'Tutorial: Configurar o aprovisionamento de utilizadores automático Peakon com o Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Peakon automaticamente.
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
ms.date: 06/28/2019
ms.author: zhchia
ms.openlocfilehash: 2547f34432ca1b4b52f34c343bb4aad2f2407f53
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673148"
---
# <a name="tutorial-configure-peakon-for-automatic-user-provisioning"></a>Tutorial: Configurar Peakon para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Peakon e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para Peakon.

> [!NOTE]
>  Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial assume que já tem os seguintes pré-requisitos

* Um inquilino do Azure AD.
* [Um inquilino Peakon](https://peakon.com/us/pricing/).
* Uma conta de utilizador no Peakon com permissões de administrador.

## <a name="assigning-users-to-peakon"></a>Atribuir utilizadores a Peakon

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Peakon. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Peakon ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-peakon"></a>Dicas importantes para atribuir utilizadores a Peakon 

* Recomenda-se que um único utilizador do Azure AD está atribuído a Peakon para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para Peakon, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="set-up-peakon-for-provisioning"></a>Configurar a Peakon para aprovisionamento

1.  Inicie sessão no seu [consola de administração de Peakon](https://app.Peakon.com/login). Clique em **configuração**. 

    ![Consola de administração de Peakon](media/Peakon-provisioning-tutorial/Peakon-admin-configuration.png)

2.  Selecione **integrações**.
    
    ![Aprovisionamento de funcionário de Peakon](media/Peakon-provisioning-tutorial/Peakon-select-integration.png)

3.  Ativar **funcionário aprovisionamento**.

    ![Aprovisionamento de funcionário de Peakon](media/Peakon-provisioning-tutorial/peakon05.png)

4.  Copie os valores de **URL de 2.0 SCIM** e **Token de portador do OAuth**. Estes valores serão introduzidos na **URL de inquilino** e **segredo de Token** campo no separador aprovisionamento da sua aplicação Peakon no portal do Azure.

    ![Peakon criar Token](media/Peakon-provisioning-tutorial/peakon04.png)

## <a name="add-peakon-from-the-gallery"></a>Adicionar Peakon a partir da Galeria

Para configurar o Peakon para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Peakon a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Peakon**, selecione **Peakon** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Peakon na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-peakon"></a>Configurar o aprovisionamento automático de utilizadores para Peakon 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Peakon com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Peakon, seguindo as instruções fornecidas no [tutorial de início de sessão único de Peakon](peakon-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-peakon--in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Peakon no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Peakon**.

    ![A ligação de Peakon na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada a **SCIM 2.0 URL** e **o Token de portador do OAuth** valores que obteve anteriormente no **URL de inquilino** e **segredo de Token** , respetivamente. Clique em **Testar ligação** para garantir que o Azure AD pode ligar-se Peakon. Se a ligação falhar, certifique-se de que a conta de Peakon tem permissões de administrador e tente novamente.

    ![URL de inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

7. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Peakon**.

    ![Mapeamentos de utilizador de Peakon](media/Peakon-provisioning-tutorial/Peakon-user-mappings.png)

10. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Peakon no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Peakon para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador Peakon](media/Peakon-provisioning-tutorial/Peakon-user-attributes.png)

12. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).
    
    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Peakon do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Tem de ser expandido a partir SCIM utilizador extensão personalizada do Peakon de todos os atributos de utilizador personalizada no Peakon `urn:ietf:params:scim:schemas:extension:peakon:2.0:User`.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)