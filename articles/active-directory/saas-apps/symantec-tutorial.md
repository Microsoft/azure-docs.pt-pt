---
title: 'Tutorial: Integração do Azure Ative Directory com o Symantec Web Security Service (WSS) Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 7e2087fdd5568156a96645b25cb105cc5f3ca6b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544949"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integração do Azure Ative Directory com o Symantec Web Security Service (WSS)

Neste tutorial, você aprenderá a integrar a sua conta Symantec Web Security Service (WSS) com a sua conta Azure Ative Directory (Azure AD) para que o WSS possa autenticar um utilizador final aprovisionado no AD Azure usando a autenticação SAML e impor regras de política de utilizador ou de nível de grupo.

A integração do Serviço de Segurança Web Symantec (WSS) com a Azure AD proporciona-lhe os seguintes benefícios:

- Gerencie todos os utilizadores finais e grupos utilizados pela sua conta WSS a partir do seu portal AD Azure.

- Permitir que os utilizadores finais se autentem em WSS utilizando as suas credenciais AZure AD.

- Ativar a aplicação das regras de política de utilizador e de nível de grupo definidas na sua conta WSS.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD do Azure com o Symantec Web Security Service (WSS), precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Symantec Web Security Service (WSS) subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Symantec Web Security Service (WSS) suporta SSO iniciado no **IDP**

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Adicionar symantec Web Security Service (WSS) da galeria

Para configurar a integração do Symantec Web Security Service (WSS) no Azure AD, é necessário adicionar o Symantec Web Security Service (WSS) da galeria à sua lista de aplicações geridas para o SaaS.

**Para adicionar o Symantec Web Security Service (WSS) da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Symantec Web Security Service (WSS),** selecione **Symantec Web Security Service (WSS)** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Symantec Web Security Service (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com o Symantec Web Security Service (WSS) com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de acesso ao trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Symantec Web Security Service (WSS).

Para configurar e testar o único sinal de Azure AD com o Symantec Web Security Service (WSS), tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **Configure o Serviço de Segurança Web Symantec (WSS) Single Sign-On** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. Crie o utilizador de **[teste Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** - para ter uma contraparte de Britta Simon no Symantec Web Security Service (WSS) que está ligada à representação AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Symantec Web Security Service (WSS), execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **do Symantec Web Security Service (WSS),** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. No diálogo básico de **configuração SAML,** execute os seguintes passos:

    ![Symantec Web Security Service (WSS) Domain and URLs single sign-on](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** digite um URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na caixa de texto **URL de resposta,** digite um URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contacte [a Symantec Web Security Service (WSS) A equipa de suporte](https://www.symantec.com/contact-us) ao cliente f os valores para o URL de **identificação** e **resposta** não estão a funcionar por alguma razão.. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Configurar o Serviço de Segurança Web Symantec (WSS) Sign-On

Para configurar um único sinal no lado do Symantec Web Security Service (WSS), consulte a documentação on-line da WSS. O **Metdata XML da Federação** descarregado terá de ser importado para o portal WSS. Contacte a equipa de suporte do [Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) se precisar de assistência com a configuração no portal WSS.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.

    ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do utilizador](common/user-properties.png)

    a. No campo **Nome** entra **BrittaSimon**.
  
    b. No tipo de campo **nome de utilizador** **brittasimon \@ yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar caixa de verificação de **palavra-passe** e, em seguida, anotar o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon utilize o Azure single sign-on, concedendo acesso ao Symantec Web Security Service (WSS).

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Symantec Web Security Service (WSS)**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, escreva e selecione **o Symantec Web Security Service (WSS)**.

    ![O link Symantec Web Security Service (WSS) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-symantec-web-security-service-wss-test-user"></a>Criar utilizador de teste symantec Web Security Service (WSS)

Nesta secção, cria-se um utilizador chamado Britta Simon no Symantec Web Security Service (WSS). O nome de utilizador final correspondente pode ser criado manualmente no portal WSS ou pode esperar que os utilizadores/grupos aprovisionados no AD Azure sejam sincronizados com o portal WSS após alguns minutos (~15 minutos). Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação. O endereço IP público da máquina de utilizador final, que será usada para navegar em websites, também precisa de ser aprovisionado no portal Symantec Web Security Service (WSS).

> [!NOTE]
> Clique [aqui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter o iPaddress público da sua máquina.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, irá testar a única funcionalidade de início de súps agora que configura a sua conta WSS para utilizar o seu AD AZure para autenticação SAML.

Depois de configurar o seu navegador web para proxy traffic para WSS, quando abrir o seu navegador web e tentar navegar para um site, então você será redirecionado para a página de sign-on Azure. Introduza as credenciais do utilizador final de teste que foi a provisionado no AD Azure (isto é, BrittaSimon) e na palavra-passe associada. Uma vez autenticado, poderá navegar no site que escolheu. Se criar uma regra de política no lado da WSS para bloquear a BrittaSimon de navegar para um determinado site, então deve ver a página do bloco WSS quando tentar navegar para esse site como utilizador BrittaSimon.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

