---
title: 'Tutorial: Configure Tableau Online para fornecimento automático de utilizadores com Diretório Ativo Azure  Microsoft Docs'
description: Saiba como configurar o Diretório Ativo do Azure para fornecer e desfornecer automaticamente contas de utilizadores ao Tableau Online.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0be9c435-f9a1-484d-8059-e578d5797d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fae770950810899f7c6583fa401110c3e85022b
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77064224"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configure Tableau Online para fornecimento automático de utilizadores

Este tutorial demonstra os passos a realizar no Tableau Online e no Azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e grupos para tableau Online.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que tem:

*   Um inquilino do Azure AD.
*   Um [inquilino online tableau.](https://www.tableau.com/)
*   Uma conta de utilizador no Tableau Online com permissões de administração.

> [!NOTE]
> A integração de fornecimento de Anúncios Azure baseia-se na [API de descanso online tableau.](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm) Esta API está disponível para desenvolvedores tableau Online.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicione tableau online do Mercado Azure
Antes de configurar o Tableau Online para o fornecimento automático de utilizadores com a Azure AD, adicione o Tableau Online do Azure Marketplace à sua lista de aplicações SaaS geridas.

Para adicionar tableau online do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **tableau Online** e selecione **Tableau Online** a partir do painel de resultados. Para adicionar a aplicação, selecione **Adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir utilizadores ao Tableau Online

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de acesso ao Tableau Online. Para atribuir estes utilizadores ou grupos ao Tableau Online, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir utilizadores ao Tableau Online

*   Recomendamos que atribua um único utilizador da AD Azure ao Tableau Online para testar a configuração automática de fornecimento de utilizadores. Mais tarde, pode atribuir utilizadores ou grupos adicionais.

*   Quando atribuir um utilizador ao Tableau Online, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configure o fornecimento automático de utilizadores ao Tableau Online

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure. Utilize-o para criar, atualizar e desativar utilizadores ou grupos no Tableau Online com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar um único sign-on baseado em SAML para Tableau Online. Siga as instruções no tutorial de [inscrição individual tableau Online](tableauonline-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para Tableau Online em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Enterprise** > **Todas as aplicações** > **Tableau Online**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Tableau Online**.

    ![O link Tableau Online na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Provisionamento Online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Modo de Provisionamento Online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Na secção **credenciais de administrador,** insera o domínio, o nome de utilizador administrativo, a palavra-passe de administrador e o URL de conteúdo da sua conta Tableau Online:

   * Na caixa **de domínio,** preencha o subdomínio com base no passo 6.

   * Na caixa De Nome de **Utilizador Da Administração,** preencha o nome de utilizador da conta de administrador no seu Clarizen Tenant. Um exemplo é admin@contoso.com.

   * Na caixa **de palavra-passe do Administrador,** preencha a palavra-passe da conta de administrador que corresponda ao nome de utilizador do administrador.

   * Na caixa URL de **Conteúdo,** preencha o subdomínio com base no passo 6.

6. Depois de iniciar sessão na sua conta administrativa para o Tableau Online, pode obter os valores para URL de **Domínio** e **Conteúdo** a partir do URL da página de administração.

    * O **Domínio** da sua conta Tableau Online pode ser copiado desta parte do URL:

        ![Domínio Online tableau](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * O URL de **Conteúdo** da sua conta Tableau Online pode ser copiado a partir desta secção. É um valor definido durante a configuração da conta. Neste exemplo, o valor é "contoso":

        ![URL de conteúdo online tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > O seu **Domínio** pode ser diferente daquele mostrado aqui.

7. Depois de preencher as caixas mostradas no Passo 5, selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se ao Tableau Online. Se a ligação falhar, certifique-se de que a sua conta Tableau Online tem permissões de administração e tente novamente.

    ![Conexão de teste online tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![Email de notificação online tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecione **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Tableau**.

    ![Sincronização de utilizadores online tableau](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Tableau Online na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Tableau Online para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Tableau Online atributos de utilizador correspondentes](./media/tableau-online-provisioning-tutorial/UserAttributeMapping.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Tableau**.

    ![Sincronização do grupo Tableau Online](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Tableau Online na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Tableau Online para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Tableau Online atributos de grupo de correspondência](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento De AD Azure para Tableau Online, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estado de provisionamento online tableau](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os utilizadores ou grupos que pretende fornecer ao Tableau Online. Na secção **Definições,** selecione os valores que deseja no **Âmbito**.

    ![Âmbito online tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Poupança Online tableau](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure seja executado. 

Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir as ligações ao relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Tableau Online.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../app-provisioning/check-status-user-account-provisioning.md)de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png
