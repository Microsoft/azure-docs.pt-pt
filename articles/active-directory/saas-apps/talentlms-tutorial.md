---
title: 'Tutorial: Integração do Active Directory do Azure com TalentLMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa78ec2b5623dfd010a8fe5709916a47e221a9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57902200"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Tutorial: Integração do Active Directory do Azure com TalentLMS

Neste tutorial, saiba como integrar TalentLMS com o Azure Active Directory (Azure AD).

Integrar TalentLMS no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao TalentLMS
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para TalentLMS (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TalentLMS, terá dos seguintes itens:

- Uma subscrição do Azure
- Um TalentLMS logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando TalentLMS da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-talentlms-from-the-gallery"></a>Adicionando TalentLMS da Galeria
Para configurar a integração do TalentLMS com o Azure AD, terá de adicionar TalentLMS a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TalentLMS a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **TalentLMS**.

    ![Criar um utilizador de teste do Azure AD](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. No painel de resultados, selecione **TalentLMS**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com TalentLMS com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no TalentLMS a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TalentLMS deve ser estabelecido.

TalentLMS, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com TalentLMS, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste TalentLMS](#creating-a-talentlms-test-user)**  - para ter um equivalente da Eduarda Almeida na TalentLMS que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo TalentLMS.

**Para configurar o Azure AD início de sessão único com TalentLMS, execute os seguintes passos:**

1. No portal do Azure, sobre o **TalentLMS** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. Sobre o **TalentLMS domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.TalentLMSapp.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `http://<tenant-name>.talentlms.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente TalentLMS](https://www.talentlms.com/contact) obter esses valores. 
 
1. Na **certificado de assinatura SAML** secção, copie a **THUMBPRINT** valor a partir do certificado.

    ![Configurar o início de sessão único](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/talentlms-tutorial/tutorial_general_400.png)

1. Sobre o **TalentLMS configuração** secção, clique em **configurar TalentLMS** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa TalentLMS como administrador.

1. No **definições de conta &** secção, clique no **utilizadores** separador.
   
    ![Definições de conta &](./media/talentlms-tutorial/IC777296.png "conta & definições")

1. Clique em **início de sessão único (SSO)**,

1. Na secção de início de sessão único, execute os seguintes passos:
   
    ![Início de sessão único](./media/talentlms-tutorial/IC777297.png "início de sessão único")   

    a. Partir do **tipo de integração de SSO** lista, selecione **SAML 2.0**.

    b. Na **fornecedor de identidade (IDP)** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.
 
    c. Colar o **Thumbprint** valor a partir do portal do Azure para o **impressão digital do certificado** caixa de texto.    

    d.  Na **URL de início de sessão remoto** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.
 
    e. Na **URL de fim de sessão remoto** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    f. Preencha o seguinte: 

    * Na **TargetedID** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`
     
    * Na **nome próprio** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
    
    * Na **Apelido** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
    
    * Na **E-Mail** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`
    
1. Clique em **Guardar**.
 
> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/talentlms-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/talentlms-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/talentlms-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-talentlms-test-user"></a>Criar um utilizador de teste TalentLMS

Para ativar a utilizadores do Azure AD iniciar sessão no TalentLMS, tem de ser aprovisionados em TalentLMS. No caso de TalentLMS, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **TalentLMS** inquilino.

1. Clique em **usuários**e, em seguida, clique em **adicionar utilizador**.

1. Sobre o **adicionar utilizador** caixa de diálogo página, execute os seguintes passos:
   
    ![Adicionar utilizador](./media/talentlms-tutorial/IC777299.png "adicionar utilizador")  

    a. Na **nome próprio** caixa de texto, introduza o nome de utilizador, como **Eduarda**.

    b. Na **Apelido** caixa de texto, introduza o apelido do utilizador, como **Simon**.
 
    c. Na **endereço de E-Mail** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    d. Clique em **adicionar utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras TalentLMS utilizador conta criação ferramentas ou APIs fornecidas pelo TalentLMS para aprovisionar contas de utilizador do AAD.
 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TalentLMS.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a TalentLMS, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **TalentLMS**.

    ![Configurar o início de sessão único](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico TalentLMS no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo TalentLMS

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png

