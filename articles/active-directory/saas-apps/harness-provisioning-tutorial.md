---
title: 'Tutorial: configurar o Harness para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o Harness.
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
ms.openlocfilehash: 34d05d6392e00757bf1e5562ffd8341ad04cc9dc
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74807761"
---
# <a name="tutorial-configure-harness-for-automatic-user-provisioning"></a>Tutorial: configurar o Harness para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em conjunto e Azure Active Directory (AD do Azure) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos a serem aproveitados.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do Harness](https://harness.io/pricing/)
* Uma conta de usuário no Harness com permissões de administrador.

## <a name="assigning-users-to-harness"></a>Atribuindo usuários ao Harness

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao Harness. Depois de decidir, você pode atribuir esses usuários e/ou grupos a serem aproveitados seguindo estas instruções:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-harness"></a>Dicas importantes para atribuir usuários ao Harness

* É recomendável que um único usuário do Azure AD seja atribuído ao Harness para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao Harness, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-harness-for-provisioning"></a>Configurar o Harness para provisionamento

1. Entre no console de [Administração](https://app.harness.io/#/login)do seu equipamento. Navegue até **segurança contínua > gerenciamento de acesso**.

    ![Console de administração de Harness](media/harness-provisioning-tutorial/admin.png)

2.  Clique em **chaves de API**.

    ![Harness adicionar SCIM](media/harness-provisioning-tutorial/apikeys.png)

3. Clique em **Adicionar nova chave**. Na caixa de diálogo **Adicionar chave de API** , forneça um **nome** e selecione uma opção em **permissões herdadas do** menu suspenso. Clique no botão **Enviar** .

    ![Harness Adicionar nova chave](media/harness-provisioning-tutorial/addkey.png)

    ![Caixa de diálogo Harness Adicionar nova chave](media/harness-provisioning-tutorial/title.png)

3.  Copie a **chave**. Esse valor será inserido no campo token secreto na guia provisionamento do seu aplicativo de equipamento no portal do Azure.

    ![Harness criar token](media/harness-provisioning-tutorial/token.png)

## <a name="add-harness-from-the-gallery"></a>Adicionar o Harness da Galeria

Antes de configurar o Harness para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o Harness da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar o Harness da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **Harness**, selecione **Harness** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![Harness na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-harness"></a>Configurando o provisionamento automático de usuário para o Harness 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos em conjunto com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o Harness seguindo as instruções fornecidas no tutorial de [logon único do Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem

> [!NOTE]
> Para saber mais sobre o conjunto de pontos de extremidade SCIM, consulte [este](https://docs.harness.io/article/smloyragsm-api-keys)

### <a name="to-configure-automatic-user-provisioning-for-harness-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o Harness no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **Harness**.

    ![O link do Harness na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://app.harness.io/gateway/api/scim/account/XCPzWkCIQ46ypIu2DeT7yw` na **URL do locatário**. Insira o valor do **token de autenticação scim** recuperado anteriormente no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao Harness. Se a conexão falhar, verifique se sua conta do Harness tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários a serem aproveitados**.

    ![Harness mapeamentos de usuário](media/harness-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o Harness na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no Harness para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Harness atributos de usuário](media/harness-provisioning-tutorial/userattributes.png)

10. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory a serem aproveitados**.

    ![Controlar mapeamentos de grupo](media/harness-provisioning-tutorial/groupmappings.png)

11. Examine os atributos de grupo que são sincronizados do Azure AD para o Harness na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos em conjunto para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Controlar atributos de grupo](media/harness-provisioning-tutorial/groupattributes.png)

12. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Para habilitar o serviço de provisionamento do Azure AD para o Harness, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

14. Defina os usuários e/ou grupos que você gostaria de provisionar para o equipamento escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

15. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no Harness.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
