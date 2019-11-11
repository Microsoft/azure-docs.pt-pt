---
title: 'Tutorial: configurar o Storegate para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Storegate.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 628fa804-c0e6-4db1-ab6b-46ee9aab4d41
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: 1769b46210c766adc876dc36bf4e646fb23a6823
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905306"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Tutorial: configurar o Storegate para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Storegate e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Storegate.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Storegate](https://www.storegate.com)
* Uma conta de usuário em um Storegate com permissões de administrador.

## <a name="assign-users-to-storegate"></a>Atribuir usuários ao Storegate

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Storegate. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Storegate seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Dicas importantes para atribuir usuários ao Storegate

* É recomendável que um único usuário do Azure AD seja atribuído ao Storegate para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Storegate, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-storegate-for-provisioning"></a>Configurar o Storegate para provisionamento

Antes de configurar o Storegate para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do Storegate.

1. Entre no console do [administrador do Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue até as configurações clicando no ícone de usuário no canto superior direito e selecione configurações de **conta**.

    ![Storegate adicionar SCIM](media/storegate-provisioning-tutorial/admin.png)

2. Em configurações, navegue até **configurações de > de equipe** e verifique se o comutador de alternância está ativado na seção **logon único** .

    ![Configurações de Storegate](media/storegate-provisioning-tutorial/team.png)

    ![Botão de alternância Storegate](media/storegate-provisioning-tutorial/sso.png)

3. Copie a **URL** e o **token**do locatário. Esses valores serão inseridos nos campos **URL do locatário** e **token secreto** , respectivamente, na guia provisionamento do aplicativo Storegate na portal do Azure. 

    ![Storegate criar token](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Adicionar o Storegate da Galeria

Para configurar o Storegate para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Storegate da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![A folha aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Storegate**, selecione **Storegate** no painel de resultados. 

    ![Storegate na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se no Storegate** , que o redirecionará para a página de logon do Storegate. 

    ![Storegate OIDC Add](media/storegate-provisioning-tutorial/signup.png)

6.  Entre no console do [administrador do Storegate](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) e navegue até as configurações clicando no ícone de usuário no canto superior direito e selecione configurações de **conta**.

    ![Logon do Storegate](media/storegate-provisioning-tutorial/admin.png)

7. Em configurações, navegue até **configurações de > de equipe** e clique no botão de alternância na seção logon único, isso iniciará o fluxo de consentimento. Clique em **Ativar**.

    ![Equipe do Storegate](media/storegate-provisioning-tutorial/team.png)

    ![SSO do Storegate](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate ativar](media/storegate-provisioning-tutorial/activate.png)

8. Como Storegate é um aplicativo OpenIDConnect, opte por fazer logon no Storegate usando sua conta corporativa da Microsoft.

    ![Storegate OIDC logon](media/storegate-provisioning-tutorial/login.png)

9. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado para sua conta do Storegate.

    ![Consentimento Storegate OIDc](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Configurar o provisionamento automático de usuário para o Storegate 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Storegate com base em atribuições de usuário e/ou grupo no Azure AD.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade de SCIM do Storegate, confira [isso](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Storegate no Azure AD

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Storegate**.

    ![O link do Storegate na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://dialpad.com/scim` na **URL do locatário**. Insira o valor que você recuperou e salvou anteriormente de Storegate no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Storegate. Se a conexão falhar, verifique se sua conta do Storegate tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Storegate**.

    ![Mapeamentos de usuário Storegate](media/storegate-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Storegate na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Storegate para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do Storegate](media/storegate-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Storegate, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o Storegate escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Storegate.

Para obter mais informações sobre como ler os logs de provisionamento do Azure AD, consulte [relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
