---
title: 'Tutorial: Integração do Active Directory do Azure com Jobscience | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Jobscience.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8199f106c234e216a0982dc9e51413ccf30ae93a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60268829"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Integração do Active Directory do Azure com Jobscience

Neste tutorial, saiba como integrar Jobscience com o Azure Active Directory (Azure AD).

Integrar Jobscience no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Jobscience
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Jobscience (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Jobscience, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Jobscience logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Jobscience da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-jobscience-from-the-gallery"></a>Adicionando Jobscience da Galeria
Para configurar a integração do Jobscience com o Azure AD, terá de adicionar Jobscience a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Jobscience a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Jobscience**.

    ![Criar um utilizador de teste do Azure AD](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. No painel de resultados, selecione **Jobscience**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Jobscience com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Jobscience a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Jobscience deve ser estabelecido.

Jobscience, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Jobscience, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Jobscience](#creating-a-jobscience-test-user)**  - para ter um equivalente da Eduarda Almeida na Jobscience que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Jobscience.

**Para configurar o Azure AD início de sessão único com Jobscience, execute os seguintes passos:**

1. No portal do Azure, sobre o **Jobscience** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Sobre o **Jobscience domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `http://<company name>.my.salesforce.com`
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Obter este valor [equipa de suporte de cliente Jobscience](https://www.jobscience.com/support) ou do perfil de SSO, irá criar o qual será explicado mais tarde no tutorial. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/jobscience-tutorial/tutorial_general_400.png)

1. Sobre o **Jobscience configuração** secção, clique em **configurar Jobscience** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Inicie sessão no site da sua empresa Jobscience como administrador.

1. Aceda a **configuração**.
   
   ![Programa de configuração](./media/jobscience-tutorial/IC784358.png "configuração")

1. No painel de navegação esquerdo, no **Administer** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
   
   ![Meu domínio](./media/jobscience-tutorial/ic767825.png "meu domínio")

1. Para verificar que seu domínio foi configurado corretamente, certifique-se de que está a ser "**passo 4 implementadas para os utilizadores**" e rever seu "**minhas configurações de domínio**".

    ![Domínio implementado para o usuário](./media/jobscience-tutorial/ic784377.png "implementado utilizador de domínio")

1. No site de empresa Jobscience, clique em **controlos de segurança**e, em seguida, clique em **configurações de logon único**.
    
    ![Controlos de segurança](./media/jobscience-tutorial/ic784364.png "controlos de segurança")

1. Na **definições de início de sessão único** secção, execute os seguintes passos:
    
    ![Único configurações de logon](./media/jobscience-tutorial/ic781026.png "único configurações de logon")
    
    a. Selecione **SAML ativada**.

    b. Clique em **Novo**.

1. Sobre o **SAML único início de sessão definição editar** caixa de diálogo, execute os seguintes passos:
    
    ![SAML única início de sessão definição](./media/jobscience-tutorial/ic784365.png "SAML única início de sessão na definição")
    
    a. Na **nome** caixa de texto, escreva um nome para a sua configuração.

    b. Na **emissor** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    c. Na **Id de entidade** caixa de texto, tipo `https://salesforce-jobscience.com`

    d. Clique em **procurar** para carregar o certificado do Azure AD.

    e. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto de utilizador**.

    f. Como **localização de identidade de SAML**, selecione **identidade é no elemento NameIdentfier da declaração de assunto**.

    g. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    h. Na **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    i. Clique em **Guardar**.

1. No painel de navegação esquerdo, no **Administer** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
    
    ![Meu domínio](./media/jobscience-tutorial/ic767825.png "meu domínio")

1. No **meu domínio** página, além da **marca de página de início de sessão** secção, clique em **editar**.
    
    ![Página de início de sessão de imagem corporativa](./media/jobscience-tutorial/ic767826.png "imagem corporativa da página início de sessão")

1. No **uma imagem corporativa de página de início de sessão** na página a **serviço de autenticação** secção, o nome do seu **as definições de SSO SAML** é apresentado. Selecione-o e, em seguida, clique em **guardar**.
    
    ![Página de início de sessão de imagem corporativa](./media/jobscience-tutorial/ic784366.png "imagem corporativa da página início de sessão")

1. Para obter o SP iniciada pelo início de sessão único no, clique em URL de início de sessão no **definições de início de sessão único** no **controlos de segurança** secção de menu.

    ![Controlos de segurança](./media/jobscience-tutorial/ic784368.png "controlos de segurança")
    
    Clique no perfil SSO que criou no passo acima. Esta página mostra o início de sessão único na URL para a sua empresa (por exemplo, [ https://companyname.my.salesforce.com?so=companyid ](https://companyname.my.salesforce.com?so=companyid).    

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-jobscience-test-user"></a>Criar um utilizador de teste Jobscience

Para habilitar os utilizadores do Azure AD iniciar sessão no Jobscience, tem de ser aprovisionados em Jobscience. No caso de Jobscience, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Pode utilizar quaisquer outras Jobscience utilizador conta criação ferramentas ou APIs fornecidas pelo Jobscience para aprovisionar o Azure Active Directory contas de utilizador.
>  
        
**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu **Jobscience** site da empresa como administrador.

1. Vá para configuração.
   
   ![Programa de configuração](./media/jobscience-tutorial/ic784358.png "configuração")
1. Aceda a **gerir utilizadores \> utilizadores**.
   
   ![Os utilizadores](./media/jobscience-tutorial/ic784369.png "utilizadores")
1. Clique em **novo utilizador**.
   
   ![Todos os utilizadores](./media/jobscience-tutorial/ic784370.png "todos os utilizadores")
1. Sobre o **Editar utilizador** caixa de diálogo, execute os seguintes passos:
   
   ![Edição do utilizador](./media/jobscience-tutorial/ic784371.png "edição do utilizador")
   
   a. Na **nome próprio** caixa de texto, escreva um nome próprio do utilizador, como a Eduarda.
   
   b. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon.
   
   c. Na **Alias** caixa de texto, escreva um nome de alias do utilizador, como brittas.

   d. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

   e. Na **nome de utilizador** caixa de texto, escreva um nome de utilizador do utilizador, como Brittasimon@contoso.com.

   f. Na **Nick nome** caixa de texto, escreva um nome de nick do utilizador, como Simon.

   g. Clique em **Guardar**.

    
> [!NOTE]
> O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Jobscience.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Jobscience, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Jobscience**.

    ![Configurar o início de sessão único](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Jobscience no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Jobscience.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png

