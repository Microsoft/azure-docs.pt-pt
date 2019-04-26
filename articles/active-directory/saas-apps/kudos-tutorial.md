---
title: 'Tutorial: Integração do Active Directory do Azure com Parabéns pelo artigo | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Parabéns pelo artigo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5b1fa6284979a8c0bd0698f85b119117dd27a05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60261460"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Tutorial: Integração do Active Directory do Azure com Parabéns pelo artigo

Neste tutorial, saiba como integrar Parabéns pelo artigo com o Azure Active Directory (Azure AD).

Parabéns pelo artigo a integração com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Parabéns pelo artigo
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Parabéns pelo artigo (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Parabéns, precisa do seguinte:

- Uma subscrição do Azure
- Um Parabéns pelo artigo logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Parabéns pelo artigo a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-kudos-from-the-gallery"></a>Adicionar Parabéns pelo artigo a partir da Galeria
Para configurar a integração de parabéns pelo artigo no Azure AD, terá de adicionar Parabéns pelo artigo a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Parabéns pelo artigo a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Parabéns pelo artigo**.

    ![Criar um utilizador de teste do Azure AD](./media/kudos-tutorial/tutorial_kudos_search.png)

1. No painel de resultados, selecione **parabéns**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Parabéns pelo artigo com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Parabéns pelo artigo a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Parabéns pelo artigo deve ser estabelecido.

Parabéns, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Parabéns, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de parabéns](#creating-a-kudos-test-user)**  - para ter um equivalente da Eduarda Almeida na Parabéns pelo artigo que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Parabéns pelo artigo.

**Para configurar o Azure AD início de sessão único com Parabéns pelo artigo, execute os seguintes passos:**

1. No portal do Azure, sobre o **Parabéns pelo artigo** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

1. Sobre o **Parabéns pelo artigo domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/kudos-tutorial/tutorial_kudos_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<company>.kudosnow.com`
    
    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de parabéns pelo artigo](http://success.kudosnow.com/home) para obter este valor. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/kudos-tutorial/tutorial_general_400.png)

1. Sobre o **configuração Parabéns pelo artigo** secção, clique em **configurar Parabéns pelo artigo** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/kudos-tutorial/tutorial_kudos_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Parabéns pelo artigo como um administrador.

1. No menu na parte superior, clique em **definições**.
   
    ![As definições](./media/kudos-tutorial/ic787806.png "definições")

1. Clique em **integrações \> SSO**.

1. Na **SSO** secção, execute os seguintes passos:
   
    ![SSO](./media/kudos-tutorial/ic787807.png "SSO")
   
    a. Na **iniciar sessão no URL** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure. 

    b. Abra o certificado com codificação base 64 no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **certificado X.509** caixa de texto
   
    c. Na **URL de fim de sessão**, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.
   
    d. Na **seu URL de parabéns pelo artigo** caixa de texto, escreva o nome da sua empresa.
   
    e. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/kudos-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/kudos-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/kudos-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/kudos-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-kudos-test-user"></a>Criar um utilizador de teste de parabéns pelo artigo

Para habilitar logon Parabéns pelo artigo de utilizadores do Azure AD, tem de ser aprovisionados em Parabéns pelo artigo. 

No caso de parabéns, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu **Parabéns pelo artigo** site da empresa como administrador.

1. No menu na parte superior, clique em **definições**.
   
   ![As definições](./media/kudos-tutorial/ic787806.png "definições")

1. Clique em **utilizador administrador**.

1. Clique nas **usuários** separador e, em seguida, clique em **adicionar um utilizador**.
   
   ![Utilizador administrador](./media/kudos-tutorial/ic787809.png "utilizador administrador")

1. Na **adicionar um utilizador** secção, execute os seguintes passos:
   
    ![Adicionar um usuário](./media/kudos-tutorial/ic787810.png "adicionar um utilizador")
   
    a. Tipo de **nome próprio**, **Apelido**, **E-Mail** e outros detalhes de uma conta válida do Azure Active Directory que pretende aprovisionar em caixas de texto relacionadas.
   
    b. Clique em **criar utilizador**.

>[!NOTE]
>Pode utilizar quaisquer outras Parabéns pelo artigo utilizador conta criação ferramentas ou APIs fornecidas pelo Parabéns pelo artigo para aprovisionar contas de utilizador do AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Parabéns pelo artigo.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Parabéns pelo artigo, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Parabéns pelo artigo**.

    ![Configurar o início de sessão único](./media/kudos-tutorial/tutorial_kudos_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de parabéns pelo artigo no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Parabéns pelo artigo. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png

