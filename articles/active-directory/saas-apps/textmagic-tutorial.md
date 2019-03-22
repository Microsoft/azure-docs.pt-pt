---
title: 'Tutorial: Integração do Active Directory do Azure com TextMagic | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a91d6878f509f47f029a3b8aaf7bbbe4e6f35435
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57869800"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Tutorial: Integração do Active Directory do Azure com TextMagic

Neste tutorial, saiba como integrar TextMagic com o Azure Active Directory (Azure AD).

Integrar TextMagic no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao TextMagic.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para TextMagic (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TextMagic, terá dos seguintes itens:

- Uma subscrição do Azure
- Um TextMagic logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando TextMagic da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-textmagic-from-the-gallery"></a>Adicionando TextMagic da Galeria

Para configurar a integração do TextMagic com o Azure AD, terá de adicionar TextMagic a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TextMagic a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **TextMagic**, selecione **TextMagic** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![TextMagic na lista de resultados](./media/textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com TextMagic com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no TextMagic a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TextMagic deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com TextMagic, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste TextMagic](#creating-a-textmagic-test-user)**  - para ter um equivalente da Eduarda Almeida na TextMagic que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo TextMagic.

**Para configurar o Azure AD início de sessão único com TextMagic, execute os seguintes passos:**

1. No portal do Azure, sobre o **TextMagic** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![TextMagic domínio e URLs únicas início de sessão em informações](./media/textmagic-tutorial/tutorial_textmagic_url.png)

    Na **identificador** caixa de texto, escreva um URL: `https://my.textmagic.com/saml/metadata`

5. Aplicação de TextMagic espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador e afirmações** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador e afirmações** caixa de diálogo.

    ![image](./media/textmagic-tutorial/i4-attribute.png)

6. No **afirmações de utilizador** secção sobre o **atributos de utilizador e afirmações** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name  | Atributo de origem  | Espaço de nomes |
    | --------------- | --------------- | --------------- |
    | Empresa | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefone               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    
    a. Clique em **ícone de edição** para editar o **o valor do identificador de nome** partir **user.userprinicipalname** para **user.mail**.

    ![Atributo de TextMagic](./media/textmagic-tutorial/tutorial_textmagic_email.png)

    b. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    c. Na **nome**caixa de texto, escreva o nome de atributo apresentado para essa linha.

    d. Introduza o **espaço de nomes** valor.

    e. Selecione a origem de dado **atributo**.

    f. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    g. Clique em **OK**.

    h. Clique em **Guardar**. 

7. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/textmagic-tutorial/tutorial_textmagic_certificate.png) 

8. Sobre o **configurar TextMagic** secção, copie o URL adequado, de acordo com seus requisitos.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração de TextMagic](common/configuresection.png)

9. Numa janela do browser web diferente, inicie sessão no site da sua empresa TextMagic como administrador.

10. Selecione **definições da conta** sob o nome de utilizador.

    ![Configuração de TextMagic](./media/textmagic-tutorial/config1.png)

11. Clique no SEPARADOR **único início de sessão (SSO)** e preencha os campos seguintes:  
    
    ![Configuração de TextMagic](./media/textmagic-tutorial/config2.png)

    a. Na **ID de entidade do fornecedor de identidade:** caixa de texto, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    b. Na **fornecedor de identidade SSO URL:** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    c. Na **fornecedor de identidade SLO URL:** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Abra sua **certificado com codificação base 64** no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado público x509:** caixa de texto.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_02.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.
  
### <a name="creating-a-textmagic-test-user"></a>Criar um utilizador de teste TextMagic

Aplicação suporta **Just-in de aprovisionamento de utilizadores de tempo** e depois dos utilizadores de autenticação serão criados no aplicativo automaticamente. Tem de preencher as informações de uma vez no primeiro início de sessão para ativar a conta secundárias no sistema.
Não existe nenhum item de ação para nesta secção.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TextMagic.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **TextMagic**.

    ![Configurar o início de sessão único](./media/textmagic-tutorial/tutorial_textmagic_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico TextMagic no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo TextMagic.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
