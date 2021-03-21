---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Fidelity NetBenefits | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Fidelity NetBenefits.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.openlocfilehash: ac41bf89a3c2997fe0c179ef1a64787b5fb8d617
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453591"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Tutorial: Integração do Azure Ative Directory com a Fidelity NetBenefits

Neste tutorial, aprende-se a integrar a Fidelity NetBenefits com o Azure Ative Directory (Azure AD).
A integração da Fidelity NetBenefits com a Azure AD proporciona-lhe os seguintes benefícios:

* Você pode controlar em Azure AD que tem acesso a Fidelity NetBenefits.
* Pode permitir que os seus utilizadores sejam automaticamente inscritos na Fidelity NetBenefits (Single Sign-On) com as suas contas AD Azure.
* Pode gerir as suas contas numa localização central - o portal Azure.

Se quiser saber mais detalhes sobre a integração da aplicação SaaS com o Azure AD, consulte o que é o acesso à [aplicação e o único acesso ao Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AZure AD com a Fidelity NetBenefits, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver um ambiente AD Azure, pode ter um mês de julgamento [aqui.](https://azure.microsoft.com/pricing/free-trial/)
* Fidelity NetBenefits subscrição única ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD um único sinal de acesso em um ambiente de teste.

* Fidelity NetBenefits suporta **IDP** iniciado SSO

* Fidelity NetBenefits suporta provisão de utilizadores **just in time**

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Adicionar Fidelity NetBenefits da galeria

Para configurar a integração da Fidelity NetBenefits no AD Azure, é necessário adicionar Fidelity NetBenefits da galeria à sua lista de aplicações geridas pelo SaaS.

**Para adicionar Fidelity NetBenefits da galeria, execute os seguintes passos:**

1. No **[portal Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique no ícone **Azure Ative Directory.**

    ![O botão Azure Ative Directory](common/select-azuread.png)

2. Navegue para **Aplicações Empresariais** e, em seguida, selecione a opção **Todas as Aplicações.**

    ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em Novo botão de **aplicação** no topo do diálogo.

    ![O novo botão de aplicação](common/add-new-app.png)

4. Na caixa de pesquisa, **digite Fidelity NetBenefits**, selecione **Fidelity NetBenefits** do painel de resultados e, em seguida, clique em **Adicionar** botão para adicionar a aplicação.

     ![Fidelity NetBenefits na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Nesta secção, configura e testa o Azure AD com um único sign-on com Fidelity NetBenefits com base num utilizador de teste chamado **Britta Simon**.
Para um único sinal de sação a funcionar, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Fidelity NetBenefits.

Para configurar e testar o Azure AD com um único sign-on com Fidelity NetBenefits, você precisa completar os seguintes blocos de construção:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o Fidelity NetBenefits Single Sign-On](#configure-fidelity-netbenefits-single-sign-on)** - para configurar as definições de Sign-On únicas no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com Britta Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que Britta Simon utilize um único sinal de Azure.
5. **[Create Fidelity NetBenefits test user](#create-fidelity-netbenefits-test-user)** - para ter uma contraparte de Britta Simon em Fidelity NetBenefits que está ligada à representação AD AD Azure do utilizador.
6. **[Teste um único sinal](#test-single-sign-on)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar Azure AD único sinal de inscrição

Nesta secção, você ativa a Azure AD um único sinal no portal Azure.

Para configurar o Azure AD com o Fidelity NetBenefits, execute os seguintes passos:

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Fidelity NetBenefits,** selecione **Single sign-on**.

    ![Configurar link único de inscrição](common/select-sso.png)

2. No diálogo do **método de inscrição única,** selecione o modo **SAML/WS-Fed** para ativar um único sinal de súplica.

    ![Único modo de seleção de s-on](common/select-saml-option.png)

3. Na **configuração single Sign-On com página SAML,** clique em **Editar** o ícone para abrir o diálogo **básico de configuração SAML.**

    ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

4. Na **configuração single Sign-On com página SAML,** execute os seguintes passos:

    ![Fidelity NetBenefits Domain e URLs informações únicas de súmis](common/idp-intiated.png)

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão:

    Para o ambiente de testes:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Para o ambiente de produção:  `urn:sp:fidelity:geninbndnbparts20`

    b. Na caixa de texto **URL de resposta,** digite um URL que será fornecido pela Fidelidade no momento da implementação ou contacte o gestor de serviço do cliente da Fidelidade.

5. A aplicação Fidelity NetBenefits espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem seguinte mostra a lista de atributos predefinidos, onde como **identificador** de nome é mapeado com **user.userprincipalname**. A aplicação Fidelity NetBenefits espera que **o identificador** de nomes seja mapeado com **o employeeid** ou qualquer outra reclamação que seja aplicável à sua Organização como **identificador** de nomes, pelo que é necessário editar o mapeamento do atributo clicando no ícone **Editar** e alterar o mapeamento do atributo.

    ![image](common/edit-attribute.png)

    >[!Note]
    >A Fidelity NetBenefits apoia a Federação Estática e Dinâmica. Estático significa que não utilizará SAML com base apenas no fornecimento do utilizador a tempo e a Dinâmica significa que suporta apenas a tempo o fornecimento do utilizador. Para a utilização de clientes com base em JIT, os clientes têm de adicionar mais algumas reclamações no AD Azure, como data de nascimento do utilizador, etc. Estes detalhes são fornecidos pelo gestor de serviços de **clientes da Fidelidade** atribuído e eles têm que ativar esta federação dinâmica para o seu exemplo.

6. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** para descarregar o **Metadadata XML** da Federação a partir das opções dadas de acordo com o seu requisito e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

7. Na secção **Configurar Fidelity NetBenefits,** copie os URL(s) apropriados de acordo com o seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de Inicio de Sessão

    b. Identificador Azure Ad

    c. Logout URL

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Configurar Fidelidade NetBenefits Single Sign-On

Para configurar um único sign-on no lado **Fidelity NetBenefits,** você precisa enviar o **Metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte Fidelity NetBenefits](mailto:SSOMaintenance@fmr.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

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

Nesta secção, você permite que Britta Simon utilize a Azure single sign-on, concedendo acesso a Fidelity NetBenefits.

1. No portal Azure, selecione **Aplicações empresariais**, selecione **Todas as aplicações,** em seguida, selecione **Fidelity NetBenefits**.

    ![Painel Aplicações empresariais](common/enterprise-applications.png)

2. Na lista de candidaturas, selecione **Fidelity NetBenefits**.

    ![O link Fidelity NetBenefits na lista de Aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **Utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique no botão **Adicionar utilizador** e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O painel de atribuição de adição](common/add-assign-user.png)

5. No diálogo **de Utilizadores e grupos** selecione **Britta Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.

6. Se estiver à espera de qualquer valor de função na afirmação SAML, então no diálogo **'Fun's Select** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.

7. No diálogo **'Adicionar Atribuição'** clique no botão **'Atribuir'.**

### <a name="create-fidelity-netbenefits-test-user"></a>Criar utilizador de teste Fidelity NetBenefits

Nesta secção, cria-se um utilizador chamado Britta Simon in Fidelity NetBenefits. Se estiver a criar uma federação estática, por favor trabalhe com o gestor de serviços de **clientes Fidelity** designado para criar utilizadores na plataforma Fidelity NetBenefits. Estes utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

Para a Federação Dinâmica, os utilizadores são criados utilizando o fornecimento do utilizador Just In Time. Para a utilização de clientes com base em JIT, os clientes têm de adicionar mais algumas reclamações no AD Azure, como data de nascimento do utilizador, etc. Estes detalhes são fornecidos pelo gestor de serviços de **clientes da Fidelidade** atribuído e eles têm que ativar esta federação dinâmica para o seu exemplo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo Fidelity NetBenefits no Painel de Acesso, deverá ser automaticamente inscrito nos Fidelity NetBenefits para os quais configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](../conditional-access/overview.md)