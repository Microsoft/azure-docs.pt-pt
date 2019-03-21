---
title: 'Tutorial: Integração do Active Directory do Azure com HubSpot | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e HubSpot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bbb307654d4aaf753a4a3284875dee4f5707f2a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57901739"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Tutorial: Integração do Active Directory do Azure com HubSpot

Neste tutorial, saiba como integrar HubSpot com o Azure Active Directory (Azure AD).

Integrar HubSpot no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao HubSpot
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para HubSpot (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com HubSpot, terá dos seguintes itens:

- Uma subscrição do Azure
- Um HubSpot logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando HubSpot da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-hubspot-from-the-gallery"></a>Adicionando HubSpot da Galeria

Para configurar a integração do HubSpot com o Azure AD, terá de adicionar HubSpot a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar HubSpot a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

4. Na caixa de pesquisa, escreva **HubSpot**. Selecione **HubSpot** no painel de resultados e, em seguida, clique o **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/hubspot-tutorial/tutorial_hubspot_addfromgallery.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com HubSpot com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no HubSpot a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no HubSpot deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com HubSpot, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **Criar um utilizador de teste HubSpot** - para ter um equivalente da Eduarda Almeida na HubSpot que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo HubSpot.

**Para configurar o Azure AD início de sessão único com HubSpot, execute os seguintes passos:**

1. No portal do Azure, sobre o **HubSpot** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/tutorial_general_301.png)

3. Se precisar de alterar para **SAML** modo de qualquer outro modo, clique em **alterar único início de sessão em modo** na parte superior do ecrã.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/tutorial_general_300.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/tutorial_general_302.png)

5. Na **configuração básica de SAML** secção execute os seguintes passos, se desejar configurar a aplicação na **IDP** iniciada pelo modo:

    ![HubSpot domínio e URLs únicas início de sessão em informações](./media/hubspot-tutorial/tutorial_hubspot_url.png)

    a. Na **identificador** caixa de texto, digite a URL usando o seguinte padrão: `https://api.hubspot.com/login-api/v1/saml/login?portalId=<CUSTOMER ID>`

    b. Na **URL de resposta** caixa de texto, digite a URL usando o seguinte padrão: `https://api.hubspot.com/login-api/v1/saml/acs?portalId=<CUSTOMER ID>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta que é explicado posteriormente neste tutorial. Contacte [equipa de suporte de cliente HubSpot](https://help.hubspot.com/) obter esses valores.

    c. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![HubSpot domínio e URLs únicas início de sessão em informações](./media/hubspot-tutorial/tutorial_hubspot_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva o URL: `https://app.hubspot.com/login`

6. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/tutorial_hubspot_certificate.png)

7. Sobre o **configurar HubSpot** secção, clique no botão Copiar para copiar o **URL de início de sessão** e **identificador do Azure AD** valores.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/tutorial_hubspot_configure.png)

8. Abra um novo separador no seu browser e inicie sessão na sua conta de administrador HubSpot.

9. Clique em **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/config1.png)

10. Clique em **predefinições de conta**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/config2.png)

11. Desloque para baixo para o **Security** secção e clique em **configurar**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/config3.png)

12. Sobre o **configurar o início de sessão único** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/hubspot-tutorial/config4.png)

    a. Clique em **cópia** botão para copiar o **público-alvo URl(Service Provider Entity ID)** valor e cole-o no **identificador** caixa de texto no **SAML básico Configuração** secção no portal do Azure.

    b. Clique em **cópia** botão para copiar o **iniciar sessão no URl, o ACS, o destinatário ou o redirecionamento** valor e cole-o no **URL de resposta** caixa de texto no **SAML básico Configuração** secção no portal do Azure.

    c. Na **identificador do fornecedor de identidade ou URL do emissor** caixa de texto, colar a **do Azure AD identificador** valor que copiou do portal do Azure.

    d. Na **URL fornecedor de identidade única Sign-On** caixa de texto, colar a **URL de início de sessão** valor que copiou do portal do Azure.

    e. Abra sua transferido **Certificate(Base64)** ficheiro no bloco de notas. Copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa.

    f. Clique em **Verificar**.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](./media/hubspot-tutorial/create_aaduser_01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](./media/hubspot-tutorial/create_aaduser_02.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.

### <a name="creating-a-hubspot-test-user"></a>Criar um utilizador de teste HubSpot

Para ativar a utilizadores do Azure AD iniciar sessão no HubSpot, tem de ser aprovisionados em HubSpot.
No caso de HubSpot, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **HubSpot** site da empresa como administrador.

2. Clique em **ícone de definições** no canto superior direito da página.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/config1.png)

3. Clique em **utilizadores e as Equipes**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/user1.png)

4. Clique em **criar utilizador**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/user2.png)

5. Introduza o endereço de e-mail do utilizador, como **brittasimon\@contoso.com** no **adicionar e-mail addess(es)** caixa de texto e clique em **seguinte**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/user3.png)

6. Sobre o **criar utilizadores** secção, percorra a cada guia individual e selecione as opções apropriadas e as permissões para o utilizador e clique em **próxima**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/user4.png)

7. Clique em **enviar** para enviar o convite ao utilizador.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > O utilizador irá obter ativado depois de aceitar o convite.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para HubSpot.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **HubSpot**.

    ![Configurar o início de sessão único](./media/hubspot-tutorial/tutorial_hubspot_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico HubSpot no painel de acesso, deve obter automaticamente a página de início de sessão de aplicativo HubSpot.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/hubspot-tutorial/tutorial_general_01.png
[2]: ./media/hubspot-tutorial/tutorial_general_02.png
[3]: ./media/hubspot-tutorial/tutorial_general_03.png
[4]: ./media/hubspot-tutorial/tutorial_general_04.png
[100]: ./media/hubspot-tutorial/tutorial_general_100.png
[200]: ./media/hubspot-tutorial/tutorial_general_200.png
[201]: ./media/hubspot-tutorial/tutorial_general_201.png
[202]: ./media/hubspot-tutorial/tutorial_general_202.png
[203]: ./media/hubspot-tutorial/tutorial_general_203.png
