---
title: 'Tutorial: Integração do Diretório Ativo Azure com a TOPdesk - Public | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o TOPdesk - Público.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: b787102065f93588cd796027123d61072e9d5aea
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601020"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Diretório Ativo Azure com a TOPdesk - Público

Neste tutorial, você vai aprender a integrar o TOPdesk - Público com Azure Ative Directory (Azure AD). Quando integra o TOPdesk - Público com Azure AD, pode:

* Controlo em Azure AD que tem acesso ao TOPdesk - Público.
* Capacitar os seus utilizadores a serem automaticamente inscritos no TOPdesk - Público com as suas contas AD AZure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* TOPdesk - Assinatura ativada por um único sinal público (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* TOPdesk - Apoios públicos **SP** iniciou SSO.

## <a name="add-topdesk---public-from-the-gallery"></a>Adicionar TOPdesk - Público da galeria

Para configurar a integração do TOPdesk - Público em Azure AD, é necessário adicionar TOPdesk - Público da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **TOPdesk - Público** na caixa de pesquisa.
1. Selecione **TOPdesk - Público** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Configure e teste Azure AD SSO para TOPdesk - Público

Configure e teste Azure AD SSO com TOPdesk - Público usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado em TOPdesk - Público.

Para configurar e testar Azure AD SSO com TOPdesk - Público, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o TOPdesk - SSO Público](#configure-topdesk---public-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Criar TOPdesk - utilizador de teste público](#create-topdesk---public-test-user)** - para ter uma contrapartida de B.Simon em TOPdesk - Público que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **TOPdesk -** Integração de aplicações públicas, encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4.  Na secção **de Configuração Básica SAML,** se tiver **um ficheiro de metadados do Fornecedor de Serviços,** execute os seguintes passos:

    >[!NOTE]
    >Você receberá o **ficheiro de metadados do Fornecedor** de Serviços da secção **Configurar TOPdesk - Public Single Sign-On,** que é explicado mais tarde no tutorial.

    a. Clique **em Carregar o ficheiro de metadados**.
    
    ![Carregar ficheiro de metadados](common/upload-metadata.png)

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    ![escolher arquivo de metadados](common/browse-upload-metadata.png)

    c. Após o carregamento com sucesso do ficheiro de metadados, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção de Configuração BÁSICA SAML.

    d. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net`

    e. Na caixa de texto DO URL do **Identifier,** preencha o URL de metadados TOPdesk que pode obter a partir da configuração TOPdesk. Deve utilizar o seguinte padrão: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Se os valores de URL **de identificação** e **resposta** não forem preenchidos automaticamente, é necessário inseri-los manualmente. Para o Identificador, siga o padrão acima mencionado e obtenha o valor URL de resposta a partir da secção **Configure TOPdesk - Sign-On único público,** que é explicada mais tarde no tutorial. O valor **URL de inscrição** não é real, pelo que é necessário atualizar o valor com o URL Sign-On real. Contacte [a TOPdesk - Equipa de apoio ao Cliente Público](https://my.topdesk.com/) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

6. Na **secção set up TOPdesk - Secção pública,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Criar um utilizador de teste AZure AD 

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao TOPdesk - Público.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **TOPdesk - Public**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função &quot;Acesso Predefinido&quot; selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name=&quot;configure-topdesk---public-sso&quot;></a>Configure TOPdesk - SSO público

1. Inscreva-se no seu **SITE TOPdesk - Empresa Pública** como administrador.

2. No menu **TOPdesk,** clique em **Definições**.
   
    ![Definições](./media/topdesk-public-tutorial/menu.png &quot;Definições")

3. Clique em **Definições de Início** de Sessão .
   
    ![Definições de início de sessão](./media/topdesk-public-tutorial/login.png "Definições de início de sessão")

4. Expandir o menu **'Definições de início de sessão'** e, em seguida, clicar em **Geral**.
   
    ![Configurações Gerais](./media/topdesk-public-tutorial/general.png "Definições Gerais")

5. Na secção **Pública** da secção de configuração de **login DA SAML,** execute os seguintes passos:
   
    ![Definições Técnicas](./media/topdesk-public-tutorial/public.png "Definições Técnicas")
   
    a. Clique **em Baixar** para descarregar o ficheiro de metadados públicos e guarde-o localmente no seu computador.
   
    b. Abra o ficheiro de metadados descarregado e, em seguida, localize o nó **De Serviço de Afirmação.**

    ![Serviço De Afirmação](./media/topdesk-public-tutorial/service.png "Serviço De Afirmação")
   
    c. Copie o valor **AfirmaçãoConsumerService,** cole este valor na caixa de texto **URL de resposta** na secção **configuração BÁSICA SAML.**      
   
6. Para criar um ficheiro de certificado, execute os seguintes passos:
    
    ![Certificado](./media/topdesk-public-tutorial/certificate-file.png "Certificado")
    
    a. Abra o ficheiro de metadados descarregado do portal Azure.
    
    b. Expandir o nó **RoleDescriptor** que tem um **xsi:tipo** de **fed:ApplicationServiceType**.
    
    c. Copie o valor do nó **X509Certificado.**
    
    d. Guarde o valor **X509Certificado** copiado localmente no seu computador num ficheiro.

7. Na secção **Público,** clique **em Adicionar**.
    
    ![SAML Login](./media/topdesk-public-tutorial/add.png "SAML Login")

8. Na página de diálogo **do assistente de configuração SAML,** execute os seguintes passos:
    
    ![Assistente de Configuração SAML](./media/topdesk-public-tutorial/configuration.png "Assistente de Configuração SAML")
    
    a. Para fazer o upload do seu ficheiro de metadados descarregado a partir do portal Azure, no portal **Federation Metadas,** clique em **Procurar**.

    b. Para fazer o upload do seu arquivo de certificado, em **Certificado (RSA)**, clique em **Procurar**.

    c. Para fazer o upload do ficheiro de logotipo que obteve da equipa de suporte TOPdesk, no **ícone do logotipo,** clique em **Procurar**.

    d. Na caixa de texto do **nome do utilizador,** escreva `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

    e. Na caixa de texto **do nome do Visor,** escreva um nome para a sua configuração.

    f. Clique em **Guardar**.

### <a name="create-topdesk---public-test-user"></a>Criar TOPdesk - Utilizador de teste público

Para permitir que os utilizadores da Azure AD assinem na TOPdesk - Público, devem ser a provisionados na TOPdesk - Público. No caso da TOPdesk - Público, o provisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o provisionamento do utilizador, execute os seguintes passos:

1. Inscreva-se no seu **SITE TOPdesk - Empresa Pública** como administrador.

2. No menu em cima, clique em **TOPdesk \> New Support Files \> \> Person**.
   
    ![Pessoa](./media/topdesk-public-tutorial/files.png "Pessoa")

3. No diálogo New Person, execute os seguintes passos:
   
    ![Nova Pessoa](./media/topdesk-public-tutorial/new.png "Nova Pessoa")
   
    a. Clique no separador Geral.

    b. Na caixa de texto **sobrenome,** escreva sobrenome do utilizador como Simon
 
    c. Selecione um **Site** para a conta.
 
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador público ou APIs fornecidas pela TOPdesk - Públicas para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para TOPdesk - URL de inscrição pública onde pode iniciar o fluxo de login. 

* Vá diretamente ao TOPdesk - URL de inscrição pública e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no TOPdesk - Azulejo público nas Minhas Apps, este será redirecionado para TOPdesk - URL de inscrição pública. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado TOPdesk - Público pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
