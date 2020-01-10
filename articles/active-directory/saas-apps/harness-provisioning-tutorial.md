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
ms.openlocfilehash: 9d00024351c18789e26120cc2af006b9aac4232d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75767845"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: configurar o Harness para provisionamento automático de usuário

Neste artigo, você aprenderá a configurar o Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente os usuários ou grupos a serem aproveitados.

> [!NOTE]
> Este artigo descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter informações importantes sobre esse serviço e as respostas para perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste artigo pressupõe que você já tenha os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Harness](https://harness.io/pricing/)
* Uma conta de usuário no Harness com permissões de *administrador*

## <a name="assign-users-to-harness"></a>Atribuir usuários ao Harness

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários ou grupos no Azure AD precisam de acesso ao Harness. Você pode atribuir esses usuários ou grupos ao aproveitamento seguindo as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir usuários ao Harness

* Recomendamos que você atribua um único usuário do Azure AD ao Harness para testar a configuração automática de provisionamento de usuário. Usuários ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Harness, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de **atribuição** . Os usuários com a função de *acesso padrão* são excluídos do provisionamento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Harness para provisionamento

1. Entre no console de [Administração](https://app.harness.io/#/login)do seu equipamento e vá para **segurança contínua** > **Gerenciamento de acesso**.

    ![Console de administração de Harness](media/harness-provisioning-tutorial/admin.png)

1. Selecione **chaves de API**.

    ![Link de chaves de API de Harness](media/harness-provisioning-tutorial/apikeys.png)

1. Selecione **Adicionar chave de API**. 

    ![Harness adicionar link de chave de API](media/harness-provisioning-tutorial/addkey.png)

1. No painel **Adicionar chave de API** , faça o seguinte:

    ![Painel Adicionar chave de API do Harness](media/harness-provisioning-tutorial/title.png)
   
   a. Na caixa **nome** , forneça um nome para a chave.  
   b. Na lista suspensa **permissões herdadas de** , selecione uma opção. 
   
1. Selecione **submeter**.

1. Copie a **chave** para uso posterior neste tutorial.

    ![Harness criar token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicionar o Harness da Galeria

Antes de configurar o Harness para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Harness da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![O botão "Azure Active Directory"](common/select-azuread.png)

1. Selecione **aplicativos empresariais** > **todos os aplicativos**.

    ![O link "todos os aplicativos"](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão "novo aplicativo"](common/add-new-app.png)

1. Na caixa de pesquisa, insira **Harness**, selecione **Harness** na lista de resultados e, em seguida, selecione o botão **Adicionar** para adicionar o aplicativo.

    ![Harness na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-harness"></a>Configurar o provisionamento automático de usuário para o Harness 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos em conjunto com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Harness seguindo as instruções no tutorial do [conjunto de logon único](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). Você pode configurar o logon único independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o conjunto de pontos de extremidade SCIM, consulte o artigo [chaves de API](https://docs.harness.io/article/smloyragsm-api-keys) de Harness.

Para configurar o provisionamento automático de usuário para o Harness no Azure AD, faça o seguinte:

1. Na [portal do Azure](https://portal.azure.com), selecione **aplicativos empresariais** > **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Harness**.

    ![O link do Harness na lista de aplicativos](common/all-applications.png)

1. Selecione **provisionamento**.

    ![O botão de provisionamento](common/provisioning.png)

1. Na lista suspensa **modo de provisionamento** , selecione **automático**.

    ![A lista suspensa "modo de provisionamento"](common/provisioning-automatic.png)

1. Em **credenciais de administrador**, faça o seguinte:

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)
 
   a. Na caixa **URL do locatário** , digite **`https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw`** .  
   b. Na caixa **token secreto** , insira o valor do token de autenticação scim que você salvou na etapa 6 da seção "configurar o Harness para provisionamento".  
   c. Selecione **testar conexão** para garantir que o Azure ad possa se conectar ao Harness. Se a conexão falhar, verifique se sua conta do Harness tem permissões de *administrador* e tente novamente.

1. Na caixa **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![A caixa "email de notificação"](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **mapeamentos**, selecione **sincronizar Azure Active Directory usuários a serem aproveitados**.

    ![Aproveite o link "sincronizar Azure Active Directory usuários a serem aproveitados"](media/harness-provisioning-tutorial/usermappings.png)

1. Em **mapeamentos de atributo**, examine os atributos de usuário que são sincronizados do Azure ad para o Harness. Os atributos selecionados como *correspondência* são usados para corresponder as contas de usuário no Harness para operações de atualização. Selecione **salvar** para confirmar as alterações.

    ![Painel "mapeamentos de atributo" do usuário do Harness](media/harness-provisioning-tutorial/userattributes.png)

1. Em **mapeamentos**, selecione **sincronizar grupos de Azure Active Directory a serem aproveitados**.

    ![Aproveite o link "sincronizar grupos de Azure Active Directory para o equipamento"](media/harness-provisioning-tutorial/groupmappings.png)

1. Em **mapeamentos de atributo**, examine os atributos de grupo que são sincronizados do Azure ad para o Harness. Os atributos selecionados como propriedades *correspondentes* são usados para corresponder os grupos em conjunto para operações de atualização. Selecione **salvar** para confirmar as alterações.

    ![Painel "mapeamentos de atributo" do grupo de Harness](media/harness-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de escopo, consulte [provisionamento de aplicativo baseado em atributo com filtros de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Em **configurações**, para habilitar o serviço de provisionamento do Azure ad para o Harness, alterne a opção de **status de provisionamento** para **ativado**.

    ![Comutador de status de provisionamento alternado para "ativado"](common/provisioning-toggle-on.png)

1. Em **configurações**, na lista suspensa **escopo** , selecione como deseja sincronizar os usuários ou grupos que você está Provisionando para o equipamento.

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, selecione **salvar**.

    ![O botão de salvamento de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial dos usuários ou grupos que você está Provisionando. A sincronização inicial demora mais para ser executada do que as mais recentes. As sincronizações ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Para monitorar o progresso, vá para a seção **detalhes de sincronização** . Você também pode seguir links para um relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Harness.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatório sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
