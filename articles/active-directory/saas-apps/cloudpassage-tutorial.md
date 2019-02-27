---
title: 'Tutorial: Integração do Active Directory do Azure com CloudPassage | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45d5e0cfe39436b9af3a2b671a194bce88b91720
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883492"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Tutorial: Integração do Active Directory do Azure com CloudPassage

Neste tutorial, saiba como integrar CloudPassage com o Azure Active Directory (Azure AD).
Integrar CloudPassage no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao CloudPassage.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para CloudPassage (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com CloudPassage, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* CloudPassage logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta CloudPassage **SP** iniciada SSO

## <a name="adding-cloudpassage-from-the-gallery"></a>Adicionando CloudPassage da Galeria

Para configurar a integração do CloudPassage com o Azure AD, terá de adicionar CloudPassage a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar CloudPassage a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **CloudPassage**, selecione **CloudPassage** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![CloudPassage na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com CloudPassage com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no CloudPassage deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com CloudPassage, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar CloudPassage Single Sign-On](#configure-cloudpassage-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste CloudPassage](#create-cloudpassage-test-user)**  - para ter um equivalente da Eduarda Almeida na CloudPassage que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com CloudPassage, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **CloudPassage** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![CloudPassage domínio e URLs únicas início de sessão em informações](common/sp-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://portal.cloudpassage.com/saml/init/accountid`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://portal.cloudpassage.com/saml/consume/accountid`. Pode obter o valor deste atributo clicando **documentação de configuração de SSO** no **configurações de logon único** seção do CloudPassage portal.

    ![Configurar o início de sessão único](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o URL de resposta real. Contacte [equipa de suporte de cliente CloudPassage](https://www.cloudpassage.com/company/contact/) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de CloudPassage espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos: 

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | FirstName |user.givenname |
    | Apelido |user.surname |
    | e-mail |user.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

8. Sobre o **configurar CloudPassage** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-cloudpassage-single-sign-on"></a>Configurar CloudPassage Single Sign-On

1. Numa janela do browser diferente, início de sessão no site da sua empresa CloudPassage como administrador.

1. No menu na parte superior, clique em **configurações**e, em seguida, clique em **administração de sites**. 
   
    ![Configurar o início de sessão único][12]

1. Clique nas **definições de autenticação** separador. 
   
    ![Configurar o início de sessão único][13]

1. Na **definições de início de sessão único** secção, execute os seguintes passos: 
   
    ![Configurar o início de sessão único][14]

    a. Selecione **sign-on(SSO) ativar único (SSO documentação de configuração)** caixa de verificação.
    
    b. Colar **identificador do Azure Ad** para o **URL de emissor SAML** caixa de texto.
  
    c. Colar **URL de início de sessão** para o **URL de ponto final SAML** caixa de texto.
  
    d. Colar **URL de fim de sessão** para o **página de destino de fim de sessão** caixa de texto.
  
    e. Abra o seu certificado transferido no bloco de notas, copie o conteúdo de certificado transferido para a área de transferência e, em seguida, cole-o para o **x 509 certificado** caixa de texto.
  
    f. Clique em **Guardar**.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para CloudPassage.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **CloudPassage**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **CloudPassage**.

    ![A ligação de CloudPassage na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-cloudpassage-test-user"></a>Criar utilizador de teste CloudPassage

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no CloudPassage.

**Para criar um usuário chamado Eduarda Almeida no CloudPassage, execute os seguintes passos:**

1. Início de sessão na sua **CloudPassage** site da empresa como administrador. 

1. Na barra de ferramentas na parte superior, clique em **configurações**e, em seguida, clique em **administração de sites**. 
   
    ![Criar um utilizador de teste CloudPassage][22] 

1. Clique nas **usuários** separador e, em seguida, clique em **adicionar novo utilizador**. 
   
    ![Criar um utilizador de teste CloudPassage][23]

1. Na **adicionar novo utilizador** secção, execute os seguintes passos: 
   
    ![Criar um utilizador de teste CloudPassage][24]
    
    a. Na **nome próprio** caixa de texto, escreva a Eduarda. 
  
    b. Na **Apelido** caixa de texto, digite Simon.
  
    c. Na **nome de utilizador** caixa de texto, o **E-Mail** caixa de texto e o **volte a escrever E-Mail** caixa de texto, escreva o nome de utilizador da Eduarda no Azure AD.
  
    d. Como **tipo de acesso**, selecione **ativar o acesso do Portal de Halo**.
  
    e. Clique em **Adicionar**.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico CloudPassage no painel de acesso, deve ser automaticamente sessão iniciada no CloudPassage para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

