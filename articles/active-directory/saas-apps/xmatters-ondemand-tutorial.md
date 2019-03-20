---
title: 'Tutorial: Integração do Active Directory do Azure com xMatters OnDemand | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e xMatters OnDemand.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b5ec711f0e43d9d29d962d43ed8b1d86338db87
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881219"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Integração do Active Directory do Azure com xMatters OnDemand

Neste tutorial, saiba como integrar xMatters OnDemand com o Azure Active Directory (Azure AD).

Integrar xMatters OnDemand no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao xMatters OnDemand
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para xMatters OnDemand (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com xMatters OnDemand, terá dos seguintes itens:

- Uma subscrição do Azure
- Um xMatters OnDemand logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando xMatters OnDemand da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Adicionando xMatters OnDemand da Galeria
Para configurar a integração do xMatters OnDemand para o Azure AD, terá de adicionar xMatters OnDemand a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar xMatters OnDemand a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **xMatters OnDemand**.

    ![Criar um utilizador de teste do Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_search.png)

1. No painel de resultados, selecione **xMatters OnDemand**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com xMatters que ondemand com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa de saber o que o utilizador de contraparte xMatters OnDemand for para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no xMatters OnDemand deve ser estabelecido.

XMatters OnDemand, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com xMatters OnDemand, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de OnDemand xMatters](#creating-a-xmatters-ondemand-test-user)**  - para ter um equivalente da Eduarda Almeida na xMatters OnDemand que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão único no portal do Azure e configurar o início de sessão único na sua xMatters aplicação OnDemand.

**Para configurar o Azure AD início de sessão único com xMatters OnDemand, execute os seguintes passos:**

1. No portal do Azure, sobre o **xMatters OnDemand** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Configurar o início de sessão único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_samlbase.png)

1. Sobre o **xMatters OnDemand domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_url.png)
    
    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au/`|
    | `https://<companyname>.cs1.xmatters.com/`|
    | `https://<companyname>.xmatters.com/`|
    | `https://www.xmatters.com`|
    | `https://<companyname>.xmatters.com.au/`|

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.au1.xmatters.com.au`|
    | `https://<companyname>.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>`|
    | `https://<companyname>.au1.xmatters.com.au/<instancename>`|

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte de xMatters OnDemand](https://www.xmatters.com/company/contact-us/) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado localmente como **c:\\XMatters OnDemand.cer**.

    ![Configurar o início de sessão único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_certificate.png)

    > [!IMPORTANT]
    > Tem de reencaminhar o certificado para o [equipa de suporte de xMatters OnDemand](https://www.xmatters.com/company/contact-us/). O certificado tem de ser carregado pela equipe de suporte do xMatters antes de pode finalizar a configuração de início de sessão única. 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/xmatters-ondemand-tutorial/tutorial_general_400.png)

1. Sobre o **xMatters configuração OnDemand** secção, clique em **configurar xMatters OnDemand** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa XMatters OnDemand como administrador.

1. Na barra de ferramentas na parte superior, clique em **administrador**e, em seguida, clique em **detalhes da empresa** na barra de navegação no lado esquerdo.

    ![Admin](./media/xmatters-ondemand-tutorial/IC776795.png "Admin")

1. Sobre o **configuração SAML do** página, execute os seguintes passos:

    ![Configuração de SAML](./media/xmatters-ondemand-tutorial/IC776796.png "configuração de SAML")

    a. Selecione **ativar SAML**.

    b. Na **ID do fornecedor de identidade** caixa de texto, colar **ID de entidade de SAML** valor que copiou do portal do Azure.

    c. Na **início de sessão único na URL** caixa de texto, colar **SAML único início de sessão no URL do serviço** valor que copiou do portal do Azure.

    d. Na **URL de fim de sessão único** caixa de texto, colar **URL de fim de sessão**, que copiou do portal do Azure.

    e. Na página de detalhes da empresa, na parte superior, clique em **guardar alterações**.

    ![Detalhes de empresa](./media/xmatters-ondemand-tutorial/IC776797.png "da empresa detalhes")

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.

    ![Criar um utilizador de teste do Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.

    ![Criar um utilizador de teste do Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Criar um utilizador de teste do Azure AD](./media/xmatters-ondemand-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.

### <a name="creating-a-xmatters-ondemand-test-user"></a>Criar um utilizador de teste de OnDemand xMatters

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no xMatters OnDemand.

**Se precisar de criar manualmente o utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **XMatters OnDemand** inquilino.

1. Clique em **usuários** separador e clique em **adicionar utilizador**.

   ![Os utilizadores](./media/xmatters-ondemand-tutorial/IC781048.png "utilizadores")

1. Na **adicionar um utilizador** secção, execute os seguintes passos:

    ![Adicionar um usuário](./media/xmatters-ondemand-tutorial/IC781049.png "adicionar um utilizador")

    a. Selecione **Active Directory**.

    b. Na **ID de utilizador** caixa de texto, como o tipo o id de utilizador do utilizador Brittasimon@contoso.com.

    c. Na **nome próprio** caixa de texto, tipo primeiro nome do utilizador, como a Eduarda.

    d. Na **Apelido** apelido tipo do utilizador, como Simon, caixa de texto.

    e. Na **Site** caixa de texto, introduza o site válido de um Azure válido conta AD que pretende aprovisionar.

    f. Clique em **Guardar**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos xMatters OnDemand.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a xMatters OnDemand, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **xMatters OnDemand**.

    ![Configurar o início de sessão único](./media/xmatters-ondemand-tutorial/tutorial_xmattersondemand_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o xMatters OnDemand mosaico no painel de acesso, deve obter automaticamente com sessão iniciada para sua xMatters aplicação OnDemand.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/xmatters-ondemand-tutorial/tutorial_general_01.png
[2]: ./media/xmatters-ondemand-tutorial/tutorial_general_02.png
[3]: ./media/xmatters-ondemand-tutorial/tutorial_general_03.png
[4]: ./media/xmatters-ondemand-tutorial/tutorial_general_04.png

[100]: ./media/xmatters-ondemand-tutorial/tutorial_general_100.png

[200]: ./media/xmatters-ondemand-tutorial/tutorial_general_200.png
[201]: ./media/xmatters-ondemand-tutorial/tutorial_general_201.png
[202]: ./media/xmatters-ondemand-tutorial/tutorial_general_202.png
[203]: ./media/xmatters-ondemand-tutorial/tutorial_general_203.png
