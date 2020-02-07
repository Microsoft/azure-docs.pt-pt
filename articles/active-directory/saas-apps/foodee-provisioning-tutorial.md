---
title: 'Tutorial: Configure Foodee para fornecimento automático de utilizadores utilizando o Diretório Ativo Azure  ) Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente contas de utilizadores à Foodee.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: fb48deae-4653-448a-ba2f-90258edab3a7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 2195056ec66550063aba5ce5e2b977b51a6dc5e3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057831"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Tutorial: Configure Foodee para fornecimento automático de utilizadores

Este artigo mostra-lhe como configurar o Azure Ative Directory (Azure AD) em Foodee e Azure AD para fornecer ou desfornecer automaticamente utilizadores ou grupos à Foodee.

> [!NOTE]
> O artigo descreve um conector que é construído em cima do serviço de fornecimento de utilizadores da AD Azure. Para saber o que este serviço faz e como funciona, e para obter respostas a perguntas frequentes, consulte o fornecimento e o desprovisionamento de utilizadores [da Automatização para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)
>
> Este conector está atualmente em pré-visualização. Para obter mais informações sobre a funcionalidade de utilização do Azure para funcionalidades de pré-visualização, vá a [Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que cumpriu os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino da Foodee](https://www.food.ee/about/)
* Uma conta de utilizador em Foodee com permissões de Administrador

## <a name="assign-users-to-foodee"></a>Atribuir utilizadores ao Foodee 

A Azure AD utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores ou grupos da AD Azure que precisam de acesso ao Foodee. Depois de ter feito esta determinação, pode atribuir estes utilizadores ou grupos à Foodee seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Dicas importantes para atribuir utilizadores à Foodee 

Ao atribuir os utilizadores, tenha em mente as seguintes dicas:

* Recomendamos que atribua apenas um único utilizador da AD Azure à Foodee para testar a configuração do fornecimento automático de utilizadores. Mais tarde, pode atribuir utilizadores ou grupos adicionais.

* Quando estiver a atribuir um utilizador ao Foodee, selecione qualquer função específica de aplicação válida, se estiver disponível, no painel **de Atribuição.** Os utilizadores que tenham a função *de Acesso Predefinido* estão excluídos do fornecimento.

## <a name="set-up-foodee-for-provisioning"></a>Configurar foodee para o fornecimento

Antes de configurar o Foodee para o fornecimento automático de utilizadores utilizando o Azure AD, é necessário ativar o fornecimento de Sistema de Gestão de Identidade de Domínio Transversal (SCIM) no Foodee.

1. Inscreva-se na [Foodee](https://www.food.ee/login/)e, em seguida, selecione o seu ID de inquilino.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. No **portal Enterprise,** selecione **Single Sign On**.

    ![O menu foodee enterprise portal left-pane](media/Foodee-provisioning-tutorial/scim.png)

1. Copie o valor na caixa **Token DaPi** para posterior utilização. Você vai inseri-lo na caixa **Token Secreta** no separador **de provisionamento** da sua aplicação Foodee no portal Azure.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Adicione Foodee da galeria

Para configurar o Foodee para o fornecimento automático de utilizadores utilizando o Azure AD, é necessário adicionar foodee da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas.

Para adicionar Foodee da galeria de aplicações Azure AD, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O comando azure ative diretório](common/select-azuread.png)

1. Selecione **aplicações da Enterprise** > **todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **Foodee,** selecione **Foodee** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Foodee na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configure o fornecimento automático de utilizadores ao Foodee 

Nesta secção, configura o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Foodee com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar um único sinal baseado em SAML para a Foodee seguindo as instruções no tutorial de [inscrição individual foodee](Foodee-tutorial.md). Pode configurar um único sinal independente do fornecimento automático de utilizadores, embora estas duas funcionalidades se complementem.

Configure o fornecimento automático de utilizadores para Foodee em Azure AD fazendo o seguinte:

1. No [portal Azure,](https://portal.azure.com)selecione **Aplicações Empresariais** > **Todas as aplicações.**

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

1. Na lista de **Aplicações,** selecione **Foodee**.

    ![O link Foodee na lista de aplicações](common/all-applications.png)

1. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

1. Na lista de desistência do **Modo de Provisionamento,** selecione **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

1. Sob **as credenciais de administrador,** faça o seguinte:

   a. Na caixa URL do **Arrendatário,** introduza o **valor https:\//concierge.food.ee/scim/v2** valor que recuperou anteriormente.

   b. Na caixa **Secreta Token,** insira o valor **da API Token** que recuperou anteriormente.
   
   c. Para garantir que o Azure AD pode ligar-se ao Foodee, selecione **Test Connection**. Se a ligação falhar, certifique-se de que a sua conta Foodee tem permissões de administrador e, em seguida, tente novamente.

    ![A ligação de ligação de teste](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa de **e-mail de notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e, em seguida, selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma falha** verificar a caixa.

    ![A caixa de texto de e-mail de notificação](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Foodee**.

    ![Mapeamento de utilizadores foodee](media/Foodee-provisioning-tutorial/usermapping.png)

1. No âmbito do **Attribute Mappings,** reveja os atributos do utilizador que são sincronizados de Azure AD para Foodee. Os atributos que são selecionados como propriedades **correspondentes** são *usados* para combinar as contas de utilizador em Foodee para operações de atualização. 

    ![Mapeamento de utilizadores foodee](media/Foodee-provisioning-tutorial/userattribute.png)

1. Para comprometer as suas alterações, selecione **Guardar**.
1. Em **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Foodee**.

    ![Mapeamento de utilizadores foodee](media/Foodee-provisioning-tutorial/groupmapping.png)

1. No âmbito do **Attribute Mappings,** reveja os atributos do utilizador que são sincronizados de Azure AD para Foodee. Os atributos que são selecionados como propriedades **correspondentes** são *usados* para corresponder às contas de grupo em Foodee para operações de atualização.

    ![Mapeamento de utilizadores foodee](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Para comprometer as suas alterações, selecione **Guardar**.
1. Configure os filtros de deteção. Para saber como, consulte as instruções do tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de provisionamento de AD Azure para a Foodee, na secção **Definições,** altere o Estado de **Provisionamento** para **On**.

    ![O interruptor de estado de provisionamento](common/provisioning-toggle-on.png)

1. Em **Definições**, na lista de **drop-down** scope, defina os utilizadores ou grupos que pretende fornecer ao Foodee.

    ![A lista de entrega do Âmbito de Provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![O botão de poupança de configuração de fornecimento](common/provisioning-configuration-save.png)

A operação anterior inicia a sincronização inicial dos utilizadores ou grupos que definiu na lista de drop-down scope. A sincronização inicial demora mais tempo a executar do que as sincronizações subsequentes. Para mais informações, veja [quanto tempo demorará a fornecer aos utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir as ligações ao seu relatório de atividade de provisionamento. O relatório descreve todas as ações que são realizadas pelo serviço de provisionamento da AD Azure na Foodee. Para mais informações, [consulte Verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
