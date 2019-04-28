---
title: 'Tutorial: Configurar o Zscaler um para o aprovisionamento automático de utilizadores no Azure Active Directory | Documentos da Microsoft'
description: Saiba como configurar o Azure Active Directory para provisionar e desprovisionar contas de utilizador com o Zscaler um automaticamente.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 72f6ba2b-73ed-420a-863a-aff672f26fa3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 5319b0ac06c4ddf1a7627a4e7fe0bfb2694f79f6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114615"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configurar o Zscaler um para o aprovisionamento automático de utilizadores

Este tutorial demonstra os passos para executar no Zscaler um e Azure Active Directory (Azure AD) para configurar o Azure AD para aprovisionar e desaprovisionar utilizadores e grupos com o Zscaler um automaticamente.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de aprovisionamento de utilizador do Azure AD. Para obter informações sobre o que faz este serviço, como ele funciona e perguntas mais frequentes, consulte [automatizar o aprovisionamento de utilizador e a aplicações de software-como-serviço (SaaS) com o Azure Active Directory de desaprovisionamento](../active-directory-saas-app-provisioning.md).
>
> Este conector está atualmente disponível como pré-visualização. Para obter mais informações sobre os Microsoft Azure termos de utilização gerais para funcionalidades de pré-visualização, veja [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial parte do princípio de que tem:

* Um inquilino do Azure AD.
* Um inquilino do Zscaler um.
* Uma conta de utilizador no Zscaler um com permissões de administrador.

> [!NOTE]
> O Azure AD, integração de aprovisionamento conta com a API do Zscaler um SCIM. Esta API está disponível para programadores do Zscaler um para contas com o pacote da empresa.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicionar Zscaler um no Azure Marketplace

Antes de configurar o Zscaler um para automático de utilizadores de aprovisionamento com o Azure AD, adicione Zscaler um no Azure Marketplace à sua lista de aplicações de SaaS geridas.

Para adicionar um do Zscaler do Marketplace, siga estes passos.

1. Na [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O ícone do Azure Active Directory](common/select-azuread.png)

2. Aceda a **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **Zscaler um** e selecione **Zscaler um** do painel de resultados. Para adicionar a aplicação, selecione **adicionar**.

    ![Zscaler um na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir utilizadores a um do Zscaler

O Azure Active Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o aprovisionamento automático de utilizadores, decida quais os utilizadores ou grupos no Azure AD precisam de acesso a um do Zscaler. Para atribuir estes utilizadores ou grupos a Zscaler um, siga as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir utilizadores a um do Zscaler

* Recomendamos que atribua um único utilizador do Azure AD para um Zscaler para testar o configuração de aprovisionamento automático de utilizadores. Pode atribuir utilizadores adicionais ou grupos mais tarde.

* Quando atribui um utilizador Zscaler uma, selecione qualquer função específicas da aplicação válida, se estiver disponível, na caixa de diálogo de atribuição. Os utilizadores com o **acesso predefinido** função são excluídas desde o aprovisionamento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configurar o aprovisionamento automático de utilizadores para uma de Zscaler

Esta secção orienta-o pelos passos para configurar o serviço de aprovisionamento do AD do Azure. Utilize-o para criar, atualizar e desativar os utilizadores ou grupos no Zscaler um com base em atribuições de utilizador ou grupo no Azure AD.

> [!TIP]
> Também pode ativar baseado em SAML início de sessão único para Zscaler um. Siga as instruções no [Zscaler um único início de sessão tutorial](zscaler-One-tutorial.md). Início de sessão único a pode ser configurada independentemente de aprovisionamento automático de utilizadores, embora esses dois recursos complementam uma à outra.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configurar o aprovisionamento automático de utilizadores para Zscaler um no Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações empresariais** > **todas as aplicações** > **Zscaler um**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler um**.

    ![A Zscaler uma ligação na lista de aplicações](common/all-applications.png)

3. Selecione o **aprovisionamento** separador.

    ![Zscaler um aprovisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Definir o **modo de aprovisionamento** ao **automática**.

    ![Um modo de aprovisionamento Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sob o **credenciais de administrador** secção, preencha o **URL de inquilino** e **segredo de Token** caixas com as definições para o Zscaler uma conta, tal como descrito no passo 6.

6. Para obter o inquilino de URL e o token SECRETO, vá para **Administration** > **definições de autenticação** no portal do Zscaler uma interface do Usuário. Sob **tipo de autenticação**, selecione **SAML**.

    ![Definições de autenticação do Zscaler um](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecione **configurar SAML** para abrir o **configurar SAML** opções.

    ![Configurar o Zscaler um SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Selecione **aprovisionamento Enable SCIM-Based** para obter as definições **URL de Base** e **Token de portador**. Em seguida, guarde as definições. Copiar o **URL de Base** na definição **URL de inquilino** no portal do Azure. Cópia a **Token de portador** definição **segredo de Token** no portal do Azure.

7. Depois de preencher as caixas de mostrado no passo 5, selecione **Testar ligação** certificar-se de que o Azure AD pode ligar a Zscaler um. Se a ligação falhar, certifique-se de que sua Zscaler uma conta com permissões de administrador e tente novamente.

    ![Ligação de um teste de Zscaler](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Na **notificação por E-Mail** caixa, introduza o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de aprovisionamento. Selecione o **enviar uma notificação por e-mail quando ocorre uma falha** caixa de verificação.

    ![Zscaler um E-Mail de notificação](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Sob o **mapeamentos** secção, selecione **sincronizar utilizadores do Azure Active Directory Zscaler uma**.

    ![Sincronização do Zscaler um utilizador](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos de utilizador que são sincronizados a partir do Azure AD para Zscaler um da **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas de acordo com as contas de utilizador no Zscaler um para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Zscaler um atributos de utilizador correspondentes](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Sob o **mapeamentos** secção, selecione **sincronizar grupos do Azure Active Directory Zscaler uma**.

    ![Zscaler uma sincronização de grupo](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos de grupo que são sincronizados a partir do Azure AD para Zscaler um da **mapeamentos de atributos** secção. Os atributos selecionados como **correspondência** propriedades são usadas para fazer corresponder os grupos no Zscaler um para operações de atualização. Para guardar as alterações, selecione **guardar**.

    ![Atributos de grupo correspondentes Zscaler um](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de âmbito, siga as instruções a [tutorial âmbito do filtro](./../active-directory-saas-scoping-filters.md).

15. Para ativar o Azure AD para um Zscaler, do serviço de aprovisionamento no **configurações** secção, altere **estado de aprovisionamento** para **no**.

    ![Um Estado de aprovisionamento Zscaler](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores ou grupos que pretende fazer o aprovisionamento Zscaler um. Na **definições** secção, selecione os valores que pretende na **âmbito**.

    ![Zscaler um âmbito](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para aprovisionar, selecione **guardar**.

    ![Zscaler um guardar](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos na **âmbito** no **definições** secção. A sincronização inicial demora mais tempo a serem executados do que as sincronizações posteriores. Que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de aprovisionamento do AD do Azure é executado. 

Pode utilizar o **detalhes de sincronização** secção para monitorizar o progresso e siga as ligações para o relatório de atividade de aprovisionamento. O relatório descreve todas as ações executadas pelo Azure AD na Zscaler One do serviço de aprovisionamento.

Para obter informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como rever os registos e obter relatórios de atividade de aprovisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
