---
title: 'Tutorial: Configurar Tableau Online para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para procedê-lo e desprovisionar automaticamente as contas dos utilizadores ao Tableau Online.
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
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359209"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Tutorial: Configurar Tableau Online para fornecimento automático de utilizadores

Este tutorial demonstra os passos a desempenhar no Tableau Online e no Azure Ative Directory (Azure AD) para configurar a Azure AD para provisão e desprovisionamento automaticamente de utilizadores e grupos para Tableau Online.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AZure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações de software-as-a-service (SaaS) com diretório Azure Ative.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que você tem:

*   Um inquilino do Azure AD.
*   Um [inquilino online tableau.](https://www.tableau.com/)
*   Uma conta de utilizador no Tableau Online com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da API de [Repouso Online Tableau](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm). Esta API está disponível para programadores Online Tableau.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Adicionar Tableau Online do Azure Marketplace
Antes de configurar o Tableau Online para fornecimento automático de utilizadores com Azure AD, adicione Tableau Online do Azure Marketplace à sua lista de aplicações geridas pelo SaaS.

Para adicionar Tableau Online do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **o Tableau Online** e selecione **Tableau Online** a partir do painel de resultados. Para adicionar a aplicação, **selecione Adicionar**.

    ![Tableau Online na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Atribuir utilizadores ao Tableau Online

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso ao Tableau Online. Para atribuir estes utilizadores ou grupos ao Tableau Online, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Dicas importantes para atribuir utilizadores ao Tableau Online

*   Recomendamos que atribua um único utilizador Azure AD ao Tableau Online para testar a configuração automática de provisionamento do utilizador. Pode atribuir utilizadores ou grupos adicionais mais tarde.

*   Quando atribuir um utilizador ao Tableau Online, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Configure o fornecimento automático de utilizadores ao Tableau Online

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD. Utilize-o para criar, atualizar e desativar utilizadores ou grupos no Tableau Online com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar o sign-on único baseado em SAML para Tableau Online. Siga as instruções no [tutorial de inscrição única do Tableau Online.](tableauonline-tutorial.md) O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para Tableau Online em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**  >  **Tableau Online**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Tableau Online**.

    ![O link Tableau Online na lista de candidaturas](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Provisionamento Online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Modo de Provisionamento Online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. Na secção **Credenciais de Administração,** insira o domínio, nome de utilizador de administração, palavra-passe de administrador e URL de conteúdo da sua conta Tableau Online:

   * Na caixa **de domínio,** preencha o subdomínio com base no passo 6.

   * Na caixa **de nome de utilizador Admin,** preencha o nome de utilizador da conta de administração no seu Tableau Online Tenant. Um exemplo é admin@contoso.com.

   * Na caixa **de palavra-passe Admin,** preencha a palavra-passe da conta de administração que corresponde ao nome de utilizador de administração.

   * Na caixa URL de **conteúdo,** preencha o subdomínio com base no Passo 6.

6. Depois de iniciar sôm na sua conta administrativa do Tableau Online, poderá obter os valores de **URL de Domínio** e **Conteúdo** a partir do URL da página de administração.

    * O **Domínio** da sua conta Tableau Online pode ser copiado a partir desta parte do URL:

        ![Tableau Domínio Online](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * O **URL de Conteúdo** da sua conta Tableau Online pode ser copiado a partir desta secção. É um valor definido durante a configuração da conta. Neste exemplo, o valor é "contoso":

        ![URL de conteúdo online tableau](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > O seu **domínio** pode ser diferente daquele aqui mostrado.

7. Depois de preencher as caixas mostradas no Passo 5, selecione **a Ligação de Teste** para se certificar de que o Azure AD pode ligar-se ao Tableau Online. Se a ligação falhar, certifique-se de que a sua conta Tableau Online tem permissões de administração e tente novamente.

    ![Ligação de teste online tableau](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![Email de Notificação Online Tableau](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Selecione **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to Tableau**.

    ![Sincronização de utilizadores online tableau](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Tableau Online na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Tableau Online para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Tableau Online correspondente aos atributos do utilizador](./media/tableau-online-provisioning-tutorial/attribute.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to Tableau**.

    ![Sincronização de grupo online Tableau](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Tableau Online na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Tableau Online para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Atributos de grupo de correspondência online Tableau](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de Ad Azure para Tableau Online, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estatuto de Provisionamento Online Tableau](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Defina os utilizadores ou grupos que pretende providenciar ao Tableau Online. Na secção **Definições,** selecione os valores que pretende no **Âmbito**.

    ![Âmbito online tableau](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Quando estiver pronto para a provisão, **selecione Save**.

    ![Tableau Online Save](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser praticada do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD funciona. 

Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD no Tableau Online.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="change-log"></a>Change log
* 09/30/2020 - Suporte adicional para atributo "authSetting" para Utilizadores.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png