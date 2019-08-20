---
title: 'Tutorial: Configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o TheOrgWiki.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 5eb2096e-f7b6-4a56-a814-0f3d0e788063
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: f691b3e80bdde546074b9f243add82ace00c6301
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576384"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Tutorial: Configurar o TheOrgWiki para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no TheOrgWiki e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no TheOrgWiki.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um locatário do OrgWiki](https://www.theorgwiki.com/welcome/).
* Uma conta de usuário no TheOrgWiki com permissões de administrador.

## <a name="assign-users-to-theorgwiki"></a>Atribuir usuários ao TheOrgWiki

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao TheOrgWiki. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao TheOrgWiki seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Dicas importantes para atribuir usuários ao TheOrgWiki

* É recomendável que um único usuário do Azure AD seja atribuído ao TheOrgWiki para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao TheOrgWiki, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-theorgwiki-for-provisioning"></a>Configurar o TheOrgWiki para provisionamento

Antes de configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure AD, será necessário habilitar o provisionamento do SCIM no TheOrgWiki.

1. Entre no console do [administrador do TheOrgWiki](https://www.theorgwiki.com/login/). Clique em **console do administrador**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/login.png)

2. No console do administrador do, clique na **guia Configurações**. 

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navegue até **contas de serviço**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Clique em **+ conta de serviço**. Em **tipo de conta de serviço**, selecione **baseado em token**. Clique em **Guardar**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Copie os **tokens ativos**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo TheOrgWiki no portal do Azure.
     
    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>Adicionar o TheOrgWiki da Galeria

Para configurar o TheOrgWiki para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o TheOrgWiki da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **TheOrgWiki**, selecione **TheOrgWiki** no painel de resultados. 

    ![TheOrgWiki na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se no TheOrgWiki** , que o redirecionará para a página de logon do TheOrgWiki. 

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image00.png)

6.  No canto superior direito, selecione **logon**.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image02.png)

7. Como TheOrgWiki é um aplicativo OpenIDConnect, opte por fazer logon no OrgWiki usando sua conta corporativa da Microsoft.

    ![TheOrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Após uma autenticação bem-sucedida, o aplicativo será adicionado automaticamente ao seu locatário e você será redirecionado para sua conta do TheOrgWiki.

    ![OrgWiki adicionar SCIM](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Configurar o provisionamento automático de usuário para o TheOrgWiki 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no TheOrgWiki com base em atribuições de usuário e/ou grupo no Azure AD.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para TheOrgWiki no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **TheOrgWiki**.

    ![O link do OrgWiki na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` a **URL de locatário**. 

    Exemplo: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> O **valor** de subdomínio só pode ser definido durante o processo de inscrição inicial para TheOrgWiki.
 
6. Insira o valor do token no campo de **token secreto** que você recuperou anteriormente de TheOrgWiki. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao TheOrgWiki. Se a conexão falhar, verifique se sua conta do TheOrgWiki tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para TheOrgWiki**.

    ![Mapeamentos de usuário TheOrgWiki](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para o TheOrgWiki na seção de **mapeamento de atributo** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no TheOrgWiki para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do TheOrgWiki](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para o TheOrgWiki, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você deseja provisionar para o OrgWiki escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes. Para obter mais informações sobre quanto tempo levará para o provisionamento de usuários e/ou grupos, consulte [quanto tempo levará para provisionar usuários](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Você pode usar a seção **status atual** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no TheOrgWiki. Para obter mais informações, consulte [verificar o status do provisionamento do usuário](../manage-apps/application-provisioning-when-will-provisioning-finish-specific-user.md). Para ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de conta de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md).
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md).