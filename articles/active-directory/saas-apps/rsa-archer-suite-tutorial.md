---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a RSA Archer Suite | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o RSA Archer Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: d96f1e0413dac8477e2fc40bf84e88318abf3f9d
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284991"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rsa-archer-suite"></a>Tutorial: Azure Ative Directory single sign-on (SSO) integração com ASA Archer Suite

Neste tutorial, você vai aprender a integrar a RSA Archer Suite com Azure Ative Directory (Azure AD). Quando integrar a RSA Archer Suite com Azure AD, pode:

* Controle em Azure AD que tem acesso à RSA Archer Suite.
* Permita que os seus utilizadores sejam automaticamente inscritos na RSA Archer Suite com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* RSA Archer Suite assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* A RSA Archer Suite suporta a **SP** iniciada SSO.
* A RSA Archer Suite suporta o fornecimento do utilizador **Just In Time.**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="add-rsa-archer-suite-from-the-gallery"></a>Adicione a Suíte RSA Archer da galeria

Para configurar a integração da RSA Archer Suite em Azure AD, você precisa adicionar AA Archer Suite da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add a partir da secção **da galeria,** **escreva A Suite Archer RSA** na caixa de pesquisa.
1. Selecione **RSA Archer Suite** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-rsa-archer-suite"></a>Configurar e testar Azure AD SSO para a RSA Archer Suite

Configure e teste Azure AD SSO com RSA Archer Suite usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na RSA Archer Suite.

Para configurar e testar o Azure AD SSO com a RSA Archer Suite, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure rSA Archer Suite SSO](#configure-rsa-archer-suite-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create RSA Archer Suite test user](#create-rsa-archer-suite-test-user)** - para ter uma contraparte de B.Simon em RSA Archer Suite que está ligada à representação AD AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **RSA Archer Suite,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<BASE_URL>/default.aspx?IDP=<REALM_NAME>`

    b. Na caixa de texto **identifier (Entity ID),** digite o valor: `RSAArcherSuite_TENANT_STRING`

    > [!NOTE]
    > O valor do sinal de URL não é real. Atualizar o valor com o sinal real no URL. Contacte [a equipa de apoio ao cliente da RSA Archer Suite](mailto:archersupport@rsa.com) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação RSA Archer Suite espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação RSA Archer Suite espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name |  Atributo de origem|
    | -------------- | --------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | PhoneNumber | user.telephonenumber |
    | City | user.city |
    | Zipcode | user.postalcode |
    | Estado | user.state |
    | Rua | user.streetaddress |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração RSA Archer Suite,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, permitindo o acesso à RSA Archer Suite.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **RSA Archer Suite**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-rsa-archer-suite-sso"></a>Configurar A Suíte RSA Archer SSO

1. Inscreva-se no website da RSA Archer Suite num navegador diferente como administrador.

1. Execute os seguintes passos na página seguinte.

    ![Configurar A Suíte RSA Archer SSO](./media/rsa-archer-suite-tutorial/configuration.png)

    a. Vá ao separador **'Sign-on' único** e selecione **o SAML** como um **modo de Sign-On único** a partir do dropdown.

    b. Selecione Deixe a caixa **de verificação manual de bypass.**

    c. Dar um nome válido na caixa de texto de identificação da **entidade de instância.**

    d. Cole o **Valor da impressão digital na** caixa de texto da impressão digital do **certificado.**

    e. Clique no botão **Select** e carre fique no upload do ficheiro **XML da Federação** descarregada a partir do portal Azure.

    f. **Guarde** as definições de Sign-On única. 

### <a name="create-rsa-archer-suite-test-user"></a>Criar utilizador de teste RSA Archer Suite

Nesta secção, um utilizador chamado B.Simon é criado na RSA Archer Suite. A RSA Archer Suite suporta o provisionamento do utilizador just-in-time, o que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir na RSA Archer Suite, uma nova é criada após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para rSA Archer Suite Sign-on URL onde pode iniciar o fluxo de login. 

2. Vá diretamente ao URL de inscrição da Suite RSA Archer e inicie o fluxo de login a partir daí.

3. Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo da Suíte Archer RSA no Painel de Acesso, deverá ser automaticamente inscrito na Suite RSA Archer para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos Seguintes

 Uma vez configurado RSA Archer Suite, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).