---
title: 'Tutorial: configurar o Looop para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Looop.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 0efe2262-43c3-4e0c-97fa-9344385638e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: cb1329e778f053c664227c5dfc373c0bbedb1f0a
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134700"
---
# <a name="tutorial-configure-looop-for-automatic-user-provisioning"></a>Tutorial: configurar o Looop para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no Looop e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no Looop.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Looop](https://www.looop.co/pricing/)
* Uma conta de usuário em um Looop com permissões de administrador.

## <a name="assign-users-to-looop"></a>Atribuir usuários ao Looop

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Looop. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao Looop seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-looop"></a>Dicas importantes para atribuir usuários ao Looop

* É recomendável que um único usuário do Azure AD seja atribuído ao Looop para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Looop, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-looop-for-provisioning"></a>Configurar o Looop para provisionamento

Antes de configurar o Looop para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do Looop.

1. Entre no console do [administrador do Looop](https://app.looop.co/#/login) e selecione **conta**. Em **configurações de conta** , selecione **autenticação**.

    ![Looop adicionar SCIM](media/looop-provisioning-tutorial/admin.png)

2. Gere um novo token clicando em **Redefinir token** em **integração do scim**.

    ![Looop adicionar SCIM](media/looop-provisioning-tutorial/resettoken.png)

3. Copie o **ponto de extremidade scim** e o **token**. Esses valores serão inseridos nos campos **URL do locatário** e **token secreto** na guia provisionamento do aplicativo Looop no portal do Azure. 

    ![Looop criar token](media/looop-provisioning-tutorial/token.png)

## <a name="add-looop-from-the-gallery"></a>Adicionar o Looop da Galeria

Para configurar o Looop para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o Looop da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **Looop**, selecione **Looop** no painel de resultados. 

    ![Looop na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se no Looop** , que o redirecionará para a página de logon do Looop. 

    ![Looop OIDC Add](media/looop-provisioning-tutorial/signup.png)

6. Como Looop é um aplicativo OpenIDConnect, opte por fazer logon no Looop usando sua conta corporativa da Microsoft.

    ![Looop OIDC logon](media/looop-provisioning-tutorial/msftlogin.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado para sua conta do Looop.

    ![Consentimento Looop OIDc](media/looop-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-looop"></a>Configurar o provisionamento automático de usuário para o Looop 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no Looop com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-looop-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para Looop no Azure AD:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Looop**.

    ![O link do Looop na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://<organisation_domain>.looop.co/scim/v2` na **URL do locatário**. Por exemplo, `https://demo.looop.co/scim/v2`. Insira o valor que você recuperou e salvou anteriormente de Looop no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Looop. Se a conexão falhar, verifique se sua conta do Looop tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para Looop**.

    ![Mapeamentos de usuário Looop](media/looop-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Looop na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Looop para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do Looop](media/looop-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para conector de metaredes**.

    ![Mapeamentos de grupo Looop](media/looop-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o conector de metaredes na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no conector de metaredes para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos do grupo Looop](media/looop-provisioning-tutorial/groupattributes.png)

10. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o Looop, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o Looop escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

13. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Looop.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)


