---
title: 'Tutorial: Integração do Active Directory do Azure com o Infor CloudSuite | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Infor CloudSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a2f4f843-00d2-4522-a29d-6496cc5a781a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d7e912d01b6f8b7119e0fa0e88341b7cb56a93d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57860674"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-cloudsuite"></a>Tutorial: Integração do Active Directory do Azure com o Infor CloudSuite

Neste tutorial, saiba como integrar o Infor CloudSuite com o Azure Active Directory (Azure AD).

Integrar o Infor CloudSuite no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Infor CloudSuite.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Infor CloudSuite (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Infor CloudSuite, terá dos seguintes itens:

- Uma subscrição do Azure
- Infor CloudSuite logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Infor CloudSuite da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-infor-cloudsuite-from-the-gallery"></a>Adicionando Infor CloudSuite da Galeria

Para configurar a integração do Infor CloudSuite com o Azure AD, terá de adicionar Infor CloudSuite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Infor CloudSuite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Infor CloudSuite**, selecione **Infor CloudSuite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Infor CloudSuite na lista de resultados](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Infor CloudSuite com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Infor CloudSuite a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Infor CloudSuite deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o Infor CloudSuite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar utilizador de teste Infor CloudSuite](#creating-infor-cloudsuite-test-user)**  - para ter um equivalente da Eduarda Almeida na Infor CloudSuite que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Infor CloudSuite.

**Para configurar o Azure AD início de sessão único com o Infor CloudSuite, execute os seguintes passos:**

1. No portal do Azure, sobre o **Infor CloudSuite** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial-general-301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Infor CloudSuite domínio e URLs únicas início de sessão em informações](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url1.png)

    a. Na **identificador** caixa de texto, escreva um URL:
    
    | | |
    |-|-|
    | ` http://mingle-sso.inforcloudsuite.com`|
    | `http://mingle-sso.se1.inforcloudsuite.com`|
    | `http://mingle-sso.eu1.inforcloudsuite.com`|
    | `http://mingle-sso.se2.inforcloudsuite.com`|
    | |

    b. Na **URL de resposta** caixa de texto, escreva um URL:

    | | |
    |-|-|
    | ` https://mingle-sso.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se1.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.se2.inforcloudsuite.com:443/sp/ACS.saml2 `|
    | `https://mingle-sso.eu1.inforcloudsuite.com:443/sp/ACS.saml2`|
    | |

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Infor CloudSuite domínio e URLs únicas início de sessão em informações](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-url2.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    | | |
    |-|-|
    | `https://mingle-portal.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.eu1.inforcloudsuite.com/Tenant-Name/`|
    | `https://mingle-portal.se1.inforcloudsuite.com/Tenant-Name/ `|
    | `https://mingle-portal.se2.inforcloudsuite.com/Tenant-Name/`| 

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de CloudSuite Infor](mailto:support@infor.com) para obter este valor.

6. No **certificado de assinatura SAML** página, além da **certificado de assinatura SAML** secção, clique em **transferir** para transferir **XML de metadados de Federação** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-certificate.png)

7. Para configurar o início de sessão único num **Infor CloudSuite** lado, terá de enviar o transferido **XML de metadados de Federação** para [equipa de suporte de Infor CloudSuite](mailto:support@infor.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

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

### <a name="creating-infor-cloudsuite-test-user"></a>Criar Infor CloudSuite para o utilizador de teste

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Infor CloudSuite. Infor CloudSuite suporta just-in-time de aprovisionamento que pode ser ativado pelo administrador inquilino. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Infor CloudSuite se não existir ainda.

> [!Note]
> Se precisar de criar manualmente um utilizador, contacte [equipa de suporte de Infor CloudSuite](mailto:support@infor.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Infor CloudSuite.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Infor CloudSuite**.

    ![Configurar o início de sessão único](./media/inforcloudsuite-tutorial/tutorial-inforcloudsuite-app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Infor CloudSuite no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação Infor CloudSuite.
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
