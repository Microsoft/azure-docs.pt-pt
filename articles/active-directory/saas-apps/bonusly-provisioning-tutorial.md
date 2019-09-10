---
title: 'Tutorial: Configurar o bônus para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o bônus.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 692eff26dbbc2289ec589a327dbe58958b414a56
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842719"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Tutorial: Configurar de bônus para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em bônus e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente os usuários e/ou grupos para o bônus.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem o seguinte:

* Um locatário do Azure AD
* Um [locatário bônus](https://bonus.ly/pricing)
* Uma conta de usuário em especial com permissões de administrador

> [!NOTE]
> A integração de provisionamento do Azure AD depende da [API REST](https://konghq.com/solutions/gateway/), que está disponível para os desenvolvedores de bônus.

## <a name="adding-bonusly-from-the-gallery"></a>Adicionando bônus da Galeria

Antes de configurar o bônus para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o bônus da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o bônus à galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue até **aplicativos empresariais** e, em seguida, selecione a opção **todos os aplicativos** .

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **bônus**, selecione **bônus** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Bónus na lista de resultados](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Atribuir usuários ao bônus

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram "atribuídos" a um aplicativo no Azure AD são sincronizados. 

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao bônus. Depois de decidir, você pode atribuir esses usuários e/ou grupos para bônus seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Dicas importantes para atribuir usuários ao bônus

* É recomendável que um único usuário do Azure AD seja atribuído ao bônus para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao bônus, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configurando o provisionamento automático de usuário para o bônus

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos de um bônus com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o bônus, seguindo as instruções fornecidas no tutorial de [logon único de bônus](bonus-tutorial.md). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o bônus no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **aplicativos empresariais**, selecione **todos os aplicativos**e, em seguida, selecione **bônus**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **bônus**.

    ![O link de bônus na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Na seção **credenciais de administrador** , insira o **token secreto** de sua conta de bônus, conforme descrito na etapa 6.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. O **token secreto** para sua conta de bônus está localizado em **integrações de > de administrador > empresa**. Na seção **se você deseja codificar** , clique em **API > criar novo token de acesso de API** para criar um novo token secreto.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Na tela a seguir, digite um nome para o token de acesso na caixa de texto fornecida e pressione **criar chave de API**. O novo token de acesso será exibido por alguns segundos em um pop-up.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/Token02.png)

8. Ao preencher os campos mostrados na etapa 5, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao bônus. Se a conexão falhar, verifique se sua conta de bônus tem permissões de administrador e tente novamente.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Clique em **Guardar**.

11. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o bônus**.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Examine os atributos de usuário que são sincronizados do Azure AD para bônus na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em especial para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o bônus, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Defina os usuários e/ou grupos que você gostaria de provisionar para bônus escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Provisionamento de bônus](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD em especial.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
