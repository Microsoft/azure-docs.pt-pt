---
title: 'Tutorial: Integração do Active Directory do Azure com a segurança TINFOIL | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d2001e221ef9c02ee2ab80b647a9bafe6490432
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882149"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Tutorial: Integração do Active Directory do Azure com o TINFOIL SECURITY

Neste tutorial, saiba como integrar o TINFOIL SECURITY com o Azure Active Directory (Azure AD).

Integrar o TINFOIL SECURITY com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao TINFOIL SECURITY
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o TINFOIL SECURITY (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o TINFOIL SECURITY, terá dos seguintes itens:

- Uma subscrição do Azure
- Um TINFOIL SECURITY logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar TINFOIL SECURITY a partir da Galeria
1. Configurar e testar o Azure AD início de sessão único

## <a name="add-tinfoil-security-from-the-gallery"></a>Adicionar TINFOIL SECURITY a partir da Galeria
Para configurar a integração do TINFOIL SECURITY para o Azure AD, terá de adicionar TINFOIL SECURITY a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TINFOIL SECURITY a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **TINFOIL SECURITY**, selecione **TINFOIL SECURITY** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![TINFOIL SECURITY da Galeria](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o TINFOIL SECURITY com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no TINFOIL SECURITY para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TINFOIL SECURITY tem de ser estabelecida.

TINFOIL SECURITY, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o TINFOIL SECURITY, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  - para ter um equivalente da Eduarda Almeida na TINFOIL SECURITY, que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação do TINFOIL SECURITY.

**Para configurar o Azure AD início de sessão único com a segurança TINFOIL, execute os seguintes passos:**

1. No portal do Azure, sobre o **TINFOIL SECURITY** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![SAML com base em início de sessão](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

1. Sobre o **TINFOIL SECURITY domínio e URLs** secção, o utilizador não tem de efetuar outros passos de como a aplicação já está pré-integrada com o Azure.

    ![Configurar o início de sessão único](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


1. Sobre o **certificado de assinatura SAML** secção, copie a **THUMBPRINT** valor.

    ![Secção do certificado de assinatura SAML](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

1. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:
    
    ![Atributos](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "atributos")
    
    | Nome do Atributo    |   Valor do Atributo |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Clique em **adicione o atributo de utilizador**.
    
    ![Adicionar atributo](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "atributos")
    
    ![Adicionar atributo](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "atributos")
    
    b. Na **nome do atributo** caixa de texto, tipo **accountid**.
    
    c. Na **valor do atributo** textbox, o valor de colar o ID de conta que obterá mais tarde no tutorial.
    
    d. Clique em **OK**.    

1. Clique em **guardar** botão.

    ![Botão Guardar](./media/tinfoil-security-tutorial/tutorial_general_400.png)

1. Sobre o **TINFOIL SECURITY Configuration** secção, clique em **configurar TINFOIL SECURITY** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de segurança TINFOIL](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa TINFOIL SECURITY como um administrador.

1. Na barra de ferramentas na parte superior, clique em **minha conta**.
   
    ![Dashboard](./media/tinfoil-security-tutorial/ic798971.png "Dashboard")

1. Clique em **segurança**.
   
    ![Segurança](./media/tinfoil-security-tutorial/ic798972.png "segurança")

1. Sobre o **Single Sign-On** configuração página, execute os seguintes passos:
   
    ![Início de sessão único](./media/tinfoil-security-tutorial/ic798973.png "início de sessão único")
   
    a. Selecione **ativar SAML**.
   
    b. Clique em **configuração Manual**.
   
    c. Na **URL de publicação de SAML** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure
   
    d. Na **impressão digital do certificado de SAML** caixa de texto, cole o valor de **Thumbprint** que copiou do **certificado de assinatura SAML** secção.
  
    e. Cópia **sua ID de conta** valor e cole o valor na **o valor do atributo** caixa de texto em **adicionar atributo** secção no portal do Azure.
   
    f. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/tinfoil-security-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos -> todos os utilizadores](./media/tinfoil-security-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Utilizador](./media/tinfoil-security-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/tinfoil-security-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Criar um utilizador de teste do TINFOIL SECURITY

Para habilitar os utilizadores do Azure AD iniciar sessão no TINFOIL SECURITY, tem de ser aprovisionados em TINFOIL SECURITY. No caso do TINFOIL SECURITY, o aprovisionamento é uma tarefa manual.

**Para obter um utilizador aprovisionado, execute os seguintes passos:**

1. Se o utilizador é uma parte de uma conta de empresa, terá [entre em contato com a equipa de suporte do TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) para obter a conta de utilizador que criou.

1. Se o utilizador for um utilizador normal do TINFOIL SECURITY SaaS, o utilizador pode adicionar um funcionário para qualquer um dos sites do utilizador. Isso dispara um processo para enviar um convite para o e-mail especificado para criar uma nova conta de utilizador do TINFOIL SECURITY.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de TINFOIL SECURITY ou APIs fornecidas pelo TINFOIL SECURITY para aprovisionar contas de utilizador do Azure AD.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao TINFOIL SECURITY.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a TINFOIL SECURITY, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **TINFOIL SECURITY**.

    ![Selecione o TINFOIL SECURITY](./media/tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do TINFOIL SECURITY no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação do TINFOIL SECURITY. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/tinfoil-security-tutorial/tutorial_general_203.png

