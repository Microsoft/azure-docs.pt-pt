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
ms.openlocfilehash: 969a2fb5444ae8ece2aa302c04a5bbb85dcca917
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77057724"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: configurar o G Suite para provisionamento automático de usuário

O objetivo deste tutorial é demonstrar as etapas a serem executadas no G Suite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o G Suite.

> [!NOTE]
> Este tutorial descreve um conector criado sobre o serviço de provisionamento de usuário do Azure AD. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

> [!NOTE]
> O conector do G Suite foi atualizado recentemente em outubro de 2019. As alterações feitas no conector do G Suite incluem:
> - Adição de suporte para atributos adicionais de usuário e grupo do G Suite. 
> - Dados de atributos de destino G Suite atualizados para corresponder ao que é definido [aqui](https://developers.google.com/admin-sdk/directory).
> - Mapeamentos de atributo padrão atualizados.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o G Suite, você precisa dos seguintes itens:

- Um locatário do Azure AD
- [Um inquilino G Suite](https://gsuite.google.com/pricing.html)
- Uma conta de usuário em um G Suite com permissões de administrador.

## <a name="assign-users-to-g-suite"></a>Atribuir usuários ao G Suite

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao G Suite. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao G Suite seguindo estas instruções:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Dicas importantes para atribuir usuários ao G Suite

* É recomendável que um único usuário do Azure AD seja atribuído ao G Suite para testar a configuração automática de provisionamento de usuário. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário ao G Suite, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-g-suite-for-provisioning"></a>Configurar o G Suite para provisionamento

Antes de configurar o G Suite para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no G Suite.

1. Inscreva-se na [consola G Suite Admin](https://admin.google.com/) com a sua conta de administrador e, em seguida, selecione **Security**. Se não vir o link, pode estar escondido sob o menu **Mais Controlos** na parte inferior do ecrã.

    ![Selecione segurança.][10]

1. Na página **de Segurança,** selecione **Referência API**.

    ![Selecione referência de API.][15]

1. **Selecione ativar o acesso a API**.

    ![Selecione referência de API.][16]

   > [!IMPORTANT]
   > Para cada utilizador que pretenda fornecer à G Suite, o seu nome de utilizador em Azure AD **deve** estar ligado a um domínio personalizado. Por exemplo, nomes de utilizadores que se parecem com bob@contoso.onmicrosoft.com não são aceites pela G Suite. Por outro lado, bob@contoso.com é aceite. Pode alterar o domínio de um utilizador existente seguindo as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Depois de adicionar e verificar os domínios personalizados desejados com o Azure AD, você deve verificá-los novamente com o G Suite. Para verificar os domínios no G Suite, consulte as seguintes etapas:

    a. Na [Consola G Suite Admin,](https://admin.google.com/)selecione **Domínios**.

    ![Selecionar domínios][20]

    b. Selecione **Adicionar um domínio ou um pseudónimo de domínio.**

    ![Adicionar um novo domínio][21]

    c. Selecione **Adicionar outro domínio**, e, em seguida, digitar o nome do domínio que pretende adicionar.

    ![Digite seu nome de domínio][22]

    d. Selecione Continuar e verificar a **propriedade do domínio.** Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções completas sobre como verificar o seu domínio com o Google, consulte Verificar a [propriedade do seu site](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para todos os domínios adicionais que você pretende adicionar ao G Suite.

1. Em seguida, determine qual conta de administrador você deseja usar para gerenciar o provisionamento de usuário no G Suite. Navegue para **papéis de administrador.**

    ![Selecionar Google Apps][26]

1. Para o **papel de Administrador** dessa conta, editar os **Privilégios** para esse papel. Certifique-se de que permite todos os **privilégios da Admin API** para que esta conta possa ser utilizada para o provisionamento.

    ![Selecionar Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Adicionar o G Suite da Galeria

Para configurar o G Suite para o provisionamento automático de usuário com o Azure AD, será necessário adicionar o G Suite da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados. 

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão do Azure Active Directory](common/select-azuread.png)

1. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicativo](common/add-new-app.png)

1. Na caixa de pesquisa, introduza a **Suite G,** selecione **G Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configurando o provisionamento automático de usuário para o G Suite 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no G Suite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para a G Suite, seguindo as instruções fornecidas no tutorial de [sinalização G Suite Single](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos se complementem.

> [!NOTE]
> Para saber mais sobre o ponto final da G Suite, consulte a API do [Diretório.](https://developers.google.com/admin-sdk/directory)

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o G Suite no Azure AD:

1. Inicie sessão no [portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Folha aplicativos empresariais](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **G Suite**.

    ![O link do G Suite na lista de aplicativos](common/all-applications.png)

1. Selecione o separador **Provisioning.**

    ![Guia provisionamento](common/provisioning.png)

1. Detete o **modo de provisionamento** para **automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

1. Na secção **credenciais de administrador,** selecione **Autorizar**. Ele abre uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

    ![Autorização do G Suite](media/google-apps-provisioning-tutorial/authorize.png)

1. Confirme que você deseja conceder permissões do Azure AD para fazer alterações no seu locatário do G Suite. Selecione **Aceitar**.

    ![Confirme as permissões.][28]

1. No portal Azure, selecione **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação. Se a conexão falhar, verifique se sua conta do G Suite tem permissões de administrador de equipe. Em seguida, tente o passo **autorizado** novamente.

1. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Guardar**.

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to G Suite**.

    ![Mapeamentos de usuário do G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

1. Reveja os atributos do utilizador que são sincronizados de Azure AD para G Suite na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em G Suite para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de usuário do G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to G Suite**.

    ![Mapeamentos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Reveja os atributos do grupo que são sincronizados de Azure AD para G Suite na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em G Suite para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração. A interface do usuário exibe o conjunto padrão de mapeamentos de atributo entre o Azure AD e o G Suite. Você pode optar por adicionar atributos adicionais, como a unidade org, clicando em Adicionar novo mapeamento.

    ![Atributos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de provisionamento de AD Azure para g suite, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

1. Defina os utilizadores e/ou grupos que deseja fornecer à G Suite, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Escopo de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais para ser executada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Azure AD esteja em execução. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na G Suite.

> [!NOTE]
> Se os usuários já tiverem uma conta pessoal/consumidor existente usando o endereço de email do usuário do Azure AD, isso poderá causar algum problema que poderia ser resolvido usando a ferramenta de transferência do Google antes de executar a sincronização de diretórios.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="common-issues"></a>Problemas comuns
* As falhas de autorização podem ocorrer quando a conta usada para estabelecer uma conexão não é para um administrador no GSuite. Certifique-se de que a conta utilizada para autorizar o acesso tem permissões de administração sobre **todos os domínios** com os que os utilizadores precisam de ser aprovisionados. 
* A Azure AD suporta utilizadores incapacitantes na GSuite para que não possam aceder à aplicação, mas não apaga utilizadores na GSuite.

## <a name="next-steps"></a>Passos seguintes

* [Saiba como rever os registos e obter relatórios sobre a atividade de provisionamento](../app-provisioning/check-status-user-account-provisioning.md)

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
