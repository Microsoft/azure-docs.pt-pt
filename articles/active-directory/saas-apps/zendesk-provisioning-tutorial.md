---
title: 'Tutorial: Configurar Zendesk para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisá-lo e desprovisionar automaticamente as contas dos utilizadores para o Zendesk.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359719"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configurar Zendesk para o provisionamento automático do utilizador

Este tutorial demonstra os passos a desempenhar no Diretório Ativo Zendesk e Azure Ative (Azure AD) para configurar a Azure AD para provisão automática e desprovisionar utilizadores e grupos para Zendesk.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AZure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de aplicações de software-as-a-service (SaaS) com diretório Azure Ative.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que você tem:

* Um inquilino do Azure AD.
* Um inquilino Zendesk com o plano Profissional ou melhor habilitado.
* Uma conta de utilizador em Zendesk com permissões de administração.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicione Zendesk do Azure Marketplace

Antes de configurar zendesk para fornecimento automático de utilizadores com Azure AD, adicione Zendesk do Azure Marketplace à sua lista de aplicações geridas pelo SaaS.

Para adicionar Zendesk do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Zendesk** e selecione **Zendesk** a partir do painel de resultados. Para adicionar a aplicação, **selecione Adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir utilizadores ao Zendesk

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso ao Zendesk. Para atribuir estes utilizadores ou grupos à Zendesk, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação da empresa](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir utilizadores à Zendesk

* Hoje em dia, os papéis de Zendesk são automaticamente e dinamicamente povoados no portal Azure UI. Antes de atribuir papéis zendesk aos utilizadores, certifique-se de que uma sincronização inicial é completada contra Zendesk para recuperar os papéis mais recentes no seu inquilino Zendesk.

* Recomendamos que atribua um único utilizador Azure AD à Zendesk para testar a sua configuração inicial de provisionamento automático do utilizador. Pode atribuir utilizadores ou grupos adicionais mais tarde após os testes serem bem sucedidos.

* Quando atribuir um utilizador ao Zendesk, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configure o fornecimento automático de utilizadores ao Zendesk 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD. Utilize-o para criar, atualizar e desativar utilizadores ou grupos em Zendesk com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar um único sinal baseado em SAML para Zendesk. Siga as instruções no [tutorial de inscrição única zendesk](zendesk-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para Zendesk em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações da Empresa**  >  **Todas as aplicações**  >  **Zendesk**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zendesk**.

    ![O link Zendesk na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Aprovisionamento**.

    ![Provisão Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Defina o **Modo de Aprovisionamento** como **Automático**.

    ![Modo de Provisionamento Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. Na secção **Credenciais Admin,** insira o nome de utilizador de administração, o token secreto e o domínio da sua conta Zendesk. Exemplos destes valores são:

   * Na caixa **de nome de utilizador Admin,** preencha o nome de utilizador da conta de administração no seu inquilino Zendesk. Um exemplo é admin@contoso.com.

   * Na caixa **de Token Secreto,** preencha o símbolo secreto como descrito no passo 6.

   * Na caixa de **domínio,** preencha o subdomínio do seu inquilino Zendesk. Por exemplo, para uma conta com uma URL de inquilino `https://my-tenant.zendesk.com` de, o seu subdomínio é **o meu inquilino.**

6. O símbolo secreto da sua conta Zendesk está localizado nas Definições **ADMIN**  >  **API**  >  . Certifique-se de que **o Acesso Token** está definido para **Ativado**.

    ![Configurações de administração Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Símbolo secreto de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas mostradas no Passo 5, selecione **a Ligação de Teste** para se certificar de que o Azure AD pode ligar-se ao Zendesk. Se a ligação falhar, certifique-se de que a sua conta Zendesk tem permissões de administração e tente novamente.

    ![Conexão de teste Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de provisionamento. Selecione a **notificação Enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![E-mail de notificação de Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecione **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para Zendesk**.

    ![Sincronização do utilizador Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zendesk na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Zendesk para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Zendesk correspondendo atributos de utilizador](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para Zendesk**.

    ![Sincronização do grupo Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zendesk na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar os grupos em Zendesk para operações de atualização. Para guardar quaisquer alterações, **selecione Guardar**.

    ![Atributos do grupo de combinação Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de AD Azure para o Zendesk, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estado de Provisionamento zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os utilizadores ou grupos que pretende providenciar ao Zendesk. Na secção **Definições,** selecione os valores que pretende no **Âmbito**.

    ![Miração Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para a provisão, **selecione Save**.

    ![Zendesk Save](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser praticada do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD funciona. 

Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD em Zendesk.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Zendesk suporta o uso de grupos apenas para utilizadores com funções **de Agente.** Para mais informações, consulte a [documentação zendesk.](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)

* Quando uma função personalizada é atribuída a um utilizador ou grupo, o serviço de fornecimento automático de utilizadores Azure AD também atribui o **agente** de função predefinido . Só os agentes podem ser designados para um papel personalizado. Para mais informações, consulte a [documentação zendesk API](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
