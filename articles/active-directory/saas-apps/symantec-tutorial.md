---
title: 'Tutorial: Integração do Diretório Ativo Azure com o Serviço de Segurança Web Symantec (WSS) [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Symantec Web Security Service (WSS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d496015440deb80a0159ed0ec234ae60c2c64a66
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73159942"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integração de Diretório Ativo Azure com o Serviço de Segurança Web Symantec (WSS)

Neste tutorial, aprenderá a integrar a sua conta Symantec Web Security Service (WSS) com a sua conta Azure Ative Directory (Azure AD) para que a WSS possa autenticar um utilizador final aprovisionado no Azure AD utilizando a autenticação SAML e impor regras de política de utilizador ou de grupo.

Integrar o Serviço de Segurança Web Symantec (WSS) com a Azure AD proporciona-lhe os seguintes benefícios:

- Gerencie todos os utilizadores finais e grupos utilizados pela sua conta WSS a partir do portal Azure AD.

- Permitir que os utilizadores finais se autentiquem no WSS utilizando as suas credenciais De AD Azure.

- Ative a aplicação das regras de política de nível de utilizador e de grupo definidas na sua conta WSS.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o Symantec Web Security Service (WSS), precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Assinatura única ativada pelo Serviço de Segurança Web Symantec (WSS)

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* Symantec Web Security Service (WSS) suporta **IDP** iniciado SSO

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Adicionar o Serviço de Segurança Web Symantec (WSS) da galeria

Para configurar a integração do Serviço de Segurança Web Symantec (WSS) em Azure AD, precisa adicionar o Serviço de Segurança Web Symantec (WSS) da galeria à sua lista de aplicações saaS geridas.

**Para adicionar o Serviço de Segurança Web Symantec (WSS) da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite o Serviço de **Segurança Web Symantec (WSS)**, selecione **Symantec Web Security Service (WSS)** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Serviço de Segurança Web Symantec (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa um único sinal de Azure AD com o Symantec Web Security Service (WSS) com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no Serviço de Segurança Web Symantec (WSS).

Para configurar e testar o único sinal de Azure AD com o Symantec Web Security Service (WSS), é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. Configure o Serviço de **Segurança Web Symantec (WSS) Single Sign-On** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador de teste Symantec Web Security Service (WSS)](#create-symantec-web-security-service-wss-test-user)** - para ter uma contrapartida da Britta Simon no Serviço de Segurança Web Symantec (WSS) que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on azure ad com o Symantec Web Security Service (WSS), execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações do Serviço de **Segurança Web Symantec (WSS),** selecione **single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. No diálogo básico de **configuração SAML,** execute os seguintes passos:

    ![Domínio e URLs de serviço de segurança web Symantec (WSS)](common/idp-intiated.png)

    a. Na caixa de texto **identificador,** digite um URL:`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na caixa de texto **URL resposta,** escreva um URL:`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contacte a equipa de suporte ao [cliente da Symantec Web Security Service (WSS)](https://www.symantec.com/contact-us) f os valores do **Identificador** e **do URL de Resposta** não estão a funcionar por alguma razão.. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Configure Serviço de Segurança Web Symantec (WSS) Single Sign-On

Para configurar um único sinal no lado do Serviço de Segurança Web Symantec (WSS), consulte a documentação online da WSS. O Descarregamento **de Metadados da Federação XML** terá de ser importado para o portal WSS. Contacte a equipa de suporte do Serviço de [Segurança Web Symantec (WSS)](https://www.symantec.com/contact-us) se precisar de assistência com a configuração no portal WSS.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **tipo** de campo de nome utilizador **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao Serviço de Segurança Web Symantec (WSS).

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **Symantec Web Security Service (WSS)**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, digite e selecione **Symantec Web Security Service (WSS)**.

    ![O link Symantec Web Security Service (WSS) na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-symantec-web-security-service-wss-test-user"></a>Criar o utilizador de teste Symantec Web Security Service (WSS)

Nesta secção, cria-se uma utilizadora chamada Britta Simon no Serviço de Segurança Web Symantec (WSS). O nome de utilizador final correspondente pode ser criado manualmente no portal WSS ou pode esperar que os utilizadores/grupos aprovisionados no Azure AD sejam sincronizados no portal WSS após alguns minutos (~15 minutos). Os utilizadores devem ser criados e ativados antes de utilizar um único sinal. O endereço IP público da máquina de utilizador final, que será utilizado para navegar em websites também precisa de ser aprovisionado no portal Symantec Web Security Service (WSS).

> [!NOTE]
> Clique [aqui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter o endereço público da sua máquina.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, irá testar a funcionalidade de entrada única agora que configura a sua conta WSS para utilizar o seu AD Azure para autenticação SAML.

Depois de configurar o seu navegador web para proxy tráfego para WSS, quando abrir o seu navegador web e tentar navegar para um site, então você será redirecionado para a página de entrada de sinais do Azure. Introduza as credenciais do utilizador final de teste que foi aprovisionado no Azure AD (isto é, BrittaSimon) e na senha associada. Uma vez autenticado, poderá navegar no site que escolheu. Caso crie uma regra de política do lado wSS para impedir a BrittaSimon de navegar para um determinado site, então deve ver a página do bloco WSS quando tentar navegar para esse site como utilizador BrittaSimon.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

