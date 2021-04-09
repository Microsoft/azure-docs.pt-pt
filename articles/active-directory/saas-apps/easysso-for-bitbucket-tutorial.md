---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory (SSO) com a EasySSO para o BitBucket | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EasySSO para o BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 6fdc9c70d1c9fc67c38edfd794354f9e03321c73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731430"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Tutorial: Azure Ative Directy integração única (SSO) com easySSO para BitBucket

Neste tutorial, você vai aprender a integrar easySSO para BitBucket com Azure Ative Directory (Azure AD). Quando integra o EasySSO para o BitBucket com AZure AD, pode:

* Control em Azure AD que tem acesso ao EasySSO para o BitBucket.
* Ative os seus utilizadores a serem automaticamente inscritos no EasySSO para o BitBucket com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição da EasySSO para o BitBucket que está ativada para um único sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EasySSO para BitBucket suporta SSO iniciado com SP e iniciado em IdP.
* O EasySSO para o BitBucket suporta o fornecimento de utilizadores "just-in-time".

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Adicione EasySSO para BitBucket da galeria

Para configurar a integração do EasySSO para o BitBucket em AD Azure, é necessário adicionar easySSO para o BitBucket da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **de galeria,** escreva **EasySSO para BitBucket** na caixa de pesquisa.
1. Selecione **EasySSO para BitBucket** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Configure e teste Azure AD SSO para easySSO para BitBucket

Configure e teste Azure AD SSO com EasySSO para BitBucket utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador AZure AD e o utilizador relacionado no EasySSO para o BitBucket.

Para configurar e testar o Azure AD SSO com easySSO para o BitBucket, execute os seguintes passos:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    1. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
1. [Configure o EasySSO para o BitBucket SSO](#configure-easysso-for-bitbucket-sso) para configurar as definições de inscrição única no lado da aplicação.
    1. [Crie um EasySSO para o utilizador de teste BitBucket](#create-an-easysso-for-bitbucket-test-user) ter uma contraparte de B.Simon no EasySSO para o BitBucket, ligado à representação AD AD do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **EasySSO para** integração de aplicações BitBucket, encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Screenshot de Configurar single Sign-On com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado **IdP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um URL que utiliza o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL que utiliza o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Selecione **Definir URLs adicionais** e faça o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    - Na caixa de texto **URL de entrada de inscrição,** digite um URL que utiliza o seguinte padrão: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de apoio easySSO](mailto:support@techtime.co.nz) para obter estes valores em caso de dúvida. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação EasySSO para BitBucket espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos predefinidos](common/default-attributes.png)

1. A aplicação EasySSO para BitBucket também espera que mais alguns atributos sejam repercutidos na resposta SAML. A tabela a seguir mostra isto. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
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

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
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

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Configurar easySSO para BitBucket SSO

1. Para automatizar a configuração dentro do Zoom, é necessário instalar a extensão do **navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Zoom** irá direcioná-lo para a aplicação Zoom. A partir daí, forneça as credenciais de administração para assinar em Zoom. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-10.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Zoom manualmente, numa janela diferente do navegador web, inscreva-se no site da empresa Zoom como administrador.

1. Vá para a secção **de administração.**

    ![Screenshot da instância BitBucket, com ícone de engrenagem em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Localizar e selecionar **EasySSO**.

    ![Screenshot da opção Easy SSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Selecione **SAML**. Isto leva-o à secção de configuração SAML.

    ![Screenshot da página de administração easySSO, com SAML em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Selecione o separador **Certificados** e é apresentado com o seguinte ecrã:

    ![Screenshot do separador Certificados, com várias opções em destaque](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Localizar o **Certificado (Base64)** ou **o Ficheiro de Metadados** que guardou na secção anterior deste tutorial. Pode proceder de uma das seguintes formas:

    - Utilize o **Ficheiro de Metadados** da Federação de Aplicações que descarregou para um ficheiro local no seu computador. Selecione o botão de rádio **Upload** e siga o caminho específico para o seu sistema operativo.

    - Abra o **Ficheiro de Metadados** da Federação de Aplicações para ver o conteúdo do ficheiro, em qualquer editor de texto simples. Copie-o para a pasta. Selecione **Input** e cole o conteúdo da área de transferência no campo de texto.
 
    - Faça uma configuração totalmente manual. Abra o Certificado da Federação de Aplicações **(Base64)** para ver o conteúdo do ficheiro, em qualquer editor de texto simples. Copie-o para a prancheta e cole-o no campo de texto **de assinatura de token IdP.** Em seguida, vá ao separador **Geral** e preencha os campos DE URL e **ID de** **ENCA** COM O POST com os respetivos valores para **URL de Login** e **identificador AD AD** que guardou anteriormente.
 
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

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para EasySSO para sinal bitBucket em URL onde pode iniciar o fluxo de login.

* Vá diretamente ao EasySSO para o URL de inscrição do BitBucket e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no EasySSO para o BitBucket para o qual configura o SSO

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no easySSO para o bitBucket nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no EasySSO para o BitBucket para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado EasySSO para o BitBucket, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o acesso condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).