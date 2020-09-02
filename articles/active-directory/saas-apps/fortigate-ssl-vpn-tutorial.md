---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com FortiGate SSL VPN Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o FortiGate SSL VPN.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecb53d661b1171f9c1b18d37d0bb35952645ba7e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299716"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutorial: Azure Ative Directory integração única (SSO) com FortiGate SSL VPN

Neste tutorial, você vai aprender a integrar FortiGate SSL VPN com Azure Ative Directory (Azure AD). Quando integrar a VPN FortiGate SSL com a AD Azure, pode:

* Controlo em Azure AD que tem acesso à VPN FortiGate SSL.
* Permitir que os seus utilizadores sejam automaticamente inscritos na VPN FortiGate SSL com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* FortiGate SSL VPN assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* FortiGate SSL VPN suporta **SSO** iniciado SP
* Uma vez configurado FortiGate SSL VPN, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-fortigate-ssl-vpn-from-the-gallery"></a>Adicionar FortiGate SSL VPN da galeria

Para configurar a integração do FortiGate SSL VPN em Azure AD, é necessário adicionar o FortiGate SSL VPN da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite FortiGate SSL VPN** na caixa de pesquisa.
1. Selecione **FortiGate SSL VPN** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configure e teste Azure AD SSO para FortiGate SSL VPN

Configure e teste Azure AD SSO com FortiGate SSL VPN usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no FortiGate SSL VPN.

Para configurar e testar o Azure AD SSO com a FortiGate SSL VPN, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure FortiGate SSL SSL SSO](#configure-fortigate-ssl-vpn-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **Create FortiGate SSL VPN test user** - para ter uma contraparte de B.Simon em FortiGate SSL VPN que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-single-sign-on)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **FortiGate SSL,** encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<FQDN>/remote/login`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<FQDN>/remote/saml/metadata`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://><FQDN/remote/saml/login`

    d. Na caixa de texto **URL logout,** digite um URL utilizando o seguinte padrão: `https://<FQDN>/remote/saml/logout`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real em URL, Identificador, URL de resposta e URL logout. Contacte [a equipa de suporte ao cliente da FortiGate SSL para](mailto:tac_amer@fortinet.com) obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação FortiGate SSL VPN espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação FortiGate SSL VPN espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome |  Atributo de origem|
    | ------------ | --------- |
    | nome de utilizador | user.userprincipalname |
    | group | utilizador.grupos |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração FortiGate SSL VPN,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao FortiGate SSL VPN.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **FortiGate SSL VPN**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-a-security-group-for-the-test-user"></a>Criar um grupo de segurança para o utilizador de teste

Nesta secção, irá criar um grupo de segurança no Azure Ative Directory para o utilizador de teste. Este grupo de segurança será utilizado pelo FortiGate para conceder acesso à rede de utilizadores através da VPN.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**e, em seguida, selecione **Grupos**.
1. Selecione **Novo grupo** na parte superior do ecrã.
1. Nas propriedades do **Grupo Novo,** siga estes passos:
   1. No campo **tipo Grupo,** selecione **Segurança**.
   1. No campo **Nome**, introduza `FortiGateAccess`.
   1. No campo **de descrição** do grupo, insira `Group for granting FortiGate VPN access` .
   1. Para as **funções AD Azure podem ser atribuídas às definições de grupo (Pré-visualização),** selecione **Nº**.
   1. No campo do **tipo de Adesão,** selecione **Atribuído**.
   1. Nos **Membros**, selecione **Não selecionados membros**.
   1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
   1. Selecione **Criar**.
1. Uma vez devolvido à lâmina **de grupos** no Diretório Ativo Azure, localize o grupo **fortiGate Access** e tome nota do **Id** do Objeto para utilização posterior.

## <a name="configure-fortigate-ssl-vpn-sso"></a>Configurar fortiGate SSL VPN SSO

### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Faça o upload do Certificado SAML base64 para o aparelho FortiGate

Depois de completar a configuração SAML da app FortiGate no seu inquilino, você descarregou o certificado SAML codificado Base64. Isto deve ser enviado para o aparelho FortiGate:

1. Inscreva-se no portal de gestão do seu aparelho FortiGate.
1. No menu da esquerda, clique em **Sistema**.
1. No **Sistema,** clique em **Certificados.**
1. Clique em Certificado Remoto **de Importação**  ->  **Remote Certificate**.
1. Navegue para o certificado descarregado a partir da implementação da App FortiGate no inquilino Azure, selecione-o e clique **em OK**

Após o envio do certificado, tome nota do seu nome no Certificado Remoto **de**  >  **Certificados**  >  **de**Sistema . Por padrão, será nomeado REMOTE_Cert_**N** onde **N** é um valor inteiro.

### <a name="perform-fortigate-command-line-configuration"></a>Executar configuração da linha de comando FortiGate

Os seguintes passos requerem que o URL de logout Azure seja configurado. Este URL contém um ponto de interrogação (?). São necessários passos especiais para submeter este personagem com sucesso. Os passos não podem ser executados a partir da Consola CLI FortiGate. Em vez disso, estabeleça uma sessão de SSH para a aplicação fortiGate usando uma ferramenta como a PuTTY. Se o seu aparelho FortiGate for uma máquina virtual Azure, pode executar os seguintes passos a partir da consola virtual da máquina virtual Azure.

Para realizar estes passos, necessitará dos valores registados anteriormente:

- ID de entidade
- URL de Resposta
- Logout URL
- Azure Login URL
- Identificador de Azure Ad
- Azure Logout URL
- Nome do certificado DE SAML base64 (REMOTE_Cert_N)

1. Estabeleça uma sessão de SSH à aplicação do FortiGate e inscreva-se com uma conta de Administrador FortiGate.
1. Executar os seguintes comandos:

   ```console
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > O **URL Azure Logout** contém um `?` personagem. Tem de introduzir uma sequência de chave especial para fornecer corretamente o URL à consola em série FortiGate. O URL geralmente é `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Para introduzir o URL Azure Logout na consola em série, insira `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Em seguida, selecione CTRL+V e cole o resto do URL para completar a linha: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

### <a name="configure-fortigate-for-group-matching"></a>Configure FortiGate para a correspondência de grupo

Nesta secção, irá configurar o FortiGate para reconhecer o ID do Objeto do Grupo de Segurança em que o utilizador de teste reside. Isto permitirá ao FortiGate tomar decisões de acesso com base nesta adesão ao grupo.

Para realizar estes passos, você precisará do Id de Objeto do grupo de segurança **FortiGateAccess** criado anteriormente

1. Estabeleça uma sessão de SSH na sua Aplicação FortiGate e inscreva-se com uma conta de Administrador FortiGate.
1. Executar os seguintes comandos:

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

### <a name="create-fortigate-vpn-portals-and-firewall-policy"></a>Criar portais VPN fortiGate e política de firewall

Nesta secção, configura os Portais VPN do FortiGate e a Política de Firewall que concedem acesso ao grupo de segurança, **o FortiGateAccess** criado acima.

Trabalhe com a [equipa de apoio fortiGate](mailto:tac_amer@fortinet.com) para adicionar os Portais VPN e a Política de Firewall à plataforma VPN FortiGate. Estes passos devem estar completos antes de utilizar uma única s placa.

## <a name="test-single-sign-on"></a>Teste único de inscrição 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo VPN VPN do FortiGate SSL no Painel de Acesso, deverá ser automaticamente inscrito no FortiGate SSL VPN para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

A Microsoft e o FortiGate recomendam que utilize o cliente Fortinet VPN, FortiClient para a melhor experiência final do utilizador final.

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente FortiGate SSL VPN com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
