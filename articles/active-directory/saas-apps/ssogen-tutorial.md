---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SSOGEN - Azure AD SSO Gateway para a Oracle E-Business Suite - EBS, PeopleSoft e JDE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9eeafaf0f5fbfaff9394ced0a0623f2fb462ed4d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647001"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Tutorial: Azure Ative Directy integração única de sign-on (SSO) com SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE

Neste tutorial, você aprenderá a integrar sSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft, e JDE com Azure Ative Directory (Azure AD). Quando integrar o SSOGEN - Azure AD SSO Gateway para a Oracle E-Business Suite - EBS, PeopleSoft e JDE com Azure AD, pode:

* Control em Azure AD que tem acesso a SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE.
* Permita que os seus utilizadores sejam automaticamente inscritos na SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE suporta sSO iniciado **SP e IDP.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-from-the-gallery"></a>Adicionar SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE da galeria

Para configurar a integração da SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE em AD Azure, você precisa adicionar SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE da galeria à sua lista de apps geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** tipo **SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE** na caixa de pesquisa.
1. Selecione **SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde"></a>Configure e teste Azure AD SSO para SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE

Configure e teste Azure AD SSO com SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE.

Para configurar e testar Azure AD SSO com SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE SSO](#configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e utilizador de testes JDE](#create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user)** - para ter uma contraparte de B.Simon em SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE que está ligada à representação AZURE AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração **de aplicações SSOGEN - Azure AD SSO para Oracle E-Business Suite - EBS, PeopleSoft e JDE,** encontre a secção **Gestão** e selecione **um único sinal de s-on.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<customer_name>.ssogen.com/ssogen/login?client_name=<customer_name>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de resposta real e Sign-On URL. Contacte [a SSOGEN - Azure AD SSO Gateway para a Oracle E-Business Suite - EBS, PeopleSoft e JDE Client support team](mailto:support@ssogen.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A sua aplicação SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft e JDE application espera que **o identificador** de nomes seja mapeado com **o utilizador.onpremisessamaccountname**, por isso é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique no botão de cópia para copiar o Url de **metadados da Federação de Aplicações** e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/copy-metadataurl.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-sso"></a>Configure SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE SSO

Para configurar um único sign-on em **SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE** lado, encontre abaixo a documentação de registo SSO específica para aplicações:

* Oráculo EBS - Azure Ad SSO Integração: [https://www.ssogen.com/oracle-ebs-sso-ldap/](https://www.ssogen.com/oracle-ebs-sso-ldap/)
* PeopleSoft - Azure AD SSO Integration: [https://www.ssogen.com/peoplesoft-sso/](https://www.ssogen.com/peoplesoft-sso/)
* JD Edwards - Azure Ad SSO Integração: [https://www.ssogen.com/oracle-jde-sso/](https://www.ssogen.com/oracle-jde-sso/)
* Apache - Azure Ad SSO Integração: [https://www.ssogen.com/apache-sso-authentication/](https://www.ssogen.com/apache-sso-authentication/)

### <a name="create-ssogen---azure-ad-sso-gateway-for-oracle-e-business-suite---ebs-peoplesoft-and-jde-test-user"></a>Criar SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e utilizador de teste JDE

A Azure AD envia o Identificador único do utilizador (ID de nome) para a aplicação do utilizador, depois de a autenticação ter sido bem sucedida.  Certifique-se de que o Identificador único do utilizador (ID nome) corresponde ao registo do utilizador na sua aplicação, FND_USER. USER_NAME na Oracle EBS, por exemplo.

Por favor contacte [info@ssogen.com](mailto:info@ssogen.com) e [support@ssogen.com](mailto:support@ssogen.com) para qualquer apoio.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE Assinar no URL onde pode iniciar o fluxo de login.  

* Vá ao SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE Sign-on URL diretamente e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e JDE para os quais configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Ao clicar no SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS, PeopleSoft e TL JDE nas Minhas Apps, se configurado no modo SP, você seria redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, você deve ser automaticamente inscrito no SSOGEN - Azure AD SSO Gateway para Oracle E-Business Suite - EBS , PeopleSoft, e JDE para os quais você criou o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar o SSOGEN - Azure AD SSO Gateway para a Oracle E-Business Suite - EBS, PeopleSoft e JDE, pode impor o controlo de sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).