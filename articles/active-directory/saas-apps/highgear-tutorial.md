---
title: 'Tutorial: Integração do Diretório Ativo Azure com a HighGear Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o HighGear.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed06586435315935566ca0b1519b182d4fc47d39
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159051"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Tutorial: Integração de Diretório Ativo Azure com HighGear

Neste tutorial, pode aprender a integrar o HighGear com o Azure Ative Directory (Azure AD).
Integrar o HighGear com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a HighGear.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no HighGear (Single Sign-On) com as suas contas Azure AD.
* Você pode gerir suas contas em um local central - o portal Azure.

Se quiser saber mais detalhes sobre a integração de apps saaS com a Azure AD, consulte [o que é o acesso à aplicação e o único registo com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração da AD Azure com o HighGear, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver um ambiente de AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Um sistema HighGear com licença Enterprise ou Unlimited

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você pode aprender a configurar e testar o único sign-on Azure AD em um ambiente de teste.

* HighGear suporta **SP e IdP** iniciadoS SSO

## <a name="adding-highgear-from-the-gallery"></a>Adicionando HighGear da galeria

Para configurar a integração do HighGear em Azure AD, precisa de adicionar highGear da galeria à sua lista de aplicações saaS geridas.

**Para adicionar HighGear da galeria, execute os seguintes passos:**

1. No **[portal Azure,](https://portal.azure.com)** no painel de navegação à esquerda, clique no ícone do **Diretório Ativo Azure.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no novo botão de **aplicação** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, digite **HighGear,** **selecione HighGear** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![HighGear na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Nesta secção, pode aprender a configurar e testar um único sign-on azure com o seu sistema HighGear com base num utilizador de teste chamado **Britta Simon**.
Para que o único início de sessão funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado no seu sistema HighGear.

Para configurar e testar o único sinal de Azure AD com o seu sistema HighGear, é necessário completar os seguintes blocos de construção:

1. **[Configure O Único Sinal do Azure AD](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Single HighGear](#configure-highgear-single-sign-on)** - para configurar as definições de início de sessão individuais no lado da aplicação HighGear.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com Britta Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de AD Azure.
5. **[Crie um utilizador](#create-highgear-test-user)** de teste HighGear - para ter uma contrapartida da Britta Simon no HighGear que esteja ligada à representação da AD Azure do utilizador. 
6. **[Teste o único sinal para](#test-single-sign-on)** verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configure Azure AD único sign-on

Nesta secção, pode aprender a ativar o único sinal de Entrada de Azure AD no portal Azure.

Para configurar o único sinal de Azure AD com o seu sistema HighGear, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **HighGear,** selecione **Single sign-on**.

    ![Configurar um único link de sinalização](common/select-sso.png)

2. No diálogo **Select a Single sign-on,** selecione o modo **SAML/WS-Fed** para ativar um único sinal.

    ![Modo de seleção de sinal único](common/select-saml-option.png)

3. Na configuração do Open Sign-On com a página **SAML,** clique no ícone **Editar** para abrir o diálogo básico de **configuração SAML.**

    ![Editar Configuração Básica do SAML](common/edit-urls.png)

4. Na secção **Basic SAML Configuration,** execute os seguintes passos:

    ![HighGear Domain e URLs informações únicas de inscrição](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** colhe o valor do campo ID da **Entidade fornecedora** de serviços que está na página de Definições de Início de Início único no seu sistema HighGear.

    ![O campo ID da entidade prestadora de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Terá de iniciar sessão no seu sistema HighGear para aceder à página de Definições de Início de Acesso Único. Assim que iniciar sessão, mova o rato sobre o separador 'Administração' em HighGear e clique no item do menu Definições de Inscrição Única.
    
    ![O item do menu definições de sinal único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Na caixa de texto **URL resposta,** colhe o valor do URL do Serviço de **Consumidores de Afirmação (ACS)** da página Definições de Início único de Início de Sessão no seu sistema HighGear.

    ![O campo URL do Serviço de Consumidores de Afirmação (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

     ![HighGear Domain e URLs informações únicas de inscrição](common/metadata-upload-additional-signon.png)

     Na caixa de texto **de URL sign-on,** colá o valor do campo ID da **Entidade fornecedora** de serviços que está na página de Definições de Início único no seu sistema HighGear. (Este ID da entidade é também o URL base do sistema HighGear que deve ser utilizado para o sign-on iniciado por SP.)

    ![O campo ID da entidade prestadora de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação, resposta e URL de sinal na página **de Definições de Sinal Único** no seu sistema HighGear. Se precisar de ajuda, contacte a Equipa de [Suporte highGear](mailto:support@highgear.com).

4. Na configuração de um único sinal com página **SAML,** na secção Certificado de **Assinatura SAML,** clique em **Baixar** o **Certificado (Base64)** e guardá-lo no seu computador. Você precisará dele em um passo posterior da configuração Single Sign-On.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

6. Na secção **Configurar HighGear,** note a localização dos seguintes URLs.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de login. Você precisará deste valor em Passo #2 em **Configurar HighGear Single Sign-On** abaixo.

    b. Identificador Azure AD. Você precisará deste valor em Step #3 em **Configure HighGear Single Sign-On** abaixo.

    c. URL de logout. Você precisará deste valor em Step #4 em **Configure HighGear Single Sign-On** abaixo.

### <a name="configure-highgear-single-sign-on"></a>Configure o sinal único de highgear

Para configurar o HighGear para um único sinal, faça login no seu sistema HighGear. Assim que iniciar sessão, mova o rato sobre o separador 'Administração' em HighGear e clique no item do menu Definições de Inscrição Única.

![O item do menu definições de sinal único](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. No Nome do Fornecedor de **Identidade,** escreva uma descrição curta que aparecerá no botão de início de sessão single do HighGear na página de Login. Por exemplo: Azure AD

2. No campo **URL single Sign-On (SSO)** em HighGear, colá-lo o valor do campo URL de **Login** que está na secção **set up HighGear** em Azure.

3. No campo ID da **Entidade fornecedora** de identidade em HighGear, cola o valor do campo **de identificação Azure AD** que está na secção **set up HighGear** em Azure.

4. No campo **DE URL Single Logout (SLO)** em HighGear, colá-lo o valor do campo URL de **Logout** que está na secção **set up HighGear** em Azure.

5. Utilize o Bloco de Notas para abrir o certificado que descarregou da secção **de Certificado de Assinatura SAML** em Azure. Devia ter descarregado o formato **Certificado (Base64).** Copie o conteúdo do certificado do Bloco de Notas e cole-o no campo de Certificado de **Fornecedor de Identidade** em HighGear.

6. Envie um e-mail à Equipa de [Suporte highGear](mailto:support@highgear.com) para solicitar o seu Certificado HighGear. Siga as instruções que recebe deles para preencher os campos de palavra-passe do **Certificado HighGear** e **do Certificado HighGear.**

7. Clique no botão **Guardar** para guardar a configuração de sinal único highGear.

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

    c. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa password.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que britta Simon utilize um único sign-on Azure, concedendo acesso ao HighGear.

1. No portal Azure, selecione **Aplicações Empresariais,** selecione **Todas as aplicações,** e, em seguida, selecione **HighGear**.

    ![Lâmina de aplicações da empresa](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **HighGear**.

    ![A ligação HighGear na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **adicionar** utilizador e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição adicionar](common/add-assign-user.png)

5. Nos **utilizadores e grupos** de diálogo selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de algum valor de papel na afirmação do SAML, então no diálogo **Select Role** selecione a função apropriada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.

7. No diálogo **adicionar atribuição** clique no botão **Atribuir.**

### <a name="create-highgear-test-user"></a>Criar utilizador de teste HighGear

Para criar um utilizador de teste HighGear para testar a sua configuração de início de sessão individual, faça login no seu sistema HighGear.

1. Clique no botão **Criar Novo Contacto.**

    ![O botão Criar Novo Contacto](media/highgear-tutorial/create-new-contact-button.png)

    Aparecerá um menu que lhe permite escolher o tipo de contacto que pretende criar.

2. Clique no item do menu **Individual** para criar um utilizador HighGear.

    Um painel deslizará para fora à direita para que possa escrever as informações para o novo utilizador.  
    ![O novo formulário de contacto](media/highgear-tutorial/new-contact-form.png)

3. No campo **Nome,** digite um nome para o contacto. Por exemplo: Britta Simon

4. Clique no menu **Mais Opções** e selecione o item do menu **Conta Info.**

    ![Clicar no item do menu da Conta Info](media/highgear-tutorial/account-info-menu-item.png)

5. Detete o campo **de iniciar sessão** de canto para sim.

    O campo **enable single sign-on** será automaticamente definido para Sim também.

6. No campo Id do **utilizador de um único sinal,** digite o id do utilizador. Por exemplo: BrittaSimon@contoso.com

    A secção Informação de Conta deve agora ser algo assim:  
    ![A secção de Informação de Conta acabada](media/highgear-tutorial/finished-account-info-section.png)

7. Para evitar o contacto, clique no botão **Guardar** na parte inferior do painel.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo HighGear no Painel de Acesso, deve ser automaticamente inscrito no HighGear para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

