---
title: 'Tutorial: configurar o IDEO para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente contas de usuário para o IDEO.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: de4f06a3-83e9-46ce-80ee-03d706b91c81
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2019
ms.author: Zhchia
ms.openlocfilehash: d6bc3170162710e86359b374d1ef707bba0ce268
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152544"
---
# <a name="tutorial-configure-ideo-for-automatic-user-provisioning"></a>Tutorial: configurar o IDEO para o provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no IDEO e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos no IDEO.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).
>
> Este conector está atualmente em visualização pública. Para obter mais informações sobre os termos de uso geral de Microsoft Azure para recursos de visualização, consulte [termos de uso suplementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes pré-requisitos:

* Um locatário do Azure AD
* [Um locatário do IDEO](https://www.shape.space/product/pricing)
* Uma conta de usuário em IDEO | Forma com permissões de administrador.

## <a name="assign-users-to-ideo"></a>Atribuir usuários ao IDEO

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao IDEO. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao IDEO seguindo as instruções aqui:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-ideo"></a>Dicas importantes para atribuir usuários ao IDEO

* É recomendável que um único usuário do Azure AD seja atribuído ao IDEO para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao IDEO, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="set-up-ideo-for-provisioning"></a>Configurar o IDEO para provisionamento

Antes de configurar o IDEO para o provisionamento automático de usuário com o Azure AD, será necessário recuperar algumas informações de provisionamento do IDEO.

1. Para o **token secreto** , contate a equipe de suporte do IDEO em productsupport@ideo.com. Esse valor será inserido no campo **token secreto** na guia provisionamento do seu aplicativo IDEO no portal do Azure. 

## <a name="add-ideo-from-the-gallery"></a>Adicionar o IDEO da Galeria

Para configurar o IDEO para o provisionamento automático de usuário com o Azure AD, você precisará adicionar o IDEO da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados.

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, digite **ideo**, selecione **ideo** no painel de resultados. 

    ![IDEO na lista de resultados](common/search-new-app.png)

5. Selecione o botão **inscrever-se no ideo** , que o redirecionará para a página de logon do ideo. 

    ![IDEO OIDC Add](media/ideo-provisioning-tutorial/signup.png)

6. Como IDEO é um aplicativo OpenIDConnect, opte por fazer logon no IDEO usando sua conta corporativa da Microsoft.

    ![IDEO OIDC logon](media/ideo-provisioning-tutorial/login.png)

7. Após uma autenticação bem-sucedida, aceite a solicitação de consentimento para a página de consentimento. O aplicativo será automaticamente adicionado ao seu locatário e você será redirecionado para sua conta do IDEO.

    ![Consentimento IDEO OIDc](media/ideo-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-ideo"></a>Configurar o provisionamento automático de usuário para o IDEO 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no IDEO com base em atribuições de usuário e/ou grupo no Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-ideo-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para IDEO no Azure AD:

1. Iniciar sessão no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **ideo**.

    ![O link do IDEO na lista de aplicativos](common/all-applications.png)

3. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **credenciais de administrador** , insira `https://profile.ideo.com/api/scim/v2` na **URL do locatário**. Insira o valor que você recuperou da equipe de suporte do IDEO no **token secreto**. Clique em **testar conexão** para garantir que o Azure ad possa se conectar ao ideo. Se a conexão falhar, verifique se sua conta do IDEO tem permissões de administrador e tente novamente.

    ![URL do locatário + token](common/provisioning-testconnection-tenanturltoken.png)

6. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

7. Clique em **Guardar**.

8. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para IDEO**.

    ![Mapeamentos de usuário IDEO](media/ideo-provisioning-tutorial/usermappings.png)

9. Examine os atributos de usuário que são sincronizados do Azure AD para o IDEO na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no ideo para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do IDEO](media/ideo-provisioning-tutorial/userattributes.png)

10. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Para habilitar o serviço de provisionamento do Azure AD para o IDEO, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](media/ideo-provisioning-tutorial/groupmappings.png)

12. Defina os usuários e/ou grupos que você deseja provisionar para o IDEO escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](media/ideo-provisioning-tutorial/groupattributes.png)

13. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no ideo.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)


