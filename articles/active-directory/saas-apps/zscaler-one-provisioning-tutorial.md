---
title: 'Tutorial: Configurar Zscaler One para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisá-lo e desprovisionar automaticamente as contas dos utilizadores para o Zscaler One.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b8b6383c7808fd6c298d7776fc10572631bc6ddc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006222"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configurar Zscaler One para fornecimento automático de utilizadores

Este tutorial demonstra os passos a executar no Zscaler One e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão e desprovisionamento automaticamente de utilizadores e grupos para Zscaler One.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AZure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações de software-as-a-service (SaaS) com diretório Azure Ative.](../app-provisioning/user-provisioning.md)


## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que você tem:

* Um inquilino do Azure AD.
* Um inquilino Zscaler One.
* Uma conta de utilizador em Zscaler One com permissões de administração.

> [!NOTE]
> A integração de provisionamento da AD Azure depende da Zscaler One SCIM API. Esta API está disponível para os desenvolvedores da Zscaler One para contas com o pacote Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicione Zscaler One do Azure Marketplace

Antes de configurar o Zscaler One para o fornecimento automático de utilizadores com Azure AD, adicione o Zscaler One do Azure Marketplace à sua lista de aplicações geridas pelo SaaS.

Para adicionar Zscaler One do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **zscaler One** e selecione **Zscaler One** do painel de resultados. Para adicionar a aplicação, **selecione Adicionar**.

    ![Zscaler Um na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir utilizadores ao Zscaler One

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso ao Zscaler One. Para atribuir estes utilizadores ou grupos ao Zscaler One, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação da empresa](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir utilizadores ao Zscaler One

* Recomendamos que atribua um único utilizador Azure AD à Zscaler One para testar a configuração automática de provisionamento do utilizador. Pode atribuir utilizadores ou grupos adicionais mais tarde.

* Quando atribuir um utilizador ao Zscaler One, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configure o fornecimento automático de utilizadores ao Zscaler One

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD. Utilize-o para criar, atualizar e desativar utilizadores ou grupos em Zscaler One com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar o sign-on único baseado em SAML para Zscaler One. Siga as instruções no [Zscaler Um único tutorial de inscrição](zscaler-One-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para zscaler one em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**  >  **Zscaler One**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zscaler One**.

    ![O link Zscaler One na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Zscaler One Provisioning](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Modo de Provisionamento Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sob a secção **credenciais de administrador,** preencha as caixas DE URL e **Token Secreto** do **Arrendatário** com as definições para a sua conta Zscaler One, conforme descrito no Passo 6.

6. Para obter o URL do inquilino **Administration** e o token secreto, vá às  >  **Definições de Autenticação** da Administração no portal Zscaler One UI. No **tipo de autenticação,** selecione **SAML**.

    ![Definições de autenticação Zscaler One](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Selecione **Configurar SAML** para abrir as opções **Configure SAML.**

    ![Zscaler One Configure SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. **Selecione Ative SCIM-Based Provisioning** para obter as definições em **URL base** e **Token do portador**. Em seguida, guarde as definições. Copie a definição **de URL base** para o URL do **inquilino** no portal Azure. Copie a definição **de Token do Portador** para o Secret **Token** no portal Azure.

7. Depois de preencher as caixas mostradas no Passo 5, selecione **a Ligação de Teste** para se certificar de que o Azure AD pode ligar-se ao Zscaler One. Se a ligação falhar, certifique-se de que a sua conta Zscaler One tem permissões de administração e tente novamente.

    ![Ligação de teste Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação Zscaler One](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Zscaler One**.

    ![Sincronização do utilizador Zscaler One](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler One na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador em Zscaler One para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Zscaler Um atributo de utilizador correspondente](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Zscaler One**.

    ![Sincronização do grupo Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler One na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder aos grupos em Zscaler One para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Zscaler Um atributo de grupo correspondente](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de AD Azure para zscaler One, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estado de Provisionamento Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores ou grupos que pretende providenciar ao Zscaler One. Na secção **Definições,** selecione os valores que pretende no **Âmbito**.

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para a provisão, **selecione Save**.

    ![Zscaler One Save](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser praticada do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD funciona. 

Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD no Zscaler One.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png