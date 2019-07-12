---
title: 'Tutorial: Configurar o Envoy para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para o Envoy automaticamente.
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
ms.openlocfilehash: df4c5895e15e7e9e63ad1f3d273af1c3fdab2e90
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672730"
---
# <a name="tutorial-configure-envoy-for-automatic-user-provisioning"></a>Tutorial: Configurar o Envoy para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Envoy e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar automaticamente e os utilizadores de desaprovisionar e/ou grupos para o Envoy.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino o Envoy](https://envoy.com/pricing/)
* Uma conta de utilizador no Envoy com permissões de administrador.

## <a name="add-envoy-from-the-gallery"></a>Adicionar o Envoy a partir da Galeria

Antes de configurar o Envoy para aprovisionamento automático de utilizadores com o Azure AD, terá de adicionar o Envoy a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Envoy a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Envoy**, selecione **o Envoy** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Envoy na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-envoy"></a>Atribuir utilizadores a Envoy

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam acessar o Envoy. Depois de decidir, pode atribuir estes utilizadores e/ou grupos para o Envoy ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-envoy"></a>Dicas importantes para atribuir utilizadores a Envoy

* Recomenda-se que um único utilizador do Azure AD é atribuído para o Envoy para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para o Envoy, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-envoy"></a>Configurar o aprovisionamento automático de utilizadores para o Envoy 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Envoy com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para o Envoy, seguindo as instruções fornecidas no [o Envoy único início de sessão tutorial](envoy-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-envoy-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para o Envoy no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **o Envoy**.

    ![A ligação do Envoy na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada `https://app.envoy.com/scim/v2` no **URL de inquilino**. Para obter o **segredo de Token** da sua conta o Envoy, siga o passo a passo, conforme descrito no passo 6.

6. Inicie sessão no seu [consola de administração o Envoy](https://dashboard.envoy.com/login). Clique em **integrações**.

    ![O Envoy integrações](media/envoy-provisioning-tutorial/envoy01.png)

    Clique em **instale** para o **integração do Microsoft Azure SCIM**.

    ![O Envoy instalação](media/envoy-provisioning-tutorial/envoy02.png)

    Clique em **salvar** para **sincronizar todos os utilizadores**. 

    ![Envoy guardar](media/envoy-provisioning-tutorial/envoy03.png)

    Obter o Token segredo preenchido.
    
    ![Envoy OAUTH](media/envoy-provisioning-tutorial/envoy04.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se o Envoy. Se a ligação falhar, certifique-se de que sua conta o Envoy tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para o Envoy**.
    
    ![O Envoy atributos de utilizador](media/envoy-provisioning-tutorial/envoy-user-mappings.png)
    
11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para o Envoy no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Envoy para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![O Envoy atributos de utilizador](media/envoy-provisioning-tutorial/envoy-user-attribute.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory para o Envoy**.

    ![O Envoy mapeamentos de utilizador](media/envoy-provisioning-tutorial/envoy-group-mapping.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para o Envoy no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Envoy para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![O Envoy mapeamentos de utilizador](media/envoy-provisioning-tutorial/envoy-group-attributes.png)
    
14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o Azure AD para o Envoy do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

16. Definir a utilizadores e/ou grupos que pretende aprovisionar para o Envoy escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Envoy do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

