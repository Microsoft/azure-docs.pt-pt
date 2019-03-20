---
title: 'Tutorial: Integração do Active Directory do Azure com StatusPage | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4494996ed54b25be71367dd3e3043023d0958074
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58224044"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Tutorial: Integração do Active Directory do Azure com StatusPage

Neste tutorial, saiba como integrar StatusPage com o Azure Active Directory (Azure AD).

Integrar StatusPage no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao StatusPage
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para StatusPage (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com StatusPage, terá dos seguintes itens:

- Uma subscrição do Azure
- Um StatusPage logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando StatusPage da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-statuspage-from-the-gallery"></a>Adicionando StatusPage da Galeria
Para configurar a integração do StatusPage com o Azure AD, terá de adicionar StatusPage a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar StatusPage a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **StatusPage**.

    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_search.png)

1. No painel de resultados, selecione **StatusPage**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com StatusPage com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no StatusPage a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no StatusPage deve ser estabelecido.

StatusPage, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com StatusPage, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste StatusPage](#creating-a-statuspage-test-user)**  - para ter um equivalente da Eduarda Almeida na StatusPage que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo StatusPage.

**Para configurar o Azure AD início de sessão único com StatusPage, execute os seguintes passos:**

1. No portal do Azure, sobre o **StatusPage** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_samlbase.png)

1. Sobre o **StatusPage domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: 
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |

   > [!NOTE]
   > Contacte a equipa de suporte de StatusPage em [ SupportTeam@statuspage.io ](mailto:SupportTeam@statuspage.io)solicite os metadados necessários para configurar o início de sessão único. 
   > 
   > a. Dos metadados, copie o valor de emissor e, em seguida, cole-o para o **identificador** caixa de texto.
   > 
   > b. Dos metadados, copie o URL de resposta e, em seguida, cole-o para o **URL de resposta** caixa de texto.

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_general_400.png)

1. Sobre o **StatusPage configuração** secção, clique em **configurar StatusPage** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_configure.png) 

1. Na outra janela do browser, inicie sessão site da sua empresa StatusPage como um administrador.

1. Na barra de ferramentas principal, clique em **gerir conta**.
   
    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_06.png) 

1. Clique nas **início de sessão único** separador. 
   
    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_07.png) 

1. Na página de configuração de SSO, execute os seguintes passos:
   
      ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_08.png) 

      ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_09.png) 
 
      a. Na **URL de destino de SSO** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

      b. Abra o seu certificado transferido no bloco de notas, copiar o conteúdo e, em seguida, cole-o para o **certificado** caixa de texto. 

      c. Clique em **Guardar configuração**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-statuspage-test-user"></a>Criar um utilizador de teste StatusPage

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no StatusPage.

StatusPage suporta o aprovisionamento de just-in-time. Já ativou-lo na [configuração do Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on).

**Para criar um usuário chamado Eduarda Almeida no StatusPage, execute os seguintes passos:**

1. Início de sessão no site da sua empresa StatusPage como administrador.

1. No menu na parte superior, clique em **gerir conta**.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Clique nas **os integrantes da Equipe** separador. 
   
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Clique em **Adicionar membro da EQUIPE**. 
   
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Tipo de **endereço de E-Mail**, **nome próprio**, e **Apelido** de um utilizador válido que pretende aprovisionar em caixas de texto relacionadas. 
   
    ![Criar um utilizador de teste do Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Como **função**, escolha **administrador Client**.

1. Clique em **criar conta**.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para StatusPage.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a StatusPage, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **StatusPage**.

    ![Configurar o início de sessão único](./media/statuspage-tutorial/tutorial_statuspage_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico StatusPage no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo StatusPage.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/statuspage-tutorial/tutorial_general_01.png
[2]: ./media/statuspage-tutorial/tutorial_general_02.png
[3]: ./media/statuspage-tutorial/tutorial_general_03.png
[4]: ./media/statuspage-tutorial/tutorial_general_04.png

[100]: ./media/statuspage-tutorial/tutorial_general_100.png

[200]: ./media/statuspage-tutorial/tutorial_general_200.png
[201]: ./media/statuspage-tutorial/tutorial_general_201.png
[202]: ./media/statuspage-tutorial/tutorial_general_202.png
[203]: ./media/statuspage-tutorial/tutorial_general_203.png

