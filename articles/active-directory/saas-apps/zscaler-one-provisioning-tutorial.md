---
title: 'Tutorial: Configure Zscaler One para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador ao Zscaler One.
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
ms.author: jeedes
ms.openlocfilehash: 87413932acee576934ee50b59546371b03ceaf7e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064177"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler One para fornecimento automático de utilizadores

Este tutorial demonstra os passos a realizar no Zscaler One e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e grupos para zscaler One.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](../active-directory-saas-app-provisioning.md)


## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que tem:

* Um inquilino do Azure AD.
* Um inquilino Zscaler One.
* Uma conta de utilizador em Zscaler One com permissões de administração.

> [!NOTE]
> A integração de provisionamento da AD Azure baseia-se na API Zscaler One SCIM. Esta API está disponível para os desenvolvedores zscaler One para contas com o pacote Enterprise.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Adicione Zscaler One do Azure Marketplace

Antes de configurar o Zscaler One para o fornecimento automático de utilizadores com a Azure AD, adicione o Zscaler One do Azure Marketplace à sua lista de aplicações SaaS geridas.

Para adicionar Zscaler One do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **o Zscaler One** e selecione **Zscaler One** do painel de resultados. Para adicionar a aplicação, selecione **Adicionar**.

    ![Zscaler Um na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Atribuir utilizadores ao Zscaler One

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos da AD Azure que precisam de acesso ao Zscaler One. Para atribuir estes utilizadores ou grupos ao Zscaler One, siga as instruções em [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Dicas importantes para atribuir utilizadores ao Zscaler One

* Recomendamos que atribua um único utilizador da AD Azure ao Zscaler One para testar a configuração automática de fornecimento do utilizador. Mais tarde, pode atribuir utilizadores ou grupos adicionais.

* Quando atribuir um utilizador ao Zscaler One, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Configure o fornecimento automático de utilizadores ao Zscaler One

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure. Utilize-o para criar, atualizar e desativar utilizadores ou grupos em Zscaler One com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar um único sinal baseado em SAML para zscaler One. Siga as instruções no tutorial de inscrição único do [Zscaler One](zscaler-One-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para zscaler One em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Enterprise** > **Todas as aplicações** > **Zscaler One**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler One**.

    ![O link Zscaler One na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Fornecimento Zscaler One](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Zscaler Um Modo de Provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. Sob a secção **de Credenciais de Administrador,** preencha as **caixas DE URL** do Inquilino e **secretas** com as definições para a sua conta Zscaler One, conforme descrito no Passo 6.

6. Para obter o URL do inquilino e ficha secreta, vá à **Administração** > Definições de **Autenticação** no portal Zscaler One UI. No tipo de **autenticação,** selecione **SAML**.

    ![Definições de autenticação zscaler one](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. **Selecione Configure SAML** para abrir as opções **SAML de Configuração.**

    ![Zscaler One Configure SAML](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. **Selecione ativar o fornecimento baseado em SCIM** para obter as definições no URL **base** e **no token**do portador . Em seguida, guarde as definições. Copie a definição de **URL base** para o URL do **Inquilino** no portal Azure. Copie o cenário do **Token bearer** para **O Token Secreto** no portal Azure.

7. Depois de preencher as caixas mostradas no Passo 5, selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se ao Zscaler One. Se a ligação falhar, certifique-se de que a sua conta Zscaler One tem permissões de administração e tente novamente.

    ![Ligação de teste Zscaler One](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Zscaler Um E-mail de notificação](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zscaler One**.

    ![Zscaler Um utilizador sincronização](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zscaler One na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Zscaler One para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Zscaler One atributos de utilizador correspondentes](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Zscaler One**.

    ![Sincronização do grupo Zscaler One](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Zscaler One na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Zscaler One para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Zscaler One atributos de grupo correspondente](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de deteção](./../active-directory-saas-scoping-filters.md).

15. Para ativar o serviço de provisionamento AD Azure para zscaler One, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Zscaler Um Estado de Provisionamento](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores ou grupos que pretende fornecer ao Zscaler One. Na secção **Definições,** selecione os valores que deseja no **Âmbito**.

    ![Zscaler One Scope](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Zscaler One Save](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure seja executado. 

Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir as ligações ao relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure na Zscaler One.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../active-directory-saas-provisioning-reporting.md)de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png
