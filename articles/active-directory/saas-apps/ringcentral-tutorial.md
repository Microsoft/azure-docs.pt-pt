---
title: 'Tutorial: Integração do Active Directory do Azure com RingCentral | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e RingCentral.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d86dc27822c91bd6b2b1cb3ec53b07978061cc7d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437365"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Tutorial: Integração do Active Directory do Azure com RingCentral

Neste tutorial, saiba como integrar RingCentral com o Azure Active Directory (Azure AD).

Integrar RingCentral no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao RingCentral.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para RingCentral (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com RingCentral, terá dos seguintes itens:

- Uma subscrição do Azure
- Um RingCentral logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando RingCentral da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-ringcentral-from-the-gallery"></a>Adicionando RingCentral da Galeria
Para configurar a integração do RingCentral com o Azure AD, terá de adicionar RingCentral a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar RingCentral a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. Na caixa de pesquisa, escreva **RingCentral**, selecione **RingCentral** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com RingCentral com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no RingCentral a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no RingCentral deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com RingCentral, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste RingCentral](#create-a-ringcentral-test-user)**  - para ter um equivalente da Eduarda Almeida na RingCentral que está ligado à representação de utilizador do Azure AD.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo RingCentral.

**Para configurar o Azure AD início de sessão único com RingCentral, execute os seguintes passos:**

1. Na [portal do Azure](https://portal.azure.com/), na **RingCentral** página de integração de aplicações, selecione **início de sessão único**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Clique em **alterar único início de sessão em modo** na parte superior do ecrã para selecionar o **SAML** modo.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML** modo para ativar o início de sessão único.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **configuração básica de SAML** caixa de diálogo.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Sobre o **configuração básica de SAML** secção, se tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    a. Clique em **carregamento de ficheiro de metadados**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção caixa de texto, conforme mostrado abaixo :

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. Na **iniciar sessão no URL** caixa de texto, escreva um URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Obtém o **ficheiro de metadados do fornecedor de serviços** na página de configuração de SSO RingCentral do que é explicada mais tarde no tutorial.

6. Se não tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos:

    a. Na **iniciar sessão no URL** caixa de texto, escreva um URL:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Na **identificador** caixa de texto, escreva um URL:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Na **URL de resposta** caixa de texto, escreva um URL:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em **transferir** para transferir o certificado de entre as opções de determinado, de acordo sua requisito e guarde-o no seu computador.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. Numa janela do browser web diferente, início de sessão para RingCentral como um administrador de segurança.

9. Na parte superior, clique em **ferramentas**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Navegue para **início de sessão único**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Na **início de sessão único** página, em **configuração de SSO** secção, do **passo 1** clique em **editar** e execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Sobre o **configurar o início de sessão único** página, execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Clique em **procurar** para carregar o ficheiro de metadados que transferiu a partir do portal do Azure.

    b. Depois de carregar metadados os valores ficam preenchidos automaticamente no **informações gerais de SSO** secção.

    c. Sob **mapeamento do atributo** secção, selecione **atributo de correio eletrónico de mapa para** como `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Clique em **Guardar**.

    e. Partir **passo 2** clique em **transferir** para transferir o **ficheiro de metadados do fornecedor de serviços** e carregá-la no **configuração básica de SAML** secção para preencher o automática a **identificador** e **URL de resposta** valores no portal do Azure.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Na mesma página, navegue até **ativar o SSO** secção e execute os seguintes passos:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Selecione **ativar o serviço SSO**.
    
    b. Selecione **permitir que os utilizadores iniciar sessão com credenciais SSO ou RingCentral**.

    c. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.
 
### <a name="create-a-ringcentral-test-user"></a>Criar um utilizador de teste RingCentral

Nesta secção, vai criar um usuário chamado Eduarda Almeida no RingCentral. Trabalhar com [equipa de suporte de cliente RingCentral](https://success.ringcentral.com/RCContactSupp) para adicionar os utilizadores na plataforma RingCentral. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para RingCentral.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Na lista de aplicações, selecione **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Selecione o **Add** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

5. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico RingCentral no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo RingCentral.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

