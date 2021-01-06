---
title: 'Tutorial: Configurar Zscaler ZSCloud para fornecimento automático de utilizadores com Diretório Ativo Azure / Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Azure Ative Directory para provisão automática e desprovisionar contas de utilizadores para zscaler ZSCloud.
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
ms.openlocfilehash: fa90cbf1e467416010ae0ba83e9344a84ce52e21
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936503"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Tutorial: Configurar Zscaler ZSCloud para fornecimento automático de utilizadores

Neste tutorial, você aprenderá a configurar o Azure Ative Directory (Azure AD) para provisão automática de utilizadores e/ou grupos para Zscaler ZSCloud.

> [!NOTE]
> Este tutorial descreve um conector que é construído no serviço de fornecimento de utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz e como funciona, e respostas a perguntas frequentes, consulte [automatizar o fornecimento de utilizadores e desprovisionar as aplicações do SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos delineados neste tutorial, precisa do seguinte:

* Um inquilino do Azure AD.
* Um inquilino Zscaler ZSCloud.
* Uma conta de utilizador no Zscaler ZSCloud com permissões de administração.

> [!NOTE]
> A integração de provisionamento Azure AD depende da Zscaler ZSCloud SCIM API, que está disponível para contas enterprise.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Adicione Zscaler ZSCloud da galeria

Antes de configurar o Zscaler ZSCloud para fornecimento automático de utilizadores com Azure AD, tem de adicionar zscaler ZSCloud da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**:

![Selecione Azure Active Directory](common/select-azuread.png)

Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações**:

![Aplicações Empresariais](common/enterprise-applications.png)

Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

![Selecione nova aplicação](common/add-new-app.png)

Na caixa de pesquisa, **insira zscaler ZSCloud**. Selecione **Zscaler ZSCloud** nos resultados e, em seguida, **selecione Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Atribuir utilizadores ao Zscaler ZSCloud

Os utilizadores de AD Azure precisam de ter acesso a aplicações selecionadas antes de poderem usá-las. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que são atribuídos a uma aplicação em AZure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler ZSCloud. Depois de decidir isso, pode atribuir estes utilizadores e grupos ao Zscaler ZSCloud seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Dicas importantes para atribuir utilizadores ao Zscaler ZSCloud

* Recomendamos que atribua primeiro um único utilizador Azure AD ao Zscaler ZSCloud para testar a configuração automática de provisionamento do utilizador. Pode atribuir mais utilizadores e grupos mais tarde.

* Quando atribui um utilizador ao Zscaler ZSCloud, tem de selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **Acesso Predefinido** estão excluídos do provisionamento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o provisionamento automático do utilizador

Esta secção guia-o através dos passos para configurar o serviço de fornecimento AZure AD para criar, atualizar e desativar utilizadores e grupos no Zscaler ZSCloud com base nas atribuições de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode querer ativar o sign-on único baseado em SAML para zscaler ZSCloud. Se o fizer, siga as instruções no [tutorial de assinatura único Zscaler ZSCloud](zscaler-zsCloud-tutorial.md). O único sinal pode ser configurado independentemente do fornecimento automático do utilizador, mas as duas funcionalidades complementam-se mutuamente.

> [!NOTE]
> Quando os utilizadores e grupos são provisionados ou des provisionados, recomendamos reiniciar periodicamente o provisionamento para garantir que os membros do grupo sejam corretamente atualizados. Fazer um reinício forçará o nosso serviço a reavaliar todos os grupos e a atualizar os membros. 

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **aplicações Enterprise**  >  **Todas as aplicações**  >  **Zscaler ZSCloud:**

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler ZSCloud**:

    ![Lista de candidaturas](common/all-applications.png)

3. Selecione o **separador Provisioning:**

    ![Fornecimento Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Desa ajuste o **modo de provisionamento** para **automático:**

    ![Definir o modo de provisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Na secção **Credenciais Admin,** insira o URL do **inquilino** e **o Token Secreto** da sua conta Zscaler ZSCloud, conforme descrito no passo seguinte.

6. Para obter o URL do **inquilino** e **o Token Secreto,** **aceda** às  >  **Definições de Autenticação** administrativa no portal Zscaler ZSCloud e selecione **SAML** em **Tipo de Autenticação**:

    ![Definições de autenticação Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Selecione **Configurar SAML** para abrir a janela **Configure SAML:**

    ![Configure janela SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Selecione **Ative SCIM-Based Provisioning** e copie o **URL base** e **o Token do Portador**, e, em seguida, guarde as definições. No portal Azure, cole o **URL base** na caixa **URL** do inquilino e no **Token do Portador** na caixa De **Token Secreto.**

7. Depois de introduzir os valores nas caixas DE URL e **Secret Token** do **Arrendatário,** selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se ao Zscaler ZSCloud. Se a ligação falhar, certifique-se de que a sua conta Zscaler ZSCloud tem permissões de administração e tente novamente.

    ![Testar a ligação](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento. Selecione **Enviar uma notificação de e-mail quando ocorrer uma falha:**

    ![Configurar e-mail de notificação](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Selecione **Guardar**.

10. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Users para ZscalerZSCloud**:

    ![Sincronizar utilizadores de AD Azure](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD a Zscaler ZSCloud na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas de utilizador no Zscaler ZSCloud para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com sete mapeamentos exibidos.](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Na secção **Mappings,** selecione **Synchronize Azure Ative Directory Groups para ZscalerZSCloud**:

    ![Synchronize grupos AD Azure](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler ZSCloud na secção **De Mapeamentos de Atributos.** Os atributos selecionados como propriedades **de correspondência** são usados para combinar com os grupos no Zscaler ZSCloud para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Screenshot da secção De mapeamentos de atributos com três mapeamentos exibidos.](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Para configurar filtros de deteção, consulte as instruções no tutorial do [filtro de escotagem](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Para ativar o serviço de provisionamento Azure AD para zscaler ZSCloud, altere o **Estado de Provisionamento** para **On** na secção **Definições:**

    ![Estatuto de Provisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Defina os utilizadores e/ou grupos que pretende prestar ao Zscaler ZSCloud, escolhendo os valores que pretende no **Âmbito** na secção **Definições:**

    ![Valores de âmbito](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para provisões, **selecione Guardar:**

    ![Selecionar Guardar](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais do que as sincronizações subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Pode monitorizar o progresso na secção Detalhes da **Sincronização.** Também pode seguir links para um relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD AZure no Zscaler ZSCloud.

Para obter informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png