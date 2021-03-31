---
title: 'Tutorial: Configurar o arnês para o fornecimento automático de utilizadores com o Azure Ative Directory | Microsoft Docs'
description: Aprenda a configurar o Azure Ative Directory para provisá-lo e desprovisionar automaticamente as contas dos utilizadores para o Harness.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 13ae960f5d259314f00f8f09b2999a36c0919bc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94353735"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: Configure arnês para o fornecimento automático de utilizadores

Neste artigo, aprende-se a configurar o Azure Ative Directory (Azure AD) para provisão automática de utilizadores ou grupos para a Harness.

> [!NOTE]
> Este artigo descreve um conector que é construído em cima do serviço de fornecimento de utilizadores AZure AD. Para obter informações importantes sobre este serviço e respostas a perguntas frequentes, consulte [automatizar o fornecimento e desprovisionamento de utilizadores para aplicações SaaS com o Azure Ative Directory](../app-provisioning/user-provisioning.md).
>
> Este conector encontra-se atualmente em pré-visualização. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste artigo pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD AZure
* [Um inquilino de Arreio](https://harness.io/pricing/)
* Uma conta de utilizador em Arnês com permissões *de Administração*

## <a name="assign-users-to-harness"></a>Atribuir utilizadores ao Arnês

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que foram designados para uma aplicação em Azure AD são sincronizados.

Antes de configurar e permitir o fornecimento automático do utilizador, decida quais os utilizadores ou grupos em Azure AD que precisam de acesso ao Arnês. Em seguida, pode atribuir estes utilizadores ou grupos ao Harness seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial.](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir utilizadores ao Harness

* Recomendamos que atribua um único utilizador Azure AD ao Harness para testar a configuração automática de provisionamento do utilizador. Utilizadores ou grupos adicionais podem ser atribuídos mais tarde.

* Quando atribuir um utilizador ao Harness, deve selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo **de atribuição.** Os utilizadores com a função *Acesso Predefinido* estão excluídos do provisionamento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Arnês para provisionamento

1. Inscreva-se na sua [Consola de Arnês arnês](https://app.harness.io/#/login)e, em seguida, vá para **a Gestão contínua de** acesso à  >  **segurança.**

    ![Consola de Admin harness](media/harness-provisioning-tutorial/admin.png)

1. Selecione **Teclas API**.

    ![Ligação de chaves API de arnês](media/harness-provisioning-tutorial/apikeys.png)

1. **Selecione Adicionar Tecla API**. 

    ![Ligação chave API de arreio adicionar a api](media/harness-provisioning-tutorial/addkey.png)

1. No painel **de teclas Add Api,** faça o seguinte:

    ![Arreio Adicionar painel de chave Api](media/harness-provisioning-tutorial/title.png)
   
   a. Na caixa **Nome,** forneça um nome para a chave.  
   b. Nas **Permissões Herdadas da** lista de suspensos, selecione uma opção. 
   
1. Selecione **Submeter**.

1. Copie a **chave** para utilização posterior neste tutorial.

    ![Arnês Criar Ficha](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicione arnês da galeria

Antes de configurar o Harness para o fornecimento automático de utilizadores com Azure AD, tem de adicionar o Harness da galeria de aplicações AD AZure à sua lista de aplicações geridas pelo SaaS.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O botão "Azure Ative Directory"](common/select-azuread.png)

1. Selecione **aplicações da Empresa**  >  **Todas as aplicações**.

    ![O link "Todas as aplicações"](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O botão "Nova aplicação"](common/add-new-app.png)

1. Na caixa de pesquisa, insira o **Arnês,** selecione **Arnês** na lista de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

    ![Arnês na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configure o fornecimento automático do utilizador ao Arnês 

Esta secção guia-o através dos passos para configurar o serviço de fornecimento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Arnês com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Pode também optar por ativar o único sinal de Arnês baseado em SAML seguindo as instruções no tutorial de inscrição única do [Arnês.](./harness-tutorial.md) Pode configurar um único sinal de inscrição independente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do Harness SCIM, consulte o artigo [API Keys](https://docs.harness.io/article/smloyragsm-api-keys) do Harness.

Para configurar o fornecimento automático do utilizador para o Arnês em Azure AD, faça o seguinte:

1. No [portal Azure](https://portal.azure.com), selecione **Aplicações empresariais**  >  **Todas as aplicações**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

1. Na lista de candidaturas, selecione **Harness**.

    ![O link do Arnês na lista de aplicações](common/all-applications.png)

1. **Selecione Provisioning**.

    ![O botão de provisionamento](common/provisioning.png)

1. Na lista de espera do modo de **provisionamento,** selecione **Automatic**.

    ![A lista de abandono do "Modo de Provisionamento"](common/provisioning-automatic.png)

1. De acordo com **as credenciais de administração,** faça o seguinte:

    ![INQUILINO URL + Token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Na caixa URL do **inquilino,** insira **`https://app.harness.io/gateway/api/scim/account/<your_harness_account_ID>`** . Pode obter o ID da sua conta harness a partir do URL no seu browser quando estiver iniciado no Harness.
   b. Na caixa **Secret Token,** insira o valor de Token de autenticação SCIM que guardou no passo 6 da secção "Configurar o arnês para provisões".  
   c. Selecione **a Ligação de Teste** para garantir que o Azure AD pode ligar-se ao Arnês. Se a ligação falhar, certifique-se de que a sua conta de Arnês tem permissões *de administração* e, em seguida, tente novamente.

1. Na caixa de **e-mail de notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e, em seguida, selecione a **notificação enviar uma notificação por e-mail quando ocorrer uma falha** na caixa de verificação.

    ![A caixa "Email de Notificação"](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **Mapeamentos**, selecione **Synchronize Azure Ative Directory Users to Harness**.

    ![Ligação "Synchronize Azure Ative Directory Users to Harness"](media/harness-provisioning-tutorial/usermappings.png)

1. Em **'Mappings' de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD a Harness. Os atributos selecionados como *Matching* são utilizados para combinar as contas do utilizador no Arnês para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Painel de "Atribuir Mapeamentos" do utilizador do arnês](media/harness-provisioning-tutorial/userattributes.png)

1. Em **Mapeamentos**, selecione **Synchronize Azure Ative Directory Groups to Harness**.

    ![Ligação "Synchronize Azure Ative Directory Groups to Harness"](media/harness-provisioning-tutorial/groupmappings.png)

1. Em **'Mappings' de Atributos,** reveja os atributos do grupo que são sincronizados de Azure AD a Harness. Os atributos selecionados como propriedades *de correspondência* são utilizados para combinar com os grupos em Arnês para operações de atualização. **Selecione Guardar** para escoar quaisquer alterações.

    ![Painel do grupo de arreios "Atribuim mapeamentos"](media/harness-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de deteção, consulte [o provisionamento de aplicações baseados em Atributos com filtros de escoamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Em **Definições**, para permitir o serviço de prestação de Ad Azure para arnês, alternar o interruptor **de Estado de Provisionamento** para **On**.

    ![Alternância do Estado de Provisionamento alternada para "On"](common/provisioning-toggle-on.png)

1. Em **Definições**, na lista de **drop-down** scope, selecione como pretende sincronizar os utilizadores ou grupos que está a forragem para o Harness.

    ![Âmbito de Aprovisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para a provisão, **selecione Save**.

    ![O botão de provisão economize](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial dos utilizadores ou grupos que está a agrutar. A sincronização inicial demora mais tempo a ser praticada do que as mais antigas. As sincronizações ocorrem aproximadamente a cada 40 minutos, desde que o serviço de fornecimento AZure AD esteja em execução. Para monitorizar o progresso, aceda à secção Detalhes da **Sincronização.** Também pode seguir links para um relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de fornecimento de AD AZure em Arnês.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatório sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento da conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como analisar os registos e obter relatórios sobre a atividade de aprovisionamento](../app-provisioning/check-status-user-account-provisioning.md)