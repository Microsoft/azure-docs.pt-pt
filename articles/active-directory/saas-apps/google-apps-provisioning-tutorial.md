---
title: 'Tutorial: configurar o G Suite para o provisionamento automático de usuário com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e desprovisionar automaticamente contas de usuário do Azure AD para o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8239b25f5ebf94d7368190f3169d0637d9943df1
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720305"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: configurar o G Suite para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no G Suite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o G Suite.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para obter detalhes importantes sobre o que esse serviço faz, como ele funciona e perguntas frequentes, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> O conector do G Suite foi atualizado recentemente em outubro de 2019. As alterações feitas no conector do G Suite incluem:
- Adição de suporte para atributos adicionais de usuário e grupo do G Suite. 
- Atualizado os nomes de atributo de destino do G Suite para corresponder ao que está definido [aqui](https://developers.google.com/admin-sdk/directory).
- Mapeamentos de atributo padrão atualizados.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o G Suite, você precisa dos seguintes itens:

- Um locatário do Azure AD
- [Um locatário do G Suite](https://gsuite.google.com/pricing.html)
- Uma conta de usuário em um G Suite com permissões de administrador.

## <a name="assign-users-to-g-suite"></a>Atribuir usuários ao G Suite

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao G Suite. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao G Suite seguindo estas instruções:

* [Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Dicas importantes para atribuir usuários ao G Suite

* É recomendável que um único usuário do Azure AD seja atribuído ao G Suite para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao G Suite, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os usuários com a função de **acesso padrão** são excluídos do provisionamento.

## <a name="setup-g-suite-for-provisioning"></a>Configurar o G Suite para provisionamento

Antes de configurar o G Suite para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no G Suite.

1. Entre no console de [Administração do G Suite](https://admin.google.com/) com sua conta de administrador e selecione **segurança**. Se você não vir o link, ele poderá estar oculto no menu **mais controles** na parte inferior da tela.

    ![Selecione segurança.][10]

1. Na página **segurança** , selecione **referência de API**.

    ![Selecione referência de API.][15]

1. Selecione **habilitar acesso à API**.

    ![Selecione referência de API.][16]

   > [!IMPORTANT]
   > Para cada usuário que você pretende provisionar para o G Suite, seu nome de usuário no Azure AD **deve** estar vinculado a um domínio personalizado. Por exemplo, nomes de usuário semelhantes a bob@contoso.onmicrosoft.com não são aceitos pelo G Suite. Por outro lado, bob@contoso.com é aceito. Você pode alterar o domínio de um usuário existente seguindo as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Depois de adicionar e verificar os domínios personalizados desejados com o Azure AD, você deve verificá-los novamente com o G Suite. Para verificar os domínios no G Suite, consulte as seguintes etapas:

    a. No [console de administração do G Suite](https://admin.google.com/), selecione **domínios**.

    ![Selecionar domínios][20]

    b. Selecione **Adicionar um domínio ou um alias de domínio**.

    ![Adicionar um novo domínio][21]

    c. Selecione **Adicionar outro domínio**e digite o nome do domínio que você deseja adicionar.

    ![Digite seu nome de domínio][22]

    d. Selecione **continuar e verifique a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como verificar seu domínio com o Google, consulte [verificar a propriedade do site](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para todos os domínios adicionais que você pretende adicionar ao G Suite.

1. Em seguida, determine qual conta de administrador você deseja usar para gerenciar o provisionamento de usuário no G Suite. Navegue até **funções de administrador**.

    ![Selecionar Google Apps][26]

1. Para a **função de administrador** dessa conta, edite os **privilégios** para essa função. Certifique-se de habilitar todos os **privilégios de API de administrador** para que essa conta possa ser usada para provisionamento.

    ![Selecionar Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Adicionar o G Suite da Galeria

Para configurar o G Suite para o provisionamento automático de usuário com o Azure AD, será necessário adicionar o G Suite da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados. 

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

1. Vá para **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, insira **g Suite**, selecione **g Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configurando o provisionamento automático de usuário para o G Suite 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no G Suite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o G Suite, seguindo as instruções fornecidas no [tutorial de logon único do g Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto de extremidade da API do diretório do G Suite, consulte [API de diretório](https://developers.google.com/admin-sdk/directory).

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o G Suite no Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicativos, selecione **G Suite**.

    ![O link do G Suite na lista de aplicativos](common/all-applications.png)

1. Selecione a guia **provisionamento** .

    ![Guia provisionamento](common/provisioning.png)

1. Defina o **modo de provisionamento** como **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

1. Na seção **credenciais de administrador** , selecione **autorizar**. Ele abre uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

    ![Autorização do G Suite](media/google-apps-provisioning-tutorial/authorize.png)

1. Confirme que você deseja conceder permissões do Azure AD para fazer alterações no seu locatário do G Suite. Selecione **Aceitar**.

    ![Confirme as permissões.][28]

1. Na portal do Azure, selecione **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo. Se a conexão falhar, verifique se sua conta do G Suite tem permissões de administrador de equipe. Em seguida, tente a etapa de **autorização** novamente.

1. No campo **email de notificação** , insira o endereço de email de uma pessoa ou grupo que deve receber as notificações de erro de provisionamento e marque a caixa de seleção- **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Guardar**.

1. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o G Suite**.

    ![Mapeamentos de usuário do G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Examine os atributos de usuário que são sincronizados do Azure AD para o G Suite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no G Suite para operações de atualização. Selecione o botão **salvar** para confirmar as alterações.

    ![Atributos de usuário do G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o G Suite**.

    ![Mapeamentos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Examine os atributos de grupo que são sincronizados do Azure AD para o G Suite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no G Suite para operações de atualização. Selecione o botão **salvar** para confirmar as alterações. A interface do usuário exibe o conjunto padrão de mapeamentos de atributo entre o Azure AD e o G Suite. Você pode optar por adicionar atributos adicionais, como a unidade org, clicando em Adicionar novo mapeamento.

    ![Atributos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de escopo, consulte as instruções a seguir fornecidas no [tutorial de filtro de escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para habilitar o serviço de provisionamento do Azure AD para o G Suite, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

1. Defina os usuários e/ou grupos que você deseja provisionar para o G Suite escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para provisionar, clique em **salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **escopo** na seção **configurações** . A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no G Suite.

> [!NOTE]
> Se os usuários já tiverem uma conta pessoal/consumidor existente usando o endereço de email do usuário do Azure AD, isso poderá causar algum problema que poderia ser resolvido usando a ferramenta de transferência do Google antes de executar a sincronização de diretórios.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="common-issues"></a>Problemas comuns
* As falhas de autorização podem ocorrer quando a conta usada para estabelecer uma conexão não é para um administrador no GSuite. Verifique se a conta usada para autorizar o acesso tem permissões de administrador em **todos os domínios** aos quais os usuários precisam ser provisionados. 

## <a name="next-steps"></a>Passos seguintes

* [Saiba como examinar os logs e obter relatórios sobre a atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)

<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
