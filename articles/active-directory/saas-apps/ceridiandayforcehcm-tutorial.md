---
title: 'Tutorial: Integração do Azure Ative Directory com a Ceridian Dayforce HCM Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: bfc370dbe1f44f353df4140de7a6136de9360423
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760151"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Integração do Diretório Ativo Azure com a Ceridian Dayforce HCM

Neste tutorial, você aprende a integrar Ceridian Dayforce HCM com Azure Ative Directory (Azure AD).
A integração da Ceridian Dayforce HCM com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Ceridian Dayforce HCM.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos no Ceridian Dayforce HCM (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Ceridian Dayforce HCM, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Ceridian Dayforce HCM única assinatura ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Ceridian Dayforce HCM suporta **SP** iniciado SSO

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Adicionando Ceridian Dayforce HCM da galeria

Para configurar a integração da Ceridian Dayforce HCM em Azure AD, você precisa adicionar Ceridian Dayforce HCM da galeria à sua lista de aplicações geridas saaS.

**Para adicionar Ceridian Dayforce HCM da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Ceridian Dayforce HCM,** selecione **Ceridian Dayforce HCM** do painel de resultados e clique em **Adicionar** botão adicionar a aplicação.

     ![Ceridian Dayforce HCM na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, você configura e testa Azure AD single sign-on com Ceridian Dayforce HCM com base em um utilizador de teste chamado **Britta Simon**.
Para um único sinal de trabalho, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Ceridian Dayforce HCM.

Para configurar e testar o Azure AD com o Ceridian Dayforce HCM, é necessário completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Sign-On Único da Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Ceridian Dayforce HCM test user](#create-ceridian-dayforce-hcm-test-user)** - para ter uma contraparte de Britta Simon em Ceridian Dayforce HCM que está ligada à representação Azure AD do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD single sign-on com Ceridian Dayforce HCM, execute os seguintes passos:

1. No [portal Azure,](https://portal.azure.com/)na página de integração da aplicação **Ceridian Dayforce HCM,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na secção **de Configuração Básica SAML,** execute os seguintes passos:

    ![Ceridian Dayforce HCM Domain e URLs informações únicas de súplic](common/sp-identifier-reply.png)

    a. Na caixa de texto **do Sign On URL,** digite o URL utilizado pelos seus utilizadores para iniciar sing-on na sua aplicação Ceridian Dayforce HCM.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Para teste | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Na caixa de texto **identifier,** digite um URL utilizando o seguinte padrão:

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp` |

    c. Na caixa de texto **URL de resposta,** digite o URL utilizado pela Azure AD para publicar a resposta.

    | Ambiente | URL |
    | :-- | :-- |
    | Para produção | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Para teste | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de Sign-On real, identifier e responder URL. Contacte [a ceridian Dayforce HCM Equipa de suporte ao cliente](https://www.ceridian.com/support) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

5. A aplicação Ceridian Dayforce HCM espera as afirmações DOL num formato específico. Configure os seguintes pedidos para este pedido. Pode gerir os valores destes atributos a partir da secção **Atributos** do Utilizador na página de integração de aplicações. Na **configuração single Sign-On com** a página SAML, clique no botão **Editar** para abrir o diálogo **de Atributos do Utilizador.**

    ![A screenshot mostra atributos do utilizador com o ícone editar selecionado.](common/edit-attribute.png)

6. Na secção **'Reclamações** de Utilizador' no diálogo **'Atributos do Utilizador',** configurar o atributo de ficha SAML como mostrado na imagem acima e executar os seguintes passos:
    
    | Nome | Atributo de origem|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Clique **Em Adicionar nova reivindicação** para abrir o diálogo de reclamações do utilizador **Gerir.**

    ![A Screenshot mostra as reclamações do Utilizador com a opção de adicionar uma nova reclamação.](common/new-save-attribute.png)

    ![A screenshot mostra a caixa de diálogo de reclamações do utilizador Manage onde pode introduzir os valores descritos.](common/new-attribute-details.png)

    b. Na caixa de texto **'Nome',** digite o nome do atributo indicado para esta linha.

    c. Deixe o **Espaço Namespace** em branco.

    d. Selecione Fonte como **Atributo**.

    e. A partir da lista **de atributos 'Fonte',** selecione o atributo do utilizador que pretende utilizar para a sua implementação. Por exemplo, se pretender utilizar o EmployeeID como identificador único do utilizador e tiver armazenado o valor do atributo na ExtensionAttribute2, então selecione user.extensionattribute2.

    f. Clique **em Ok**

    exemplo, Clique em **Guardar**.

7. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

8. Na secção **Configuração Ceridian Dayforce HCM,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Configure Ceridian Dayforce HCM Single Sign-On

Para configurar um único sign-on no lado **Ceridian Dayforce HCM,** você precisa enviar os **Metadados XML** descarregados e URLs copiados apropriados do portal Azure para a equipa de [suporte Ceridian Dayforce HCM](https://www.ceridian.com/support). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon use Azure single sign-on, concedendo acesso ao Ceridian Dayforce HCM.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Ceridian Dayforce HCM**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Ceridian Dayforce HCM**.

    ![O link Ceridian Dayforce HCM na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Criar utilizador de teste Ceridian Dayforce HCM

Nesta secção, você cria um utilizador chamado Britta Simon em Ceridian Dayforce HCM. Trabalhe com a [equipa de apoio da Ceridian Dayforce HCM](https://www.ceridian.com/support) para adicionar os utilizadores na plataforma Ceridian Dayforce HCM. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Ceridian Dayforce HCM no Painel de Acesso, deverá ser automaticamente inscrito no HCM da Ceridian Dayforce para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

