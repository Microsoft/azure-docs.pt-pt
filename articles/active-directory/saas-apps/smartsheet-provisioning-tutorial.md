---
title: 'Tutorial: Configurar o Smartsheet para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador ao Smartsheet.
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
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: f0ca2dfa90e1312db664962e7ffbe6b3f4dd96e1
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670943"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Tutorial: Configurar o Smartsheet para o aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Smartsheet e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar os utilizadores e/ou grupos ao Smartsheet.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD
* [Um inquilino do Smartsheet](https://www.smartsheet.com/pricing)
* Uma conta de utilizador com um plano de Smartsheet Enterprise ou Enterprise Premier com permissões de administrador do sistema.

## <a name="assign-users-to-smartsheet"></a>Atribuir utilizadores a Smartsheet

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso ao Smartsheet. Depois de decidir, pode atribuir estes utilizadores e/ou grupos ao Smartsheet, seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-smartsheet"></a>Dicas importantes para atribuir utilizadores a Smartsheet

* Recomenda-se que um único utilizador do Azure AD é atribuído ao Smartsheet para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao Smartsheet, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

* Para se certificar de paridade em atribuições de funções de utilizador entre Smartsheet e o Azure AD, é recomendado para utilizar as atribuições de funções mesmo preenchidas na lista de utilizadores completa do Smartsheet. Para obter esta lista de utilizador do Smartsheet, navegue para **conta de administrador > Gestão de utilizadores > mais ações > Transferir a lista de utilizadores (csv)** .

* Para aceder a determinados recursos na aplicação, Smartsheet exige que o usuário ter múltiplas funções. Para saber mais sobre os tipos de utilizador e permissões no Smartsheet, aceda a [tipos de utilizador e permissões](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions).

*  Se um utilizador tiver várias funções atribuídas no Smartsheet, **tem** Certifique-se de que estas atribuições de função são replicadas no Azure AD para evitar um cenário em que os utilizadores poderiam perder acesso a objetos de Smartsheet permanentemente. Cada função exclusiva no Smartsheet **tem** ser atribuídos a um grupo diferente no Azure AD. O usuário **tem** , em seguida, ser adicionado a cada um dos grupos correspondentes às funções assim o desejar. 

## <a name="set-up-smartsheet-for-provisioning"></a>Configurar o Smartsheet para o aprovisionamento

Antes de configurar o Smartsheet para automático de utilizadores de aprovisionamento com o Azure AD, terá de ativar o aprovisionamento de SCIM no Smartsheet.

1. Inicie sessão como um **SysAdmin** no **[Smartsheet portal](https://app.smartsheet.com/b/home)** e navegue para **administrador de conta**.

    ![Administrador de conta de Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Aceda a **controlos de segurança > automático de utilizador de aprovisionamento > Editar**.

    ![Controlos de segurança do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adicionar e validar os domínios de e-mail para os utilizadores que pretende aprovisionar a partir do Azure AD ao Smartsheet. Escolher **não ativada** para se certificar de que todas as ações de aprovisionamento apenas ser originado do Azure AD e também garantir que a sua lista de utilizadores do Smartsheet é sincronizada com as atribuições do Azure AD.

    ![Aprovisionamento de utilizadores do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Depois de concluída a validação, terá de ativar o domínio. 

    ![Domínio de ativar do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Gerar a **segredo de Token** necessárias para configurar o aprovisionamento automático de utilizadores com o Azure AD, navegando até **aplicações e integrações**.

    ![Instalação do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Escolher **acesso à API**. Clique em **gerar novo token de acesso**.

    ![Instalação do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Defina o nome do Token de acesso a API. Clique em **OK**.

    ![Instalação do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Copie o Token de acesso de API e guardá-lo, pois esta será a única vez, pode vê-lo. Isto é necessário no **segredo de Token** campo no Azure AD.

    ![Token do Smartsheet](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="add-smartsheet-from-the-gallery"></a>Adicionar Smartsheet a partir da Galeria

Para configurar o Smartsheet para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Smartsheet a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

1. Na  **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o **nova aplicação** botão na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Smartsheet**, selecione **Smartsheet** no painel de resultados. 

    ![Smartsheet na lista de resultados](common/search-new-app.png)

5. Selecione o **Inscreva-se Smartsheet** botão que irá redirecioná-lo à página de início de sessão do Smartsheet. 

    ![Adicionar Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-add.png)

6. Como Smartsheet é uma aplicação de OpenIDConnect, optar por iniciar sessão no Smartsheet com a conta de trabalho da Microsoft.

    ![Início de sessão do Smartsheet OIDC](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-login.png)

7. Após uma autenticação com êxito, aceite a solicitação de consentimento para a página de consentimento. O aplicativo, em seguida, irá ser automaticamente adicionado ao seu inquilino e será redirecionado para a sua conta de Smartsheet.

    ![Smartsheet OIDc Consent](media/smartsheet-provisioning-tutorial/smartsheet-OIDC-consent.png)

## <a name="configure-automatic-user-provisioning-to-smartsheet"></a>Configurar o aprovisionamento automático de utilizadores ao Smartsheet 

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Smartsheet com base em atribuições de utilizador e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Para configurar o aprovisionamento de utilizadores automático de Smartsheet no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais**, em seguida, selecione **todos os aplicativos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Smartsheet**.

    ![A ligação de Smartsheet na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Guia de aprovisionamento](common/provisioning.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Guia de aprovisionamento](common/provisioning-automatic.png)

5. Sob o **credenciais de administrador** secção, de entrada `https://scim.smartsheet.com/v2/` no **URL de inquilino**. O valor que obtidos e guardado anteriormente do Smartsheet de entrada **segredo de Token**. Clique em **Testar ligação** para garantir que o Azure AD pode estabelecer ligação ao Smartsheet. Se a ligação falhar, certifique-se de que a conta de Smartsheet tem permissões de administrador do sistema e tente novamente.

    ![Certificado de](common/provisioning-testconnection-tenanturltoken.png)

6. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação - **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![E-Mail de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory ao Smartsheet**.

    ![Mapeamentos de utilizador do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-mappings.png)

9. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD ao Smartsheet no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Smartsheet para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Atributos de utilizador do Smartsheet](media/smartsheet-provisioning-tutorial/smartsheet-user-attributes.png)

10. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para ativar o Azure AD para Smartsheet do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Estado de aprovisionamento ativado](common/provisioning-toggle-on.png)

12. Definir a utilizadores e/ou grupos que pretende fazer o aprovisionamento Smartsheet escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Âmbito de aprovisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![A guardar a configuração de aprovisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Smartsheet do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações de conector

* Smartsheet não suporta exclusões de forma recuperável. Quando um usuário **Active Directory** atributo é definido como False, Smartsheet elimina o utilizador permanentemente.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../manage-apps/check-status-user-account-provisioning.md)
