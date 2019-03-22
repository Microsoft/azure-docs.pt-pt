---
title: 'Tutorial: Integração do Active Directory do Azure com Peakon | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b841cbe838156cd00876a9a13c7a9cbe1790ea80
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57842216"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Tutorial: Integração do Active Directory do Azure com Peakon

Neste tutorial, saiba como integrar Peakon com o Azure Active Directory (Azure AD).

Integrar Peakon no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Peakon.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Peakon (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Peakon, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Peakon logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Peakon da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-peakon-from-the-gallery"></a>Adicionando Peakon da Galeria

Para configurar a integração do Peakon com o Azure AD, terá de adicionar Peakon a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Peakon a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Peakon**, selecione **Peakon** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Peakon na lista de resultados](./media/peakon-tutorial/tutorial_peakon_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Peakon com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Peakon a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Peakon deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Peakon, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste Peakon](#creating-a-peakon-test-user)**  - para ter um equivalente da Eduarda Almeida na Peakon que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Peakon.

**Para configurar o Azure AD início de sessão único com Peakon, execute os seguintes passos:**

1. No portal do Azure, sobre o **Peakon** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **configuração de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Peakon domínio e URLs únicas início de sessão em informações](./media/peakon-tutorial/tutorial_peakon_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://app.peakon.com/saml/<companyid>/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://app.peakon.com/saml/<companyid>/assert`

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Peakon domínio e URLs únicas início de sessão em informações](./media/peakon-tutorial/tutorial_peakon_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL: `https://app.peakon.com/login`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta que é explicado latert no tutorial.

6. Na **certificado de assinatura SAML** secção, clique em **transferir** transferir **certificado (bruto)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/peakon-tutorial/tutorial_peakon_certificate.png) 

7. Sobre o **configurar Peakon** secção, copie o URL adequado, de acordo com seus requisitos.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração de Peakon](common/configuresection.png)

8. Numa janela do browser web diferente, inicie sessão no Peakon como um administrador.

9. Na barra de menu no lado esquerdo da página, clique em **Configuration**, em seguida, navegue até à **integrações**.

    ![A configuração](./media/peakon-tutorial/tutorial_peakon_config.png)

10. No **integrações** página, clique em **Single Sign-On**.

    ![A única](./media/peakon-tutorial/tutorial_peakon_single.png)

11. Sob **início de sessão único** secção, clique em **ativar**.

    ![A ativar](./media/peakon-tutorial/tutorial_peakon_enable.png)

12. Sobre o **início de sessão único para os funcionários através de SAML** secção, execute os seguintes passos:

    ![O saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Na **URL de início de sessão de SSO** caixa de texto, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    b. Na **URL de fim de sessão de SSO** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    c. Clique em **Escolher ficheiro** para carregar o certificado que transferiu do portal do Azure, na caixa de certificado.

    d. Clique nas **ícone** para copiar o **ID de entidade** e cole no **identificador** caixa de texto no **configuração básica de SAML** secção no portal do Azure.

    e. Clique nas **ícone** para copiar o **URL de resposta (ACS)** e cole na **URL de resposta** caixa de texto no **configuração básica de SAML**   secção no portal do Azure.

    f. Clicar em **Guardar**

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

### <a name="creating-a-peakon-test-user"></a>Criar um utilizador de teste Peakon

Para ativar os utilizadores do Azure AD iniciar sessão no Peakon, tem de ser aprovisionados em Peakon.  
No caso de Peakon, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Peakon como um administrador.

2. Na barra de menu no lado esquerdo da página, clique em **Configuration**, em seguida, navegue até à **funcionários**.

    ![O funcionário](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. No canto superior direito da página, clique em **funcionário adicionar**.

      ![O funcionário de adicionar](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Sobre o **novo funcionário** caixa de diálogo página, execute os seguintes passos:

     ![O novo funcionário](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Na **Name** caixa de texto, nome do primeiro tipo como **Eduarda** e o apelido como **simon**.

    b. Na **E-Mail** caixa de texto, escreva o endereço de e-mail, como **Brittasimon\@contoso.com**.

    c. Clique em **funcionário criar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Peakon.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Peakon**.

    ![Configurar o início de sessão único](./media/peakon-tutorial/tutorial_peakon_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo, selecione a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Peakon no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Peakon.
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
