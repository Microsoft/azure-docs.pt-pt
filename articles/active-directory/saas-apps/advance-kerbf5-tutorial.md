---
title: 'Tutorial: Azure Ative Directy integração única com F5 | Microsoft Docs'
description: Neste artigo, aprenda os passos necessários para integrar o F5 com o Azure Ative Directory (Azure AD).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: db8977e484e8d1f2cf4b30427d47ba45969f2147
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654464"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Tutorial: Azure Ative Directory (AD) integração única (SSO) com F5

Neste tutorial, você vai aprender a integrar F5 com Azure Ative Directory (Azure AD). Quando integrar o F5 com AZure AD, pode:

* Controlo em Azure AD que tem acesso a F5.
* Ative os seus utilizadores a serem automaticamente inscritos no F5 com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* F5 assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

F5 suporta **SP e IDP** iniciado SSO.

F5 SSO pode ser configurado de três maneiras diferentes:

- [Configurar f5 único sinal para aplicação Kerberos Avançada](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configurar f5 único sinal para a aplicação Kerberos](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Adicionar F5 da galeria

Para configurar a integração do F5 no AD Azure, é necessário adicionar F5 da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **F5** na caixa de pesquisa.
1. Selecione **F5** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Configurar e testar Azure AD único sinal para F5

Configure e teste Azure AD SSO com F5 usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em F5.

Para configurar e testar o Azure AD SSO com F5, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure f5-SSO](#configure-f5-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create F5 test user](#create-f5-test-user)** - para ter uma contraparte de B.Simon em F5 que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **F5,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<YourCustomFQDN>.f5.com/`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte [a equipa de suporte do Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar F5,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a F5.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **F5**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-f5-sso"></a>Configurar F5 SSO

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configurar f5 único sinal para a aplicação Kerberos](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Configurar f5 único sinal para aplicação Kerberos Avançada

1. Abra uma nova janela do navegador web e inscreva-se no seu site da empresa F5 (Advanced Kerberos) como administrador e execute os seguintes passos:

1. É necessário importar o Certificado de Metadados para o F5 (Kerberos Avançados) que será utilizado mais tarde no processo de configuração. Vá para **a Gestão de Certificados > Sistema > Gestão de Certificados de Tráfego >> Lista de Certificados SSL**. Clique em **Importar** do canto direito.

    ![Screenshot que destaca o botão Importação para importar o Certificado de Metadados.](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Para configurar o IDP SAML, aceda à **Federação de > > Prestador de Serviços SAML > Criar > A partir de Metadados**.

    ![Screenshot que realça como criar o IDP SAML a partir de metadados.](./media/advance-kerbf5-tutorial/configure02.png)

    ![Screenshot que mostra o novo ecrã do conector IdP SAML.](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![Configuração F5 (Kerberos Avançados)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Screenshot que mostra o ecrã de definições de sinal único. ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Especificar o Certificado enviado a partir da Tarefa 3

    ![Screenshot que mostra o ecrã do Conector IdP Editar SAML.](./media/advance-kerbf5-tutorial/configure06.png)

    ![Screenshot que mostra o ecrã de definições de serviço de logout único.](./media/advance-kerbf5-tutorial/configure07.png)

 1. Para configurar o SAML SP, aceda à **Federação de > de Acesso > Federação de Serviços SAML > Serviços SP Locais > Criar**.

    ![Screenshot que mostra o ecrã onde cria um serviço SP local.](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Clique em **OK**.

1. Selecione a configuração SP e clique **em Ligar/UnBind IdP Connectors**.

     ![Screenshot que mostra o Fornecedor de Serviços SAML.](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Clique em **Adicionar Nova Linha** e selecione o **conector IdP externo** criado no passo anterior.

    ![Screenshot que realça o botão Add New Row.](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Para configurar Kerberos SSO, **acessar > único sign-on > Kerberos**

    >[!Note]
    >Necessitará da Conta delegação de Kerberos para ser criada e especificada. Consulte a secção KCD (consulte o apêndice para referências variáveis)

    • Nome de utilizador Fonte  `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    • Origem do Reino do Utilizador  `session.logon.last.domain`

    ![Screenshot que destaca o Access > Single Sign On.](./media/advance-kerbf5-tutorial/configure11.png)

1. Para configurar o perfil de acesso, **aceder > perfil/políticas > perfil de acesso (por sessão)**.

    ![Screenshot que realça o separador Propriedades na opção menu Perfis/Políticas.](./media/advance-kerbf5-tutorial/configure12.png)

    ![Screenshot que mostra o separador Domínios SSO/Auth.](./media/advance-kerbf5-tutorial/configure13.png)

    ![Screenshot que mostra o separador Política de Acesso.](./media/advance-kerbf5-tutorial/configure14.png)

    ![Screenshot que mostra o separador Propriedades na Política de Acesso.](./media/advance-kerbf5-tutorial/configure15.png)

    ![Screenshot que mostra as propriedades para Atributo Variável.](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}}}

    * session.ad.lastactualdomain TEXT superdemo.live

    ![Screenshot que mostra as propriedades da consulta de AD.](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Screenshot que mostra o separador 'Regras da Sucursal' e a regra da Conta De Cheque.](./media/advance-kerbf5-tutorial/configure18.png)

    ![Screenshot que mostra as caixas de texto de expressão variável personalizada e personalizada.](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Screenshot que mostra os valores nos campos SSO Token Name e SSO Token Password.](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Para adicionar novo nó, aceda a **> Nodes > Lista de Nó de Tráfego Local > +**.

    ![Screenshot que destaca o tráfego local > Nós.](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Para criar uma nova Piscina, vá a **Local Traffic > Pools > Pool List > Create.**

     ![Screenshot que destaca o tráfego local > Piscinas.](./media/advance-kerbf5-tutorial/configure22.png)

 1. Para criar um novo servidor virtual, aceda a **Servidores Virtuais > de Tráfego Local > Lista de ServidorEs Virtuais > +**.

    ![Screenshot que destaca o tráfego local > servidores virtuais.](./media/advance-kerbf5-tutorial/configure23.png)

1. Especifique o perfil de acesso criado no passo anterior.

    ![Screenshot que mostra onde especifica o perfil de acesso que criou.](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Criação da Delegação Kerberos 

>[!Note]
>Para mais detalhes consulte [aqui](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

* **Passo 1: Criar uma Conta delegada**

    * Exemplo
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Passo 2: Definir SPN (na conta de delegação APM)**

    *  Exemplo
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Passo 3: Delegação SPN (para a Conta de Serviço de Aplicações)**

    * Configurar a delegação adequada para a conta da delegação F5.
    * No exemplo abaixo, a conta da delegação APM está a ser configurada para o KCD para FRP-App1.superdemo.live app.

        ![Screenshot que mostra o separador APM Delegatio Account Properties > Delegação.](./media/advance-kerbf5-tutorial/configure25.png)

1. Fornecer os detalhes mencionados no [](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) documento de referência acima

1. Apêndice- SAML – Mapeamentos variáveis BIG-IP mostrados abaixo:

    ![Screenshot que mostra o separador > Ative Sessions.](./media/advance-kerbf5-tutorial/configure26.png)

    ![Screenshot que mostra as variáveis e as teclas de sessão.](./media/advance-kerbf5-tutorial/configure27.png) 

1. Abaixo está toda a lista de atributos SAML padrão. GivenName é representado usando a seguinte corda.
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Sessão | Atributo |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.microsoft.com/identity/claims/displayname | utilizador0 |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | utilizador0 |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http: \/ /schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-formato:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:nomes:tc:SAML:2.0:status:Sucesso |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oásis:nomes:tc:SAML:2.0:cm:portador |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Criar utilizador de teste F5

Nesta secção, cria-se um utilizador chamado B.Simon em F5. Trabalhe com [a equipa de suporte do Cliente F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) para adicionar os utilizadores na plataforma F5. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. 

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo F5 no Painel de Acesso, deverá ser automaticamente inscrito no F5 para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente F5 com Azure AD](https://aad.portal.azure.com/)

- [Configurar f5 único sinal para aplicação baseada em cabeçalho](headerf5-tutorial.md)

- [Configurar f5 único sinal para a aplicação Kerberos](kerbf5-tutorial.md)

- [F5 BIG-IP APM e Ad AD Azure para acesso híbrido seguro](../manage-apps/f5-aad-integration.md)

- [Tutorial para implementar F5 BIG-IP Virtual Edition VM em Azure IaaS para acesso híbrido seguro](../manage-apps/f5-bigip-deployment-guide.md)

- [Tutorial para integração única de sign-on do Azure Ative Directory com F5 BIG-IP para VPN sem palavra-passe](../manage-apps/f5-aad-password-less-vpn.md)