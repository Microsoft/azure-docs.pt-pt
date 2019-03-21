---
title: 'Tutorial: Integração do Active Directory do Azure com Wdesk | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75d0e962169529ab8d17aeeeed8aab26e7b7e994
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57880879"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Tutorial: Integração do Active Directory do Azure com Wdesk

Neste tutorial, saiba como integrar Wdesk com o Azure Active Directory (Azure AD).

Integrar Wdesk no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Wdesk
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Wdesk (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Wdesk, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Wdesk início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Wdesk da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-wdesk-from-the-gallery"></a>Adicionando Wdesk da Galeria
Para configurar a integração do Wdesk com o Azure AD, terá de adicionar Wdesk a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Wdesk a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Wdesk**.

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/tutorial_wdesk_search.png)

1. No painel de resultados, selecione **Wdesk**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/tutorial_wdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Wdesk com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Wdesk a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Wdesk deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no Wdesk.

Para configurar e testar o Azure AD início de sessão único com Wdesk, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Wdesk](#creating-a-wdesk-test-user)**  - para ter um equivalente da Eduarda Almeida na Wdesk que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Wdesk.

**Para configurar o Azure AD início de sessão único com Wdesk, execute os seguintes passos:**

1. No portal do Azure, sobre o **Wdesk** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_samlbase.png)

1. Sobre o **Wdesk domínio e URLs** secção, se desejar configurar a aplicação na **IDP** modo iniciado execute os seguintes passos:

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

1. Verifique **Mostrar definições de URL avançadas**. Se desejar configurar a aplicação no **SP** iniciada pelo modo, executar o passo seguinte:

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Obter esses valores do portal de WDesk quando configurar o SSO. 
  
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_general_400.png)
    
1. Numa janela do browser web diferente, início de sessão para Wdesk como um administrador de segurança.

1. No canto inferior esquerdo, clique em **administrador** e escolha **administrador de conta**:
 
     ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. No Wdesk administrador, navegue até **Security**, em seguida, **SAML** > **definições de SAML**:

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. Sob **definições gerais**, verifique o **ativar SAML início de sessão único**:

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

1. Sob **detalhes do fornecedor de serviço**, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Copiar o **URL de início de sessão** e cole-o na **Url de início de sessão** caixa de texto no portal do Azure.
   
      b. Copiar o **Url de metadados** e cole-o na **identificador** caixa de texto no portal do Azure.
       
      c. Copiar o **url de consumidor** e cole-o na **Url de resposta** caixa de texto no portal do Azure.
   
      d. Clique em **guardar** no portal do Azure para guardar as alterações.      

1. Clique em **configurar definições de IdP** para abrir **editar definições de IdP** caixa de diálogo. Clique em **Escolher ficheiro** para localizar o **METADATA** ficheiro que guardou a partir do portal do Azure, em seguida, carregá-lo.
    
    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
1. Clique em **guardar alterações**.

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-wdesk-test-user"></a>Criar um utilizador de teste Wdesk

Para ativar a utilizadores do Azure AD iniciar sessão no Wdesk, tem de ser aprovisionados em Wdesk. Wdesk, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Wdesk como um administrador de segurança.
1. Navegue para **administrador** > **conta de administrador**.

     ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

1. Clique em **membros** sob **pessoas**.

1. Agora, clique em **Adicionar membro** para abrir **Adicionar membro** caixa de diálogo. 
   
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser1.png)  

1. Na **usuário** texto, introduza o nome de utilizador do utilizador, como **brittasimon\@contoso.com** e clique em **continuar** botão.

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser3.png)

1.  Introduza os detalhes, conforme mostrado abaixo:
  
    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. Na **email** texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    b. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    c. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

1. Clique em **membro guardar** botão.  

    ![Criar um utilizador de teste do Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Wdesk.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Wdesk, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Wdesk**.

    ![Configurar o início de sessão único](./media/wdesk-tutorial/tutorial_wdesk_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Wdesk no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Wdesk.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/wdesk-tutorial/tutorial_general_01.png
[2]: ./media/wdesk-tutorial/tutorial_general_02.png
[3]: ./media/wdesk-tutorial/tutorial_general_03.png
[4]: ./media/wdesk-tutorial/tutorial_general_04.png

[100]: ./media/wdesk-tutorial/tutorial_general_100.png

[200]: ./media/wdesk-tutorial/tutorial_general_200.png
[201]: ./media/wdesk-tutorial/tutorial_general_201.png
[202]: ./media/wdesk-tutorial/tutorial_general_202.png
[203]: ./media/wdesk-tutorial/tutorial_general_203.png

