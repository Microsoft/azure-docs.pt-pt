---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com Workday Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4867a1735f091085f64bbe7010969bd086f820a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88527178"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Tutorial: Azure Ative Directory integração única (SSO) com Workday

Neste tutorial, você vai aprender a integrar o Workday com o Azure Ative Directory (Azure AD). Quando integrar o Workday com a Azure AD, pode:

* Controlo em Azure AD que tem acesso ao Workday.
* Ative os seus utilizadores a serem automaticamente inscritos no Workday com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por um único sign-on (SSO) do dia de trabalho.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Workday suporta **SP** iniciado SSO.

* Uma vez configurado o Workday, pode impor o Controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O Controlo de Sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Adicionar dia de trabalho da galeria

Para configurar a integração do Workday em Azure AD, precisa adicionar Workday da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva **Workday** na caixa de pesquisa.
1. Selecione **Workday** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Configurar e testar Azure AD único sign-on para workday

Configure e teste Azure AD SSO com Workday usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Workday.

Para configurar e testar a Azure AD SSO com workday, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
2. **[Configure o Workday](#configure-workday)** para configurar as definições SSO no lado da aplicação.
    1. Crie o utilizador de **[teste Workday](#create-workday-test-user)** para ter uma contraparte de B.Simon em Workday que esteja ligada à representação AD AZure do utilizador.
3. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Workday,** encontre a secção **Gerir** e selecione **'Único sinal de s-on'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `http://www.workday.com`

    c. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL e URL de resposta de assinatura real. A url de resposta deve ter um subdomínio, por exemplo: www. wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Usar algo como `http://www.myworkday.com` obras, mas `http://myworkday.com` não funciona. Contacte [a equipa de suporte do Cliente workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

6. A sua aplicação Workday espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação Workday espera que **o identificador** de nomes seja mapeado com **user.mail**, **UPN,** etc., pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Aqui mapeamos o ID de nome com UPN (user.userprincipalname) como padrão. Tem de mapear o ID do nome com o ID do utilizador real na sua conta workday (o seu email, UPN, etc.) para um trabalho bem sucedido de SSO.

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Para modificar as opções **de Assinatura** de acordo com o seu requisito, clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Selecione **Sign SAML resposta e afirmação** para **a opção de assinatura**.

    b. Clicar em **Guardar**

1. Na secção Configurar o **Workday,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Workday.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Workday**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-workday"></a>Configure dia de trabalho

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Workday como administrador.

2. Na **caixa de pesquisa** procure com o nome **EditAr Configuração do Inquilino – Segurança** no lado superior esquerdo da página inicial.

    ![Editar Segurança do Inquilino](./media/workday-tutorial/IC782925.png "Editar Segurança do Inquilino")

3. Na secção **URLs de reorientação,** execute os seguintes passos:

    ![URLs de reorientação](./media/workday-tutorial/IC7829581.png "URLs de reorientação")

    a. Clique **em Adicionar Linha**.

    b. No **URL de redirecionamento de login**, **URL de redirecionamento de tempo** e caixa de texto URL de **redirecionamento móvel,** cole o **URL de login** que copiou a partir da secção **Configurar** o Dia de Trabalho do portal Azure.

    c. Na caixa de texto **url de redirecionamento de logout,** cole o **URL logout** que copiou a partir da secção **Configurar** o Dia de Trabalho do portal Azure.

    d. Na caixa de texto **Usada para Ambientes,** selecione o nome do ambiente.  

   > [!NOTE]
   > O valor do atributo Ambiente está ligado ao valor do URL do arrendatário:  
   > -Se o nome de domínio do URL do inquilino do Workday começar com impl, por exemplo: * https://www.myworkday.com/ "inquilino"/login-saml2.htmld),* o atributo **Ambiente** deve ser definido como Implementação.  
   > -Se o nome de domínio começar com outra coisa, tem de contactar a equipa de suporte do [Cliente Workday](https://www.workday.com/en-us/partners-services/services/support.html) para obter o valor **ambiente** correspondente.

4. Na secção **configuração SAML,** execute os seguintes passos:

    ![Configuração SAML](./media/workday-tutorial/IC782926.png "Configuração SAML")

    a.  Selecione **Ativar a autenticação SAML**.

    b.  Clique **em Adicionar Linha**.

5. Na secção **Saml Identity Providers,** execute os seguintes passos:

    ![Fornecedores de Identidade SAML](./media/workday-tutorial/IC7829271.png "Fornecedores de Identidade SAML")

    a. Na caixa de texto **Name Provider Identity,** escreva um nome de fornecedor (por exemplo: *SPInitiatedSSO).*

    b. No portal Azure, na secção Configurar o **Workday,** copie o valor do **identificador Azure AD** e, em seguida, cole-o na caixa de texto do **Emitente.**

    ![Fornecedores de Identidade SAML](./media/workday-tutorial/IC7829272.png "Fornecedores de Identidade SAML")

    c. No portal Azure, na secção Configurar o **Dia do Trabalho,** copie o valor **URL logout** e, em seguida, cole-o na caixa de texto **URL de resposta logout.**

    d. No portal Azure, na secção Configurar o **Workday,** copie o valor URL do **Login** e, em seguida, cole-o na caixa de texto **URL do Serviço IdP SSO.**

    e. Na caixa de texto **Usada para Ambientes,** selecione o nome do ambiente.

    f. Clique no **Certificado de Chave Pública do Fornecedor de Identidade**e, em seguida, clique em **Criar**.

    ![Criar](./media/workday-tutorial/IC782928.png "Criar")

    exemplo, Clique **em Criar a chave pública x509.**

    ![Criar](./media/workday-tutorial/IC782929.png "Criar")

6. Na secção **Ver x509 Chave Pública,** execute os seguintes passos:

    ![Ver chave pública x509](./media/workday-tutorial/IC782930.png "Ver chave pública x509")

    a. Na caixa de texto **Name,** digite um nome para o seu certificado (por exemplo: *PPE \_ SP*).

    b. Na caixa de texto **Válido,** digite o válido do valor do atributo do seu certificado.

    c.  Na caixa de texto **Válido Para** que o valor do seu certificado seja válido.

    > [!NOTE]
    > Pode obter o válido a partir da data e o válido até à data do certificado descarregado clicando duas vezes.  As datas estão listadas no separador **Detalhes.**
    >
    >

    d.  Abra o certificado codificado base-64 no bloco de notas e, em seguida, copie o conteúdo do mesmo.

    e.  Na caixa de texto **do Certificado,** cole o conteúdo da sua área de transferência.

    f.  Clique em **OK**.

7. Efetue os seguintes passos:

    ![Configuração SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Configuração SSO")

    a.  Na caixa de texto **iD do fornecedor de serviços,** escreva **http://www.workday.com** .

    b. **Selecione Não Esvazie o Pedido de Autenticação Iniciado pelo SP**.

    c. Como **Método de Assinatura pedido de autenticação,** selecione **SHA256**.

    ![Método de assinatura de pedido de autenticação](./media/workday-tutorial/WorkdaySSOConfiguration.png "Método de assinatura de pedido de autenticação")

    d. Clique em **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Certifique-se de que configura corretamente uma única inscrição. Caso ative um único sinal de entrada com configuração incorreta, poderá não conseguir introduzir a aplicação com as suas credenciais e ficar bloqueado. Nesta situação, o Workday fornece um url de login de backup onde os utilizadores podem iniciar sessão usando o seu nome de utilizador normal e palavra-passe no seguinte formato:[O URL do dia de trabalho]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Criar utilizador de teste workday

Nesta secção, cria-se um utilizador chamado B.Simon in Workday. Trabalhe com [a equipa de suporte do Cliente Workday](https://www.workday.com/en-us/partners-services/services/support.html) para adicionar os utilizadores na plataforma Workday. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo workday no Painel de Acesso, deverá ser automaticamente inscrito no Dia de Trabalho para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o Dia de Trabalho com a Azure AD](https://aad.portal.azure.com)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Como proteger o Dia de Trabalho com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/protect-workday)