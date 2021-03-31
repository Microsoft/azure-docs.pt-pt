---
title: 'Tutorial: Configurar foodee para o fornecimento automático de utilizadores utilizando o Azure Ative Directory | Microsoft Docs'
description: Saiba como configurar o Azure Ative Directory para provisão automática e desprovisionar as contas dos utilizadores à Foodee.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 8b4bfa7e9bf457d79c6c4a0b5255bce4fe36dff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358691"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Tutorial: Configurar Foodee para o fornecimento automático de utilizadores

Este artigo mostra-lhe como configurar o Azure Ative Directory (Azure AD) na Foodee e Azure AD para provisão automática ou deprovisionamento de utilizadores ou grupos à Foodee.

> [!NOTE]
> O artigo descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AZure AD. Para saber o que este serviço faz e como funciona, e para obter respostas a perguntas frequentes, consulte [o fornecimento de utilizadores da Automamate e desprovisionando as aplicações do SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em pré-visualização. Para obter mais informações sobre a funcionalidade Azure termos de utilização para funcionalidades de pré-visualização, aceda aos [Termos Complementares de Utilização para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que conheceu os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino foodee](https://www.food.ee/about/)
* Uma conta de utilizador em Foodee com permissões de Administração

## <a name="assign-users-to-foodee"></a>Atribuir utilizadores à Foodee 

A Azure AD utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, deve decidir quais os utilizadores ou grupos em Azure AD que precisam de acesso à Foodee. Depois de ter feito esta determinação, pode atribuir estes utilizadores ou grupos à Foodee seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-foodee"></a>Dicas importantes para atribuir utilizadores à Foodee 

Ao atribuir utilizadores, tenha em mente as seguintes dicas:

* Recomendamos que atribua apenas um único utilizador Azure AD à Foodee para testar a configuração do fornecimento automático do utilizador. Pode atribuir utilizadores ou grupos adicionais mais tarde.

* Quando atribuir um utilizador à Foodee, selecione qualquer função específica de aplicação válida, se estiver disponível, no painel **de atribuição.** Os utilizadores que tenham a função *De Acesso Predefinido* estão excluídos do provisionamento.

## <a name="set-up-foodee-for-provisioning"></a>Criar Foodee para provisionamento

Antes de configurar a Foodee para o fornecimento automático de utilizadores utilizando o Azure AD, tem de ativar o fornecimento do Sistema de Gestão de Identidade de Domínio Cruzado (SCIM) em Foodee.

1. Inscreva-se na [Foodee](https://www.food.ee/login/)e, em seguida, selecione a sua identificação de inquilino.

    :::image type="content" source="media/Foodee-provisioning-tutorial/tenant.png" alt-text="Screenshot do menu principal do portal da empresa Foodee. Um espaço reservado para inquilinos é visível no menu." border="false":::

1. No **portal Enterprise**, selecione Single Sign **On**.

    ![O menu de painéis de esquerda do Foodee Enterprise Portal](media/Foodee-provisioning-tutorial/scim.png)

1. Copie o valor na caixa token da **API** para posterior utilização. Você vai inseri-lo na caixa **Secret Token** no **separador Provisioning** da sua aplicação Foodee no portal Azure.

    :::image type="content" source="media/Foodee-provisioning-tutorial/token.png" alt-text="Screenshot de uma página no portal da empresa Foodee. Um valor simbólico A P I é destacado." border="false":::

## <a name="add-foodee-from-the-gallery"></a>Adicione Foodee da galeria

Para configurar a Foodee para o fornecimento automático de utilizadores utilizando o Azure AD, é necessário adicionar a Foodee da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

Para adicionar Foodee da galeria de aplicações AZure, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O comando do Diretório Ativo Azure](common/select-azuread.png)

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![O painel de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **Nova aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Foodee,** **selecione Foodee** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar a aplicação.

    ![Foodee na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configure o fornecimento automático de utilizadores à Foodee 

Nesta secção, configura o serviço de fornecimento AZure AD para criar, atualizar e desativar utilizadores ou grupos em Foodee com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode ativar o sign-on único baseado em SAML para a Foodee seguindo as instruções no [tutorial de inscrição única foodee.](Foodee-tutorial.md) Pode configurar um único sinal de inscrição independente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

Configure o fornecimento automático de utilizadores para a Foodee em Azure AD, fazendo o seguinte:

1. No [portal Azure](https://portal.azure.com), selecione **Aplicações empresariais**  >  **Todas as aplicações**.

    ![Painel de aplicações da empresa](common/enterprise-applications.png)

1. Na lista **de Aplicações,** selecione **Foodee**.

    ![O link Foodee na lista de Aplicações](common/all-applications.png)

1. Selecione o separador **Aprovisionamento**.

    ![Screenshot das opções De gestão com a opção Provisioning chamada.](common/provisioning.png)

1. Na lista de espera do modo de **provisionamento,** selecione **Automatic**.

    ![Screenshot da lista de retirada do modo de provisionamento com a opção Automática chamada.](common/provisioning-automatic.png)

1. De acordo com **as credenciais de administração,** faça o seguinte:

   a. Na caixa URL do **inquilino,** **insira o https: \/ /concierge.food.ee/scim/v2** valor que recuperou anteriormente.

   b. Na caixa **Secret Token,** insira o valor **API Token** que recuperou anteriormente.
   
   c. Para garantir que o Azure AD pode ligar-se ao Foodee, selecione **Test Connection**. Se a ligação falhar, certifique-se de que a sua conta Foodee tem permissões de administrador e, em seguida, tente novamente.

    ![A ligação de ligação de teste](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e, em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![A caixa de texto de e-mail de notificação](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **Mappings**, **selecione Synchronize Azure Ative Directory Users to Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/usermapping.png" alt-text="Screenshot da secção mappings. Em Nome, destaca-se o Synchronize Azure Ative Directory Users to Foodee." border="false":::

1. Em **'Mappings' de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD a Foodee. Os atributos que são selecionados como propriedades **de correspondência** são *usados* para combinar as contas do utilizador em Foodee para operações de atualização. 

    :::image type="content" source="media/Foodee-provisioning-tutorial/userattribute.png" alt-text="Screenshot da página De mapeamentos de atributos. Uma tabela lista os atributos Azure Ative Directory e Foodee e a precedência correspondente." border="false":::

1. Para cometer as suas alterações, **selecione Guardar**.
1. Em **Mappings**, **selecione Synchronize Azure Ative Directory Groups to Foodee**.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupmapping.png" alt-text="Screenshot da secção mappings. Em Nome, é destacado o Synchronize Azure Ative Directory Groups to Foodee." border="false":::

1. Em **'Mappings' de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD a Foodee. Os atributos que são selecionados como propriedades **de correspondência** são *usados* para combinar as contas do grupo em Foodee para operações de atualização.

    :::image type="content" source="media/Foodee-provisioning-tutorial/groupattribute.png" alt-text="Screenshot da página De mapeamentos de atributos. Uma tabela lista atributos do Azure Ative Directory, atributos Foodee e a precedência correspondente." border="false":::

1. Para cometer as suas alterações, **selecione Guardar**.
1. Configure os filtros de deteção. Para saber como, consulte as instruções no tutorial do [filtro Scoping](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de prestação de Ad Azure para a Foodee, na secção **Definições,** altere o **Estado de Provisionamento** para **On**.

    ![O interruptor de estado de provisionamento](common/provisioning-toggle-on.png)

1. Em **Definições**, na lista de drop-down **scope,** defina os utilizadores ou grupos que pretende providenciar à Foodee.

    ![A lista de abandono do âmbito de provisão](common/provisioning-scope.png)

1. Quando estiver pronto para a provisão, **selecione Save**.

    ![O botão de poupança de configuração de provisionamento](common/provisioning-configuration-save.png)

A operação anterior inicia a sincronização inicial dos utilizadores ou  grupos que definiu na lista de drop-down scope. A sincronização inicial demora mais tempo a ser efetua do que as sincronizações subsequentes. Para obter mais informações, veja [quanto tempo demorará a providenciar aos utilizadores?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users)

Pode utilizar a secção **Estado Atual** para monitorizar o progresso e seguir links para o seu relatório de atividade de provisionamento. O relatório descreve todas as ações realizadas pelo serviço de fornecimento de Azure AD na Foodee. Para obter mais informações, [consulte verifique o estado do fornecimento do utilizador](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os registos de provisionamento da AD Azure, consulte [reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)
