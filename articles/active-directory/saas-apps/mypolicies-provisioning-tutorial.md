---
title: 'Tutorial: Configurar mypolicies para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para mypolicies.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: f000896d-a78c-4d20-a79c-74c1f9b4961a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: a1dbab4850d7db5d6ce8243062cb976013653250
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68602181"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Tutorial: Configurar mypolicies para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas em mypolicies e Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para mypolicies.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte automatizar o [provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um inquilino do Azure AD.
* [Um locatário](https://mypolicies.com/index.html#section10)mypolicies.
* Uma conta de usuário em mypolicies com permissões de administrador.

## <a name="assigning-users-to-mypolicies"></a>Atribuindo usuários ao mypolicies

Azure Active Directory usa um conceito chamado *atribuições* para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso a mypolicies. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao mypolicies seguindo estas instruções:
* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Dicas importantes para atribuir usuários ao mypolicies

* É recomendável que um único usuário do Azure AD seja atribuído a mypolicies para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao mypolicies, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="setup-mypolicies-for-provisioning"></a>Configurar mypolicies para provisionamento

Antes de configurar mypolicies para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM em mypolicies.

1. Entre em contato com seu representante do mypolicies em **support@mypolicies.com** para obter o token secreto necessário para configurar o provisionamento do SCIM.

2.  Salve o valor do token fornecido pelo representante mypolicies. Esse valor será inserido no campo **token secreto** na guia provisionamento do aplicativo mypolicies na portal do Azure.

## <a name="add-mypolicies-from-the-gallery"></a>Adicionar mypolicies da Galeria

Para configurar mypolicies para o provisionamento automático de usuário com o Azure AD, você precisa adicionar o mypolicies da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

**Para adicionar mypolicies da Galeria de aplicativos do Azure AD, execute as seguintes etapas:**

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insiramypolicies, selecione mypolicies no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![mypolicies na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Configurando o provisionamento automático de usuário para mypolicies 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos em mypolicies com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para mypolicies, seguindo as instruções fornecidas no [tutorial de logon único](mypolicies-tutorial.md)do mypolicies. O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para mypolicies no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecionemypolicies.

    ![O link mypolicies na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://<myPoliciesCustomDomain>.mypolicies.com/scim` a **URL** de locatário `<myPoliciesCustomDomain>` em que é seu domínio personalizado mypolicies. Você pode recuperar seu domínio de cliente mypolicies, da sua URL.
Exemplo: `<demo0-qa>`. mypolicies.com.

6. Em **token secreto**, insira o valor do token que foi recuperado anteriormente. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao mypolicies. Se a conexão falhar, verifique se sua conta mypolicies tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

7. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![E-mail de Notificação](common/provisioning-notification-email.png)

8. Clique em **Guardar**.

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para**mypolicies.

    ![Mapeamentos de usuário mypolicies](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para mypolicies na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário em mypolicies para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Mapeamentos de usuário mypolicies](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

12. Para habilitar o serviço de provisionamento do Azure AD para mypolicies, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

13. Defina os usuários e/ou grupos que você deseja provisionar para mypolicies escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

14. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD em mypolicies.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* mypolicies sempre requer **username**, **email** e **externalId**.
* mypolicies não dá suporte a exclusões rígidas para atributos de usuário.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos Seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)
