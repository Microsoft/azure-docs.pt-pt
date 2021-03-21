---
title: 'Tutorial: Configurar Zscaler Três para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Azure Ative Directory para provisões e desprovisionar automaticamente contas de utilizadores para zscaler 3.
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
ms.openlocfilehash: c16c02a870edb1a777b63da6fea57fc02136d643
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936605"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Três para fornecimento automático de utilizadores

Neste tutorial, você aprenderá a configurar o Azure Ative Directory (Azure AD) para provisão automática de utilizadores e/ou grupos para Zscaler Three.

> [!NOTE]
> Este tutorial descreve um conector que é construído no serviço de fornecimento de utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz e como funciona, e respostas a perguntas frequentes, consulte [automatizar o fornecimento de utilizadores e desprovisionar as aplicações do SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos delineados neste tutorial, precisa do seguinte:

* Um inquilino do Azure AD.
* Um inquilino Zscaler 3.
* Uma conta de utilizador em Zscaler 3 com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da Zscaler ZSCloud SCIM API, que está disponível para contas enterprise.

## <a name="adding-zscaler-three-from-the-gallery"></a>Adicionando Zscaler Três da galeria

Antes de configurar o Zscaler Three para o fornecimento automático de utilizadores com Azure AD, tem de adicionar zscaler 3 da galeria de aplicações AD AD Azure à sua lista de aplicações geridas pelo SaaS.

No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

![Selecione Azure Active Directory](common/select-azuread.png)

Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**:

![Aplicações Empresariais](common/enterprise-applications.png)

Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

![Selecione nova aplicação](common/add-new-app.png)

Na caixa de pesquisa, **insira Zscaler Três**. Selecione **Zscaler Três** nos resultados e, em seguida, selecione **Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Atribuir utilizadores ao Zscaler Three

Os utilizadores de AD Azure precisam de ter acesso a aplicações selecionadas antes de poderem usá-las. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que são atribuídos a uma aplicação em AZure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler Three. Depois de decidir isso, pode atribuir estes utilizadores e grupos ao Zscaler Three seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Dicas importantes para atribuir utilizadores ao Zscaler Three

* Recomendamos que atribua primeiro um único utilizador Azure AD ao Zscaler Three para testar a configuração automática de provisionamento do utilizador. Pode atribuir mais utilizadores e grupos mais tarde.

* Quando atribui um utilizador ao Zscaler Three, tem de selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o provisionamento automático do utilizador

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD para criar, atualizar e desativar utilizadores e grupos em Zscaler Three com base nas atribuições de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode querer ativar o sign-on único baseado em SAML para zscaler três. Se o fizer, siga as instruções no [tutorial de inscrição de Zscaler Three](zscaler-three-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, mas as duas funcionalidades complementam-se mutuamente.

> [!NOTE]
> Quando os utilizadores e grupos são provisionados ou des provisionados, recomendamos reiniciar periodicamente o provisionamento para garantir que os membros do grupo sejam corretamente atualizados. Fazer um reinício forçará o nosso serviço a reavaliar todos os grupos e a atualizar os membros. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Zscaler Três**:

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zscaler Three**:

    ![Lista de candidaturas](common/all-applications.png)

3. Selecione o **separador Provisioning:**

    ![Zscaler Três Provisioning](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Desa ajuste o **modo de provisionamento** para **automático:**

    ![Definir o modo de provisionamento](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. Na secção **Credenciais Admin,** insira o URL do **Inquilino** e **o Token Secreto** da sua conta Zscaler Three, conforme descrito no passo seguinte.

6. Para obter o **URL do inquilino** e o **Token Secreto,** **aceda** às  >  **Definições de Autenticação** administrativa no portal Zscaler Three e selecione **SAML** sob **Tipo de Autenticação**:

    ![Três definições de autenticação Zscaler](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Selecione **Configurar SAML** para abrir a janela **Configure SAML:**

    ![Configure janela SAML](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Selecione **Ative SCIM-Based Provisioning** e copie o **URL base** e **o Token do Portador**, e, em seguida, guarde as definições. No portal Azure, cole o **URL base** na caixa **URL** do inquilino e no **Token do Portador** na caixa De **Token Secreto.**

7. Depois de introduzir os valores nas caixas DE URL e **Secret Token** do **Arrendatário,** selecione **Test Connection** para se certificar de que a Azure AD pode ligar-se ao Zscaler Three. Se a ligação falhar, certifique-se de que a sua conta Zscaler 3 tem permissões de administração e tente novamente.

    ![Testar a ligação](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione **Enviar uma notificação de e-mail quando ocorrer uma falha:**

    ![Configurar e-mail de notificação](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users to ZscalerThree**:

    ![Sincronizar utilizadores de AD Azure](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler Três na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para corresponder às contas do utilizador em Zscaler Three para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com sete mapeamentos exibidos.](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups to ZscalerThree**:

    ![Synchronize grupos AD Azure](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler Three na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Zscaler Three para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com três mapeamentos exibidos.](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de deteção, consulte as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de AD Azure para o Zscaler Three, altere o **Estado de Provisionamento** para **On** na secção **Definições:**

    ![Estatuto de Provisionamento](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores e/ou grupos que pretende prestar ao Zscaler Three, escolhendo os valores que pretende no **Âmbito** na secção **Definições:**

    ![Valores de âmbito](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisões, **selecione Guardar:**

    ![Selecionar Guardar](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais do que as sincronizações subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode monitorizar o progresso na secção Detalhes da **Sincronização.** Também pode seguir links para um relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD AZure em Zscaler Three.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png