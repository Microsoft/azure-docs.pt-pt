---
title: 'Tutorial: Azure Ative Directory integração única (SSO) com linkedIn learning | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LinkedIn Learning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e5c6bf41e1a3bf92c9141c0d3b54dd58ead2bf3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727304"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Tutorial: Azure Ative Directory integração única (SSO) com o LinkedIn Learning

Neste tutorial, você vai aprender a integrar LinkedIn Learning com Azure Ative Directory (Azure AD). Quando integra o LinkedIn Learning com AZure AD, pode:

* Control em Azure AD que tem acesso ao LinkedIn Learning.
* Capacitar os seus utilizadores a serem automaticamente inscritos no LinkedIn Learning com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pelo LinkedIn Learning (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* LinkedIn Learning apoia **SP e IDP** iniciado SSO
* LinkedIn Learning suporta **o fornecimento de utilizadores just in time**


## <a name="adding-linkedin-learning-from-the-gallery"></a>Adicionar LinkedIn Learning da galeria

Para configurar a integração do LinkedIn Learning em Azure AD, precisa adicionar o LinkedIn Learning da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite LinkedIn Learning** na caixa de pesquisa.
1. Selecione **LinkedIn Learning** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Configure e teste Azure AD SSO para a aprendizagem do LinkedIn

Configure e teste Azure AD SSO com LinkedIn Learning usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no LinkedIn Learning.

Para configurar e testar o Azure AD SSO com o LinkedIn Learning, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o LinkedIn Learning SSO](#configure-linkedin-learning-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create LinkedIn Learning test user](#create-linkedin-learning-test-user)** - para ter uma contrapartida de B.Simon no LinkedIn Learning que está ligada à representação AD Ad do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **LinkedIn Learning,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

     a. Na caixa de texto **identifier,** insira o **ID** da Entidade copiado do Portal LinkedIn. 

    b. Na caixa de texto **URL de resposta,** introduza o url do **Serviço de Apoio ao Consumidor de Afirmação (ACS)** copiado do Portal LinkedIn.

    c. Se desejar configurar a aplicação no modo **INICIADOR SP,** clique em Definir a opção **URLs adicionais** na secção **de Configuração BASIC SAML,**  onde irá especificar o URL de inscrição. Para criar o url de login copie o Url do **Serviço de Apoio ao Consumidor de Afirmação (ACS)** e substitua /saml/ por /login/. Uma vez feito, o URL de inscrição deve ter o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn Learning Domain e URLs informações únicas de súmis](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Estes valores não são de valor real. Irá atualizar estes valores com o ATUAL URL de Identificação e Resposta, que é explicado mais tarde na secção **Configure LinkedIn Learning SSO** do tutorial.

1. A aplicação LinkedIn Learning espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação LinkedIn Learning espera que **o identificador** de nomes seja mapeado com **o user.mail,** pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **De Aprendizagem do LinkedIn,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao LinkedIn Learning.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **LinkedIn Learning**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-linkedin-learning-sso"></a>Configurar linkedIn learning SSO

1. Numa janela diferente do navegador web, inscreva-se no seu inquilino LinkedIn Learning como administrador.

2. No **Centro de Contas**, clique em **Definições** **Globais**. Além disso, **selecione Learning - Predefinição** da lista de abandono.

    ![A screenshot mostra as Definições Globais onde pode selecionar Predefinido.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Clique **em OR Clique aqui para carregar e copiar campos individuais a partir do formulário** e copiar o url do **Id** da Entidade e **do Serviço de Apoio ao Consumidor de Afirmação (ACS)** e colá-lo na secção **de Configuração Básica SAML** no portal Azure.

    ![A screenshot mostra single Sign-On onde pode introduzir os valores descritos.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Aceda à secção **de Definições de Administração LinkedIn.** Faça o upload do ficheiro XML que descarregou a partir do portal Azure clicando na opção **de ficheiro Upload XML.**

    ![O Screenshot mostra configurar as definições do fornecedor de serviços LinkedIn S S O onde pode carregar um ficheiro X M L.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Clique **em** para ativar SSO. Alterações de estado SSO **de Não Ligado a** **Conectado**

    ![O Screenshot mostra single Sign-On onde pode ativar os utilizadores Autenticados com S S O.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Criar utilizador de teste linkedIn Learning

A Aplicação de Aprendizagem LinkedIn suporta o fornecimento do utilizador a tempo e após a autenticação os utilizadores são criados automaticamente na aplicação. Na página de definições de administração no portal LinkedIn Learning, ligue o interruptor Atribuir automaticamente licenças a **ativas Apenas** no provisionamento de tempo, o que também irá atribuir uma licença ao utilizador.

   ![Criar um utilizador de teste AZure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Sinal de Aprendizagem linkedIn no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de início de acesso ao LinkedIn Learning e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no LinkedIn Learning para o qual configura o SSO 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo linkedIn Learning nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no LinkedIn Learning para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o LinkedIn Learning, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).