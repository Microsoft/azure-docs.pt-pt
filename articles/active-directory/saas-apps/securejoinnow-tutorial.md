---
title: 'Tutorial: Integração do Active Directory do Azure com o conector de JoinNow SecureW2 | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SecureW2 JoinNow conector.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 2445b3af-f827-40de-9097-6f5c933d0f53
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b367befb90ec28ece963d67b479749e1c8ad363
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60340004"
---
# <a name="tutorial-azure-active-directory-integration-with-securew2-joinnow-connector"></a>Tutorial: Integração do Active Directory do Azure com o conector de JoinNow SecureW2

Neste tutorial, saiba como integrar SecureW2 JoinNow conector com o Azure Active Directory (Azure AD).

Integrar SecureW2 JoinNow conector com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao SecureW2 JoinNow conector.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o conector de JoinNow SecureW2 (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SecureW2 JoinNow conector, terá dos seguintes itens:

- Uma subscrição do Azure
- Um conector de JoinNow SecureW2 logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SecureW2 JoinNow conector da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-securew2-joinnow-connector-from-the-gallery"></a>Adicionando SecureW2 JoinNow conector da Galeria
Para configurar a integração de SecureW2 JoinNow conector com o Azure AD, terá de adicionar SecureW2 JoinNow conector a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SecureW2 JoinNow conector a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **SecureW2 JoinNow conector**, selecione **SecureW2 JoinNow conector** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![SecureW2 JoinNow conector na lista de resultados](./media/securejoinnow-tutorial/tutorial_securejoinnow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o conector de JoinNow SecureW2 com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no SecureW2 JoinNow conector a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SecureW2 JoinNow conector tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com SecureW2 JoinNow conector, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste do conector de JoinNow SecureW2](#create-a-securew2-joinnow-connector-test-user)**  - para ter um equivalente da Eduarda Almeida no SecureW2 JoinNow conector que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo SecureW2 JoinNow conector.

**Para configurar o Azure AD início de sessão único com o conector de JoinNow SecureW2, execute os seguintes passos:**

1. No portal do Azure, sobre o **SecureW2 JoinNow conector** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/securejoinnow-tutorial/tutorial_securejoinnow_samlbase.png)

3. Sobre o **SecureW2 JoinNow conector do domínio e URLs** secção, execute os seguintes passos:

    ![SecureW2 JoinNow conector do domínio e URLs únicas início de sessão em informações](./media/securejoinnow-tutorial/tutorial_securejoinnow_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<organization-identifier>-auth.securew2.com/auth/saml/SSO`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<organization-identifier>-auth.securew2.com/auth/saml`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de conector de JoinNow SecureW2](mailto:support@securew2.com) obter esses valores. 

4. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/securejoinnow-tutorial/tutorial_securejoinnow_certificate.png) 

5. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/securejoinnow-tutorial/tutorial_general_400.png)

6. Para configurar o início de sessão único num **SecureW2 JoinNow conector** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte do conector de JoinNow SecureW2](mailto:support@securew2.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/securejoinnow-tutorial/create_aaduser_01.png)

2. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/securejoinnow-tutorial/create_aaduser_02.png)

3. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/securejoinnow-tutorial/create_aaduser_03.png)

4. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/securejoinnow-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-securew2-joinnow-connector-test-user"></a>Criar um utilizador de teste SecureW2 JoinNow conector

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SecureW2 JoinNow conector. Trabalhar com [equipa de suporte de cliente de conector de JoinNow SecureW2](mailto:support@securew2.com) para adicionar os utilizadores na plataforma SecureW2 JoinNow conector. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao SecureW2 JoinNow conector.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a SecureW2 JoinNow conector, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

2. Na lista de aplicações, selecione **SecureW2 JoinNow conector**.

    ![A ligação de SecureW2 JoinNow conector na lista de aplicações](./media/securejoinnow-tutorial/tutorial_securejoinnow_app.png)  

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

5. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

6. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

7. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

**Para testar a aplicação, execute os seguintes passos:** 

a. Abra o cliente de SecureW2 JoinNow conector, selecione o seu dispositivo apropriado na lista e clique em **sessão** botão.

b. O browser predefinido deve ser aberto e deve estar novamente direcionado para o portal do Azure para a autenticação.

c. Na autenticação com êxito, ele deverá retornar para a página de destino inicial de SecureW2 JoinNow conector.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/securejoinnow-tutorial/tutorial_general_01.png
[2]: ./media/securejoinnow-tutorial/tutorial_general_02.png
[3]: ./media/securejoinnow-tutorial/tutorial_general_03.png
[4]: ./media/securejoinnow-tutorial/tutorial_general_04.png

[100]: ./media/securejoinnow-tutorial/tutorial_general_100.png

[200]: ./media/securejoinnow-tutorial/tutorial_general_200.png
[201]: ./media/securejoinnow-tutorial/tutorial_general_201.png
[202]: ./media/securejoinnow-tutorial/tutorial_general_202.png
[203]: ./media/securejoinnow-tutorial/tutorial_general_203.png

