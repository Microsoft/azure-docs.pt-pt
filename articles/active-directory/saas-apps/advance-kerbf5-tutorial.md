---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com f5 [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9c5fb47a-1c5d-437a-b4c1-dbf739eaf5e3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984fd0c7946a50922315269c87e08b1c35b74348
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74074763"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com F5

Neste tutorial, você vai aprender a integrar F5 com o Azure Ative Directory (Azure AD). Quando integrar F5 com AD Azure, pode:

* Controlo em Azure AD que tem acesso a F5.
* Permita que os seus utilizadores sejam automaticamente inscritos no F5 com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por um único sinal (SSO) f5.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* F5 suporta **SP e IDP** iniciadoS SSO
* F5 SSO pode ser configurado de três maneiras diferentes.

- [Configure F5 único sign-on para aplicação Kerberos Avançada](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configure f5 único sinal para candidatura kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adicionando F5 da galeria

Para configurar a integração de F5 em Azure AD, você precisa adicionar F5 da galeria à sua lista de aplicações SaaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite F5** na caixa de pesquisa.
1. Selecione **F5** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configure e teste Azure AD único signo para F5

Configure e teste Azure AD SSO com F5 utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em F5.

Para configurar e testar o Azure AD SSO com F5, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure F5-SSO](#configure-f5-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. Criar o utilizador de **[teste F5](#create-f5-test-user)** - para ter uma contraparte de B.Simon em F5 que esteja ligada à representação azure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **F5,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta real e URL de sinalização. Contacte a equipa de apoio ao [cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Deconfiguração de F5,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao F5.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **F5**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-f5-sso"></a>Configure F5 SSO

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configure f5 único sinal para candidatura kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Configure F5 único sign-on para aplicação Kerberos Avançada

1. Abra uma nova janela do navegador web e inscreva-se no site da sua empresa F5 (Advanced Kerberos) como administrador e execute os seguintes passos:

1. É necessário importar o Certificado de Metadados para o F5 (Advanced Kerberos) que será utilizado mais tarde no processo de configuração. Ir para **a Gestão de Certificados de > sistema > Gestão de Certificados de Tráfego >> Lista de Certificados SSL.** Clique na **Importação** do canto direito.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Para configurar o IDP SAML, aceda ao **Access > Federation > Prestador de serviços SAML > Criar > a partir de metadados**.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure02.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Especificar o Certificado enviado da Tarefa 3

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure06.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Para configurar o SAML SP, aceda à Federação de **> de Acesso > Federação de Serviços SAML > Serviços Locais de SP > Criar**.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Clique em **OK**.

1. Selecione a configuração SP e clique em **ligar/ligar conectores idp**.

     ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Clique em **Adicionar Nova Linha** e selecione o **conector IdP externo** criado em passo anterior.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Para configurar Kerberos SSO, **Access > Single Sign-on > Kerberos**

    >[!Note]
    >Necessitará da Conta de Delegação Kerberos para ser criada e especificada. Consulte a secção KCD (Consulte o apêndice para referências variáveis)

    • Fonte de nome de utilizador`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Fonte do Reino do Utilizador`session.logon.last.domain`

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure11.png)

1. Para configurar o Perfil de Acesso, **o Acesso > Perfil/Políticas > Perfil de Acesso (por política de sessão)**.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure12.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure13.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure14.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure15.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}}}}

    * session.ad.lastrealdomínio TEXT superdemo.live

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure18.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Para adicionar novo nó, vá aos **nós de tráfego local > > nó> +**+ .

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Para criar uma nova Piscina, vá a **Piscinas locais > piscinas > > Criar.**

     ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Para criar um novo servidor virtual, vá a Servidores virtuais > de tráfego local > lista de **servidores virtuais > +**.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure23.png)

1. Especifique o perfil de acesso criado em etapa anterior.

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Criação da Delegação Kerberos 

>[!Note]
>Para mais detalhes consulte [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Passo 1: Criar uma conta de delegação**

    * Exemplo
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Passo 2: Definir SPN (na conta da delegação da APM)**

    *  Exemplo
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Passo 3: Delegação da SPN (para a conta de serviço de aplicações)**

    * Criação da delegação adequada para a Conta delegada f5.
    * No exemplo abaixo, a conta de delegação da APM está a ser configurada para a APLICAÇÃO FRP-App1.superdemo.live.

        ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure25.png)

1. Fornecer os detalhes mencionados no documento de referência acima ao abrigo [deste](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

1. Apêndice- SAML – Mapeamentos variáveis F5 BIG-IP apresentados abaixo:

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure26.png)

    ![Configuração F5 (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure27.png) 

1. Abaixo está toda a lista de Atributos SAML padrão. GivenName é representado usando a seguinte corda.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| | |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | utilizador0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | utilizador0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.resultado | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:nomes:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:nomes:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validadeNãoAntes | `<ID>` |
| eb46b6b6.session.saml.last.validadeNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Criar o utilizador de teste F5

Nesta secção, cria-se um utilizador chamado B.Simon em F5. Trabalhe com a equipa de suporte ao [Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os utilizadores na plataforma F5. Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo F5 no Painel de Acesso, deve ser automaticamente inscrito no F5 para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente F5 com Azure AD](https://aad.portal.azure.com/)

- [Configure o único sinal de F5 para a aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configure f5 único sinal para candidatura kerberos](kerbf5-tutorial.md)

