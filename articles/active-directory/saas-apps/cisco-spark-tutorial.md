---
title: 'Tutorial: Azure Ative Directory Integração única (SSO) com a Cisco Webex | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592528"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Tutorial: Azure Ative Directory Single sign-on (SSO) integração com a Cisco Webex

Neste tutorial, você vai aprender a integrar Cisco Webex com Azure Ative Directory (Azure AD). Quando integrar a Cisco Webex com Azure AD, pode:

* Controle em Azure AD que tem acesso à Cisco Webex.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Cisco Webex com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Cisco Webex assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Cisco Webex suporta SSO iniciado **SP.**
* A Cisco Webex suporta [**o fornecimento automatizado de utilizadores.**](./cisco-webex-provisioning-tutorial.md)

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionar Cisco Webex da galeria

Para configurar a integração da Cisco Webex no Azure AD, é necessário adicionar a Cisco Webex da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Add da secção **de galeria,** **digite Cisco Webex** na caixa de pesquisa.
1. Selecione **Cisco Webex** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Configure e teste Azure AD SSO para Cisco Webex

Configure e teste Azure AD SSO com Cisco Webex usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Cisco Webex.

Para configurar e testar o Azure AD SSO com a Cisco Webex, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
2. **[Configure](#configure-cisco-webex)** a Cisco Webex para configurar as definições SSO no lado da aplicação.
    1. Crie o utilizador de **[teste Cisco Webex](#create-cisco-webex-test-user)** para ter uma contraparte de B.Simon na Cisco Webex que está ligada à representação AD AD do utilizador.
3. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **da Cisco Webex,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** faça o upload do ficheiro **de metadados do Fornecedor de Serviços** descarregado e configuure a aplicação realizando os seguintes passos:

    >[!Note]
    >Você receberá o ficheiro de metadados do Fornecedor de Serviços da secção **Configure Cisco Webex,** que é explicado mais tarde no tutorial. 

    a. Clique **em Carregar o ficheiro de metadados**.

    b. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

    c. Após a conclusão com sucesso do ficheiro de metadados do Fornecedor de Serviços, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção **de Configuração BÁSICA SAML:**

    No **Signo na** caixa de texto URL, cole o valor do URL de **resposta,** que é preenchido automaticamente por upload de ficheiros de metadados SP.

1. A aplicação Cisco Webex espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Cisco Webex espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
  
    | Name |  Atributo de origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  O valor do atributo de origem é por predefinição mapeado para o nome de utilizador. Isto pode ser alterado para user.mail ou user.onpremiseuserprincipalname ou qualquer outro valor de acordo com a definição no Webex.


1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Cisco Webex.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Cisco Webex**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-cisco-webex"></a>Configurar Cisco Webex

1. Inscreva-se na Cisco Webex com as suas credenciais de administrador.

1. Selecione **Configurações de Organização** e na secção **de Autenticação,** clique em **Modificar**.

    ![A screenshot mostra Definições de autenticação onde pode selecionar Modificar.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. **Selecione Integrar um fornecedor de identidade de terceiros. (Avançado)** e clique no **Next**.

    ![Screenshot mostra Integrar um fornecedor de identidade de terceiros.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Clique no **Download Metadata File** para descarregar o **ficheiro de metadados do Fornecedor de Serviços** e guardá-lo no seu computador, clique em **Seguinte**.

    ![A screenshot mostra o ficheiro de metadados do fornecedor de serviço.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Clique na opção **do navegador de ficheiros** para localizar e carregar o ficheiro de metadados Azure AD. Em seguida, **selecione Exigir certificado assinado por uma autoridade de certificado em Metadados (mais seguro)** e clique em **Seguinte**.

    ![A screenshot mostra a página de Metadados Import I d P.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Selecione **Test SSO Connection**, e quando um novo separador de navegador abrir, autente com Azure AD ao iniciar sessão.

1. Volte ao separador de navegador **Cisco Cloud Collaboration Management.** Se o teste tiver sido bem sucedido, selecione **Este teste foi bem sucedido. Ativar a opção Single Sign-On** e clicar **em Seguinte**.

1. Clique em **Guardar**.

> [!NOTE]
> Para saber mais sobre como configurar o Cisco Webex, consulte [esta](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) página.

### <a name="create-cisco-webex-test-user"></a>Criar utilizador de teste Cisco Webex

Nesta secção, um utilizador chamado B.Simon é criado na Cisco Webex.Esta aplicação suporta o fornecimento automático de utilizadores, o que permite o fornecimento automático e desprovisionamento com base nas suas regras de negócio.  A Microsoft recomenda a utilização automática de provisões sempre que possível. Veja como permitir o fornecimento automático para [Cisco Webex](./cisco-webex-provisioning-tutorial.md).

Se precisar de criar um utilizador manualmente, execute os seguintes passos:

1. Inscreva-se na Cisco Webex com as suas credenciais de administrador.

2. Clique **nos Utilizadores** e, em seguida, **gerencie os Utilizadores.**
   
    ![A screenshot mostra a página do Utilizadores onde pode gerir os utilizadores.](./media/cisco-spark-tutorial/user-1.png) 

3. Na janela **'Gerir utilizadores',** selecione **Manualmente Adicionar ou Modificar utilizadores**.

    ![A screenshot mostra a página do Utilizador onde pode gerir os utilizadores e selecionar Manualmente Adicionar ou Modificar utilizadores.](./media/cisco-spark-tutorial/user-2.png)

4. Selecione **Nomes e endereço de e-mail**. Em seguida, preencha a caixa de texto da seguinte forma:

    ![A screenshot mostra a caixa de diálogo dos Utilizadores de Mange onde pode adicionar ou modificar manualmente os utilizadores.](./media/cisco-spark-tutorial/user-3.png) 

    a. Na caixa de texto **Name Name,** escreva o primeiro nome do utilizador como **B**.

    b. Na caixa de texto **Do Último Nome,** escreva o apelido de utilizador como **Simon**.

    c. Na caixa de texto **do endereço de e-mail,** escreva o endereço de e-mail do utilizador como b.simon@contoso.com .

5. Clique no sinal de mais para adicionar B.Simon. Em seguida, clique **em Seguinte**.

6. Na janela **'Adicionar Serviços para Utilizadores',** clique em **Adicionar Utilizadores** e, em seguida, **Termine**.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de acesso webex da Cisco, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de login da Cisco Webex e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo Cisco Webex nas Minhas Apps, este será redirecionado para o URL de assinatura webex da Cisco. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Cisco Webex, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)