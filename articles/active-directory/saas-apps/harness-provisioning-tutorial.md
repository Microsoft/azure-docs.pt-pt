---
title: 'Tutorial: Configure o arnês para o fornecimento automático de utilizadores com diretório ativo Azure [ Microsoft Docs'
description: Aprenda a configurar o Diretório Ativo Azure para fornecer e desfornecer automaticamente as contas dos utilizadores à Harness.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0cdb970b-440b-4e7c-9118-2f03baab6a20
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: 518d86fff04a23f1c1e63c44c53485b99f30637d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057843"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: Configure o arnês para o fornecimento automático de utilizadores

Neste artigo, aprende-se a configurar o Azure Ative Directory (Azure AD) para fornecer e desfornecer automaticamente utilizadores ou grupos à Harness.

> [!NOTE]
> Este artigo descreve um conector que é construído em cima do serviço de fornecimento de utilizadores DaAzure AD. Para obter informações importantes sobre este serviço e respostas a perguntas frequentes, consulte o fornecimento e o fornecimento de utilizadores [da Automatização para aplicações SaaS com o Diretório Ativo azure.](../app-provisioning/user-provisioning.md)
>
> Este conector encontra-se atualmente em pré-visualização. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

## <a name="prerequisites"></a>Pré-requisitos

O cenário delineado neste artigo pressupõe que já tem os seguintes pré-requisitos:

* Um inquilino da AD Azure
* [Um inquilino de Harness](https://harness.io/pricing/)
* Uma conta de utilizador em Harness com permissões *de Administrador*

## <a name="assign-users-to-harness"></a>Atribuir utilizadores ao Harness

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, decida quais os utilizadores ou grupos em AD Azure que precisam de acesso ao Harness. Pode então atribuir estes utilizadores ou grupos ao Harness seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir utilizadores ao Harness

* Recomendamos que atribua um único utilizador da AD Azure à Harness para testar a configuração automática de fornecimento do utilizador. Utilizadores ou grupos adicionais podem ser atribuídos mais tarde.

* Quando atribuir um utilizador ao Harness, deve selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo **de Atribuição.** Os utilizadores com a função *de Acesso Predefinido* estão excluídos do fornecimento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Arnês para o fornecimento

1. Inscreva-se na consola [Harness Admin](https://app.harness.io/#/login)e, em seguida, vá para a**Gestão**Contínua de Acesso à **Segurança** > .

    ![Consola de arnês](media/harness-provisioning-tutorial/admin.png)

1. Selecione **Teclas API**.

    ![Ligação de teclas API do arnês](media/harness-provisioning-tutorial/apikeys.png)

1. **Selecione Adicionar Tecla API**. 

    ![Harness Adicionar Ligação chave API](media/harness-provisioning-tutorial/addkey.png)

1. No painel **Add Api Key,** faça o seguinte:

    ![Harness Adicionar painel chave Api](media/harness-provisioning-tutorial/title.png)
   
   a. Na caixa **nome,** forneça um nome para a chave.  
   b. Nas **Permissões Herdadas da** lista de drop-down, selecione uma opção. 
   
1. Selecione **Submeter**.

1. Copie a **Chave** para posterior utilização neste tutorial.

    ![Arnês Criar Token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicione o Arnês da galeria

Antes de configurar o Harness para o fornecimento automático de utilizadores com o Azure AD, tem de adicionar o Harness da galeria de aplicações Azure AD à sua lista de aplicações SaaS geridas.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O botão "Azure Ative Directory"](common/select-azuread.png)

1. Selecione **aplicações** > Enterprise**Todas as aplicações**.

    ![O link "Todas as aplicações"](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O botão "Nova aplicação"](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **o Harness**, selecione **O arnês** na lista de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

    ![Arnês na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configure o fornecimento automático de utilizadores ao Harness 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores ou grupos em Harness com base em atribuições de utilizador ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o Harness seguindo as instruções no tutorial de [inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)do Harness . Pode configurar um único sinal independente do fornecimento automático do utilizador, embora estas duas funcionalidades se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do Harness SCIM, consulte o artigo Harness [API Keys.](https://docs.harness.io/article/smloyragsm-api-keys)

Para configurar o fornecimento automático de utilizadores para o Harness em Azure AD, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)selecione **Aplicações Empresariais** > **Todas as aplicações.**

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Harness**.

    ![O link Harness na lista de aplicações](common/all-applications.png)

1. Selecione **Provisionamento**.

    ![O botão provisionamento](common/provisioning.png)

1. Na lista de desistência do **Modo de Provisionamento,** selecione **Automático**.

    ![A lista de abandono do "Modo de Provisionamento"](common/provisioning-automatic.png)

1. Sob **as credenciais de administrador,** faça o seguinte:

    ![URL do inquilino + Token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Na caixa URL do **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** **Inquilino,** introduza .  
   b. Na caixa **Token Secreta,** introduza o valor token de autenticação SCIM que guardou no passo 6 da secção "Preparar para o provisionamento".  
   c. Selecione **ligação** de teste para garantir que o Azure AD pode ligar-se ao Harness. Se a ligação falhar, certifique-se de que a sua conta Harness tem permissões *de administrador* e, em seguida, tente novamente.

1. Na caixa de **e-mail de notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e, em seguida, selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma caixa** de verificação de verificação.

    ![A caixa "Notificação de Email"](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Harness**.

    ![Harness "Synchronize Azure Ative Directory Users to Harness"](media/harness-provisioning-tutorial/usermappings.png)

1. No âmbito dos **Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Harness. Os atributos selecionados como *Matching* são utilizados para combinar as contas de utilizador no Harness para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Painel de "Atributomappings" do utilizador do arnês](media/harness-provisioning-tutorial/userattributes.png)

1. Em **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Harness**.

    ![Ligação "Synchronize Azure Ative Directory Groups to Harness"](media/harness-provisioning-tutorial/groupmappings.png)

1. No âmbito dos **Mapeamentos de Atributos,** reveja os atributos do grupo que são sincronizados de Azure AD a Harness. Os atributos selecionados como propriedades *correspondentes* são usados para combinar os grupos em Harness para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Painel do grupo de arreios "Atributomappings"](media/harness-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de deteção, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

1. Em **Definições**, para ativar o serviço de provisionamento AD Azure para o Arnês, alternar o interruptor de estado de **provisionamento** para **ligar**.

    ![Interruptor de estado de provisionamento alternado para "On"](common/provisioning-toggle-on.png)

1. Em **Definições**, na lista de **drop-down** scope, selecione como pretende sincronizar os utilizadores ou grupos que está a fornecer ao Harness.

    ![Âmbito de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![O botão de provisão Salvar](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial dos utilizadores ou grupos que está a fornecer. A sincronização inicial demora mais tempo a ser desempenhada do que as posteriores. As sincronizações ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Para monitorizar o progresso, vá à secção detalhes de **sincronização.** Também pode seguir ligações a um relatório de atividade de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento da AD Azure em Harness.

Para obter mais informações sobre como ler os registos de fornecimento de AD Azure, consulte [relatório sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
