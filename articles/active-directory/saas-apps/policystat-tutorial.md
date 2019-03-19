---
title: 'Tutorial: Integração do Active Directory do Azure com PolicyStat | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e PolicyStat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 602dd0fd4050a3b4336c560bbd82b32f3903b93c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57850201"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Integração do Active Directory do Azure com PolicyStat

Neste tutorial, saiba como integrar PolicyStat com o Azure Active Directory (Azure AD).

Integrar PolicyStat no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao PolicyStat
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para PolicyStat (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PolicyStat, terá dos seguintes itens:

- Uma subscrição do Azure
- Um PolicyStat logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando PolicyStat da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-policystat-from-the-gallery"></a>Adicionando PolicyStat da Galeria
Para configurar a integração do PolicyStat com o Azure AD, terá de adicionar PolicyStat a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar PolicyStat a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **PolicyStat**.

    ![Criar um utilizador de teste do Azure AD](./media/policystat-tutorial/tutorial_policystat_search.png)

1. No painel de resultados, selecione **PolicyStat**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/policystat-tutorial/tutorial_policystat_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com PolicyStat com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no PolicyStat a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PolicyStat deve ser estabelecido.

PolicyStat, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com PolicyStat, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste PolicyStat](#creating-a-policystat-test-user)**  - para ter um equivalente da Eduarda Almeida na PolicyStat que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo PolicyStat.

**Para configurar o Azure AD início de sessão único com PolicyStat, execute os seguintes passos:**

1. No portal do Azure, sobre o **PolicyStat** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_policystat_samlbase.png)

1. Sobre o **PolicyStat domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_policystat_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.policystat.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.policystat.com/saml2/metadata/`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente PolicyStat](http://www.policystat.com/support/) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_policystat_certificate.png) 

1. É o objetivo desta secção descrevem como permitir que os utilizadores sejam autenticados PolicyStat com suas contas no Azure AD com Federação com base no protocolo SAML.

    O aplicativo PolicyStat espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizados para sua **atributos de Token SAML** configuração.  

     Captura de ecrã seguinte mostra um exemplo disso.

     ![Atributos](./media/policystat-tutorial/tutorial_policystat_attribute.png "atributos")

1. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    | Nome do Atributo    |   Valor do Atributo |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_policystat_04.png)

    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_policystat_addatribute.png)
    
    b. Na **nome do atributo** caixa de texto, tipo **uid**.

    c. Na **valor do atributo** caixa de texto, selecione **ExtractMailPrefix()**.    
   
    d. Partir do **correio** lista, selecione **User.mail**.
    
    e. Clique em **Ok**

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_general_400.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa PolicyStat como um administrador.

1. Clique nas **administrador** separador e, em seguida, clique em **configuração de início de sessão único** no painel de navegação à esquerda.
   
    ![Menu de administrador](./media/policystat-tutorial/ic808633.png "Menu de administrador")

1. Na **programa de configuração** secção, selecione **integração de início de sessão único ativar**.
   
    ![A única configuração de início de sessão](./media/policystat-tutorial/ic808634.png "única configuração de início de sessão")

1. Clique em **configurar atributos**e, em seguida, no **configurar atributos** secção, execute os seguintes passos:
   
    ![A única configuração de início de sessão](./media/policystat-tutorial/ic808635.png "única configuração de início de sessão")
   
    a. Na **atributo de nome de utilizador** caixa de texto, tipo **uid**.

    b. Na **atributo de nome próprio** caixa de texto, tipo **firstname** do utilizador **Eduarda**.

    c. Na **último nome do atributo** caixa de texto, tipo **lastname** do utilizador **Simon**.

    d. Na **atributo de correio eletrónico** caixa de texto, tipo **emailaddress** do utilizador **BrittaSimon\@contoso.com**.

    e. Clique em **guardar alterações**.

1. Clique em **Your IDP metadados**e, em seguida, no **Your IDP metadados** secção, execute os seguintes passos:
   
    ![A única configuração de início de sessão](./media/policystat-tutorial/ic808636.png "única configuração de início de sessão")
   
    a. A abrir o ficheiro de metadados baixado, copie o conteúdo e, em seguida, cole-o para o **seus metadados do fornecedor de identidade** caixa de texto.

    b. Clique em **guardar alterações**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/policystat-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/policystat-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/policystat-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/policystat-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-policystat-test-user"></a>Criar um utilizador de teste PolicyStat

Para habilitar logon PolicyStat de utilizadores do Azure AD, tem de ser aprovisionados em PolicyStat.  

PolicyStat oferece suporte apenas no tempo de aprovisionamento de utilizador. Isso significa que, não terá de adicionar utilizadores manualmente ao PolicyStat. Os utilizadores serão sejam adicionados automaticamente no seu primeiro início de sessão através de SSO.

>[!NOTE]
>Pode utilizar quaisquer outras PolicyStat utilizador conta criação ferramentas ou APIs fornecidas pelo PolicyStat para aprovisionar contas de utilizador do Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para PolicyStat.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a PolicyStat, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **PolicyStat**.

    ![Configurar o início de sessão único](./media/policystat-tutorial/tutorial_policystat_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico PolicyStat no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo PolicyStat.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/policystat-tutorial/tutorial_general_01.png
[2]: ./media/policystat-tutorial/tutorial_general_02.png
[3]: ./media/policystat-tutorial/tutorial_general_03.png
[4]: ./media/policystat-tutorial/tutorial_general_04.png

[100]: ./media/policystat-tutorial/tutorial_general_100.png

[200]: ./media/policystat-tutorial/tutorial_general_200.png
[201]: ./media/policystat-tutorial/tutorial_general_201.png
[202]: ./media/policystat-tutorial/tutorial_general_202.png
[203]: ./media/policystat-tutorial/tutorial_general_203.png

