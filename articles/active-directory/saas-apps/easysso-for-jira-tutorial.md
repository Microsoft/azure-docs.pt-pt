---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com easySSO para Jira | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EasySSO para a Jira.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/28/2020
ms.author: jeedes
ms.openlocfilehash: b813f1f77b7bda2627f5275e772a3ee7c9d12834
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454359"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Tutorial: Azure Ative Directy integração única (SSO) com easySSO para Jira

Neste tutorial, você vai aprender a integrar easySSO para Jira com Azure Ative Directory (Azure AD). Quando integra o EasySSO para Jira com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Jira.
* Ative os seus utilizadores a serem automaticamente inscritos na Jira com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EasySSO para jira assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EasySSO para Jira suporta **SP e IDP** iniciado SSO
* EasySSO para Jira suporta provisão de utilizadores **Just In Time**
* Uma vez configurado EasySSO para Jira, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-jira-from-the-gallery"></a>Adicionar EasySSO para Jira da galeria

Para configurar a integração da EasySSO para Jira em AD Azure, precisa de adicionar EasySSO para Jira da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **EasySSO para Jira** na caixa de pesquisa.
1. Selecione **EasySSO para Jira** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-jira"></a>Configurar e testar Azure AD único sinal para easySSO para Jira

Configure e teste Azure AD SSO com EasySSO para Jira usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na EasySSO para a Jira.

Para configurar e testar o Azure AD SSO com easySSO para jira, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o EasySSO para jira SSO](#configure-easysso-for-jira-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie easySSO para o utilizador de teste Jira](#create-easysso-for-jira-test-user)** - para ter uma contraparte de B.Simon na EasySSO para Jira que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página **EasySSO para** integração de aplicações Jira, encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte da EasySSO](mailto:support@techtime.co.nz) para obter estes valores em caso de dúvida. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A EasySSO para a aplicação Jira espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a EasySSO para a aplicação Jira espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | utilizador.sobrenome |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Caso os utilizadores da Azure AD tenham **configurado o nome sAMAccountName,** teria de mapear **a urna:oid:0.9.2342.19200300.100.1.1 para** o atributo **sAMAccountName.**
    
1. Na **configuração de um único sessão de sessão com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** links para **opções De Certificado (Base64)** ou **Federação De Metadados XML** e guardar qualquer um ou todos para o seu computador. Mais tarde, vai precisar dele para configurar a Jira EasySSO.

    ![O link de descarregamento de certificado](media/easysso-for-jira-tutorial/azure-ad-SAML-certificate.png)
    
    Se planeia executar o EasySSO para a configuração Jira manualmente com certificado, também precisa copiar o **URL de Login** e o **Identificador AD Azure** da secção abaixo e guardá-los no seu computador.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD
    
Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à EasySSO para Jira.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **EasySSO para Jira**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-easysso-for-jira-sso"></a>Configure EasySSO para Jira SSO

1. Inscreva-se na sua instância Atlassian Jira com privilégios de Administrador e navegue para a secção **'Gerir apps'.** 

    ![Gerir Aplicações](media/easysso-for-jira-tutorial/jira-admin-1.png)

2. No lado esquerdo, localize o **EasySSO** e clique nele.

    ![SSO fácil](media/easysso-for-jira-tutorial/jira-admin-2.png)

3. Selecione a opção **SAML.** Isto irá levá-lo à secção de configuração SAML.

    ![SAML](media/easysso-for-jira-tutorial/jira-admin-3.png)

4. Selecione o separador **Certificados** na parte superior e será apresentado com o seguinte ecrã:

    ![URL de metadados](media/easysso-for-jira-tutorial/jira-admin-4.png)

5. Agora, localizar **Certificado (Base64)** ou **Metadados File** que guardou nos passos anteriores da configuração **AZure AD SSO.** Tem as seguintes opções sobre como proceder:

    a. Utilize o **Ficheiro de Metadados** da Federação de Aplicações que descarregou para o ficheiro local no seu computador. Selecione **carregar** o botão de rádio e siga o diálogo do ficheiro de upload específico para o seu sistema operativo.

    **OR**

    b. Abra o **Ficheiro de Metadados** da Federação de Aplicações para ver o conteúdo (em qualquer editor de texto simples) do ficheiro e copiá-lo na área de transferência. Selecione a opção **entrada** e cole o conteúdo da área de transferência no campo de texto.

     **OR**

    c. Configuração totalmente manual. Abra o Certificado da Federação de Aplicações **(Base64)** para ver o conteúdo (em qualquer editor de texto simples) do ficheiro e copiá-lo na área de transferência. Cole-o no campo de texto **de assinatura de IdP Token.** Em seguida, navegue para separador **geral** e preencha os campos de URL e **ID de ID** **de ID de encadernação** post com os respetivos valores para **URL de Login** e **identificador AD AD que** guardou anteriormente.
 
6. Clique em **Guardar** o botão na parte inferior da página. Verá que o conteúdo dos ficheiros Metadados ou Certificado é analisado nos campos de configuração. A configuração EasySSO para Jira está completa.

7. Para obter a melhor experiência de teste, navegue no **separador Look & Feel** e consulte a opção **botão de login SAML.** Isto permitirá um botão separado no ecrã de login Jira especificamente para testar a sua integração Azure AD SAML de ponta a ponta. Pode deixar este botão ligado e configurar a sua colocação, cor e tradução para o modo de produção também.

    ![Olhar & Sentir](media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Se tiver algum problema, contacte a equipa de [suporte da EasySSO.](mailto:support@techtime.co.nz)

### <a name="create-easysso-for-jira-test-user"></a>Crie easySSO para o utilizador de teste Jira

Nesta secção, um utilizador chamado Britta Simon é criado em Jira. O EasySSO para jira suporta o fornecimento de utilizadores just-in-time, que é **desativado** por padrão. Para ativar o fornecimento do utilizador, tem de verificar explicitamente criar o utilizador na opção **de login bem sucedida** na secção geral da configuração do plugin EasySSO. Se um utilizador já não existir em Jira, um novo é criado após a autenticação.

No entanto, se não pretender permitir o fornecimento automático de utilizadores no primeiro login do utilizador, os utilizadores devem existir em Diretórios de Utilizador backend, como lDAP ou Atlassian Crowd.

![Aprovisionamento de utilizadores](media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Teste SSO 

### <a name="idp-initiated-workflow"></a>Fluxo de trabalho iniciado pelo IdP

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo EasySSO para Jira no Painel de Acesso, deverá ser automaticamente inscrito na instância Jira para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

### <a name="sp-initiated-workflow"></a>Fluxo de trabalho iniciado pela SP

Nesta secção, testa a configuração de login Azure AD com o botão de Login Jira **SAML.**

![Login SAML do utilizador](media/easysso-for-jira-tutorial/jira-admin-7.png)

Este cenário pressupõe que tenha ativado **o botão de login DO SAML** no **separador Olhar & Sinta o** separador na sua página de configuração Jira EasySSO (ver acima). Abra o URL de login Jira no modo incógnito do navegador para evitar qualquer interferência nas sessões existentes. Clique no botão **DE Início de Sessão SAML** e será redirecionado para o fluxo de autenticação do utilizador Azure. Uma vez concluído com sucesso, será redirecionado de volta para a sua instância Jira como utilizador autenticado via SAML.

Há a possibilidade de encontrar o seguinte ecrã depois de ser redirecionado de volta do AZure AD

![Ecrã de falha EasySSO](media/easysso-for-jira-tutorial/jira-admin-8.png)

Neste caso, tem de seguir as [instruções desta página]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) para ter acesso ao ficheiro **atlassian-jira.log.** Os detalhes do erro estarão disponíveis pelo ID de referência encontrado na página de erro easySSO.

Se tiver algum problema em digerir as mensagens de registo, contacte a [equipa de suporte da EasySSO.](mailto:support@techtime.co.nz)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente easySSO para Jira com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger a EasySSO para jira com visibilidade e controlos avançados](/cloud-app-security/proxy-intro-aad)