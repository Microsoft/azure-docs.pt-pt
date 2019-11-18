---
title: 'Tutorial: configurar o SSO do Soloinsight-CloudGate para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o SSO do Soloinsight-CloudGate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 07558ceb-d406-40e7-90b8-1b40fdc829e7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 0d16c5e811de99bc9f9e35cbd023db51d5997bb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152713"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Tutorial: configurar o SSO do Soloinsight-CloudGate para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em Soloinsight-CloudGate SSO e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o SSO Soloinsight-CloudGate.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário de SSO Soloinsight-CloudGate](https://www.soloinsight.com/)
* Uma conta de usuário no SSO Soloinsight-CloudGate com permissões de administrador.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Atribuindo usuários ao SSO do Soloinsight-CloudGate

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao SSO Soloinsight-CloudGate. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao SSO do Soloinsight-CloudGate seguindo as instruções aqui:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Dicas importantes para atribuir usuários ao SSO do Soloinsight-CloudGate

* É recomendável que um único usuário do Azure AD seja atribuído ao SSO do Soloinsight-CloudGate para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao SSO do Soloinsight-CloudGate, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Configurar o SSO do Soloinsight-CloudGate para provisionamento

1. Entre em seu [console de administrador do SSO do Soloinsight-CloudGate](https://soloinsight.sigateway.com/login). Navegue até **administração > configurações do sistema**.

    ![Console de administração de SSO do Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Navegue para **geral**.

    ![Soloinsight-CloudGate SSO Add SCIM](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Role para baixo até o final da página para obter a **URL do locatário** e o **token secreto**. Copie o **token secreto**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo de SSO Soloinsight-CloudGate no portal do Azure.

    ![Token de criação de SSO do Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionar o SSO do Soloinsight-CloudGate da Galeria

Antes de configurar o SSO do Soloinsight-CloudGate para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o SSO do Soloinsight-CloudGate da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o SSO do Soloinsight-CloudGate da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **SSO do Soloinsight-CloudGate**, selecione **SSO do Soloinsight-CloudGate** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Soloinsight-CloudGate SSO na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Configurando o provisionamento automático de usuário para o SSO do Soloinsight-CloudGate 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos em Soloinsight-CloudGate SSO com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o SSO do Soloinsight-CloudGate, seguindo as instruções fornecidas no [tutorial de logon único do SSO do Soloinsight-CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem uns aos outros

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o SSO do Soloinsight-CloudGate no Azure AD:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Soloinsight-CLOUDGATE SSO**.

    ![O link de SSO Soloinsight-CloudGate na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://sigateway.com/scim/v2/sync/serviceproviderconfig` na **URL do locatário**. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao SSO do Soloinsight-CloudGate. Se a conexão falhar, verifique se sua conta de SSO do Soloinsight-CloudGate tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o SSO Soloinsight-CloudGate**.

    ![Mapeamentos de usuário do SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o SSO do Soloinsight-CloudGate na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no SSO Soloinsight-CloudGate para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário de SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o SSO Soloinsight-CloudGate**.

    ![Mapeamentos de grupo de SSO do Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o SSO do Soloinsight-CloudGate na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no SSO Soloinsight-CloudGate para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos do grupo de SSO Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o SSO do Soloinsight-CloudGate, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você deseja provisionar para o SSO do Soloinsight-CloudGate escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no SSO do Soloinsight-CloudGate.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

