---
title: 'Tutorial: Configure Zscaler ZSCloud para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Neste tutorial, você aprenderá a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizador para Zscaler ZSCloud.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: a752be80-d3ef-45d1-ac8f-4fb814c07b07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: af9d4aa15a4ff2784d2e1ca1334d9c24a8d12251
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "77062681"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Tutorial: Configure Zscaler ZSCloud para fornecimento automático de utilizadores

Neste tutorial, você aprenderá a configurar o Azure Ative Directory (Azure AD) para fornecer e desfornecer automaticamente utilizadores e/ou grupos para Zscaler ZSCloud.

> [!NOTE]
> Este tutorial descreve um conector que é construído no serviço de fornecimento de utilizadores da AD Azure. Para detalhes importantes sobre o que este serviço faz e como funciona, e respostas a perguntas frequentes, consulte o fornecimento e o [deprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../active-directory-saas-app-provisioning.md)


## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos descritos neste tutorial, precisa do seguinte:

* Um inquilino do Azure AD.
* Um inquilino Zscaler ZSCloud.
* Uma conta de utilizador no Zscaler ZSCloud com permissões de administração.

> [!NOTE]
> A integração de provisionamento de AD Azure baseia-se na Zscaler ZSCloud SCIM API, que está disponível para contas da Enterprise.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Adicione Zscaler ZSCloud da galeria

Antes de configurar o Zscaler ZSCloud para o fornecimento automático de utilizadores com a AD Azure, tem de adicionar zscaler ZSCloud da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory:**

![Selecione Azure Active Directory](common/select-azuread.png)

Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as aplicações:**

![Aplicações Empresariais](common/enterprise-applications.png)

Para adicionar uma aplicação, selecione **Nova aplicação** na parte superior da janela:

![Selecione Nova aplicação](common/add-new-app.png)

Na caixa de pesquisa, introduza **zscaler ZSCloud**. Selecione **Zscaler ZSCloud** nos resultados e, em seguida, **selecione Adicionar**.

![Lista de resultados](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Atribuir utilizadores ao Zscaler ZSCloud

Os utilizadores de Anúncios Azure precisam de ter acesso a aplicações selecionadas antes de poderem utilizá-las. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que são atribuídos a uma aplicação em Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso ao Zscaler ZSCloud. Depois de decidir isso, pode atribuir estes utilizadores e grupos ao Zscaler ZSCloud seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Dicas importantes para atribuir utilizadores ao Zscaler ZSCloud

* Recomendamos que atribua primeiro um único utilizador Azure AD ao Zscaler ZSCloud para testar a configuração automática de fornecimento de utilizadores. Mais tarde pode atribuir mais utilizadores e grupos.

* Ao atribuir um utilizador ao Zscaler ZSCloud, tem de selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="set-up-automatic-user-provisioning"></a>Configurar o fornecimento automático de utilizadores

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e grupos no Zscaler ZSCloud com base em atribuições de utilizador e grupo em Azure AD.

> [!TIP]
> Também pode querer ativar um único sinal baseado em SAML para Zscaler ZSCloud. Se o fizer, siga as instruções no tutorial de [inscrição individual Zscaler ZSCloud](zscaler-zsCloud-tutorial.md). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, mas as duas funcionalidades complementam-se mutuamente.

1. Inscreva-se no [portal Azure](https://portal.azure.com) e selecione **aplicações** > Enterprise**Todas as aplicações** > **Zscaler ZSCloud**:

    ![Aplicações Empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Zscaler ZSCloud:**

    ![Lista de candidaturas](common/all-applications.png)

3. Selecione o separador **Provisioning:**

    ![Fornecimento Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Desloque o modo de **provisionamento** para **automático:**

    ![Definir o modo de provisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. Na secção **credenciais de administrador,** insira o URL do **Inquilino** e o **Token Secreto** da sua conta Zscaler ZSCloud, conforme descrito no passo seguinte.

6. Para obter o **URL** do Inquilino e **Token Secreto,** vá às Definições de**Autenticação** da **Administração** > no portal Zscaler ZSCloud e selecione **SAML** sob o tipo de **autenticação:**

    ![Definições de autenticação Zscaler ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    **Selecione Configure SAML** para abrir a janela **SAML configurar:**

    ![Configurar a janela SAML](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    **Selecione ativar o fornecimento baseado no SCIM** e copie o **URL base** e **o token**do portador e, em seguida, guarde as definições. No portal Azure, colhe o **URL base** na caixa **URL** do Inquilino e o Token do Portador na caixa **Secreta Token.** **Bearer Token**

7. Depois de introduzir os valores nas **caixas DEURL** do Inquilino e **secretas,** selecione **Test Connection** para se certificar de que o Azure AD pode ligar-se ao Zscaler ZSCloud. Se a ligação falhar, certifique-se de que a sua conta Zscaler ZSCloud tem permissões de administração e tente novamente.

    ![Testar a ligação](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento. Selecione Enviar uma notificação por **e-mail quando ocorrer uma falha:**

    ![Configurar e-mail de notificação](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Selecione **Guardar**.

10. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to ZscalerZSCloud**:

    ![Utilizadores de Anúncios Azure Sincronizados](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Reveja os atributos do utilizador que são sincronizados de Azure AD para Zscaler ZSCloud na secção **Demapeamento** sacar. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no Zscaler ZSCloud para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Mapeamentos de atributos](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to ZscalerZSCloud**:

    ![Synchronize Grupos AD Azure](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Reveja os atributos do grupo que são sincronizados de Azure AD a Zscaler ZSCloud na secção **Demapeamento** sacar. Os atributos selecionados como propriedades **Correspondentes** são usados para combinar os grupos em Zscaler ZSCloud para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Mapeamentos de atributos](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Para configurar filtros de deteção, consulte as instruções do tutorial do [filtro Descodificação](./../active-directory-saas-scoping-filters.md).

15. Para ativar o serviço de provisionamento De AD Azure para zscaler ZSCloud, altere o Estado de **Provisionamento** para **On** na secção **Definições:**

    ![Estado de provisionamento](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Defina os utilizadores e/ou grupos que pretende fornecer ao Zscaler ZSCloud, escolhendo os valores que deseja no âmbito do **Âmbito** na secção **Definições:**

    ![Valores de âmbito](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Quando estiver pronto para fornecer, selecione **Guardar:**

    ![Selecionar Guardar](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e grupos definidos no âmbito do **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo do que as sincronizações subsequentes, que ocorrem a cada 40 minutos, desde que o serviço de provisionamento DaD Azure esteja em funcionamento. Pode monitorizar o progresso na secção Detalhes de **Sincronização.** Também pode seguir links para um relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento De Azure AD no Zscaler ZSCloud.

Para obter informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático de conta](../active-directory-saas-provisioning-reporting.md)de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png
