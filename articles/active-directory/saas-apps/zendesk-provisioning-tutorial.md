---
title: 'Tutorial: Configure Zendesk para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador à Zendesk.
services: active-directory
documentationcenter: ''
author: zhchia
writer: zhchia
manager: beatrizd-msft
ms.assetid: 01d5e4d5-d856-42c4-a504-96fa554baf66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a480119ee88521b920be88669f6d80e3754d24d3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77062755"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Tutorial: Configure Zendesk para fornecimento automático de utilizadores

Este tutorial demonstra os passos a executar na Zendesk e azure Ative Directory (Azure AD) para configurar a AD Azure para fornecer e desfornecer automaticamente utilizadores e grupos à Zendesk.

> [!NOTE]
> Este tutorial descreve um conector que é construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para obter informações sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o [fornecimento e o desprovisionamento de utilizadores automate para aplicações de software como um serviço (SaaS) com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que tem:

* Um inquilino do Azure AD.
* Um inquilino Zendesk com o plano Profissional ou melhor habilitado.
* Uma conta de utilizador em Zendesk com permissões de administração.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Adicione Zendesk do Mercado Azure

Antes de configurar o Zendesk para o fornecimento automático de utilizadores com a Azure AD, adicione zendesk do Azure Marketplace à sua lista de aplicações SaaS geridas.

Para adicionar Zendesk do Marketplace, siga estes passos.

1. No [portal Azure,](https://portal.azure.com)no painel de navegação à esquerda, selecione **Azure Ative Directory**.

    ![O ícone do Diretório Ativo Azure](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior da caixa de diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza **zendesk** e selecione **Zendesk** a partir do painel de resultados. Para adicionar a aplicação, selecione **Adicionar**.

    ![Zendesk na lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Atribuir utilizadores à Zendesk

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de acesso ao Zendesk. Para atribuir estes utilizadores ou grupos à Zendesk, siga as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Dicas importantes para atribuir utilizadores à Zendesk

* Hoje em dia, as funções Zendesk são automaticamente e dinamicamente povoadas no portal Azure UI. Antes de atribuir as funções zendesk aos utilizadores, certifique-se de que uma sincronização inicial é concluída contra a Zendesk para recuperar as mais recentes funções no seu inquilino Zendesk.

* Recomendamos que atribua um único utilizador da AD Azure à Zendesk para testar a configuração inicial de fornecimento automático do utilizador. Pode atribuir utilizadores ou grupos adicionais mais tarde após o sucesso dos testes.

* Quando atribuir um utilizador à Zendesk, selecione qualquer função específica de aplicação válida, se disponível, na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Configure o fornecimento automático de utilizadores à Zendesk 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure. Utilize-o para criar, atualizar e desativar utilizadores ou grupos em Zendesk com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar um único sinal baseado em SAML para zendesk. Siga as instruções no tutorial de [inscrição individual](zendesk-tutorial.md)do Zendesk . O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Configure o fornecimento automático de utilizadores para zendesk em Azure AD

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicações** > Enterprise**Todas as aplicações** > **Zendesk**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zendesk**.

    ![O link Zendesk na lista de aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Fornecimento zendesk](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Modo de provisionamento Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. De acordo com a secção **de Credenciais de Administrador,** insera o nome de utilizador do administrador, o token secreto e o domínio da sua conta Zendesk. Exemplos destes valores são:

   * Na caixa de nome de **utilizador da Admin,** preencha o nome de utilizador da conta de administrador no seu inquilino Zendesk. Um exemplo é admin@contoso.com.

   * Na caixa **Secreta Token,** preencha o símbolo secreto como descrito no Passo 6.

   * Na caixa **de Domínio,** preencha o subdomínio do seu inquilino Zendesk. Por exemplo, para uma conta `https://my-tenant.zendesk.com`com um URL de inquilino de, o seu subdomínio é **o meu inquilino.**

6. O símbolo secreto da sua conta Zendesk está localizado nas > **Definições**da **Admin** > **API**. Certifique-se de que o **Acesso token** está programado para **ativado**.

    ![Definições de administração Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Ficha secreta zendesk](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Depois de preencher as caixas mostradas no Passo 5, selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se à Zendesk. Se a ligação falhar, certifique-se de que a sua conta Zendesk tem permissões de administração e tente novamente.

    ![Conexão zendesk test](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail da pessoa ou grupo para receber as notificações de erro de fornecimento. Selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![E-mail de notificação Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Selecione **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Zendesk**.

    ![Sincronização do utilizador Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zendesk na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Zendesk para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Atributos de utilizador correspondentes zendesk](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Zendesk**.

    ![Sincronização do grupo Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Zendesk na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Zendesk para operações de atualização. Para guardar quaisquer alterações, selecione **Guardar**.

    ![Atributos de grupo de correspondência Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. Para configurar filtros de deteção, siga as instruções no tutorial do [filtro de deteção](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento de AD Azure para zendesk, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![Estado de Provisionamento Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Defina os utilizadores ou grupos que pretende fornecer à Zendesk. Na secção **Definições,** selecione os valores que deseja no **Âmbito**.

    ![Âmbito Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![Poupança Zendesk](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Esta operação inicia a sincronização inicial de todos os utilizadores ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a executar do que as sincronizações posteriores. Ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento da AD Azure seja executado. 

Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir as ligações ao relatório de atividades de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure na Zendesk.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../app-provisioning/check-status-user-account-provisioning.md)de utilizador .

## <a name="connector-limitations"></a>Limitações do conector

* A Zendesk suporta a utilização de grupos para utilizadores apenas com funções **de Agente.** Para mais informações, consulte a documentação do [Zendesk.](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups)

* Quando uma função personalizada é atribuída a um utilizador ou grupo, o serviço de fornecimento automático de utilizadores Da AD Azure também atribui a função padrão **Agente**. Só os agentes podem ser atribuídos um papel personalizado. Para mais informações, consulte a documentação da [API da Zendesk.](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests) 

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
