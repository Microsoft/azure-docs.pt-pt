---
title: 'Tutorial: Integração do Azure Ative Directory com a AirWatch | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: 4955062e6f0d0c231d09964c985df12284e3733c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653342"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrar AirWatch com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar AirWatch com Azure Ative Directory (Azure AD). Quando integrar o AirWatch com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao AirWatch.
* Ativar os seus utilizadores a serem automaticamente inscritos no AirWatch com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:
 
* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por airWatch (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 

* AirWatch suporta SSO iniciado **SP.**

## <a name="add-airwatch-from-the-gallery"></a>Adicione AirWatch da galeria

Para configurar a integração do AirWatch no Azure AD, é necessário adicionar AirWatch da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **AirWatch** na caixa de pesquisa.
1. Selecione **AirWatch** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Configure e teste Azure AD SSO para AirWatch

Configure e teste Azure AD SSO com AirWatch usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no AirWatch.

Para configurar e testar o Azure AD SSO com a AirWatch, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o AirWatch SSO](#configure-airwatch-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create AirWatch test user](#create-airwatch-test-user)** - para ter uma contraparte de B.Simon em AirWatch que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **AirWatch,** encontre a secção **'Gerir'** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com página SAML,** clique no ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    1. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Na caixa de texto **identifier (Entity ID),** digite o valor como: `AirWatch`

    > [!NOTE]
    > Este valor não é real. Atualize este valor com o URL de inscrição real. Contacte a [equipa de suporte do Cliente AirWatch](https://www.air-watch.com/company/contact-us/) para obter este valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação AirWatch espera as afirmações SAML num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

1. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** edite as alegações utilizando o **ícone Editar** ou adicione as alegações utilizando **adicionar uma nova alegação** para configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:

    | Name |  Atributo de origem|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos Source,** digite o valor de atributo mostrado para esta linha.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o Metadados XML e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configurar AirWatch,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao AirWatch.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AirWatch**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="configure-airwatch-sso"></a>Configure AirWatch SSO

1. Numa janela diferente do navegador web, inscreva-se no site da empresa AirWatch como administrador.

1. Na página de definições. Selecione **Configurações > Serviços de Diretório de Integração empresarial >**.

   ![Definições](./media/airwatch-tutorial/services.png "Definições")

1. Clique no **separador Utilizador,** na caixa de texto **Base DN,** digite o nome de domínio e, em seguida, clique em **Guardar**.

   ![Screenshot que realça a caixa de texto Base DN.](./media/airwatch-tutorial/user.png "User")

1. Clique no **separador 'Servidor'.**

   ![Servidor](./media/airwatch-tutorial/directory.png "Servidor")

1. Execute os seguintes passos na secção **LDAP:**

    ![Screenshot que mostra as alterações que precisa de fazer na secção LDAP.](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. Como **Tipo de Diretório**, selecione **Nenhum**.

    b. Selecione **use SAML para autenticação**.

1. Na secção **SAML 2.0,** para fazer o upload do certificado descarregado, clique em **Upload**.

    ![Carregar](./media/airwatch-tutorial/uploads.png "Carregar")

1. Na secção **Pedido,** execute os seguintes passos:

    ![Secção de pedido](./media/airwatch-tutorial/request.png "Pedir")  

    a. Como **Tipo de Ligação de Pedido**, selecione **POST**.

    b. No portal Azure, no sinal único de configuração na página de diálogo **AirWatch,** copie o valor URL de **login** e, em seguida, cole-o na caixa de texto **de URL do Fornecedor de Identidade.**

    c. Como **Formato NameID**, selecione **endereço de e-mail**.

    d. Como **Autenticação Pedido De Segurança**, selecione **Nenhum**.

    e. Clique em **Guardar**.

1. Clique novamente no **separador Utilizador.**

    ![Utilizador](./media/airwatch-tutorial/users.png "User")

1. Na secção **Atributo,** execute os seguintes passos:

    ![Atributo](./media/airwatch-tutorial/attributes.png "Atributo")

    a. Na caixa de texto do **identificador de objetos,** escreva `http://schemas.microsoft.com/identity/claims/objectidentifier` .

    b. Na caixa de texto do **nome de utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    c. Na caixa de texto **'Mostrar' 'Mostrar',** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    d. Na caixa de texto **name First,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` .

    e. Na caixa de texto **do último nome,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` .

    f. Na caixa de texto **por e-mail,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    exemplo, Clique em **Guardar**.

### <a name="create-airwatch-test-user"></a>Criar utilizador de teste AirWatch

Para permitir que os utilizadores Azure AD entrem no AirWatch, devem ser adustados ao AirWatch. No caso do AirWatch, o provisionamento é uma tarefa manual.

**Para configurar o provisionamento do utilizador, execute os seguintes passos:**

1. Inscreva-se no site da empresa **AirWatch** como administrador.

2. No painel de navegação do lado esquerdo, clique em **Contas** e, em seguida, clique em **Utilizadores**.
  
   ![Utilizadores](./media/airwatch-tutorial/accounts.png "Utilizadores")

3. No menu **Utilizadores,** clique em **'Ver lista'** e, em seguida, clique **em Adicionar > Adicionar Utilizador**.
  
   ![Screenshot que realça os botões Adicionar e Adicionar Utilizador.](./media/airwatch-tutorial/add.png "Adicionar Utilizador")

4. No diálogo **do utilizador Add / Editar,** execute os seguintes passos:

   ![Adicionar utilizador](./media/airwatch-tutorial/save.png "Adicionar Utilizador")

   a. Digite o **nome de utilizador**, **Password**, **Confirme a Palavra-passe,** **o Primeiro Nome,** o Último **Nome,** o Endereço de **E-mail** de uma conta de Diretório Ativo Azure válida que pretende apresentar nas caixas de texto relacionadas.

   b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador airWatch ou APIs fornecidas pela AirWatch para fornecer contas de utilizadores Azure AD.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de entrada de sinal de AirWatch, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição da AirWatch e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo AirWatch nas Minhas Apps, este será redirecionado para o URL de sign-on airWatch. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o AirWatch, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).