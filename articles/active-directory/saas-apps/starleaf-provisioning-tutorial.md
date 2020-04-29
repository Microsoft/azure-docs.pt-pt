---
title: 'Tutorial: Configure StarLeaf para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer automaticamente e desfornecer contas de utilizador ao StarLeaf.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: na
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 520373fc6a05bcaada973273e3553f9da623c669
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77064296"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Tutorial: Configure starleaf para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados no StarLeaf e no Azure Ative Directory (Azure AD) para configurar o Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos ao StarLeaf.

> [!NOTE]
>  Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em Pré-visualização. Para obter mais informações sobre os termos gerais de utilização do Microsoft Azure para funcionalidades de pré-visualização, consulte [os Termos Suplementares de Utilização para as Pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste tutorial pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um inquilino Do StarLeaf.](https://www.starleaf.com/solutions/)
* Uma conta de utilizador no StarLeaf com permissões de administrador.

## <a name="assign-users-to-starleaf"></a>Atribuir utilizadores ao StarLeaf
O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e grupos em AD Azure que precisam de acesso ao StarLeaf. Em seguida, pode atribuir os utilizadores e grupos ao StarLeaf seguindo [estas instruções](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Dicas importantes para atribuir utilizadores ao StarLeaf

* Recomenda-se que um único utilizador da AD Azure seja atribuído ao StarLeaf para testar a configuração automática de fornecimento de utilizadores. Utilizadores e grupos adicionais podem ser atribuídos mais tarde.

* Ao atribuir um utilizador ao StarLeaf, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função de Acesso Predefinido estão excluídos do fornecimento.

## <a name="set-up-starleaf-for-provisioning"></a>Configurar o StarLeaf para o fornecimento

Antes de configurar o StarLeaf para o fornecimento automático de utilizadores com a AD Azure, terá de configurar o fornecimento de SCIM no StarLeaf:

1. Inscreva-se na consola [StarLeaf Admin](https://portal.starleaf.com/#page=login). Navegar para **integrações** > **Adicionar integração.**

    ![StarLeaf Adicionar SCIM](media/starleaf-provisioning-tutorial/image00.png)

2. Selecione o **Tipo** para ser o Microsoft Azure Ative Directory. Introduza um nome adequado no **Nome**. Clique em **Aplicar**.

    ![StarLeaf Adicionar SCIM](media/starleaf-provisioning-tutorial/image01.png)

3.  Os valores de URL base **scim** e **de acesso** serão então apresentados. Estes valores serão inseridos nos campos URL do **Tenant** e **Secret Token** no separador de provisionamento da sua aplicação StarLeaf no portal Azure. 

    ![StarLeaf Criar Token](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>Adicione starLeaf da galeria

Para configurar o StarLeaf para o fornecimento automático de utilizadores com a AD Azure, é necessário adicionar o StarLeaf da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

**Para adicionar o StarLeaf na galeria de aplicações Azure AD, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, introduza o **StarLeaf**, selecione **StarLeaf** no painel de resultados.
    ![StarLeaf na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Configure o fornecimento automático de utilizadores ao StarLeaf

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos no StarLeaf com base em atribuições de utilizador e/ou grupo em Azure AD.

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **StarLeaf**.

    ![O link StarLeaf na lista de Aplicações](common/all-applications.png)

3. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

4. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

5. No âmbito da secção de Credenciais de Administrador, insere os valores de URL base **sCIM** e **access token** recuperados anteriormente em URL de **Inquilino** e **Token Secreto,** respectivamente. Clique em **Ligação de Teste** para garantir que o Azure AD pode ligar-se ao StarLeaf. Se a ligação falhar, certifique-se de que a sua conta StarLeaf tem permissões de administrador e tente novamente.

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verifique a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma caixa** de falha.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to StarLeaf**.

    ![StarLeaf Criar Token](media/starleaf-provisioning-tutorial/usermapping.png)

9. Reveja os atributos do utilizador que são sincronizados de Azure AD para StarLeaf na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador no StarLeaf para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![StarLeaf Criar Token](media/starleaf-provisioning-tutorial/userattribute.png)


10. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


11. Para ativar o serviço de provisionamento de AD Azure para o StarLeaf, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

12. Defina os utilizadores e/ou grupos que gostaria de fornecer ao StarLeaf, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure no StarLeaf.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [Relatórios sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Limitações do conector

* O StarLeaf não suporta atualmente o fornecimento de grupos. 
* O StarLeaf exige que os valores de **e-mail** e **userName** tenham o mesmo valor de origem.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [rever os registos e obtenha relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md).
