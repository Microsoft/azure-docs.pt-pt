---
title: 'Tutorial: configurar o Elium para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Elium.
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
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: a4ddcf27869ea7484f98329d14d01bfad83af219
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709525"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Tutorial: configurar o Elium para o provisionamento automático de usuário

Este tutorial mostra como configurar o Elium e o Azure Active Directory (Azure AD) para provisionar e desprovisionar automaticamente usuários ou grupos para o Elium.

> [!NOTE]
> Este tutorial descreve um conector que é criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz e como ele funciona, e para perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em pré-visualização. Para obter os termos de uso gerais dos recursos do Azure na versão prévia, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial pressupõe que você já tenha os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Elium](https://www.elium.com/pricing/)
* Uma conta de usuário no Elium, com permissões de administrador

## <a name="assigning-users-to-elium"></a>Atribuindo usuários ao Elium

O Azure AD usa um conceito chamado *atribuições* para determinar quais usuários recebem acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, decida quais usuários e grupos no Azure AD precisam de acesso ao Elium. Em seguida, atribua esses usuários e grupos ao Elium seguindo as etapas em [atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Dicas importantes para atribuir usuários ao Elium 

Recomendamos que você atribua um único usuário do Azure AD ao Elium para testar a configuração automática de provisionamento de usuário. Mais usuários e grupos podem ser atribuídos posteriormente.

Ao atribuir um usuário ao Elium, você deve selecionar uma função válida específica do aplicativo (se houver alguma disponível) na caixa de diálogo de atribuição. Os usuários que têm a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-elium-for-provisioning"></a>Configurar o Elium para provisionamento

Antes de configurar o Elium para o provisionamento automático de usuário com o Azure AD, você deve habilitar o sistema de provisionamento do SCIM (gerenciamento de identidade entre domínios) no Elium. Siga estes passos.

1. Entre no Elium e vá para **meu perfil** > **configurações**.

    ![Item de menu configurações em Elium](media/Elium-provisioning-tutorial/setting.png)

1. No canto inferior esquerdo, em **avançado**, selecione **segurança**.

    ![Link de segurança em Elium](media/Elium-provisioning-tutorial/security.png)

1. Copie a **URL do locatário** e os valores do **token secreto** . Você usará esses valores posteriormente, em campos correspondentes na guia **provisionamento** do aplicativo Elium na portal do Azure.

    ![URL do locatário e campos de token secretos em Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Adicionar o Elium da Galeria

Para configurar o Elium para o provisionamento automático de usuário com o Azure AD, você também deve adicionar o Elium da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS (software como serviço) gerenciados. Siga estes passos.

1. No [portal do Azure](https://portal.azure.com), no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![Azure Active Directory item de menu](common/select-azuread.png)

1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

     ![Folha de aplicativos empresariais do Azure AD](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione **novo aplicativo** na parte superior do painel.

    ![Novo link de aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, digite **Elium**, selecione **Elium** na lista de resultados e, em seguida, selecione **Adicionar** para adicionar o aplicativo.

    ![Caixa de pesquisa da Galeria](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Configurar o provisionamento automático de usuário para o Elium

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e grupos no Elium, com base nas atribuições de usuário e de grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único para Elium com base em Security Assertion Markup Language (SAML) seguindo as instruções no tutorial de [logon único do Elium](Elium-tutorial.md). Você pode configurar o logon único independentemente do provisionamento automático de usuário, embora os dois recursos se complementem.

Para configurar o provisionamento automático de usuário para Elium no Azure AD, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com), selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais do Azure AD](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **Elium**.

    ![Lista de aplicativos na folha aplicativos empresariais](common/all-applications.png)

1. Selecione a guia **provisionamento** .

    ![Guia provisionamento na folha aplicativos empresariais](common/provisioning.png)

1. Defina o **modo de provisionamento** como **automático**.

    ![Configuração automática para o modo de provisionamento](common/provisioning-automatic.png)

1. Na seção **credenciais de administrador** , digite **\<urldolocatário\>/scim/v2** no campo **URL do locatário** . (O **urldolocatário** é o valor recuperado anteriormente do console de administração do Elium.) Além disso, digite o valor do **token de segredo** Elium no campo **token secreto** . Por fim, selecione **testar conexão** para verificar se o Azure AD pode se conectar ao Elium. Se a conexão falhar, verifique se sua conta do Elium tem permissões de administrador e tente novamente.

    ![URL do locatário e campos de token secreto em credenciais de administrador](common/provisioning-testconnection-tenanturltoken.png)

1. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que receberá as notificações de erro de provisionamento. Em seguida, marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha** .

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Guardar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Elium**.

    ![Sincronizar link para mapear usuários do Azure AD para Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Examine os atributos de usuário que são sincronizados do Azure AD para o Elium na seção **mapeamentos de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Elium para operações de atualização. Selecione **salvar** para confirmar as alterações.

    ![Mapeamentos de atributo entre o Azure AD e o Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Para configurar filtros de escopo, siga as instruções no [tutorial filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o Elium, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento definido como ativado](common/provisioning-toggle-on.png)

1. Defina os usuários e grupos que você deseja provisionar para o Elium selecionando os valores desejados na caixa de listagem suspensa **escopo** na seção **configurações** .

    ![Caixa de listagem escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, selecione **salvar**.

    ![Botão Salvar para configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e grupos definidos no **escopo** na seção **configurações** . Esse processo de sincronização inicial leva mais tempo do que as sincronizações posteriores. Para obter mais informações sobre o tempo necessário para o provisionamento, consulte [quanto tempo levará para provisionar os usuários?](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Use a seção **status atual** para monitorar o progresso e siga os links para o relatório de atividade de provisionamento. O relatório de atividade de provisionamento descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Elium. Para obter mais informações, consulte [verificar o status do provisionamento do usuário](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
