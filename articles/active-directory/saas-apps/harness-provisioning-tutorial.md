---
title: 'Tutorial: configurar o Harness para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente as contas de usuário para o Harness.
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
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057843"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: configurar o Harness para provisionamento automático de usuário

Neste artigo, você aprenderá a configurar o Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente os usuários ou grupos a serem aproveitados.

> [!NOTE]
> Este artigo descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter informações importantes sobre este serviço e respostas a perguntas frequentes, consulte o fornecimento e o fornecimento de utilizadores [da Automatização para aplicações SaaS com o Diretório Ativo azure.](../app-provisioning/user-provisioning.md)
>
> Este conector está atualmente em pré-visualização. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste artigo pressupõe que você já tenha os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um inquilino de Harness](https://harness.io/pricing/)
* Uma conta de utilizador em Harness com permissões *de Administrador*

## <a name="assign-users-to-harness"></a>Atribuir usuários ao Harness

O Azure Ative Directory utiliza um conceito chamado *atribuições* para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Harness. Pode então atribuir estes utilizadores ou grupos ao Harness seguindo as instruções em [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir usuários ao Harness

* Recomendamos que você atribua um único usuário do Azure AD ao Harness para testar a configuração automática de provisionamento de usuário. Usuários ou grupos adicionais podem ser atribuídos posteriormente.

* Quando atribuir um utilizador ao Harness, deve selecionar qualquer função específica de aplicação válida (se disponível) na caixa de diálogo **de Atribuição.** Os utilizadores com a função *de Acesso Predefinido* estão excluídos do fornecimento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Harness para provisionamento

1. Inscreva-se na consola [Harness Admin](https://app.harness.io/#/login)e, em seguida, vá para a **Continua Security** > **Access Management**.

    ![Console de administração de Harness](media/harness-provisioning-tutorial/admin.png)

1. Selecione **Teclas API**.

    ![Link de chaves de API de Harness](media/harness-provisioning-tutorial/apikeys.png)

1. **Selecione Adicionar Tecla API**. 

    ![Harness adicionar link de chave de API](media/harness-provisioning-tutorial/addkey.png)

1. No painel **Add Api Key,** faça o seguinte:

    ![Painel Adicionar chave de API do Harness](media/harness-provisioning-tutorial/title.png)
   
   a. Na caixa **nome,** forneça um nome para a chave.  
   b. Nas **Permissões Herdadas da** lista de drop-down, selecione uma opção. 
   
1. Selecione **Submeter**.

1. Copie a **Chave** para posterior utilização neste tutorial.

    ![Harness criar token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicionar o Harness da Galeria

Antes de configurar o Harness para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Harness da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No [portal Azure,](https://portal.azure.com)no painel esquerdo, selecione **Azure Ative Directory**.

    ![O botão "Azure Active Directory"](common/select-azuread.png)

1. Selecione **aplicações da Enterprise** > **todas as aplicações**.

    ![O link "todos os aplicativos"](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O botão "novo aplicativo"](common/add-new-app.png)

1. Na caixa de pesquisa, introduza **o Harness**, selecione **O arnês** na lista de resultados e, em seguida, selecione o botão **Adicionar** para adicionar a aplicação.

    ![Harness na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configurar o provisionamento automático de usuário para o Harness 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos em conjunto com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para o Harness seguindo as instruções no tutorial de [inscrição única](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)do Harness . Você pode configurar o logon único independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final do Harness SCIM, consulte o artigo Harness [API Keys.](https://docs.harness.io/article/smloyragsm-api-keys)

Para configurar o provisionamento automático de usuário para o Harness no Azure AD, faça o seguinte:

1. No [portal Azure,](https://portal.azure.com)selecione **Aplicações Empresariais** > **Todas as aplicações.**

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **Harness**.

    ![O link do Harness na lista de aplicativos](common/all-applications.png)

1. Selecione **Provisionamento**.

    ![O botão de provisionamento](common/provisioning.png)

1. Na lista de desistência do **Modo de Provisionamento,** selecione **Automático**.

    ![A lista suspensa "modo de provisionamento"](common/provisioning-automatic.png)

1. Sob **as credenciais de administrador,** faça o seguinte:

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Na caixa URL do **Inquilino,** introduza **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** .  
   b. Na caixa **Token Secreta,** introduza o valor token de autenticação SCIM que guardou no passo 6 da secção "Preparar para o provisionamento".  
   c. Selecione **ligação** de teste para garantir que o Azure AD pode ligar-se ao Harness. Se a ligação falhar, certifique-se de que a sua conta Harness tem permissões *de administrador* e, em seguida, tente novamente.

1. Na caixa de **e-mail de notificação,** introduza o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e, em seguida, selecione a **notificação de e-mail enviar uma notificação de e-mail quando ocorrer uma caixa** de verificação de verificação.

    ![A caixa "email de notificação"](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to Harness**.

    ![Aproveite o link "sincronizar Azure Active Directory usuários a serem aproveitados"](media/harness-provisioning-tutorial/usermappings.png)

1. No âmbito dos **Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de Azure AD para Harness. Os atributos selecionados como *Matching* são utilizados para combinar as contas de utilizador no Harness para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Painel "mapeamentos de atributo" do usuário do Harness](media/harness-provisioning-tutorial/userattributes.png)

1. Em **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to Harness**.

    ![Aproveite o link "sincronizar grupos de Azure Active Directory para o equipamento"](media/harness-provisioning-tutorial/groupmappings.png)

1. No âmbito dos **Mapeamentos de Atributos,** reveja os atributos do grupo que são sincronizados de Azure AD a Harness. Os atributos selecionados como propriedades *correspondentes* são usados para combinar os grupos em Harness para operações de atualização. Selecione **Guardar** para cometer quaisquer alterações.

    ![Painel "mapeamentos de atributo" do grupo de Harness](media/harness-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de deteção, consulte o [fornecimento de aplicações baseado no Atributo com filtros](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)de deteção .

1. Em **Definições**, para ativar o serviço de provisionamento AD Azure para o Arnês, alternar o interruptor de estado de **provisionamento** para **ligar**.

    ![Comutador de status de provisionamento alternado para "ativado"](common/provisioning-toggle-on.png)

1. Em **Definições**, na lista de **drop-down** scope, selecione como pretende sincronizar os utilizadores ou grupos que está a fornecer ao Harness.

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fornecer, selecione **Guardar**.

    ![O botão de salvamento de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial dos usuários ou grupos que você está Provisionando. A sincronização inicial demora mais para ser executada do que as mais recentes. As sincronizações ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Para monitorizar o progresso, vá à secção detalhes de **sincronização.** Você também pode seguir links para um relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Harness.

Para obter mais informações sobre como ler os registos de fornecimento de AD Azure, consulte [relatório sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)
