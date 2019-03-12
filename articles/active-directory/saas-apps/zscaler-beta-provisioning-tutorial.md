---
title: 'Tutorial: Configurar o Zscaler Beta para aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para aprovisionar e desaprovisionar contas de utilizador para o Beta do Zscaler automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: na
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2019
ms.author: v-ant-msft
ms.openlocfilehash: b2fb001afc2f9644edb2c7598b86d2c0d50571d5
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57572430"
---
# <a name="tutorial-configure-zscaler-beta-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler Beta para aprovisionamento automático de utilizadores

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Zscaler Beta e o Azure Active Directory (Azure AD) para configurar o Azure AD automaticamente aprovisionar e desaprovisionar os utilizadores e/ou grupos para o Zscaler Beta.

> [!NOTE]
> Este tutorial descreve um conector assentes no serviço de aprovisionamento de utilizador do Azure AD. Para obter detalhes importantes sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações SaaS com o Azure Active Directory de desaprovisionamento](../active-directory-saas-app-provisioning.md).

> Este conector está atualmente em pré-visualização pública. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que já tem o seguinte:

*   Um inquilino do Azure AD
*   Um inquilino do Zscaler Beta
*   Uma conta de utilizador na versão Beta do Zscaler com permissões de administrador

> [!NOTE]
> A integração de aprovisionamento do Azure AD depende da API de SCIM do Zscaler Beta, que está disponível para programadores do Zscaler Beta para contas com o pacote da empresa.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Adicionando Zscaler Beta da Galeria
Antes de configurar o Zscaler Beta para automático de utilizadores de aprovisionamento com o Azure AD, terá de adicionar Zscaler Beta a partir da Galeria de aplicações do Azure AD à sua lista de aplicações de SaaS geridas.

**Para adicionar o Zscaler Beta da Galeria de aplicações do Azure AD, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique nas **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais** > **todas as aplicações**.

    ![As secção de aplicações empresariais][2]

3. Para adicionar Zscaler Beta, clique a **nova aplicação** botão na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Zscaler Beta**.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/app-search.png)

5. No painel de resultados, selecione **Zscaler Beta**e, em seguida, clique nas **Add** botão para adicionar a versão Beta do Zscaler à sua lista de aplicações SaaS.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/app-search-results.png)

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/app-creation.png)

## <a name="assigning-users-to-zscaler-beta"></a>Atribuir utilizadores a versão Beta do Zscaler

O Azure Active Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas a utilizadores e/ou grupos que foram "atribuídos" a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento de utilizador automático, deve decidir o que os utilizadores e/ou grupos no Azure AD precisam de acesso a Zscaler Beta. Assim que decidir, pode atribuir estes utilizadores e/ou grupos para o Beta do Zscaler ao seguir as instruções aqui:

*   [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler-beta"></a>Dicas importantes para atribuir utilizadores a versão Beta do Zscaler

*   Recomenda-se que um único utilizador do Azure AD está atribuído a Zscaler Beta para testar o configuração de aprovisionamento automático de utilizadores. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador para o Zscaler Beta, tem de selecionar qualquer função de específicas da aplicação válida (se disponível) na caixa de diálogo atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configuring-automatic-user-provisioning-to-zscaler-beta"></a>Configurar o aprovisionamento automático de utilizadores para o Beta do Zscaler

Esta secção orienta-o pelos passos para configurar o Azure AD do serviço de aprovisionamento para criar, atualizar e desativar os utilizadores e/ou grupos na versão Beta do Zscaler com base no utilizador e/ou atribuições de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar baseado em SAML início de sessão único para Zscaler Beta, seguindo as instruções fornecidas no [Zscaler Beta único início de sessão tutorial](zscaler-beta-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementar entre si.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-beta-in-azure-ad"></a>Para configurar o aprovisionamento automático de utilizadores do Zscaler Beta no Azure AD:

1. Entrar para o [portal do Azure](https://portal.azure.com) e procure **Azure Active Directory > aplicações empresariais > todos os aplicativos**.

2. Selecione a versão Beta do Zscaler da sua lista de aplicações SaaS.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/app-instance-search.png)

3. Selecione o **aprovisionamento** separador.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-tab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-credentials.png)

5. Sob o **credenciais de administrador** secção, de entrada a **URL de inquilino** e **segredo de Token** da sua conta do Zscaler Beta, tal como descrito no passo 6.

6. Para obter o **URL de inquilino** e **segredo de Token**, navegue até **administração > definições de autenticação** na interface de utilizador do portal do Zscaler Beta e clique em  **SAML** sob **tipo de autenticação**. 

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-1.png)
    
    Clique em **configurar SAML** para abrir o **configuração SAML** opções. 

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/secret-token-2.png)
    
    Selecione **aprovisionamento Enable SCIM-Based** para obter **URL de Base** e **Token de portador**, em seguida, guarde as definições. Cópia a **URL de Base** ao **URL de inquilino**, e **Token de portador** para **segredo de Token** no portal do Azure.

7. Após preencher os campos mostrados no passo 5, clique em **Testar ligação** para garantir que o Azure AD pode ligar-se Zscaler Beta. Se a ligação falhar, certifique-se de que sua conta do Zscaler Beta com permissões de administrador e tente novamente.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/test-connection.png)
    
8. Na **notificação por E-Mail** campo, introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de aprovisionamento e marque a caixa de verificação **enviar uma notificação por e-mail quando uma falha ocorre**.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/notification.png)

9. Clique em **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar do Azure Active Directory Users para Zscaler Beta**.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Zscaler Beta no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador na versão Beta do Zscaler para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/user-attribute-mappings.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar Azure grupos do Active Directory para o Beta do Zscaler**.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para Zscaler Beta no **mapeamento do atributo** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos na versão Beta do Zscaler para operações de atualização. Selecione o **guardar** botão para consolidar as alterações.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de âmbito, consulte as seguintes instruções fornecidas a [tutorial de filtro de Scoping](./../active-directory-saas-scoping-filters.md).

15. Para ativar o Azure AD para Zscaler Beta do serviço de aprovisionamento, altere a **estado de aprovisionamento** para **no** no **definições** secção.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/provisioning-status.png)

16. Definir a utilizadores e/ou grupos que deseja fazer o aprovisionamento Zscaler Beta escolhendo os valores pretendidos na **âmbito** no **definições** secção.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, clique em **guardar**.

    ![Aprovisionamento de Beta de Zscaler](./media/zscaler-beta-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações subsequentes, o que ocorrer aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure está em execução. Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade, que descreve todas as ações executadas pelo Azure AD na versão Beta do Zscaler do serviço de aprovisionamento de aprovisionamento.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-beta-provisioning-tutorial/tutorial-general-03.png
