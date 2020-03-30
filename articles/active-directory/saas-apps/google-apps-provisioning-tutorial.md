---
title: 'Tutorial: Configure G Suite para fornecimento automático de utilizadores com Diretório Ativo Azure [ Microsoft Docs'
description: Aprenda a fornecer e desfornecer automaticamente contas de utilizador de Azure AD para G Suite.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057724"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: Configure G Suite para fornecimento automático de utilizadores

O objetivo deste tutorial é demonstrar os passos a serem realizados na G Suite e no Azure Ative Directory (Azure AD) para configurar a Azure AD para fornecer automaticamente e desfornecer utilizadores e/ou grupos para a G Suite.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Da AD Azure. Para detalhes importantes sobre o que este serviço faz, como funciona, e perguntas frequentes, consulte o fornecimento e o [desprovisionamento de utilizadores automate para aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

> [!NOTE]
> O conector G Suite foi recentemente atualizado em outubro de 2019. As alterações feitas no conector G Suite incluem:
> - Suporte adicional para atributos adicionais do utilizador g Suite e do grupo. 
> - Dados de atributos de destino G Suite atualizados para corresponder ao que é definido [aqui](https://developers.google.com/admin-sdk/directory).
> - Mapeamento de atributo sinuoso atualizado.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com a G Suite, precisa dos seguintes itens:

- Um inquilino da AD Azure
- [Um inquilino G Suite](https://gsuite.google.com/pricing.html)
- Uma conta de utilizador numa Suite G com permissões de administrador.

## <a name="assign-users-to-g-suite"></a>Atribuir utilizadores à G Suite

O Azure Ative Directory utiliza um conceito chamado atribuições para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do fornecimento automático de utilizadores, apenas os utilizadores e/ou grupos que tenham sido atribuídos a uma aplicação em AD Azure são sincronizados.

Antes de configurar e ativar o fornecimento automático de utilizadores, deve decidir quais os utilizadores e/ou grupos em Azure AD que precisam de acesso à G Suite. Uma vez decidido, pode atribuir estes utilizadores e/ou grupos à G Suite seguindo as instruções aqui:

* [Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Dicas importantes para atribuir utilizadores à G Suite

* Recomenda-se que um único utilizador da AD Azure seja atribuído à G Suite para testar a configuração automática de fornecimento do utilizador. Posteriormente, os utilizadores e/ou grupos adicionais podem ser atribuídos.

* Ao atribuir um utilizador à G Suite, deve selecionar qualquer função específica de aplicação válida (se disponível) no diálogo de atribuição. Os utilizadores com a função **de Acesso Predefinido** estão excluídos do fornecimento.

## <a name="setup-g-suite-for-provisioning"></a>Configurar suíte G para provisionamento

Antes de configurar a G Suite para o fornecimento automático de utilizadores com a AD Azure, terá de ativar o fornecimento de SCIM na G Suite.

1. Inscreva-se na [consola G Suite Admin](https://admin.google.com/) com a sua conta de administrador e, em seguida, selecione **Security**. Se não vir o link, pode estar escondido sob o menu **Mais Controlos** na parte inferior do ecrã.

    ![Selecione a segurança.][10]

1. Na página **de Segurança,** selecione **Referência API**.

    ![Selecione referência API.][15]

1. **Selecione ativar o acesso a API**.

    ![Selecione referência API.][16]

   > [!IMPORTANT]
   > Para cada utilizador que pretenda fornecer à G Suite, o seu nome de utilizador em Azure AD **deve** estar ligado a um domínio personalizado. Por exemplo, os nomes de utilizadores que parecem bob@contoso.onmicrosoft.com não são aceites pela G Suite. Por outro lado, bob@contoso.com é aceite. Pode alterar o domínio de um utilizador existente seguindo as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

1. Depois de ter adicionado e verificado os seus domínios personalizados desejados com a AD Azure, deve comprová-los novamente com a G Suite. Para verificar domínios na Suite G, consulte os seguintes passos:

    a. Na [Consola G Suite Admin,](https://admin.google.com/)selecione **Domínios**.

    ![Selecione Domínios][20]

    b. Selecione **Adicionar um domínio ou um pseudónimo de domínio.**

    ![Adicione um novo domínio][21]

    c. Selecione **Adicionar outro domínio**, e, em seguida, digitar o nome do domínio que pretende adicionar.

    ![Escreva no seu nome de domínio][22]

    d. Selecione Continuar e verificar a **propriedade do domínio.** Em seguida, siga os passos para verificar se possui o nome de domínio. Para obter instruções completas sobre como verificar o seu domínio com o Google, consulte Verificar a [propriedade do seu site](https://support.google.com/webmasters/answer/35179).

    e. Repita os passos anteriores para quaisquer domínios adicionais que pretenda adicionar à G Suite.

1. Em seguida, determine qual a conta de administração que pretende utilizar para gerir o fornecimento de utilizadores na G Suite. Navegue para **papéis de administrador.**

    ![Selecione Aplicações do Google][26]

1. Para o **papel de Administrador** dessa conta, editar os **Privilégios** para esse papel. Certifique-se de que permite todos os **privilégios da Admin API** para que esta conta possa ser utilizada para o provisionamento.

    ![Selecione Aplicações do Google][27]

## <a name="add-g-suite-from-the-gallery"></a>Adicione g suite da galeria

Para configurar a G Suite para o fornecimento automático de utilizadores com o Azure AD, terá de adicionar a G Suite da galeria de aplicações Azure AD à sua lista de aplicações saaS geridas. 

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação esquerdo, selecione **Azure Ative Directory**.

    ![O botão Azure Ative Directory](common/select-azuread.png)

1. Vá às **aplicações da Enterprise**e, em seguida, selecione **Todas as aplicações**.

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione o novo botão de **aplicação** na parte superior do painel.

    ![O novo botão de aplicação](common/add-new-app.png)

1. Na caixa de pesquisa, introduza a **Suite G,** selecione **G Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

    ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configurar o fornecimento automático de utilizadores à G Suite 

Esta secção guia-o através dos passos para configurar o serviço de provisionamento de AD Azure para criar, atualizar e desativar utilizadores e/ou grupos na G Suite com base em atribuições de utilizador e/ou grupo em Azure AD.

> [!TIP]
> Também pode optar por ativar um único sinal baseado em SAML para a G Suite, seguindo as instruções fornecidas no tutorial de [sinalização G Suite Single](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). O único sinal de inscrição pode ser configurado independentemente do fornecimento automático do utilizador, embora estas duas funcionalidades se elogiem mutuamente.

> [!NOTE]
> Para saber mais sobre o ponto final da G Suite, consulte a API do [Diretório.](https://developers.google.com/admin-sdk/directory)

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o fornecimento automático de utilizadores para g suite em Azure AD:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com). Selecione **Aplicações Empresariais**e, em seguida, selecione **Todas as aplicações**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

1. Na lista de aplicações, selecione **G Suite**.

    ![O link G Suite na lista de Aplicações](common/all-applications.png)

1. Selecione o separador **Provisioning.**

    ![Guia de provisionamento](common/provisioning.png)

1. Detete o **modo de provisionamento** para **automático**.

    ![Guia de provisionamento](common/provisioning-automatic.png)

1. Na secção **credenciais de administrador,** selecione **Autorizar**. Abre uma caixa de diálogo de autorização do Google numa nova janela do navegador.

    ![G Suite Autorizar](media/google-apps-provisioning-tutorial/authorize.png)

1. Confirme que pretende dar permissões à AD Azure para fazer alterações ao seu inquilino da G Suite. Selecione **Aceitar**.

    ![Confirme permissões.][28]

1. No portal Azure, selecione **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação. Se a ligação falhar, certifique-se de que a sua conta G Suite tem permissões de Team Admin. Em seguida, tente o passo **autorizado** novamente.

1. No campo de email de **notificação,** insira o endereço de e-mail de uma pessoa ou grupo que deve receber as notificações de erro de fornecimento e verificar a caixa de verificação - Envie uma notificação por **e-mail quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

1. Clique em **Guardar**.

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Users to G Suite**.

    ![Mapeamento de utilizador suíte G](media/google-apps-provisioning-tutorial/usermappings.png)

1. Reveja os atributos do utilizador que são sincronizados de Azure AD para G Suite na secção De Mapeamento do **Atributo.** Os atributos selecionados como propriedades **Correspondentes** são usados para combinar as contas de utilizador em G Suite para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração.

    ![Atributos de utilizador suite G](media/google-apps-provisioning-tutorial/userattributes.png)

1. Na secção **Mapeamentos,** **selecione Synchronize Azure Ative Directory Groups to G Suite**.

    ![Mapeamentos do grupo G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

1. Reveja os atributos do grupo que são sincronizados de Azure AD para G Suite na secção de Mapeamento de **Atributos.** Os atributos selecionados como propriedades **correspondentes** são usados para combinar os grupos em G Suite para operações de atualização. Selecione o botão **Guardar** para elegiro qualquer alteração. A UI exibe o conjunto padrão de mapeamentos de atributos entre a Azure AD e a G Suite. Pode optar por adicionar atributos adicionais, como a Unidade Org, clicando em adicionar um novo mapeamento.

    ![Atributos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

1. Para configurar filtros de deteção, consulte as seguintes instruções fornecidas no tutorial do [filtro Descodificação](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Para ativar o serviço de provisionamento de AD Azure para g suite, altere o Estado de **Provisionamento** para **On** na secção **Definições.**

    ![Estatuto de provisionamento Alternado](common/provisioning-toggle-on.png)

1. Defina os utilizadores e/ou grupos que deseja fornecer à G Suite, escolhendo os valores desejados no **Âmbito** na secção **Definições.**

    ![Âmbito de provisionamento](common/provisioning-scope.png)

1. Quando estiver pronto para fornecer, clique em **Guardar**.

    ![Configuração de fornecimento de poupança](common/provisioning-configuration-save.png)

Esta operação inicia a sincronização inicial de todos os utilizadores e/ou grupos definidos no **Âmbito** na secção **Definições.** A sincronização inicial demora mais tempo a ser desempenhada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento AD Azure esteja em funcionamento. Pode utilizar a secção Detalhes de **Sincronização** para monitorizar o progresso e seguir ligações ao relatório de atividades de provisionamento, que descreve todas as ações realizadas pelo serviço de provisionamento de AD Azure na G Suite.

> [!NOTE]
> Se os utilizadores já tiverem uma conta pessoal/consumidor existente utilizando o endereço de e-mail do utilizador da AD Azure, então poderá causar algum problema que possa ser resolvido utilizando a Ferramenta de Transferência do Google antes de realizar a sincronização de diretório.

Para obter mais informações sobre como ler os registos de provisionamento da AD Azure, consulte [relatórios sobre o fornecimento automático](../app-provisioning/check-status-user-account-provisioning.md)de conta de utilizador .

## <a name="additional-resources"></a>Recursos adicionais

* [Gestão do provisionamento de conta de utilizador para aplicações empresariais](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

## <a name="common-issues"></a>Problemas comuns
* Falhas de autorização podem ocorrer quando a conta usada para estabelecer uma ligação não é para um administrador na GSuite. Certifique-se de que a conta utilizada para autorizar o acesso tem permissões de administração sobre **todos os domínios** com os que os utilizadores precisam de ser aprovisionados. 
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
