---
title: 'Tutorial: Integração do Active Directory do Azure com SpringCM | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SpringCM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64aebaedd9221b2570b66bdb8550bff36b54492c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084764"
---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>Tutorial: Integração do Active Directory do Azure com SpringCM

Neste tutorial, saiba como integrar SpringCM com o Azure Active Directory (Azure AD).

Integrar SpringCM no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao SpringCM
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para SpringCM (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com SpringCM, terá dos seguintes itens:

- Uma subscrição do Azure
- Um SpringCM logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando SpringCM da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-springcm-from-the-gallery"></a>Adicionando SpringCM da Galeria
Para configurar a integração do SpringCM com o Azure AD, terá de adicionar SpringCM a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SpringCM a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **SpringCM**.

    ![Criar um utilizador de teste do Azure AD](./media/spring-cm-tutorial/tutorial_springcm_search.png)

1. No painel de resultados, selecione **SpringCM**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/spring-cm-tutorial/tutorial_springcm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com SpringCM com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no SpringCM a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SpringCM deve ser estabelecido.

SpringCM, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com SpringCM, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste SpringCM](#creating-a-springcm-test-user)**  - para ter um equivalente da Eduarda Almeida na SpringCM que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo SpringCM.

**Para configurar o Azure AD início de sessão único com SpringCM, execute os seguintes passos:**

1. No portal do Azure, sobre o **SpringCM** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/spring-cm-tutorial/tutorial_springcm_samlbase.png)

1. Sobre o **SpringCM domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/spring-cm-tutorial/tutorial_springcm_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=<identifier>`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente SpringCM](https://knowledge.springcm.com/support) para obter este valor. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Raw)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/spring-cm-tutorial/tutorial_springcm_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/spring-cm-tutorial/tutorial_general_400.png)

1. Sobre o **SpringCM configuração** secção, clique em **configurar SpringCM** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/spring-cm-tutorial/tutorial_springcm_configure.png)     

1. Numa janela do browser web diferente, inicie sessão no seu **SpringCM** site da empresa como administrador.

1. No menu na parte superior, clique em **Ir para**, clique em **preferências**e, em seguida, no **preferências de conta** secção, clique em **SAML SSO**.
   
    ![SAML SSO](./media/spring-cm-tutorial/ic797051.png "SAML SSO")

1. Na secção de configuração do fornecedor de identidade, execute os seguintes passos:
   
    ![Configuração do fornecedor de identidade](./media/spring-cm-tutorial/ic797052.png "configuração do fornecedor de identidade")
    
    a. Para carregar o certificado transferido do Azure Active Directory, clique em **selecionar o certificado de emissor** ou **certificados de emissor de alteração**.
    
    b. Colar **ID de entidade de SAML** valor, que copiou do portal do Azure para o **emissor** caixa de texto.
    
    c. Colar **SAML único início de sessão no URL do serviço** valor, que copiou do portal do Azure para o **fornecedor de serviços (SP) iniciada pelo ponto de extremidade** caixa de texto.
            
    d. Selecione **SAML ativada** como **ativar**.

    e. Clique em **Guardar**.
 
> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/spring-cm-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/spring-cm-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/spring-cm-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/spring-cm-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-springcm-test-user"></a>Criar um utilizador de teste SpringCM

Para que os utilizadores do Azure Active Directory iniciar sessão no SpringCM, tem de ser aprovisionados em SpringCM. No caso de SpringCM, o aprovisionamento é uma tarefa manual.

>[!NOTE]
>Para obter mais informações, consulte [criar e editar um utilizador SpringCM](https://knowledge.springcm.com/create-and-edit-a-springcm-user). 

**Para Aprovisionar uma conta de utilizador para SpringCM, execute os seguintes passos:**

1. Inicie sessão no seu **SpringCM** site da empresa como administrador.

1. Clique em **GOTO**e, em seguida, clique em **livro de endereços**.
   
    ![Criar utilizador](./media/spring-cm-tutorial/ic797054.png "criar utilizador")

1. Clique em **criar utilizador**.

1. Selecione um **função de utilizador**.

1. Selecione **Enviar E-Mail de ativação**.

1. Escreva o nome próprio, apelido e endereço de e-mail de uma conta de utilizador válido do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas.

1. Adicione o utilizador para um **grupo de segurança**.

1. Clique em **Guardar**.

   >[!NOTE]
   >Pode utilizar quaisquer outras SpringCM utilizador conta criação ferramentas ou APIs fornecidas pelo SpringCM para aprovisionar contas de utilizador do AAD.  
   > 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para SpringCM.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a SpringCM, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **SpringCM**.

    ![Configurar o início de sessão único](./media/spring-cm-tutorial/tutorial_springcm_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.
 
Quando clica no mosaico SpringCM no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo SpringCM.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/spring-cm-tutorial/tutorial_general_01.png
[2]: ./media/spring-cm-tutorial/tutorial_general_02.png
[3]: ./media/spring-cm-tutorial/tutorial_general_03.png
[4]: ./media/spring-cm-tutorial/tutorial_general_04.png

[100]: ./media/spring-cm-tutorial/tutorial_general_100.png

[200]: ./media/spring-cm-tutorial/tutorial_general_200.png
[201]: ./media/spring-cm-tutorial/tutorial_general_201.png
[202]: ./media/spring-cm-tutorial/tutorial_general_202.png
[203]: ./media/spring-cm-tutorial/tutorial_general_203.png

