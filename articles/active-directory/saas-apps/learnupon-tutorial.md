---
title: 'Tutorial: Integração do Active Directory do Azure com LearnUpon | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e LearnUpon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: b11c6315-c79d-4f34-9610-bd17070ab7c7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70d4e507087e645c9bfd41e7ef6b90098079ab1d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60259030"
---
# <a name="tutorial-azure-active-directory-integration-with-learnupon"></a>Tutorial: Integração do Active Directory do Azure com LearnUpon

Neste tutorial, saiba como integrar LearnUpon com o Azure Active Directory (Azure AD).

Integrar LearnUpon no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao LearnUpon
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para LearnUpon (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com LearnUpon, terá dos seguintes itens:

- Uma subscrição do Azure
- Um LearnUpon logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando LearnUpon da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-learnupon-from-the-gallery"></a>Adicionando LearnUpon da Galeria
Para configurar a integração do LearnUpon com o Azure AD, terá de adicionar LearnUpon a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar LearnUpon a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **LearnUpon**.

    ![Criar um utilizador de teste do Azure AD](./media/learnupon-tutorial/tutorial_learnupon_search.png)

1. No painel de resultados, selecione **LearnUpon**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/learnupon-tutorial/tutorial_learnupon_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com LearnUpon com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no LearnUpon a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LearnUpon deve ser estabelecido.

LearnUpon, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com LearnUpon, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste LearnUpon](#creating-a-learnupon-test-user)**  - para ter um equivalente da Eduarda Almeida na LearnUpon que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo LearnUpon.

**Para configurar o Azure AD início de sessão único com LearnUpon, execute os seguintes passos:**

1. No portal do Azure, sobre o **LearnUpon** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_samlbase.png)

1. Sobre o **LearnUpon domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_url.png)

    Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.learnupon.com/saml/consumer`

    > [!NOTE] 
    > Tenha em atenção que isto não é o valor real. terá de atualizar este valor com o URL de resposta real. Para obter este valor de contacto [equipa de suporte de LearnUpon](https://www.learnupon.com/features/support/).



1. Sobre o **certificado de assinatura SAML** secção, localize o **Thumbprint** -Isto será adicionado às suas definições de SAML LearnUpon.

    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_general_400.png)

1. Sobre o **LearnUpon configuração** secção, clique em **configurar LearnUpon** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_configure.png) 

1. Abra outra instância do navegador e início de sessão em LearnUpon com uma conta de administrador. 

1. Clique nas **definições** separador.
   
    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_06.png)

1. Clique em **Single Sign On - SAML**e, em seguida, clique em **definições gerais** para configurar as definições de SAML.
   
    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_07.png) 

1. Na **definições gerais** secção, execute os seguintes passos:
   
    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_08.png)  
  
    a. Selecione **ativada**.

    b. Selecione **versão** como **2.0**.

    c. Selecione **ignorar condições** como **não**.

    d. Na **nome do parâmetro de mensagem de Token SAML** caixa de texto, tipo o nome do parâmetro de mensagem de pedido para o URL de consumidor SAML indicado acima, que contém a asserção de SAML a ser verificado e autenticados – por exemplo **SAMLResponse** .

    e. Na **formato do nome do identificador** caixa de texto, tipo, o valor que indica onde, em sua asserção de SAML o identificador de utilizadores (endereço de E-Mail) reside - por exemplo **urn: oasis: nomes: tc: SAML:1.1:nameid-formato: endereço de correio eletrónico**.
  
    f. Na **identificar a localização do fornecedor** caixa de texto, digite o valor que indica em que os utilizadores são enviados para se clicarem no seu ícone carregado a partir do seu ecrã de início de sessão de portal do Azure.
  
    g. Na **termine URL** caixa de texto, colar a **URL de fim de sessão** que copiou do portal do Azure.
    
    h. Clique em **gerir impressões de dedo**em seguida, carregue a impressão digital do seu certificado transferido.

1. Clique em **definições de utilizador**e, em seguida, execute os seguintes passos:
   
     ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_11.png)  
 
    a. Na **formato de identificador de nome próprio** caixa de texto, tipo, o valor que indica onde no seu asserção de SAML a firstname utilizadores reside - por exemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.
  
    b. Na **formato de identificador de nome passado** caixa de texto, tipo, o valor que indica onde no seu asserção de SAML lastname utilizadores reside - por exemplo: **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/learnupon-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/learnupon-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/learnupon-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/learnupon-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-learnupon-test-user"></a>Criar um utilizador de teste LearnUpon

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no LearnUpon. LearnUpon suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Não existe nenhum item de ação para nesta secção. Durante uma tentativa de aceder LearnUpon se não existir ainda será criado um novo utilizador. Configuração do Azure AD Single Sign-On.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar [equipa de suporte de LearnUpon](https://www.learnupon.com/features/support/). 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para LearnUpon.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a LearnUpon, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **LearnUpon**.

    ![Configurar o início de sessão único](./media/learnupon-tutorial/tutorial_learnupon_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico LearnUpon no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo LearnUpon.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/learnupon-tutorial/tutorial_general_01.png
[2]: ./media/learnupon-tutorial/tutorial_general_02.png
[3]: ./media/learnupon-tutorial/tutorial_general_03.png
[4]: ./media/learnupon-tutorial/tutorial_general_04.png

[100]: ./media/learnupon-tutorial/tutorial_general_100.png

[200]: ./media/learnupon-tutorial/tutorial_general_200.png
[201]: ./media/learnupon-tutorial/tutorial_general_201.png
[202]: ./media/learnupon-tutorial/tutorial_general_202.png
[203]: ./media/learnupon-tutorial/tutorial_general_203.png

