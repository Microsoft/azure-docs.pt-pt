---
title: 'Tutorial: Integração de Diretório Ativo Azure com a SAP Business ByDesign [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o SAP Business ByDesign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b830f0760b768826b2d937b4a8b2ffbd8e9e2a2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "67091679"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Integração do Azure Active Directory com SAP Business ByDesign

Neste tutorial, aprende-se a integrar o SAP Business ByDesign com o Azure Ative Directory (Azure AD).
Integrar o SAP Business ByDesign com a Azure AD proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD quem tem acesso ao SAP Business ByDesign.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no SAP Business ByDesign (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o SAP Business ByDesign, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente AD Azure, pode obter uma [conta gratuita](https://azure.microsoft.com/free/)
* Assinatura única ativada por assinatura sap business byDesign

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o único sinal de Azure AD num ambiente de teste.

* SAP Business ByDesign suporta **SP** iniciado SSO

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>Adicionar O Design de Negócios SAP da galeria

Para configurar a integração do SAP Business ByDesign em Azure AD, precisa de adicionar o SAP Business ByDesign da galeria à sua lista de aplicações geridas do SaaS.

**Para adicionar o SAP Business ByDesign da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone **do Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **O Design de Negócios SAP,** selecione **SAP Business ByDesign** do painel de resultados e, em seguida, clique em adicionar o botão **Adicionar** a aplicação.

    ![SAP Business ByDesign na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, configura e testa o único sign-on da Azure AD com o SAP Business ByDesign com base num utilizador de teste chamado **Britta Simon**.
Para um único início de sessão funcionar, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no SAP Business ByDesign.

Para configurar e testar o único sign-on da Azure AD com o SAP Business ByDesign, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SAP Business ByDesign Single Sign-On](#configure-sap-business-bydesign-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. Crie o utilizador de **[teste SAP Business ByDesign](#create-sap-business-bydesign-test-user)** - para ter uma contrapartida da Britta Simon no SAP Business ByDesign que está ligada à representação do utilizador da AD Azure.
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, permite o único sinal de entrada do Azure AD no portal Azure.

Para configurar o único sign-on da Azure AD com o SAP Business ByDesign, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **SAP Business ByDesign,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. No **set single sign-on com** a página SAML, clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![SAP Business ByDesign Domain e URLs informações únicas de inscrição](common/sp-identifier.png)

    a. No **Sign on URL** text box, digite um URL utilizando o seguinte padrão:`https://<servername>.sapbydesign.com`

    b. Na caixa de texto **identificador (Id da entidade),** digite um URL utilizando o seguinte padrão:`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e identificador. Contacte a equipa de suporte ao [Cliente SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para obter estes valores. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

5. A aplicação SAP Business ByDesign espera as afirmações da SAML num formato específico. Configure as seguintes reclamações para esta aplicação. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na configuração do Open Sign-On com a página **SAML,** clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![image](common/edit-attribute.png)

6. Clique no ícone **Editar** para editar o valor do **identificador de nome**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Na secção **'Gerir as reclamações do utilizador',** execute os seguintes passos: ![imagem](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Selecione **A Transformação** como **Fonte**.

    b. Na lista de abandono de **transformação,** selecione **ExtractMailPrefix()**.

    c. Na lista de dropdown do **Parâmetro 1,** selecione o atributo do utilizador que pretende utilizar para a sua implementação. Por exemplo, se pretender utilizar o EmployeeID como identificador único do utilizador e tiver armazenado o valor do atributo no ExtensionAttribute2, então selecione user.extensionattribute2.

    d. Clique em **Guardar**.

8. Na configuração de um único sign-on com a página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** para descarregar o **Federation Metadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/metadataxml.png)

9. Na secção **Configurar o SAP Business ByDesign,** copie os URL(s) adequados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure AD

    c. Logout URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Configure SAP Business ByDesign Single Sign-On

1. Inscreva-se no seu portal SAP Business ByDesign com direitos de administrador.

2. Navegue para **aplicação e tarefa comum** de gestão do utilizador e clique no separador **Fornecedor de Identidade.**

3. Clique em **Novo Fornecedor de Identidade** e selecione o ficheiro XML de metadados que descarregou a partir do portal Azure. Ao importar os metadados, o sistema envia automaticamente o certificado de assinatura e o certificado de encriptação necessários.

    ![Configurar um único sinal](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Para incluir o URL do **Serviço de Consumidor de Afirmação** no pedido SAML, selecione Incluir o URL do Serviço de Consumidor de **Afirmação**.

5. Clique **em ativar um único sinal .**

6. Guarde as alterações.

7. Clique no separador **My System.**

    ![Configurar um único sinal](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. No sinal de **ad Azure na caixa** de texto URL, cola o valor URL de **Login,** que copiou do portal Azure.

    ![Configurar um único sinal](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Especifique se o funcionário pode escolher manualmente entre iniciar sessão com id do utilizador e senha ou SSO selecionando a **Seleção manual do fornecedor**de identidade .

10. Na secção **URL SSO,** especifique o URL que deve ser utilizado pelo empregado para assinar no sistema.
    Na lista de desistência do URL Enviado ao Colaborador, pode escolher entre as seguintes opções:

    **URL não-SSO**

    O sistema envia apenas o URL normal do sistema para o empregado. O empregado não pode assinar usando SSO e deve utilizar senha ou certificado.

    **SSO URL**

    O sistema envia apenas o URL SSO para o empregado. O empregado pode assinar usando sSO. O pedido de autenticação é redirecionado através do IdP.

    **Seleção Automática**

    Se o SSO não estiver ativo, o sistema envia o URL do sistema normal para o empregado. Se o SSO estiver ativo, o sistema verifica se o empregado tem uma senha. Se estiver disponível uma palavra-passe, tanto o URL SSO como o URL Não-SSO são enviados para o empregado. No entanto, se o empregado não tiver senha, apenas o URL SSO é enviado ao empregado.

11. Guarde as alterações.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado Britta Simon.

1. No portal Azure, no painel esquerdo, selecione **Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.

    ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

2. Selecione **Novo utilizador** na parte superior do ecrã.

    ![Novo botão de utilizador](common/new-user.png)

3. Nas propriedades do Utilizador, execute os seguintes passos.

    ![A caixa de diálogo do Utilizador](common/user-properties.png)

    a. No campo **Nome** entrar **BrittaSimon.**
  
    b. No **User name** tipo `brittasimon@yourcompanydomain.extension`de campo do nome do utilizador . Por exemplo, BrittaSimon@contoso.com

    c. Selecione Mostrar a caixa de verificação de **palavra-passe** e, em seguida, anote o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite à Britta Simon utilizar o único sign-on do Azure, concedendo acesso ao SAP Business ByDesign.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** em seguida, selecione **SAP Business ByDesign**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **SAP Business ByDesign**.

    ![O link SAP Business ByDesign na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-sap-business-bydesign-test-user"></a>Criar o utilizador de teste SAP Business ByDesign

Nesta secção, cria-se um utilizador chamado Britta Simon no SAP Business ByDesign. Por favor, trabalhe com a equipa de suporte ao [Cliente SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) para adicionar os utilizadores na plataforma SAP Business ByDesign. 

> [!NOTE]
> Certifique-se de que o valor NameID deve coincidir com o campo username na plataforma SAP Business ByDesign.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo SAP Business ByDesign no Painel de Acesso, deverá ser automaticamente inscrito no SAP Business ByDesign para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
