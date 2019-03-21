---
title: 'Tutorial: Integração do Active Directory do Azure com Kintone | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Kintone.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2b947dc-e1a8-4f5f-b40e-2c5180648e4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e425efda49e02c895d288b517635525e7fa444e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895690"
---
# <a name="tutorial-azure-active-directory-integration-with-kintone"></a>Tutorial: Integração do Active Directory do Azure com Kintone

Neste tutorial, saiba como integrar Kintone com o Azure Active Directory (Azure AD).

Integrar Kintone no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso para Kintone
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Kintone (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Kintone, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Kintone logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Kintone da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-kintone-from-the-gallery"></a>Adicionando Kintone da Galeria
Para configurar a integração do Kintone com o Azure AD, terá de adicionar Kintone a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Kintone a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Kintone**.

    ![Criar um utilizador de teste do Azure AD](./media/kintone-tutorial/tutorial_kintone_search.png)

1. No painel de resultados, selecione **Kintone**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/kintone-tutorial/tutorial_kintone_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Kintone com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Kintone a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Kintone deve ser estabelecido.

Kintone, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Kintone, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Kintone](#creating-a-kintone-test-user)**  - para ter um equivalente da Eduarda Almeida na Kintone que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Kintone.

**Para configurar o Azure AD início de sessão único com Kintone, execute os seguintes passos:**

1. No portal do Azure, sobre o **Kintone** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/kintone-tutorial/tutorial_kintone_samlbase.png)

1. Sobre o **Kintone domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kintone-tutorial/tutorial_kintone_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.kintone.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `https://<companyname>.cybozu.com`|
    | `https://<companyname>.kintone.com`|

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Kintone](https://www.kintone.com/contact/) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/kintone-tutorial/tutorial_kintone_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/kintone-tutorial/tutorial_general_400.png)

1. Sobre o **Kintone configuração** secção, clique em **configurar Kintone** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/kintone-tutorial/tutorial_kintone_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no seu **Kintone** site da empresa como administrador.

1. Clique em **definições**.
   
    ![As definições](./media/kintone-tutorial/ic785879.png "definições")

1. Clique em **utilizadores e de administração do sistema**.
   
    ![Utilizadores e de administração do sistema](./media/kintone-tutorial/ic785880.png "de utilizadores e de administração do sistema")

1. Sob **de administração do sistema \> Security** clique em **início de sessão**.
   
    ![Login](./media/kintone-tutorial/ic785881.png "Login")

1. Clique em **autenticação SAML ativar**.
   
    ![Autenticação SAML](./media/kintone-tutorial/ic785882.png "autenticação SAML")

1. Na secção autenticação SAML, execute os seguintes passos:
    
    ![Autenticação SAML](./media/kintone-tutorial/ic785883.png "autenticação SAML")
    
    a. Na **URL de início de sessão** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.
   
    b. Na **URL de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.
    
    c. Clique em **procurar** para carregar o certificado transferido.
    
    d. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/kintone-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/kintone-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/kintone-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/kintone-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-kintone-test-user"></a>Criar um utilizador de teste Kintone

Para permitir que os utilizadores do Azure AD iniciem sessão para Kintone, tem de ser aprovisionados para Kintone.  
No caso de Kintone, o aprovisionamento é uma tarefa manual.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Para Aprovisionar uma conta de utilizador, execute os seguintes passos:

1. Inicie sessão no seu **Kintone** site da empresa como administrador.

1. Clique em **definição**.
   
    ![As definições](./media/kintone-tutorial/ic785879.png "definições")

1. Clique em **utilizadores e de administração do sistema**.
   
    ![Administração de sistemas e usuários](./media/kintone-tutorial/ic785880.png "administração do sistema & utilizador")

1. Sob **administração de utilizadores**, clique em **departamentos e usuários**.
   
    ![Departamento e utilizadores](./media/kintone-tutorial/ic785888.png "departamento & utilizadores")

1. Clique em **novo utilizador**.
   
    ![Novos utilizadores](./media/kintone-tutorial/ic785889.png "novos utilizadores")

1. Na **novo utilizador** secção, execute os seguintes passos:
   
    ![Novos utilizadores](./media/kintone-tutorial/ic785890.png "novos utilizadores")
   
    a. Introduza um **nome a apresentar**, **nome de início de sessão**, **nova palavra-passe**, **Confirmar palavra-passe**, **endereço de correio eletrónico**, e outros detalhes de uma conta do AAD válido que pretende aprovisionar em caixas de texto relacionadas.
 
    b. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar quaisquer outras Kintone utilizador conta criação ferramentas ou APIs fornecidas pelo Kintone para aprovisionar contas de utilizador do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Kintone.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida para Kintone, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Kintone**.

    ![Configurar o início de sessão único](./media/kintone-tutorial/tutorial_kintone_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Kintone no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Kintone.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/kintone-tutorial/tutorial_general_01.png
[2]: ./media/kintone-tutorial/tutorial_general_02.png
[3]: ./media/kintone-tutorial/tutorial_general_03.png
[4]: ./media/kintone-tutorial/tutorial_general_04.png

[100]: ./media/kintone-tutorial/tutorial_general_100.png

[200]: ./media/kintone-tutorial/tutorial_general_200.png
[201]: ./media/kintone-tutorial/tutorial_general_201.png
[202]: ./media/kintone-tutorial/tutorial_general_202.png
[203]: ./media/kintone-tutorial/tutorial_general_203.png

