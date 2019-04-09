---
title: 'Tutorial: Configurar o Zscaler três para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador com o Zscaler três automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 385a1153-0f47-4e41-8f44-da1b49d7629e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: ed158ae825ec8aac24a57eb0f5a986b2124b66fb
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057800"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler três para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler três e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e/ou grupos para Zscaler três automaticamente.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../active-directory-saas-app-provisioning.md).
>
> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem o seguinte:

* Um inquilino do Azure AD
* Um inquilino do Zscaler três
* Uma conta de utilizador no Zscaler três com permissões de administrador

> [!NOTE]
> A integração de aprovisionamento do Azure AD depende da API do Zscaler três SCIM, que está disponível para programadores do Zscaler três para contas com o pacote da empresa.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler três da Galeria

Antes de configurar Zscaler três automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Zscaler três da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar Zscaler três a partir da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Zscaler três**, selecione **Zscaler três** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Zscaler três na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-zscaler-three"></a>Atribuir utilizadores a três de Zscaler

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Zscaler três. Depois de decidir, pode atribuir estes utilizadores e/ou grupos a Zscaler três ao seguir as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Dicas importantes para atribuir utilizadores a três de Zscaler

* Recomenda-se que um único utilizador do Azure AD está atribuído a Zscaler três para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um utilizador para Zscaler três, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-three"></a>Configurar o aprovisionamento automático de utilizadores para três do Zscaler

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos no Zscaler três com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Zscaler três, seguindo as instruções fornecidas no [Zscaler três único início de sessão tutorial](zscaler-three-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-three-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores para Zscaler três no Azure AD:

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Zscaler três**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler três**.

    ![A ligação Zscaler três na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino** e **segredo de Token** da sua conta Zscaler três, conforme descrito no passo 6.

6. Para obter o **URL de inquilino** e **segredo de Token**, navegue até **administração > definições de autenticação** na interface de utilizador do portal Zscaler três e clique em  **SAML** sob **tipo de autenticação**. 

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Clique em **configurar SAML** para abrir **configuração SAML** opções.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Selecione **aprovisionamento Enable SCIM-Based** para obter **URL de Base** e **Token de portador**, em seguida, guarde as definições. Cópia a **URL de Base** ao **URL de inquilino** e **Token de portador** para **segredo de Token** no portal do Azure.

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar a Zscaler três. Se a ligação falhar, certifique-se de que sua Zscaler três conta com permissões de administrador e tente novamente.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory para Zscaler três**.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Rever os atributos de utilizador que são sincronizados a partir do Azure AD Zscaler três para o **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zscaler três para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar Azure grupos do Active Directory para Zscaler três**.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Rever os atributos de grupo que são sincronizados a partir do Azure AD Zscaler três para o **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Zscaler três para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](./../active-directory-saas-scoping-filters.md).

15. Para ativar o Azure AD para Zscaler três do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento Zscaler três escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Aprovisionamento do Zscaler três](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD no Zscaler três do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
