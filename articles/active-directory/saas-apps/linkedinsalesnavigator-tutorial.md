---
title: 'Tutorial: Integração do Active Directory do Azure com o LinkedIn Sales Navigator | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e LinkedInSalesNavigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63e3e98c2c3dc8f99e733174c86965304fe483ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60257297"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-sales-navigator"></a>Tutorial: Integração do Active Directory do Azure com o LinkedIn Sales Navigator

Neste tutorial, saiba como integrar o LinkedIn Sales Navigator com o Azure Active Directory (Azure AD).

Integrar o LinkedIn Sales Navigator no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso para o LinkedIn Sales Navigator
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o LinkedIn Sales Navigator (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, navegar até [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o LinkedIn Sales Navigator, terá dos seguintes itens:

- Uma subscrição do Azure
- Um LinkedIn Sales Navigator início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Evite o uso de seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o LinkedIn Sales Navigator da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>Adicionando o LinkedIn Sales Navigator da Galeria
Para configurar a integração do LinkedIn Sales Navigator para o Azure AD, terá de adicionar o LinkedIn Sales Navigator partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o LinkedIn Sales Navigator partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **LinkedIn Sales Navigator**.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_search.png)

1. No painel de resultados, selecione **LinkedIn Sales Navigator**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o LinkedIn Sales Navigator com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no LinkedIn Sales Navigator para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LinkedIn Sales Navigator deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no LinkedIn Sales Navigator.

Para configurar e testar o Azure AD início de sessão único com o LinkedIn Sales Navigator, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do LinkedIn Sales Navigator](#creating-a-linkedin-sales-navigator-test-user)**  - para ter um equivalente da Eduarda Almeida no LinkedIn Sales Navigator que está ligado a representação do Azure AD do utilizador.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação do LinkedIn Sales Navigator.

**Para configurar o Azure AD início de sessão único com o LinkedIn Sales Navigator, execute os seguintes passos:**

1. No portal do Azure, sobre o **LinkedIn Sales Navigator** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, na **modo** selecione **baseado em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_samlbase.png)

1. Numa janela do browser web diferente, início de sessão na sua **LinkedIn Sales Navigator** Web site como um administrador.

1. Na **Centro de contas**, clique em **definições globais** sob **definições**. Além disso, selecione **Sales Navigator** na lista pendente.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Clique em **ou clique aqui para carregar e copiar campos individuais do formulário** e copie **Id de entidade** e **Url de acesso de consumidor de asserção (ACS)**.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

1. No portal do Azure, em **LinkedIn Sales Navigator domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação na **IDP** iniciada pelo modo.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url1.png)

    a. Na **identificador** caixa de texto, introduza o **ID de entidade** copiados a partir do Portal do LinkedIn 

    b. Na **URL de resposta** caixa de texto, introduza o **Url de acesso de consumidor de asserção (ACS)** copiados a partir do Portal do LinkedIn

1. Verifique **Mostrar definições de URL avançadas**, se desejar configurar a aplicação na **SP** iniciada pelo modo.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_url2.png)

    Na **URL de início de sessão** caixa de texto, digite o valor usando o seguinte padrão: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Sua **LinkedIn Sales Navigator** aplicativo espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra um exemplo. O valor predefinido **identificador de utilizador** é **user.userprincipalname** mas o LinkedIn Sales Navigator espera que seja mapeada com endereço de e-mail do utilizador. Pode usar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na configuração da sua organização. 

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/updateusermail.png)
    
1. Na **atributos de utilizador** secção, clique em **ver e editar todos os outros atributos de utilizador** e definir os atributos. O utilizador tem de adicionar quatro afirmações com o nome **e-mail**, **departamento**, **firstname**, e **lastname** e o valor deve ser mapeado com **user.mail**, **user.department**, **user.givenname**, e **user.surname** , respetivamente

    | Nome do Atributo | Valor do Atributo |
    | --- | --- |    
    | e-mail| user.mail |
    | Departamento| user.department |
    | FirstName| user.givenname |
    | Apelido| user.surname |
    
    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/userattribute.png)
    
    a. Clique em **adicionar atributo** para abrir a caixa de diálogo do atributo.
    
    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_04.png)
    
    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/tutorial_attribute_05.png)
   
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**

1. Execute os seguintes passos no **nome** atributo -

    a. Clique no atributo para abrir o **Editar atributo** janela.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/url_update.png)

    b. Eliminar o valor de URL dos **espaço de nomes**.
    
    c. Clique em **Ok** para guardar a definição.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro XML em seu computador.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_general_400.png)

1. Aceda a **definições de administração do LinkedIn** secção. Clique em **arquivo XML carregar** para carregar o ficheiro de metadados XML que transferiu do portal do Azure.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Clique em **no** para ativar o SSO. Estado SSO é alterado de **não ligado** para **ligado**

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)


> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-linkedin-sales-navigator-test-user"></a>Criar um utilizador de teste do LinkedIn Sales Navigator

Aplicativo de navegador de vendas vinculado oferece suporte apenas no Time (JIT) de aprovisionamento de utilizador e de utilizadores de autenticação são criados automaticamente no aplicativo. Ativar **atribuir licenças automaticamente** para atribuir uma licença ao utilizador.
   
   ![Criar um utilizador de teste do Azure AD](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para o LinkedIn Sales Navigator.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida para o LinkedIn Sales Navigator, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **LinkedIn Sales Navigator**.

    ![Configurar o início de sessão único](./media/linkedinsalesnavigator-tutorial/tutorial_linkedinsalesnavigator_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do LinkedIn Sales Navigator no painel de acesso, deve ser redirecionado à página organizacional, onde tem de fornecer detalhes da sua conta LinkedIn pessoais. Ele liga a sua conta pessoal com a sua conta de negócios do LinkedIn. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_01.png
[2]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_02.png
[3]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_03.png
[4]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_04.png

[100]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_100.png

[200]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_200.png
[201]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_201.png
[202]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_202.png
[203]: ./media/linkedinsalesnavigator-tutorial/tutorial_general_203.png

