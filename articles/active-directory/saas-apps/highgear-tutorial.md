---
title: 'Tutorial: Integração do Diretório Ativo Azure com a HighGear | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o HighGear.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: b3806d768e8335801e2dd097eac7a440b157de38
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92444712"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Tutorial: Integração do Diretório Ativo Azure com o HighGear

Neste tutorial, você pode aprender a integrar HighGear com Azure Ative Directory (Azure AD).
A integração da HighGear com a AD Azure proporciona-lhe os seguintes benefícios:

* Pode controlar em Azure AD que tem acesso ao HighGear.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no HighGear (Sign-On Único) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a HighGear, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um julgamento de um mês [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Um sistema HighGear com uma licença Enterprise ou Unlimited

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você pode aprender a configurar e testar Azure AD um único sinal em um ambiente de teste.

* HighGear apoia **SP e IdP** iniciado SSO

## <a name="adding-highgear-from-the-gallery"></a>Adicionando HighGear da galeria

Para configurar a integração do HighGear no AZure AD, precisa adicionar HighGear da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar HighGear da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar uma nova aplicação, clique no botão **de aplicação Novo** na parte superior do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite HighGear,** selecione **HighGear** do painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar a aplicação.

     ![HighGear na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, pode aprender a configurar e testar um único sinal de Ad AD com o seu sistema HighGear baseado num utilizador de teste chamado **Britta Simon**.
Para um único s-on para o trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no seu sistema HighGear.

Para configurar e testar o Azure AD com o seu sistema HighGear, tem de completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o HighGear Single Sign-On](#configure-highgear-single-sign-on)** - para configurar as definições de Sign-On única no lado da aplicação HighGear.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create HighGear test user](#create-highgear-test-user)** - para ter uma contraparte de Britta Simon em HighGear que está ligada à representação AD AZure do utilizador. 
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, pode aprender a ativar o único sinal de Azure AD no portal Azure.

Para configurar o Azure AD com o seu sistema HighGear, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **HighGear,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique no ícone **Editar** para abrir o diálogo **de configuração SAML básico.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![A screenshot mostra a Configuração BÁSICA SAML, onde pode introduzir o Identificador, Responder U R L e selecionar Guardar.](common/idp-intiated.png)

    a. Na caixa de texto **identifier,** cole o valor do ID da **Entidade fornecedora** de serviços que se encontra na página Definições de Sign-On Única no seu sistema HighGear.

    ![O campo de ID da Entidade fornecedora de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Terá de iniciar sessão no seu sistema HighGear para aceder à página 'Definições Sign-On Única'. Assim que iniciar sessão, mova o rato sobre o separador Administração em HighGear e clique no item do menu 'Definições Sign-On Única'.
    
    ![O item do menu de definições de Sign-On única](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Na caixa de texto **URL de resposta,** cole o valor do URL do **Serviço de Apoio ao Consumidor de Afirmação (ACS)** a partir da página Configurações de Sign-On Única no seu sistema HighGear.

    ![O campo URL do Serviço de Consumo de Afirmação (ACS)](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

     ![Screenshot mostra Definir U R Ls adicionais onde pode introduzir um sinal em U R L.](common/metadata-upload-additional-signon.png)

     Na caixa de texto **URL de entrada de registo,** cole o valor do iD da Entidade fornecedora de **serviços** que está na página Definições de Sign-On Única no seu sistema HighGear. (Este ID de entidade é também o URL base do sistema HighGear que deve ser usado para o início de súm.)

    ![O campo de ID da Entidade fornecedora de serviços](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de assinatura a partir da página **Definições de Sign-On Única** no seu sistema HighGear. Se precisar de ajuda, contacte a Equipa de [Suporte highGear.](mailto:support@highgear.com)

4. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Certificado (Base64)** e guarde-o no seu computador. Você vai precisar dele em um passo posterior da configuração single Sign-On.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

6. Na secção **set up HighGear,** note a localização dos urls seguintes.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de login. Você precisará deste valor em Step #2 em **Configure HighGear Single Sign-On** abaixo.

    b. Identificador de anúncios Azure. Você precisará deste valor em Step #3 em **Configure HighGear Single Sign-On** abaixo.

    c. URL logout. Você precisará deste valor em Step #4 em **Configure HighGear Single Sign-On** abaixo.

### <a name="configure-highgear-single-sign-on"></a>Configurar Sign-On single HighGear

Para configurar o HighGear para um único sign-on, por favor faça login no seu sistema HighGear. Assim que iniciar sessão, mova o rato sobre o separador Administração em HighGear e clique no item do menu 'Definições Sign-On Única'.

![O item do menu de definições de Sign-On única](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. No Nome do **Fornecedor de Identidade,** escreva uma breve descrição que aparecerá no botão Single Sign-On da HighGear na página de Login. Por exemplo: Azure AD

2. No campo **URL single Sign-On (SSO)** em HighGear, cole o valor do campo URL de **login** que está na secção **Configurar HighGear** em Azure.

3. No campo **ID da Entidade fornecedora de identidade** em HighGear, cole o valor do campo **Azure AD Identifier** que está na secção **Configuração HighGear** em Azure.

4. No campo **URL single Logout (SLO)** em HighGear, cole o valor do campo **URL logout** que está na secção **Configurar HighGear** em Azure.

5. Utilize o Bloco de Notas para abrir o certificado que descarregou a partir da secção certificado de **assinatura SAML** em Azure. Deveria ter descarregado o formato **Certificado (Base64).** Copie o conteúdo do certificado a partir do Bloco de Notas e cole-o no campo **de Certificado de Fornecedor de Identidade** em HighGear.

6. Envie um e-mail [à Equipa de Suporte highGear](mailto:support@highgear.com) para solicitar o seu Certificado HighGear. Siga as instruções que receber deles para preencher os campos **de certificado HighGear** e **Palavra-passe de Certificado HighGear.**

7. Clique no botão **Guardar** para guardar a configuração de Sign-On De Sign-On.

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

    c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao HighGear.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações** e, em seguida, selecione **HighGear**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **HighGear**.

    ![O link HighGear na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-highgear-test-user"></a>Criar utilizador de teste HighGear

Para criar um utilizador de teste HighGear para testar a sua configuração single Sign-On, faça login no seu sistema HighGear.

1. Clique no botão **Criar Novo Contacto.**

    ![O botão criar novo botão de contacto](media/highgear-tutorial/create-new-contact-button.png)

    Aparecerá um menu que lhe permitirá escolher o tipo de contacto que pretende criar.

2. Clique no item do menu **Individual** para criar um utilizador HighGear.

    Um painel desliza para fora à direita para que possa escrever as informações para o novo utilizador.  
    ![O novo formulário de contacto](media/highgear-tutorial/new-contact-form.png)

3. No campo **Nome,** escreva um nome para o contacto. Por exemplo: Britta Simon

4. Clique no menu **Mais Opções** e selecione o item do menu **'Informações de Conta'.**

    ![Clicando no item do menu 'Informações de Conta'](media/highgear-tutorial/account-info-menu-item.png)

5. Desa estação de registo de **latas** para sim.

    O **campo de inscrição única** ativa será automaticamente definido para Sim também.

6. No campo **single Sign-On user Id,** digite o id do utilizador. Por exemplo: BrittaSimon@contoso.com

    A secção informações de conta deve agora ser semelhante a isto:  
    ![A secção de Informação de Conta Acabada](media/highgear-tutorial/finished-account-info-section.png)

7. Para guardar o contacto, clique no botão **Guardar** na parte inferior do painel.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo HighGear no Painel de Acesso, deverá ser automaticamente inscrito no HighGear para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)