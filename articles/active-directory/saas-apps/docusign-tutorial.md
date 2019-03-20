---
title: 'Tutorial: Integração do Active Directory do Azure com o DocuSign | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fda9df8e7781a9e0c45fb1aead9f8167f89a833
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850875"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Tutorial: Integração do Active Directory do Azure com o DocuSign

Neste tutorial, saiba como integrar o DocuSign com o Azure Active Directory (Azure AD).

Integrar o DocuSign no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao DocuSign.
- Pode permitir que os utilizadores automaticamente obter assinado no DocuSign (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o DocuSign, terá dos seguintes itens:

- Uma subscrição do Azure
- Um DocuSign logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando DocuSign da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-docusign-from-the-gallery"></a>Adicionando DocuSign da Galeria

Para configurar a integração do DocuSign para o Azure AD, terá de adicionar DocuSign a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar DocuSign a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **DocuSign**, selecione **DocuSign** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![DocuSign na lista de resultados](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o DocuSign com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no DocuSign para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no DocuSign deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o DocuSign, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste do DocuSign](#creating-a-docusign-test-user)**  - para ter um equivalente da Eduarda Almeida no DocuSign que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo do DocuSign.

**Para configurar o Azure AD início de sessão único com o DocuSign, execute os seguintes passos:**

1. No portal do Azure, sobre o **DocuSign** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Domínio da DocuSign e URLs únicas início de sessão em informações](./media/docusign-tutorial/tutorial_docusign_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão real e o identificador que é explicado mais tarde **ver SAML 2.0 pontos finais** secção do tutorial.

5. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

6. Sobre o **configurar a DocuSign** secção, copie o URL adequado, de acordo com seus requisitos.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração da DocuSign](common/configuresection.png)

7. Numa janela do browser web diferente, inicie sessão no seu **portal de administração do DocuSign** como administrador.

8. No canto superior direito da página, clique no perfil **logótipo** e, em seguida, clique em **vá para o administrador**.
  
    ![A configurar o início de sessão único][51]

9. Na página de soluções do domínio, clique em **domínios**

    ![A configurar o início de sessão único][50]

10. Sob o **domínios** secção, clique em **afirmação domínio**.

    ![A configurar o início de sessão único][52]

11. Na **um domínio de afirmação** caixa de diálogo, na **nome de domínio** caixa de texto, escreva o seu domínio da empresa e, em seguida, clique em **afirmação**. Certifique-se de que verificar o domínio e o estado estiver ativo.

    ![A configurar o início de sessão único][53]

12. Na página de soluções do domínio, clique em **fornecedores de identidade**.
  
    ![A configurar o início de sessão único][54]

13. Sob **fornecedores de identidade** secção, clique em **ADD IDENTITY PROVIDER**. 

    ![A configurar o início de sessão único][55]

14. Sobre o **definições do fornecedor de identidade** página, execute os seguintes passos:

    ![A configurar o início de sessão único][56]

    a. Na **nome** caixa de texto, escreva um nome exclusivo para a sua configuração. Não utilize espaços.

    b. Na **caixa de texto do emissor do fornecedor de identidade**, cole o valor de **do Azure AD identificador**, que copiou do portal do Azure.

    c. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    d. Na **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    e. Selecione **pedido de início de sessão AuthN**.

    f. Como **AuthN enviar pedido pelo**, selecione **POST**.

    g. Como **pedido de fim de sessão de envio pelo**, selecione **obter**.

    h. Na **mapeamento de atributos personalizado** secção, clique em **adicionar novo mapeamento**.

    ![A configurar o início de sessão único][62]

    i. Escolha o campo que deseja mapear com afirmações do Azure AD. Neste exemplo, o **emailaddress** mapeada com o valor de afirmação **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. É o nome de afirmação padrão do Azure AD para a afirmação de e-mail e clique em **guardar**.

    ![A configurar o início de sessão único][57]

    > [!NOTE]
    > Utilizar o adequado **identificador de utilizador** para mapear o utilizador do Azure AD para o mapeamento de utilizador do DocuSign. Selecione o campo apropriadas e introduza o valor apropriado com base nas suas definições de organização.

    j. Na **certificados de fornecedor de identidade** secção, clique em **ADD CERTIFICATE**e, em seguida, carregue o certificado que tenha transferido a partir do portal do Azure AD e clique em **guardar**.

    ![A configurar o início de sessão único][58]

    k. Na **fornecedores de identidade** secção, clique em **AÇÕES**e, em seguida, clique em **pontos finais**.

    ![A configurar o início de sessão único][59]

    l. Na **ver SAML 2.0 pontos finais** secção na **portal de administração do DocuSign**, execute os seguintes passos:

    ![A configurar o início de sessão único][60]

    * Cópia a **URL de emissor do fornecedor de serviço**e, em seguida, cole-o para o **identificador** caixa de texto no **DocuSign domínio e URLs** secção no portal do Azure.

    * Cópia a **URL de início de sessão do fornecedor de serviço**e, em seguida, cole-o para o **URL de início de sessão** caixa de texto no **DocuSign domínio e URLs** secção no portal do Azure.

    * Clique em **fechar**

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

### <a name="creating-a-docusign-test-user"></a>Criar um utilizador de teste da DocuSign

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no DocuSign. DocuSign suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder a DocuSign, se não existir ainda.
>[!Note]
>Se precisar de criar manualmente um utilizador, contacte [equipa de suporte do DocuSign](https://support.docusign.com/).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a DocuSign.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **DocuSign**.

    ![Configurar o início de sessão único](./media/docusign-tutorial/tutorial_docusign_app.png)

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do DocuSign no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação do DocuSign.
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
[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
