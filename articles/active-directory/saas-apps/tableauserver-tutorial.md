---
title: 'Tutorial: Integração do Active Directory do Azure com o Tableau Server | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1b48d3bced2061dbe7e8ba26e2c6738e44ba4b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840431"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Tutorial: Integração do Active Directory do Azure com o Tableau Server

Neste tutorial, saiba como integrar o Tableau Server no Azure Active Directory (Azure AD).

Integrar o Tableau Server no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao servidor da Tableau.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o servidor da Tableau (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Tableau Server, terá dos seguintes itens:

- Uma subscrição do Azure
- Um servidor da Tableau logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o Tableau Server da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-tableau-server-from-the-gallery"></a>Adicionando o Tableau Server da Galeria

Para configurar a integração do Tableau Server no Azure AD, terá de adicionar o servidor da Tableau a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o servidor da Tableau a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **o servidor da Tableau**, selecione **Tableau Server** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Servidor da tableau na lista de resultados](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Tableau Server com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no servidor da Tableau a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Tableau Server deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Tableau Server, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o Tableau Server início de sessão único](#configure-tableau-server-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Criar utilizador de teste do servidor da Tableau](#create-tableau-server-test-user)**  - para ter um equivalente da Eduarda Almeida na Cisco guarda-chuva que está ligado à representação de utilizador do Azure AD.
5. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de servidor da Tableau.

**Para configurar o Azure AD início de sessão único com o Tableau Server, execute os seguintes passos:**

1. No portal do Azure, sobre o **o servidor da Tableau** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial-general-301.png)

3. Aplicação de servidor da tableau espera uma declaração personalizada **nome de utilizador** que deve ser definido conforme mostrado abaixo. Isso está sendo usado como identificador de utilizador em vez de afirmação de identificador de utilizador exclusivo. Pode gerir os valores destes atributos do **atributos de utilizador e afirmações** secção na página de integração de aplicações. Clique em **edite** botão para abrir **atributos de utilizador e afirmações** caixa de diálogo.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. No **afirmações de utilizador** secção sobre o **atributos de utilizador e afirmações** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo | Espaço de nomes |
    | ---------------| --------------- | ----------- |   
    | o nome de utilizador | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Introduza o **espaço de nomes** valor.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Guardar**.

5. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

6. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://azure.<domain name>.link`
    
    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://azure.<domain name>.link`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Os valores anteriores não são valores reais. Atualize os valores com o URL real e o identificador da página de configuração de servidor da Tableau, que é explicada mais tarde no tutorial.

7. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png)

### <a name="configure-tableau-server-single-sign-on"></a>Configurar o Tableau Server início de sessão único 

1. Para obter SSO configurado para a sua aplicação, terá de iniciar sessão com o seu inquilino do servidor da Tableau como administrador.

2. Na **CONFIGURATION** separador, selecione **identidade de utilizador e acesso**e, em seguida, selecione o **autenticação** guia de método.

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Sobre o **configuração** página, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Para **método de autenticação**, selecione o SAML.
    
    b. Selecione a caixa de seleção de **ativar a autenticação de SAML para o servidor**.

    c. URL de retorno de servidor da tableau — o URL que os utilizadores do servidor da Tableau irão aceder aos, tais como <http://tableau_server>. Usando `http://localhost` não é recomendado. Utilizar um URL com um traço à direita (por exemplo, `http://tableau_server/`) não é suportada. Cópia **URL de retorno de servidor da Tableau** e cole-o para o Azure AD **URL de início de sessão** caixa de texto no **Tableau Server domínio e URLs** secção.

    d. ID de entidade SAML, o ID da entidade identifica de forma a instalação do servidor da Tableau para o IdP. Pode introduzir o URL de servidor da Tableau novamente aqui, se assim o desejar, mas ele não tem de ser o URL de servidor da Tableau. Cópia **ID de entidade SAML** e cole-o para o Azure AD **identificador** caixa de texto no **Tableau Server domínio e URLs** secção.

    e. Clique nas **transferir o ficheiro de metadados XML** e abri-lo no aplicativo de editor de texto. Localize o URL de serviço de consumidor de asserção com Http Post e de índice 0 e copie o URL. Agora cole-o para o Azure AD **URL de resposta** na caixa de texto **Tableau Server domínio e URLs** secção.

    f. Localize o ficheiro de metadados de Federação transferido a partir do portal do Azure e, em seguida, carregue-na **o ficheiro de metadados de SAML Idp**.

    g. Introduza os nomes para os atributos que o IdP utiliza para armazenar os nomes de utilizador, nomes a apresentar e endereços de e-mail.

    h. Clicar em **Guardar**

    >[!NOTE] 
    >Cliente tem de carregar qualquer certificado na configuração da Tableau Server SAML SSO e irá obter ignorada no fluxo SSO.
    >Se precisar de ajudar a configurar o SAML no servidor da Tableau, em seguida, leia este artigo [configurar SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](common/create-aaduser-01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](common/create-aaduser-02.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.
  
### <a name="create-tableau-server-test-user"></a>Criar utilizador de teste do servidor da Tableau

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no servidor da Tableau. Precisa de aprovisionar todos os utilizadores no servidor Tableau. 

Esse nome de utilizador do utilizador deve corresponder ao valor que tiver configurado no atributo personalizado do Azure AD **nome de utilizador**. Com o mapeamento correto, a integração deve trabalhar Configuring Azure AD Single Sign-On.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar o administrador de servidor da Tableau na sua organização.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao servidor da Tableau.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Tableau Server**.

    ![Configurar o início de sessão único](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo, selecione a **atribuir** botão.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de servidor da Tableau no painel de acesso, deve obter automaticamente com sessão iniciada para a aplicação de servidor da Tableau.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
