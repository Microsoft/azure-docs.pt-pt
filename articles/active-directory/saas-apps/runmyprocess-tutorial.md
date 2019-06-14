---
title: 'Tutorial: Integração do Active Directory do Azure com RunMyProcess | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfef1371b7ac61712c0f70efd48c0e791c4c729d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60517880"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Tutorial: Integração do Active Directory do Azure com RunMyProcess

Neste tutorial, saiba como integrar RunMyProcess com o Azure Active Directory (Azure AD).

Integrar RunMyProcess no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao RunMyProcess
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para RunMyProcess (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com RunMyProcess, terá dos seguintes itens:

- Uma subscrição do Azure
- Um RunMyProcess logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui:[oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando RunMyProcess da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-runmyprocess-from-the-gallery"></a>Adicionando RunMyProcess da Galeria
Para configurar a integração do RunMyProcess com o Azure AD, terá de adicionar RunMyProcess a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar RunMyProcess a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **RunMyProcess**.

    ![Criar um utilizador de teste do Azure AD](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

1. No painel de resultados, selecione **RunMyProcess**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com RunMyProcess com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no RunMyProcess a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no RunMyProcess deve ser estabelecido.

RunMyProcess, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com RunMyProcess, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste RunMyProcess](#creating-a-runmyprocess-test-user)**  - para ter um equivalente da Eduarda Almeida na RunMyProcess que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo RunMyProcess.

**Para configurar o Azure AD início de sessão único com RunMyProcess, execute os seguintes passos:**

1. No portal do Azure, sobre o **RunMyProcess** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

1. Sobre o **RunMyProcess domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente RunMyProcess](mailto:support@runmyprocess.com) para obter o valor. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/runmyprocess-tutorial/tutorial_general_400.png)

1. Sobre o **RunMyProcess configuração** secção, clique em **configurar RunMyProcess** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

1. Numa janela do browser web diferente, início de sessão no seu inquilino RunMyProcess como administrador.

1. No painel de navegação esquerdo, clique em **conta** e selecione **configuração**.
   
    ![Configurar o início de sessão único no lado de aplicação](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Aceda a **método de autenticação** secção e executar passos abaixo:
   
    ![Configurar o início de sessão único no lado de aplicação](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Como **método**, selecione **SSO com Samlv2**. 

    b. Na **redirecionamento SSO** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    c. Na **redirecionamento de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    d. Na **formato do nome do Id** caixa de texto, digite o valor de **formato do nome do identificador** como **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**.

    e. Copie o conteúdo do ficheiro de certificado transferido e, em seguida, cole-o para o **certificado** caixa de texto. 
 
    f. Clique em **guardar** ícone.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/runmyprocess-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/runmyprocess-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/runmyprocess-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Criar um utilizador de teste RunMyProcess

Para habilitar os utilizadores do Azure AD iniciar sessão no RunMyProcess, tem de ser aprovisionados em RunMyProcess. No caso de RunMyProcess, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa RunMyProcess como administrador.

1. Clique em **conta** e selecione **utilizadores** no painel de navegação esquerdo, em seguida, clique em **novo utilizador**.
   
    ![Novo utilizador](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "novo utilizador")

1. Na **definições de utilizador** secção, execute os seguintes passos:
   
    ![Perfil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "perfil") 
  
    a. Tipo de **nome** e **email** de um Azure válido conta AD que pretende aprovisionar em caixas de texto relacionadas. 

    b. Selecione um **linguagem do IDE**, **linguagem**, e **perfil**. 

    c. Selecione **enviar-me e-mail de criação da conta**. 

    d. Clique em **Guardar**.
   
    >[!NOTE]
    >Pode utilizar quaisquer outras RunMyProcess utilizador conta criação ferramentas ou APIs fornecidas pelo RunMyProcess para aprovisionar o Azure Active Directory contas de utilizador. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para RunMyProcess.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a RunMyProcess, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **RunMyProcess**.

    ![Configurar o início de sessão único](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.

Quando clica no mosaico RunMyProcess no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo RunMyProcess.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

