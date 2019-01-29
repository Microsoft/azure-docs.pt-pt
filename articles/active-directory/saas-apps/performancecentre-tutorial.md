---
title: 'Tutorial: Integração do Active Directory do Azure com PerformanceCentre | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e PerformanceCentre.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: d363f47301517e7d4f8d8f8fdf45ffd8c146279e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165171"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Tutorial: Integração do Active Directory do Azure com PerformanceCentre

Neste tutorial, saiba como integrar PerformanceCentre com o Azure Active Directory (Azure AD).

Integrar PerformanceCentre no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao PerformanceCentre
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para PerformanceCentre (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com PerformanceCentre, terá dos seguintes itens:

- Uma subscrição do Azure
- Um PerformanceCentre logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando PerformanceCentre da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-performancecentre-from-the-gallery"></a>Adicionando PerformanceCentre da Galeria
Para configurar a integração do PerformanceCentre com o Azure AD, terá de adicionar PerformanceCentre a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar PerformanceCentre a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **PerformanceCentre**.

    ![Criar um utilizador de teste do Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_search.png)

1. No painel de resultados, selecione **PerformanceCentre**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/performancecentre-tutorial/tutorial_performancecentre_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com PerformanceCentre com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no PerformanceCentre a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no PerformanceCentre deve ser estabelecido.

PerformanceCentre, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com PerformanceCentre, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste PerformanceCentre](#creating-a-performancecentre-test-user)**  - para ter um equivalente da Eduarda Almeida na PerformanceCentre que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo PerformanceCentre.

**Para configurar o Azure AD início de sessão único com PerformanceCentre, execute os seguintes passos:**

1. No portal do Azure, sobre o **PerformanceCentre** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/performancecentre-tutorial/tutorial_performancecentre_samlbase.png)

1. Sobre o **PerformanceCentre domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/performancecentre-tutorial/tutorial_performancecentre_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `http://companyname.performancecentre.com/saml/SSO`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `http://companyname.performancecentre.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente PerformanceCentre](https://www.performancecentre.com/contact-us/) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/performancecentre-tutorial/tutorial_performancecentre_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/performancecentre-tutorial/tutorial_general_400.png)

1. Sobre o **PerformanceCentre configuração** secção, clique em **configurar PerformanceCentre** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/performancecentre-tutorial/tutorial_performancecentre_configure.png) 

1. Início de sessão na sua **PerformanceCentre** site da empresa como administrador.

1. No separador no lado esquerdo, clique em **configurar**.
   
    ![O Azure AD Single Sign-On][10]

1. No separador no lado esquerdo, clique em **diversos**e, em seguida, clique em **início de sessão único**.
   
    ![O Azure AD Single Sign-On][11]

1. Como **protocolo**, selecione **SAML**.
   
    ![O Azure AD Single Sign-On][12]

1. A abrir o ficheiro de metadados baixado no bloco de notas, copie o conteúdo, cole-o para o **metadados de fornecedor de identidade** caixa de texto e, em seguida, clique em **guardar**.
   
    ![O Azure AD Single Sign-On][13]

1. Certifique-se de que os valores para o **URL de Base de entidade** e **URL de ID de entidade** estão corretos.
    
     ![O Azure AD Single Sign-On][14]

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/performancecentre-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/performancecentre-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/performancecentre-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/performancecentre-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-performancecentre-test-user"></a>Criar um utilizador de teste PerformanceCentre

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no PerformanceCentre.

**Para criar um usuário chamado Eduarda Almeida no PerformanceCentre, execute os seguintes passos:**

1. Inicie sessão site da sua empresa PerformanceCentre como administrador.

1. No menu à esquerda, clique em **Interrelate**e, em seguida, clique em **participante criar**.
   
    ![Criar Utilizador][400]

1. Sobre o **Interrelate - criar participante** caixa de diálogo, execute os seguintes passos:
   
    ![Criar Utilizador][401]
    
    a. Escreva os atributos necessários para Eduarda Almeida em caixas de texto relacionadas.
    
    >[!IMPORTANT]
    >Atributo de nome de utilizador da Eduarda no PerformanceCentre tem de ser igual ao nome de utilizador no Azure AD.
    
    b. Selecione **administrador do cliente** como **escolha função**.
    
    c. Clique em **Guardar**. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para PerformanceCentre.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a PerformanceCentre, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **PerformanceCentre**.

    ![Configurar o início de sessão único](./media/performancecentre-tutorial/tutorial_performancecentre_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção consiste em testar a configuração de SSO do Azure AD através do painel de acesso.  

Quando clica no mosaico PerformanceCentre no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo PerformanceCentre.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/performancecentre-tutorial/tutorial_general_01.png
[2]: ./media/performancecentre-tutorial/tutorial_general_02.png
[3]: ./media/performancecentre-tutorial/tutorial_general_03.png
[4]: ./media/performancecentre-tutorial/tutorial_general_04.png
[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png

[100]: ./media/performancecentre-tutorial/tutorial_general_100.png

[200]: ./media/performancecentre-tutorial/tutorial_general_200.png
[201]: ./media/performancecentre-tutorial/tutorial_general_201.png
[202]: ./media/performancecentre-tutorial/tutorial_general_202.png
[203]: ./media/performancecentre-tutorial/tutorial_general_203.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png

