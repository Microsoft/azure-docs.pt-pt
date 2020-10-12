---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com FortiGate SSL VPN Microsoft Docs'
description: Aprenda os passos que precisa de executar para integrar o FortiGate SSL VPN com o Azure Ative Directory (Azure AD).
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
ms.openlocfilehash: 187903bfbf75ada45b9a539acd1157dfe730747a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>Tutorial: Azure Ative Directory integração única (SSO) com FortiGate SSL VPN

Neste tutorial, você vai aprender a integrar FortiGate SSL VPN com Azure Ative Directory (Azure AD). Quando integrar a VPN FortiGate SSL com a AD Azure, pode:

* Use a Azure AD para controlar quem pode aceder ao FortiGate SSL VPN.
* Permita que os seus utilizadores sejam automaticamente inscritos na VPN FortiGate SSL com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, veja [o que é o acesso à aplicação e um único acesso com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura VPN FortiGate SSL com um único sinal de sso ativado.

## <a name="tutorial-description"></a>Descrição tutorial

Neste tutorial, você vai configurar e testar Azure AD SSO em um ambiente de teste.

A FortiGate SSL VPN suporta sSO iniciado pelo SP.

Depois de configurar o FortiGate SSL VPN, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>Adicione FortiGate SSL VPN da galeria

Para configurar a integração da VPN FortiGate SSL em Azure AD, é necessário adicionar a VPN FortiGate SSL da galeria à sua lista de aplicações geridas para o SaaS:

1. Inscreva-se no [portal Azure](https://portal.azure.com) com uma conta de trabalho ou escola ou com uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Active Directory**.
1. Vá às **aplicações da Enterprise** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **insira a VPN FortiGate SSL** na caixa de pesquisa.
1. Selecione **FortiGate SSL VPN** no painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>Configure e teste Azure AD SSO para FortiGate SSL VPN

Irá configurar e testar O SSO Azure AD com FortiGate SSL VPN usando um utilizador de teste chamado B.Simon. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador correspondente no FortiGate SSL VPN.

Para configurar e testar O Azure AD SSO com FortiGate SSL VPN, você completará estes passos de alto nível:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para ativar a funcionalidade para os seus utilizadores.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com um único sinal de sessão.
    1. **[Conceder acesso ao utilizador de teste](#grant-access-to-the-test-user)** para ativar um único sinal de Azure AD para esse utilizador.
1. **[Configure o FortiGate SSL SSL SSO](#configure-fortigate-ssl-vpn-sso)** no lado da aplicação.
    1. **Crie um utilizador de teste VPN FortiGate SSL** como contrapartida da representação AZure AD do utilizador.
1. **[Teste SSO](#test-single-sign-on)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **FortiGate SSL VPN,** na secção **Gerir,** selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na **configuração de Sign-On única com** a página SAML, selecione o botão de lápis para **configuração SAML Básica** para editar as definições:

   ![Screenshot que mostra o botão de lápis para editar a configuração básica do SAML.](common/edit-urls.png)

1. Na **configuração single Sign-On com página SAML,** insira os seguintes valores:

    a. Na placa na caixa **URL,** introduza um URL no padrão `https://<FQDN>/remote/login` .

    b. Na caixa **identifier,** introduza um URL no padrão `https://<FQDN>/remote/saml/metadata` .

    c. Na caixa **URL de resposta,** introduza um URL no padrão `https://<FQDN>/remote/saml/login` .

    d. Na caixa **URL logout,** introduza um URL no padrão `https://<FQDN>/remote/saml/logout` .

    > [!NOTE]
    > Estes valores são apenas padrões. Tem de utilizar o sinal real **em URL,** **Identificador,** **URL de resposta**e URL de **logo.** Contacte a [equipa de apoio ao cliente da FortiGate SSL para](mailto:tac_amer@fortinet.com) obter os valores reais. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação FortiGate SSL VPN espera afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot que mostra os atributos predefinidos.](common/default-attributes.png)

1. As duas reclamações adicionais exigidas pela FortiGate SSL VPN são apresentadas no quadro seguinte. Os nomes destas reclamações devem corresponder aos nomes utilizados na secção de configuração da **linha de comando Perform FortiGate** deste tutorial. 

   | Nome |  Atributo de origem|
   | ------------ | --------- |
   | nome de utilizador | user.userprincipalname |
   | group | utilizador.grupos |
   
   Para criar estas alegações adicionais:
   
   1. Ao lado **dos atributos do utilizador & Reclamações**, selecione **Editar**.
   1. **Selecione Adicionar nova reclamação**.
   1. Para **nome**, insira **o nome de utilizador**.
   1. Para **o atributo Fonte**, selecione **user.userprincipalname**.
   1. Selecione **Guardar**.
   1. **Selecione Adicionar uma reivindicação de grupo**.
   1. Selecione **Todos os grupos**.
   1. Seect o Personalizar o nome da caixa **de verificação de reclamação do grupo.**
   1. Para **nome,** insira **o grupo**.
   1. Selecione **Guardar**.   

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o link **de descarregamento** ao lado **do Certificado (Base64)** para descarregar o certificado e guardá-lo no seu computador:

    ![Screenshot que mostra o link de descarregamento do certificado.](common/certificatebase64.png)

1. Na secção **Configuração FortiGate SSL VPN,** copie os URL ou URLs apropriados, com base nos seus requisitos:

    ![Screenshot que mostra os URLs de configuração.](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste chamado B.Simon no portal Azure.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** complete estes passos:
   1. Na caixa **Nome,** insira **B.Simon**.  
   1. Na caixa **do nome do utilizador,** \<username> @ \<companydomain> introduza \<extension> . . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **Mostrar a palavra-passe**e, em seguida, anotar o valor que é apresentado na caixa **palavra-passe.**
   1. Selecione **Criar**.

#### <a name="grant-access-to-the-test-user"></a>Conceder acesso ao utilizador de teste

Nesta secção, você permitirá que B.Simon utilize o Azure single sign-on, concedendo a esse utilizador acesso a FortiGate SSL VPN.

1. No portal Azure, selecione **aplicações Enterprise**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **FortiGate SSL VPN**.
1. Na página geral da aplicação, na secção **Gerir,** selecione **Utilizadores e grupos**:

   ![Screenshot que mostra a opção Utilizadores e grupos.](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** na caixa de diálogo **de atribuição de adicionar:**

    ![Screenshot que mostra o botão de utilizador Adicionar.](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** na lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Clique no botão **Selecione** na parte inferior do ecrã.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

#### <a name="create-a-security-group-for-the-test-user"></a>Criar um grupo de segurança para o utilizador de teste

Nesta secção, irá criar um grupo de segurança no Azure Ative Directory para o utilizador de teste. O FortiGate utilizará este grupo de segurança para conceder acesso à rede de utilizadores através da VPN.

1. No painel esquerdo do portal Azure, selecione **Azure Ative Directory**. Em seguida, selecione **Grupos**.
1. Selecione **Novo grupo** na parte superior do ecrã.
1. Nas propriedades do **Novo Grupo,** complete estes passos:
   1. Na lista **de tipos do Grupo,** selecione **Security**.
   1. Na caixa **de nomes do grupo,** **insira FortiGateAccess**.
   1. Na caixa **de descrição** do Grupo, insira **o Grupo para a concessão de acesso VPN fortiGate**.
   1. Para as **funções AD Azure podem ser atribuídas às definições de grupo (Pré-visualização),** selecione **Nº**.
   1. Na caixa **de tipo de adesão,** selecione **Atribuído**.
   1. Nos **Membros**, selecione **Não selecionados membros**.
   1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista **de Utilizadores** e, em seguida, clique no botão **Select** na parte inferior do ecrã.
   1. Selecione **Criar**.
1. Depois de voltar à secção **Grupos** no Diretório Ativo Azure, encontre o grupo **de acesso fortiGate** e note o **Object Id**. Vai precisar mais tarde.

### <a name="configure-fortigate-ssl-vpn-sso"></a>Configurar fortiGate SSL VPN SSO

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Faça o upload do Certificado SAML base64 para o aparelho FortiGate

Depois de ter concluído a configuração SAML da app FortiGate no seu inquilino, descarregou o certificado SAML codificado com Base64. Tem de enviar este certificado para o aparelho FortiGate:

1. Inscreva-se no portal de gestão do seu aparelho FortiGate.
1. No painel esquerdo, selecione **System**.
1. No **Sistema**, selecione **Certificados.**
1. Selecione Certificado Remoto **de Importação**  >  **Remote Certificate**.
1. Navegue pelo certificado descarregado a partir da implementação da aplicação FortiGate no inquilino Azure, selecione-o e, em seguida, selecione **OK**.

Após o envio do certificado, tome nota do seu nome no Certificado Remoto **de**  >  **Certificados**  >  **de**Sistema . Por padrão, será nomeado REMOTE_Cert_*N,* onde *N* é um valor inteiro.

#### <a name="complete-fortigate-command-line-configuration"></a>Configuração completa da linha de comando FortiGate

Os seguintes passos requerem que configuure o URL Azure Logout. Este URL contém um personagem de ponto de interrogação (?). Você precisa tomar medidas específicas para submeter este personagem com sucesso. Não é possível completar estes passos a partir da Consola CLI FortiGate. Em vez disso, estabeleça uma sessão de SSH para o aparelho FortiGate utilizando uma ferramenta como a PuTTY. Se o seu aparelho FortiGate for uma máquina virtual Azure, pode completar os seguintes passos da consola em série para máquinas virtuais Azure.

Para completar estes passos, precisará dos valores que gravou anteriormente:

- ID de entidade
- URL de Resposta
- Logout URL
- Azure Login URL
- Identificador de Azure Ad
- Azure Logout URL
- Nome do certificado DE SAML base64 (REMOTE_Cert_*N)*

1. Estabeleça uma sessão SSH no seu aparelho FortiGate e inscreva-se com uma conta de Administrador FortiGate.
1. Execute estes comandos:

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
   > O URL Azure Logout contém um `?` personagem. Tem de introduzir uma sequência de chave especial para fornecer corretamente este URL à consola em série FortiGate. O URL é geralmente `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .
   >
   > Para introduzir o URL Azure Logout na consola em série, insira `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation` .
   > 
   > Em seguida, selecione CTRL+V e cole o resto do URL para completar a linha: `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0` .

#### <a name="configure-fortigate-for-group-matching"></a>Configure FortiGate para a correspondência de grupo

Nesta secção, irá configurar o FortiGate para reconhecer o ID do Objeto do grupo de segurança que inclui o utilizador de teste. Esta configuração permitirá ao FortiGate tomar decisões de acesso com base na adesão ao grupo.

Para completar estes passos, você precisará do Id de Objeto do grupo de segurança FortiGateAccess que criou anteriormente neste tutorial.

1. Estabeleça uma sessão SSH no seu aparelho FortiGate e inscreva-se com uma conta de Administrador FortiGate.
1. Execute estes comandos:

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

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>Criar uma política de portais VPN VPN fortiGate e política de firewall

Nesta secção, irá configurar um FortiGate VPN Portals and Firewall Policy que dá acesso ao grupo de segurança FortiGateAccess que criou anteriormente neste tutorial.

Trabalhe com a [equipa de apoio fortiGate](mailto:tac_amer@fortinet.com) para adicionar os Portais VPN e a Política de Firewall à plataforma VPN FortiGate. Tem de completar este passo antes de utilizar uma única s placa.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, irá testar a sua configuração de inscrição única Azure AD utilizando o Painel de Acesso.

Quando selecionar o azulejo VPN VPN do FortiGate SSL no Painel de Acesso, deverá ser automaticamente inscrito no FortiGate SSL VPN para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [o Painel de Introdução ao Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

A Microsoft e o FortiGate recomendam que utilize o cliente Fortinet VPN, FortiClient, para a melhor experiência do utilizador final.

## <a name="additional-resources"></a>Recursos adicionais

- [Tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente FortiGate SSL VPN com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
