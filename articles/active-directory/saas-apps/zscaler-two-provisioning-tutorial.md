---
title: 'Tutorial: Configure Zscaler Dois para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador para Zscaler 2.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 0a250fcd-6ca1-47c2-a780-7a6278186a69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 364b106e7c1f01269ac02b0c2851f8824ea0f58c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77062698"
---
# <a name="tutorial-configure-zscaler-two-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler Dois para fornecimento automático de utilizadores

Neste tutorial, você aprenderá a configurar o Azure Ative Directory (Azure AD) para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Zscaler Dois.

> [!NOTE]
> Este tutorial descreve um conector que é construído no serviço de fornecimento de utilizadores da AD Azure. Para detalhes importantes sobre o que este serviço faz e como funciona, e respostas a perguntas frequentes, consulte o fornecimento e o [deprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../active-directory-saas-app-provisioning.md)

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tutorial, precisa do seguinte:

* Um inquilino do Azure AD.
* Um inquilino Zscaler Dois.
* Uma conta de utilizador em Zscaler 2 com permissões de administração.

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na Zscaler Two SCIM API, que está disponível para contas da Enterprise.

## <a name="add-zscaler-two-from-the-gallery"></a>Adicione Zscaler Dois da galeria

Antes de configurar o Zscaler 2 para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar zscaler 2 da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

![Selecione Azure Active Directory](common/select-azuread.png)

Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações:**

![Aplicações Empresariais](common/enterprise-applications.png)

Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

![Selecione Nova aplicação](common/add-new-app.png)

Na caixa de pesquisa, introduza **o Zscaler 2**. **Selecione Zscaler Dois** nos resultados e, em seguida, selecione **Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-two"></a>Atribuir utilizadores ao Zscaler 2

Os utilizadores de Anúncios Azure precisam de ter acesso a aplicações selecionadas antes de poderem utilizá-las. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que são atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler 2. Depois de decidir isso, pode atribuir estes utilizadores e grupos ao Zscaler Two seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-two"></a>Dicas importantes para atribuir utilizadores ao Zscaler 2

* Recomendamos que atribua primeiro um único utilizador da AD Azure ao Zscaler Two para testar a configuração automática de fornecimento do utilizador. Mais tarde pode atribuir mais utilizadores e grupos.

* Ao atribuir um utilizador ao Zscaler 2, tem de selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o fornecimento automático de utilizadores

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e grupos em Zscaler Dois com base em atribuições de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode querer ativar um único sinal baseado em SAML para zscaler 2. Se o fizer, siga as instruções no [tutorial de inscrição único zscaler Dois](zscaler-two-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, mas as duas funcionalidades complementam-se mutuamente.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **aplicações** > Enterprise**Todas as aplicações** > **Zscaler 2**:

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, **selecione Zscaler Dois:**

    ![Lista de candidaturas](common/all-applications.png)

3. Selecione o separador **Provisioning:**

    ![Fornecimento Zscaler Dois](./media/zscaler-two-provisioning-tutorial/provisioning-tab.png)

4. Desloque o modo de **provisionamento** para **automático:**

    ![Definir o modo de provisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-credentials.png)

5. Na secção **de Credenciais de Administrador,** insira o **URL** do Inquilino e o **Token Secreto** da sua conta Zscaler Two, conforme descrito no passo seguinte.

6. Para obter o **URL** do Inquilino e **Token Secreto,** vá às Definições de**Autenticação** da **Administração** > no portal Zscaler Two e selecione **SAML** sob o tipo de **autenticação:**

    ![Zscaler Duas Definições de Autenticação](./media/zscaler-two-provisioning-tutorial/secret-token-1.png)

    **Selecione Configure SAML** para abrir a janela **SAML configurar:**

    ![Configurar a janela SAML](./media/zscaler-two-provisioning-tutorial/secret-token-2.png)

    **Selecione ativar o fornecimento baseado no SCIM** e copie o **URL base** e **o token**do portador e, em seguida, guarde as definições. No portal Azure, colhe o **URL base** na caixa **URL** do Inquilino e o Token do Portador na caixa **Secreta Token.** **Bearer Token**

7. Depois de introduzir os valores nas **caixas DEURL** do Inquilino e **secretas,** selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se ao Zscaler 2. Se a ligação falhar, certifique-se de que a sua conta Zscaler 2 tem permissões de administração e tente novamente.

    ![Testar a ligação](./media/zscaler-two-provisioning-tutorial/test-connection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento. Selecione Enviar uma notificação por **e-mail quando ocorrer uma falha:**

    ![Configurar e-mail de notificação](./media/zscaler-two-provisioning-tutorial/notification.png)

9. Selecione **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to ZscalerTwo**:

    ![Utilizadores de Anúncios Azure Sincronizados](./media/zscaler-two-provisioning-tutorial/user-mappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zscaler 2 na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em Zscaler 2 para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Mapeamentos de atributos](./media/zscaler-two-provisioning-tutorial/user-attribute-mappings.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to ZscalerTwo**:

    ![Synchronize Grupos AD Azure](./media/zscaler-two-provisioning-tutorial/group-mappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD para Zscaler Dois na secção **DeMapeamentos de Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em Zscaler 2 para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Mapeamentos de atributos](./media/zscaler-two-provisioning-tutorial/group-attribute-mappings.png)

14. Para configurar filtros de deteção, consulte as instruções do tutorial do [filtro Descodificação](./../active-directory-saas-scoping-filters.md).

15. Para ativar o serviço de provisionamento AD Azure para zscaler 2, altere o Estado de **Provisionamento** para **On** na secção **Definições:**

    ![Estado de provisionamento](./media/zscaler-two-provisioning-tutorial/provisioning-status.png)

16. Defina os utilizadores e/ou grupos que pretende fornecer ao Zscaler Dois, escolhendo os valores que pretende no âmbito do **Âmbito** na secção **Definições:**

    ![Valores de âmbito](./media/zscaler-two-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fornecer, selecione **Guardar:**

    ![Selecionar Guardar](./media/zscaler-two-provisioning-tutorial/save-provisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no âmbito do **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo do que as sincronizações subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de provisionamento DaD Azure esteja em funcionamento. Pode monitorizar o progresso na secção Detalhes de **Sincronização.** Também pode seguir ligações a um relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure no Zscaler 2.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../active-directory-saas-provisioning-reporting.md)de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-two-provisioning-tutorial/tutorial-general-03.png
