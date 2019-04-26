---
title: 'Tutorial: Integração do Active Directory do Azure com XaitPorter | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e XaitPorter.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d33c7cb7-0550-425b-882a-619a713a71b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd4f9e561a55c010273b48fb4232348baad3582
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397665"
---
# <a name="tutorial-azure-active-directory-integration-with-xaitporter"></a>Tutorial: Integração do Active Directory do Azure com XaitPorter

Neste tutorial, saiba como integrar XaitPorter com o Azure Active Directory (Azure AD).

Integrar XaitPorter no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao XaitPorter.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para XaitPorter (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com XaitPorter, terá dos seguintes itens:

- Uma subscrição do Azure
- Um XaitPorter logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando XaitPorter da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-xaitporter-from-the-gallery"></a>Adicionando XaitPorter da Galeria
Para configurar a integração do XaitPorter com o Azure AD, terá de adicionar XaitPorter a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar XaitPorter a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **XaitPorter**, selecione **XaitPorter** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![XaitPorter na lista de resultados](./media/xaitporter-tutorial/tutorial_xaitporter_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com XaitPorter com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no XaitPorter a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no XaitPorter deve ser estabelecido.

XaitPorter, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com XaitPorter, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste XaitPorter](#create-a-xaitporter-test-user)**  - para ter um equivalente da Eduarda Almeida na XaitPorter que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo XaitPorter.

**Para configurar o Azure AD início de sessão único com XaitPorter, execute os seguintes passos:**

1. No portal do Azure, sobre o **XaitPorter** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/xaitporter-tutorial/tutorial_xaitporter_samlbase.png)

1. Sobre o **XaitPorter domínio e URLs** secção, execute os seguintes passos:

    ![XaitPorter domínio e URLs únicas início de sessão em informações](./media/xaitporter-tutorial/tutorial_xaitporter_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.xaitporter.com/saml/login`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.xaitporter.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente XaitPorter](https://www.xait.com/support/) obter esses valores.
     
1. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas. 

    ![O link de download de certificado](./media/xaitporter-tutorial/tutorial_xaitporter_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/xaitporter-tutorial/tutorial_general_400.png)

1. Forneça o **endereço IP** ou o **Url de metadados de Federação de aplicação** para o [SmartRecruiters equipa de suporte](https://www.smartrecruiters.com/about-us/contact-us/), para que XaitPorter pode garantir que o endereço IP é acessível a partir do seu Instância de XaitPorter configurar a lista de permissões ao seu lado. 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa XaitPorter como administrador.

1. Clique em **administrador**.

    ![Configurar o início de sessão único](./media/xaitporter-tutorial/user1.png)

1. Selecione **gerir o início de sessão único** partir a **configuração do System** lista suspensa.

    ![Configurar o início de sessão único](./media/xaitporter-tutorial/user2.png)

1. Na **GERIR o início de sessão-único** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/xaitporter-tutorial/user3.png)

    a. Selecione **ativar a autenticação de início de sessão única**.

    b. Na **definições do fornecedor de identidade** caixa de texto, colar **Url de metadados de Federação de aplicação** que copiou do portal do Azure e clique em **buscar**.

    c. Selecione **ativar Autocreation de utilizadores**.

    d. Clique em **OK**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/xaitporter-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/xaitporter-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/xaitporter-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/xaitporter-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-xaitporter-test-user"></a>Criar um utilizador de teste XaitPorter

Nesta secção, vai criar um usuário chamado Eduarda Almeida no XaitPorter. Trabalhar com [equipa de suporte de cliente XaitPorter](https://www.xait.com/support/) para adicionar os utilizadores na plataforma XaitPorter. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para XaitPorter.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a XaitPorter, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **XaitPorter**.

    ![A ligação de XaitPorter na lista de aplicações](./media/xaitporter-tutorial/tutorial_xaitporter_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico XaitPorter no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo XaitPorter.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/xaitporter-tutorial/tutorial_general_01.png
[2]: ./media/xaitporter-tutorial/tutorial_general_02.png
[3]: ./media/xaitporter-tutorial/tutorial_general_03.png
[4]: ./media/xaitporter-tutorial/tutorial_general_04.png

[100]: ./media/xaitporter-tutorial/tutorial_general_100.png

[200]: ./media/xaitporter-tutorial/tutorial_general_200.png
[201]: ./media/xaitporter-tutorial/tutorial_general_201.png
[202]: ./media/xaitporter-tutorial/tutorial_general_202.png
[203]: ./media/xaitporter-tutorial/tutorial_general_203.png

