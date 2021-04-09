---
title: 'Tutorial: Configure Zscaler Dois para fornecimento automático de utilizadores com Azure Ative Directory | Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Azure Ative Directory para provisões e desprovisionar automaticamente contas de utilizadores para zscaler Dois.
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
ms.openlocfilehash: 1c63639321966cbb73f5c47524f408d17ba46c3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936571"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Dois para fornecimento automático de utilizadores

Neste tutorial, você aprenderá a configurar o Azure Ative Directory (Azure AD) para provisão automática de utilizadores e/ou grupos para Zscaler Two.

> [!NOTE]
> Este tutorial descreve um conector que é construído no serviço de fornecimento de utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz e como funciona, e respostas a perguntas frequentes, consulte [automatizar o fornecimento de utilizadores e desprovisionar as aplicações do SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos delineados neste tutorial, precisa do seguinte:

* Um inquilino do Azure AD.
* Um inquilino Zscaler 2.
* Uma conta de utilizador em Zscaler 2 com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da Zscaler Two SCIM API, que está disponível para contas da Enterprise.

## <a name="add-zscaler-two-from-the-gallery"></a>Adicione Zscaler Dois da galeria

Antes de configurar o Zscaler Two para o fornecimento automático de utilizadores com Azure AD, tem de adicionar zscaler Dois da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

![Selecione Azure Active Directory](common/select-azuread.png)

Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**:

![Aplicações Empresariais](common/enterprise-applications.png)

Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

![Selecione nova aplicação](common/add-new-app.png)

Na caixa de pesquisa, **insira Zscaler 2**. Selecione **Zscaler Dois** nos resultados e, em seguida, selecione **Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Atribuir utilizadores ao Zscaler Two

Os utilizadores de AD Azure precisam de ter acesso a aplicações selecionadas antes de poderem usá-las. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que são atribuídos a uma aplicação em AZure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler Two. Depois de decidir isso, pode atribuir estes utilizadores e grupos à Zscaler Two seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Dicas importantes para atribuir utilizadores ao Zscaler Two

* Recomendamos que atribua primeiro um único utilizador Azure AD ao Zscaler Two para testar a configuração automática de provisionamento do utilizador. Pode atribuir mais utilizadores e grupos mais tarde.

* Quando atribui um utilizador ao Zscaler 2, tem de selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o provisionamento automático do utilizador

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD para criar, atualizar e desativar utilizadores e grupos em Zscaler Two com base nas atribuições de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode querer ativar o sign-on único baseado em SAML para zscaler dois. Se o fizer, siga as instruções no [tutorial de assinatura Zscaler Two](zscaler-two-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, mas as duas funcionalidades complementam-se mutuamente.

> [!NOTE]
> Quando os utilizadores e grupos são provisionados ou des provisionados, recomendamos reiniciar periodicamente o provisionamento para garantir que os membros do grupo sejam corretamente atualizados. Fazer um reinício forçará o nosso serviço a reavaliar todos os grupos e a atualizar os membros. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Zscaler 2**:

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Zscaler 2**:

    ![Lista de candidaturas](common/all-applications.png)

3. Selecione o **separador Provisioning:**

    ![Zscaler Dois Provisioning](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Desa ajuste o **modo de provisionamento** para **automático:**

    ![Definir o modo de provisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Na secção **Credenciais Admin,** insira o URL do **Inquilino** e **o Token Secreto** da sua conta Zscaler 2, conforme descrito no passo seguinte.

6. Para obter o **URL do inquilino** e o **Token Secreto,** **aceda** às  >  **Definições de Autenticação** administrativa no portal Zscaler Two e selecione **SAML** sob **Tipo de Autenticação**:

    ![Duas definições de autenticação Zscaler](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    Selecione **Configurar SAML** para abrir a janela **Configure SAML:**

    ![Configure janela SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    Selecione **Ative SCIM-Based Provisioning** e copie o **URL base** e **o Token do Portador**, e, em seguida, guarde as definições. No portal Azure, cole o **URL base** na caixa **URL** do inquilino e no **Token do Portador** na caixa De **Token Secreto.**

7. Depois de introduzir os valores nas caixas DE URL e **Secret Token** do **Arrendatário,** selecione **Test Connection** para se certificar de que a Azure AD pode ligar-se ao Zscaler Two. Se a ligação falhar, certifique-se de que a sua conta Zscaler 2 tem permissões de administração e tente novamente.

    ![Testar a ligação](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione **Enviar uma notificação de e-mail quando ocorrer uma falha:**

    ![Configurar e-mail de notificação](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para ZscalerTwo**:

    ![Sincronizar utilizadores de AD Azure](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler Two na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador em Zscaler Two para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com sete mapeamentos exibidos.](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para ZscalerTwo**:

    ![Synchronize grupos AD Azure](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler Two na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar com os grupos em Zscaler Two para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com três mapeamentos exibidos.](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de deteção, consulte as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de prestação de AD Azure para o Zscaler Two, altere o **Estado de Provisionamento** para **On** na secção **Definições:**

    ![Estatuto de Provisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores e/ou grupos que pretende prestar ao Zscaler Dois, escolhendo os valores que pretende no **Âmbito** na secção **Definições:**

    ![Valores de âmbito](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisões, **selecione Guardar:**

    ![Selecionar Guardar](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais do que as sincronizações subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode monitorizar o progresso na secção Detalhes da **Sincronização.** Também pode seguir links para um relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD AZure em Zscaler Two.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png