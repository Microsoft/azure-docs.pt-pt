---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com easySSO para BitBucket Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EasySSO para o BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.openlocfilehash: bd15798e9b642f3abc617c0fa224c530d90b5dd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88555576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Tutorial: Azure Ative Directy integração única (SSO) com easySSO para BitBucket

Neste tutorial, você vai aprender a integrar easySSO para BitBucket com Azure Ative Directory (Azure AD). Quando integra o EasySSO para o BitBucket com AZure AD, pode:

* Control em Azure AD que tem acesso ao EasySSO para o BitBucket.
* Ative os seus utilizadores a serem automaticamente inscritos no EasySSO para o BitBucket com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como integração de aplicações de serviço (SaaS) com Azure AD, consulte O que é o acesso à [aplicação e um único acesso com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição da EasySSO para o BitBucket que está ativada para um único sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EasySSO para BitBucket suporta SSO iniciado com SP e iniciado em IdP.
* O EasySSO para o BitBucket suporta o fornecimento de utilizadores "just-in-time".
* Depois de configurar o EasySSO para o BitBucket, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Adicione EasySSO para BitBucket da galeria

Para configurar a integração do EasySSO para o BitBucket em AD Azure, é necessário adicionar easySSO para o BitBucket da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **EasySSO para BitBucket** na caixa de pesquisa.
1. Selecione **EasySSO para BitBucket** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Configurar e testar a Azure AD um único sinal para easySSO para o BitBucket

Configure e teste Azure AD SSO com EasySSO para BitBucket utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no EasySSO para o BitBucket.

Para configurar e testar o Azure AD SSO com easySSO para o BitBucket, complete os seguintes passos:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configure o EasySSO para o BitBucket SSO](#configure-easysso-for-bitbucket-sso) para configurar as definições de inscrição única no lado da aplicação.
    1. [Crie um EasySSO para o utilizador de teste BitBucket](#create-an-easysso-for-bitbucket-test-user) ter uma contraparte de B.Simon no EasySSO para o BitBucket, ligado à representação AD AD do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página **EasySSO para** integração de aplicações BitBucket, encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Screenshot de Configurar single Sign-On com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado **IdP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL que utiliza o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL que utiliza o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Selecione **Definir URLs adicionais**e faça o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    - Na caixa de texto **URL de entrada de inscrição,** digite um URL que utiliza o seguinte padrão: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de apoio easySSO](mailto:support@techtime.co.nz) para obter estes valores em caso de dúvida. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação EasySSO para BitBucket espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos predefinidos](common/default-attributes.png)

1. A aplicação EasySSO para BitBucket também espera que mais alguns atributos sejam repercutidos na resposta SAML. A tabela a seguir mostra isto. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome | Atributo de origem|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | utilizador.sobrenome |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Se os utilizadores de AD do Azure tiverem **o nome de contagem sAMAccount** Configurado, tem de mapear a **urna:0.9.2342.19200300.100.1.1.1** no atributo **sAMAccountName.**
    
1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** selecione os links de descarregamento para as opções **De Certificado (Base64)** ou **Federação Metadados XML.** Guarde ambos para o seu computador. Mais tarde, vai precisar para configurar o BitBucket EasySSO.

    ![Screenshot da secção de Certificado de Assinatura SAML, com links de descarregamento em destaque](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Se planeia configurar o EasySSO para o BitBucket manualmente com um certificado, também precisa copiar **o URL de Login** e o **Identificador AD Azure**, e guardá-los no seu computador.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste, B.Simon, no portal Azure.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. Para **nome,** insira `B.Simon` .  
   1. Para **o nome de utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à EasySSO para BitBucket.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **EasySSO para BitBucket**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

    ![Screenshot da página de Utilizadores e grupos, com o utilizador Adicionar em destaque](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Configurar easySSO para BitBucket SSO

1. Inscreva-se na sua instância Atlassian BitBucket com privilégios de administrador e vá para a secção **Administração.** 

    ![Screenshot da instância BitBucket, com ícone de engrenagem em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Localizar e selecionar **EasySSO**.

    ![Screenshot da opção Easy SSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selecione **SAML**. Isto leva-o à secção de configuração SAML.

    ![Screenshot da página de administração easySSO, com SAML em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selecione o separador **Certificados** e é apresentado com o seguinte ecrã:

    ![Screenshot do separador Certificados, com várias opções em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Localizar o **Certificado (Base64)** ou **o Ficheiro de Metadados** que guardou na secção anterior deste tutorial. Pode proceder de uma das seguintes formas:

    - Utilize o **Ficheiro de Metadados** da Federação de Aplicações que descarregou para um ficheiro local no seu computador. Selecione o botão de rádio **Upload** e siga o caminho específico para o seu sistema operativo.

    - Abra o **Ficheiro de Metadados** da Federação de Aplicações para ver o conteúdo do ficheiro, em qualquer editor de texto simples. Copie-o para a pasta. Selecione **Input**e cole o conteúdo da área de transferência no campo de texto.
 
    - Faça uma configuração totalmente manual. Abra o Certificado da Federação de Aplicações **(Base64)** para ver o conteúdo do ficheiro, em qualquer editor de texto simples. Copie-o para a prancheta e cole-o no campo de texto **de assinatura de token IdP.** Em seguida, vá ao separador **Geral** e preencha os campos DE URL e **ID de ID** **de ID de ENCA** COM O POST com os respetivos valores para **URL de Login** e **identificador AD AD** que guardou anteriormente.
 
1. **Selecione Guardar** na parte inferior da página. Verá que o conteúdo dos metadados ou dos ficheiros de certificados é analisado nos campos de configuração. O EasySSO para a configuração do BitBucket está completo.

1. Para testar a configuração, vá ao **separador Olhar & Feel** e selecione **o Botão de Início de Sessão SAML**. Isto permite um botão separado no ecrã de início de súbton BitBucket, especificamente para testar a sua integração Azure AD SAML de ponta a ponta. Pode deixar este botão ligado e configurar a sua colocação, cor e tradução para o modo de produção, também.

    ![Screenshot da página SAML Look & Feel, com botão de login SAML em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Se tiver algum problema, contacte a [equipa de apoio easySSO.](mailto:support@techtime.co.nz)

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Crie um EasySSO para o utilizador de teste BitBucket

Nesta secção, cria-se um utilizador chamado Britta Simon in BitBucket. O EasySSO para o BitBucket suporta o fornecimento de utilizadores just-in-time, que é desativado por padrão. Para o ativar, tem de verificar explicitamente **criar o utilizador no login com sucesso** na secção **Geral** da configuração plug-in EasySSO. Se um utilizador já não existir no BitBucket, um novo é criado após a autenticação.

No entanto, se não pretender permitir o fornecimento automático do utilizador quando o utilizador iniciar a primeira sedutilagem, os utilizadores devem existir em diretórios de utilizadores que o caso do BitBucket faz uso. Por exemplo, este diretório pode ser LDAP ou Atlassian Crowd.

![Screenshot da secção geral da configuração plug-in EasySSO, com o utilizador Criar em início de sessão bem-sucedido realçado](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Teste SSO 

### <a name="idp-initiated-workflow"></a>Fluxo de trabalho iniciado pelo IdP

Nesta secção, testa a configuração de um único sinal de acesso Azure AD utilizando o Painel de Acesso.

Quando selecionar o easySSO para azulejo BitBucket, deverá ser automaticamente inscrito na instância BitBucket para a qual configura sSO. Para obter mais informações, consulte [iniciar sômis e inicie aplicações a partir do portal My Apps.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

### <a name="sp-initiated-workflow"></a>Fluxo de trabalho iniciado pela SP

Nesta secção, testa a configuração de entrada única AD AZure utilizando o botão de **Login BitBucket SAML.**

![Screenshot do Log no ecrã, com o LOGIN SAML em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

Este cenário pressupõe que tenha ativado o **botão de login SAML** no **separador Olhar & Feel** na sua página de configuração BitBucket EasySSO. Abra o URL de login do BitBucket no modo incógnito do navegador, para evitar qualquer interferência nas sessões existentes. Selecione **O Login SAML**e será redirecionado para o fluxo de autenticação do utilizador Azure. Depois de concluído com sucesso, é redirecionado de volta para a sua instância BitBucket como um utilizador autenticado via SAML.

Pode encontrar o seguinte ecrã depois de ser redirecionado de Azure AD:

![Screenshot do ecrã de falha EasySSO, com número de referência em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

Se o fizer, siga as [instruções desta página](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) para ter acesso ao ficheiro **atlassian-bitbucket.log.** Os detalhes do erro estarão disponíveis pelo ID de referência encontrado na página de erro easySSO.

Se tiver algum problema, contacte a [equipa de apoio easySSO.](mailto:support@techtime.co.nz)

## <a name="additional-resources"></a>Recursos adicionais

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Tutoriais para integrar aplicações SaaS no Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente easySSO para BitBucket com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Proteja o EasySSO para o BitBucket com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
