---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com linkedIn learning [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15509866980cdf85f54fc03cb77eed36f83c982f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983372"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Tutorial: Azure Ative Diretório integração de inscrição única (SSO) com linkedIn learning

Neste tutorial, aprenderá a integrar o LinkedIn Learning com o Azure Ative Directory (Azure AD). Quando integrar o LinkedIn Learning com o Azure AD, pode:

* Controlo em Azure AD que tem acesso ao LinkedIn Learning.
* Ative que os seus utilizadores sejam automaticamente inscritos no LinkedIn Learning com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* LinkedIn Learning single sign-on (SSO) enabled subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* LinkedIn Learning suporta **SP e IDP** iniciadoS SSO
* LinkedIn Learning suporta o fornecimento de utilizadores **justo no tempo**
* Assim que configurar o LinkedIn Learning, pode impor o controlo de Sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>Adicionar LinkedIn Learning a partir da galeria

Para configurar a integração do LinkedIn Learning em Azure AD, precisa de adicionar linkedIn Learning da galeria à sua lista de aplicações geridas do SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, digite **LinkedIn Learning** na caixa de pesquisa.
1. Selecione **LinkedIn Learning** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Configure e teste Azure AD único sign-on para LinkedIn Learning

Configure e teste Azure AD SSO com LinkedIn Learning utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no LinkedIn Learning.

Para configurar e testar o Azure AD SSO com o LinkedIn Learning, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
    1. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure linkedIn Learning SSO](#configure-linkedin-learning-sso)** - para configurar as definições de inscrição únicas no lado da aplicação.
    1. **[Create LinkedIn Learning test user](#create-linkedin-learning-test-user)** - para ter uma contrapartida de B.Simon no LinkedIn Learning que está ligada à representação do utilizador da AD Azure.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **LinkedIn Learning,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set single sign-on com** a página SAML, clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

     a. Na caixa de texto **Identificador,** introduza o ID da **Entidade** copiado do Portal LinkedIn. 

    b. Na caixa de texto **URL resposta,** introduza o Url do Serviço de **Consumidores de Afirmação (ACS)** copiado do Portal LinkedIn.

    c. Se desejar configurar a aplicação no modo **Iniciado SP,** clique em definir a opção **URLs adicionais** na secção **de Configuração SAML Básica,** onde irá especificar o URL de início de sessão. Para criar o seu Url de login, copie o Url do **Serviço de Consumo de Afirmação (ACS)** e substitua/saml/ por /login/. Uma vez feito, o URL de inscrição deve ter o seguinte padrão:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn Learning Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Estes valores não são de valor real. Atualizará estes valores com o URL de Identificação e Resposta real, o que é explicado mais tarde na secção **Configure LinkedIn Learning SSO** do tutorial.

1. A aplicação LinkedIn Learning espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem a seguir mostra a lista de atributos predefinidos, onde o identificador de **nomes** é mapeado com **nome de utilizador.userprincipal .** A aplicação LinkedIn Learning espera que o **identificador** de nomes seja mapeado com **user.mail**, por isso precisa editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

1. Na configuração de um único sessão com a página **SAML,** na secção Certificado de **Assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

1. Na secção **Deconfiguração do LinkedIn Learning,** copie os URL(s) adequados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao LinkedIn Learning.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **LinkedIn Learning**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-linkedin-learning-sso"></a>Configure LinkedIn Learning SSO

1. Numa janela de navegador web diferente, inscreva-se no seu inquilino linkedIn Learning como administrador.

2. No **Centro de Conta,** clique em **Definições Globais** em **Definições**. Além disso, selecione **Learning - Padrão** da lista de dropdown.

    ![Configurar um único sinal](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Clique **em OU Clique aqui para carregar e copiar campos individuais do formulário** e copiar o Url do Serviço de Consumidores **id** e de **afirmação (ACS)** e cole-o na secção **de Configuração Básica SAML** no portal Azure.

    ![Configurar um único sinal](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Vá à secção definições de **administrador linkedIn.** Faça upload do ficheiro XML que descarregou do portal Azure clicando na opção de **ficheiro Upload XML.**

    ![Configurar um único sinal](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Clique **em On** para ativar o SSO. Alterações de estado sso de **não ligados** a **conectados**

    ![Configurar um único sinal](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Criar o utilizador de teste linkedIn learning

A Aplicação de Aprendizagem LinkedIn suporta apenas a tempo o fornecimento de utilizadores e após a autenticação que os utilizadores são criados automaticamente na aplicação. Na página de definições de administração no portal LinkedIn Learning, ligue o interruptor **Automaticamente Atribuir licenças** para activaaa apenas no fornecimento de tempo e isso também atribuirá uma licença ao utilizador.

   ![Criação de um utilizador de teste azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo LinkedIn Learning no Painel de Acesso, deve ser automaticamente inscrito no LinkedIn Learning para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente o LinkedIn Learning com o Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)