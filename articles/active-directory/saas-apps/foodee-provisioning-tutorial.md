---
title: 'Tutorial: configurar o alimento para o provisionamento automático de usuário usando Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o alimento.
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
ms.openlocfilehash: 97ecf3ea324f43753030f415c8a5e254b87595e5
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170077"
---
# <a name="tutorial-configure-foodee-for-automatic-user-provisioning"></a>Tutorial: configurar o alimento para provisionamento automático de usuário

Este artigo mostra como configurar o Azure Active Directory (Azure AD) no alimento e no Azure AD para provisionar ou desprovisionar automaticamente usuários ou grupos para o alimento.

> [!NOTE]
> O artigo descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para saber o que esse serviço faz e como ele funciona e obter respostas para perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização. Para obter mais informações sobre o recurso de termos de uso do Azure para recursos de visualização, vá para [termos de uso complementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você atende aos seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do alimento](https://www.food.ee/about/)
* Uma conta de usuário no alimento com permissões de administrador

## <a name="assign-users-to-foodee"></a>Atribuir usuários ao alimento 

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários ou grupos no Azure AD precisam de acesso ao alimento. Depois de fazer essa determinação, você pode atribuir esses usuários ou grupos ao alimento seguindo as instruções em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-foodee"></a>Dicas importantes para atribuir usuários ao alimento 

Quando você estiver atribuindo usuários, tenha em mente as seguintes dicas:

* Recomendamos que você atribua apenas um único usuário do Azure AD ao alimento para testar a configuração do provisionamento automático de usuário. Você pode atribuir usuários ou grupos adicionais mais tarde.

* Quando você estiver atribuindo um usuário ao alimento, selecione qualquer função específica do aplicativo válida, se estiver disponível, no painel de **atribuição** . Os usuários que têm a função de *acesso padrão* são excluídos do provisionamento.

## <a name="set-up-foodee-for-provisioning"></a>Configurar o alimento para provisionamento

Antes de configurar o alimento para o provisionamento automático de usuário usando o AD do Azure, você precisa habilitar o sistema de provisionamento do SCIM (gerenciamento de identidade entre domínios) no alimento.

1. Entre no [alimento](https://www.food.ee/login/)e selecione sua ID de locatário.

    ![Foodee](media/Foodee-provisioning-tutorial/tenant.png)

1. Em **Enterprise Portal**, selecione **logon único**.

    ![O Enterprise Portal menu do painel esquerdo](media/Foodee-provisioning-tutorial/scim.png)

1. Copie o valor na caixa **token de API** para uso posterior. Você vai inseri-lo na caixa **token secreto** na guia **provisionamento** do aplicativo do alimento na portal do Azure.

    ![Foodee](media/Foodee-provisioning-tutorial/token.png)

## <a name="add-foodee-from-the-gallery"></a>Adicionar o alimento da Galeria

Para configurar o alimento para o provisionamento automático de usuário usando o Azure AD, você precisará adicionar o alimento da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

Para adicionar o alimento da Galeria de aplicativos do Azure AD, faça o seguinte:

1. No [portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**.

    ![O comando Azure Active Directory](common/select-azuread.png)

1. Selecione **aplicativos empresariais** > **todos os aplicativos**.

    ![O painel aplicativos empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **novo aplicativo** na parte superior do painel.

    ![O botão novo aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **alimento**, selecione **alimento** no painel de resultados e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

    ![Comida na lista de resultados](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-foodee"></a>Configurar o provisionamento automático de usuário para o alimento 

Nesta seção, você configura o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários ou grupos no alimento com base em atribuições de usuário ou de grupo no Azure AD.

> [!TIP]
> Você também pode habilitar o logon único baseado em SAML para o alimento seguindo as instruções no [tutorial de logon único do alimento](Foodee-tutorial.md). Você pode configurar o logon único independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

Configure o provisionamento automático de usuário para o alimento no Azure AD fazendo o seguinte:

1. Na [portal do Azure](https://portal.azure.com), selecione **aplicativos empresariais** > **todos os aplicativos**.

    ![Painel aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de **aplicativos** , selecione **alimento**.

    ![O link do alimento na lista de aplicativos](common/all-applications.png)

1. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

1. Na lista suspensa **modo de provisionamento** , selecione **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

1. Em **credenciais de administrador**, faça o seguinte:

   a. Na caixa **URL do locatário** , insira o valor **https: \//concierge. food. ee/scim/v2** que você recuperou anteriormente.

   b. Na caixa **token secreto** , insira o valor do **token de API** que você recuperou anteriormente.
   
   c. Para garantir que o Azure AD possa se conectar ao alimento, selecione **testar conexão**. Se a conexão falhar, verifique se sua conta do alimento tem permissões de administrador e tente novamente.

    ![O link testar conexão](common/provisioning-testconnection-tenanturltoken.png)

1. Na caixa **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![A caixa de texto email de notificação](common/provisioning-notification-email.png)

1. Selecione **Guardar**.

1. Em **mapeamentos**, selecione **sincronizar Azure Active Directory usuários para o alimento**.

    ![Mapeamentos de usuário do alimento](media/Foodee-provisioning-tutorial/usermapping.png)

1. Em **mapeamentos de atributo**, examine os atributos de usuário que são sincronizados do Azure ad para o alimento. Os atributos que são selecionados como propriedades **correspondentes** são usados para corresponder as *contas de usuário* no alimento para operações de atualização. 

    ![Mapeamentos de usuário do alimento](media/Foodee-provisioning-tutorial/userattribute.png)

1. Para confirmar suas alterações, selecione **salvar**.
1. Em **mapeamentos**, selecione **sincronizar grupos de Azure Active Directory com o alimento**.

    ![Mapeamentos de usuário do alimento](media/Foodee-provisioning-tutorial/groupmapping.png)

1. Em **mapeamentos de atributo**, examine os atributos de usuário que são sincronizados do Azure ad para o alimento. Os atributos que são selecionados como propriedades **correspondentes** são usados para corresponder as *contas de grupo* no alimento para operações de atualização.

    ![Mapeamentos de usuário do alimento](media/Foodee-provisioning-tutorial/groupattribute.png)

1. Para confirmar suas alterações, selecione **salvar**.
1. Configure os filtros de escopo. Para saber como, consulte as instruções no [tutorial filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o alimento, na seção **configurações** , altere o **status de provisionamento** para **ativado**.

    ![A opção de status de provisionamento](common/provisioning-toggle-on.png)

1. Em **configurações**, na lista suspensa **escopo** , defina os usuários ou grupos que você deseja provisionar para o alimento.

    ![A lista suspensa escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, selecione **salvar**.

    ![O botão de salvamento da configuração de provisionamento](common/provisioning-configuration-save.png)

A operação anterior inicia a sincronização inicial dos usuários ou grupos que você definiu na lista suspensa **escopo** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações, consulte [quanto tempo levará para provisionar os usuários?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento. O relatório descreve todas as ações que são executadas pelo serviço de provisionamento do Azure AD no alimento. Para obter mais informações, consulte [verificar o status do provisionamento do usuário](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciar o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
