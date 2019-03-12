---
title: 'Tutorial: Integração do Active Directory do Azure com Litmos | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Litmos.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e7f462431e286492691796db20d719cb2e0aff4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57768517"
---
# <a name="tutorial-azure-active-directory-integration-with-litmos"></a>Tutorial: Integração do Active Directory do Azure com Litmos

Neste tutorial, saiba como integrar Litmos com o Azure Active Directory (Azure AD).
Integrar Litmos no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao Litmos.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Litmos (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Litmos, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Litmos único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta Litmos **IDP** iniciada SSO

* Suporta Litmos **Just In Time** aprovisionamento de utilizadores

## <a name="adding-litmos-from-the-gallery"></a>Adicionando Litmos da Galeria

Para configurar a integração do Litmos com o Azure AD, terá de adicionar Litmos a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Litmos a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Litmos**, selecione **Litmos** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Litmos na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Litmos com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Litmos deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Litmos, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Litmos Single Sign-On](#configure-litmos-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Litmos](#create-litmos-test-user)**  - para ter um equivalente da Eduarda Almeida na Litmos que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Litmos, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Litmos** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![Litmos domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.litmos.com/account/Login`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o real identificador e o URL de resposta, que são explicadas mais tarde no tutorial ou contacte [equipa de suporte de cliente Litmos](https://www.litmos.com/contact-us) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de Litmos espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![imagem](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name |  Atributo de origem |
    |---------------|--------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-mail | user.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![imagem](common/new-save-attribute.png)

    ![imagem](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar Litmos** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-litmos-single-sign-on"></a>Configurar Litmos Single Sign-On

1. Numa janela do browser diferente, início de sessão no site da sua empresa Litmos como administrador.

2. Na barra de navegação no lado esquerdo, clique em **contas**.

    ![Secção de contas no lado de aplicação][22]

3. Clique nas **integrações** separador.

    ![Guia de integração][23]

4. Sobre o **integrações** separador, desloque para baixo até **3rd Party integrações**e, em seguida, clique em **SAML 2.0** separador.

    ![SAML 2.0 secção][24]

5. Copie o valor sob **SAML o ponto final para litmos é:** e cole-o no **URL de resposta** caixa de texto no **Litmos domínio e URLs** secção no portal do Azure.

    ![Ponto final de SAML][26]

6. No seu **Litmos** aplicativo, execute os seguintes passos:

    ![Aplicação de Litmos][25]

    a. Clique em **ativar SAML**.

    b. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509 de SAML** caixa de texto.

    c. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Litmos.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Litmos**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Litmos**.

    ![A ligação de Litmos na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-litmos-test-user"></a>Criar utilizador de teste Litmos

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Litmos. A aplicação de Litmos suporta Just-in-Time de aprovisionamento. Isso significa, uma conta de utilizador é criada automaticamente se for necessário durante uma tentativa de aceder à aplicação através do painel de acesso.

**Para criar um usuário chamado Eduarda Almeida no Litmos, execute os seguintes passos:**

1. Numa janela do browser diferente, início de sessão no site da sua empresa Litmos como administrador.

2. Na barra de navegação no lado esquerdo, clique em **contas**.

    ![Secção de contas no lado de aplicação][22]

3. Clique nas **integrações** separador.

    ![Separador de integrações][23]

4. Sobre o **integrações** separador, desloque para baixo até **3rd Party integrações**e, em seguida, clique em **SAML 2.0** separador.

    ![SAML 2.0][24]

5. Selecione **gerar automaticamente utilizadores**
  
    ![Utilizadores de gerar automaticamente][27]

### <a name="test-single-sign-on"></a>Testar início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Litmos no painel de acesso, deve ser automaticamente sessão iniciada no Litmos para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png
