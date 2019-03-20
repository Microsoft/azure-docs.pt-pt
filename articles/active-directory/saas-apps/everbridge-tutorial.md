---
title: 'Tutorial: Integração do Active Directory do Azure com EverBridge | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e EverBridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1bb62c9a11971f72a6c96c4652b136c19812cb3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839326"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Tutorial: Integração do Active Directory do Azure com EverBridge

Neste tutorial, saiba como integrar EverBridge com o Azure Active Directory (Azure AD).

Integrar EverBridge no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao EverBridge.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para EverBridge (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com EverBridge, terá dos seguintes itens:

- Uma subscrição do Azure
- Um EverBridge logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando EverBridge da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-everbridge-from-the-gallery"></a>Adicionando EverBridge da Galeria

Para configurar a integração do EverBridge com o Azure AD, terá de adicionar EverBridge a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar EverBridge a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **EverBridge**, selecione **EverBridge** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![EverBridge na lista de resultados](./media/everbridge-tutorial/tutorial_everbridge_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com EverBridge com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no EverBridge a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no EverBridge deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com EverBridge, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste EverBridge](#creating-an-everbridge-test-user)**  - para ter um equivalente da Eduarda Almeida na EverBridge que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo EverBridge.

**Para configurar o Azure AD início de sessão único com EverBridge, execute os seguintes passos:**

1. No portal do Azure, sobre o **EverBridge** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

    >[!NOTE]
    >Terá de fazer as configurações do aplicativo como o Manager Portal ou do portal do membro em ambas as extremidades ou seja, no Portal do Azure e o Portal de Everbridge.

4. Para configurar o **EverBridge** como **Portal EverBridge Manager**, no **configuração básica de SAML** secção execute os seguintes passos:

    ![EverBridge domínio e URLs únicas início de sessão em informações](./media/everbridge-tutorial/tutorial_everbridge_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://sso.everbridge.net/<API_Name>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de EverBridge](mailto:support@everbridge.com) obter esses valores.

5. Para configurar o **EverBridge** como **EverBridge membro Portal**, no **configuração básica de SAML** secção, execute os seguintes passos:

   * Se desejar configurar a aplicação no **IDP** iniciada pelo modo:

       ![EverBridge domínio e URLs únicas início de sessão em informações](./media/everbridge-tutorial/tutorial_everbridge_url1.png)

       * Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://sso.everbridge.net/<API_Name>/<Organization_ID>`

       * Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

       ![EverBridge domínio e URLs únicas início de sessão em informações](./media/everbridge-tutorial/tutorial_everbridge_url2.png)

       * Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Estes valores não são reais. Atualize estes valores com o identificador real, o URL de resposta e URL de início de sessão. Contacte [equipa de suporte de EverBridge](mailto:support@everbridge.com) obter esses valores.

6. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/everbridge-tutorial/tutorial_everbridge_certificate.png) 

7. Sobre o **configurar EverBridge** secção, copie o URL adequado, de acordo com seus requisitos.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração de EverBridge](common/configuresection.png)

8. Para obter SSO configurado para **EverBridge** como **Portal EverBridge Manager** aplicativo, execute os seguintes passos: 
 
9. Numa janela do browser web diferente, início de sessão para EverBridge como administrador.

9. No menu na parte superior, clique nas **definições** separador e selecione **Single Sign-On** sob **segurança**.
   
     ![Configurar o início de sessão único](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Na **nome** caixa de texto, escreva o nome do identificador de fornecedor (por exemplo: nome da sua empresa).
   
     b. Na **nome da API** caixa de texto, escreva o nome da API.
   
     c. Clique em **Escolher ficheiro** botão para carregar o ficheiro de metadados que transferiu a partir do portal do Azure.
   
     d. Na localização de identidade de SAML, selecione **identidade é no elemento NameIdentifier da declaração de assunto**.
   
     e. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de início de sessão** que copiou do portal do Azure.
   
     f. No fornecedor iniciada solicitar vínculo de serviço, selecione **redirecionamento de HTTP**.

     g. Clicar em **Guardar**

10. Para configurar o início de sessão único num **EverBridge** como **EverBridge membro Portal**, terá de enviar o transferido **XML de metadados de Federação** para [ A equipa de suporte Everbridge](mailto:support@everbridge.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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
  
### <a name="creating-an-everbridge-test-user"></a>Criar um utilizador de teste EverBridge

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Everbridge. Trabalhar com [equipa de suporte de EverBridge](mailto:support@everbridge.com) para adicionar os utilizadores na plataforma Everbridge.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para EverBridge.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **EverBridge**.

    ![Configurar o início de sessão único](./media/everbridge-tutorial/tutorial_everbridge_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico EverBridge no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo EverBridge.
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
