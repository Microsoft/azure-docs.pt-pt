---
title: 'Tutorial: Configurar Proxyclick para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para Proxyclick automaticamente.
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
ms.openlocfilehash: c1656e6cc0c690e5a2bccfd2efab02aa843875b8
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672894"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Tutorial: Configurar Proxyclick para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Proxyclick e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar utilizadores e/ou grupos para Proxyclick.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino Proxyclick](https://www.proxyclick.com/pricing)
* Uma conta de utilizador no Proxyclick com permissões de administrador.

## <a name="add-proxyclick-from-the-gallery"></a>Adicionar Proxyclick a partir da Galeria

Antes de configurar Proxyclick para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Proxyclick a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Proxyclick a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Proxyclick**, selecione **Proxyclick** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Proxyclick na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Atribuir utilizadores a Proxyclick

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Proxyclick. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Proxyclick ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Dicas importantes para atribuir utilizadores a Proxyclick

* Recomenda-se que um único utilizador do Azure AD está atribuído a Proxyclick para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para Proxyclick, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Configurar o aprovisionamento automático de utilizadores para Proxyclick 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Proxyclick com base em atribuições de utilizador e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Proxyclick, seguindo as instruções fornecidas no [Proxyclick único início de sessão tutorial](proxyclick-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Proxyclick no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Proxyclick**.

    ![A ligação de Proxyclick na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Para obter o **URL de inquilino** e **segredo de Token** da sua conta de Proxyclick, siga o passo a passo, conforme descrito no passo 6.

6. Inicie sessão no seu [consola de administração de Proxyclick](https://app.proxyclick.com/login//?destination=%2Fdefault). Navegue para **configurações** > **integrações** > **procurar Marketplace**.

    ![Definições de Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Integrações de Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick Marketplace](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Selecione **do Azure AD**. Clique em **instalar agora**.

    ![Proxyclick do Azure AD](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Instalação de Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Selecione **aprovisionamento de utilizadores** e clique em **iniciar integração**. 

    ![Aprovisionamento de utilizadores de Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A configuração de definições apropriada IU deve agora apresentados no **configurações** > **integrações**. Selecione **configurações** sob **(aprovisionamento de utilizador) do Azure AD**.

    ![Criar Proxyclick](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Pode encontrar os **URL de inquilino** e **segredo de Token** aqui.

    ![Proxyclick criar Token](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se Proxyclick. Se a ligação falhar, certifique-se de que a conta de Proxyclick tem permissões de administrador e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Proxyclick**.

    ![Mapeamentos de utilizador de Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Proxyclick no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Proxyclick para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador Proxyclick](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para ativar o Azure AD para Proxyclick do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

15. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento Proxyclick escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Proxyclick do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Requer Proxyclick **e-mails** e **userName** para ter o mesmo valor de origem. Todas as atualizações para qualquer um dos atributos irão modificar o outro valor.
* Proxyclick não suporta o aprovisionamento de grupos.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)

