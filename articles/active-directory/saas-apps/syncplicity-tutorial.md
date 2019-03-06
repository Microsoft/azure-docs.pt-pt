---
title: 'Tutorial: Integração do Active Directory do Azure com Syncplicity | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f6fcc4d2920841c730ef179497f9184b1f6649d
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451965"
---
# <a name="tutorial-azure-active-directory-integration-with-syncplicity"></a>Tutorial: Integração do Active Directory do Azure com Syncplicity

Neste tutorial, saiba como integrar Syncplicity com o Azure Active Directory (Azure AD).

Integrar Syncplicity no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Syncplicity
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Syncplicity (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Syncplicity, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Syncplicity logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Syncplicity da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-syncplicity-from-the-gallery"></a>Adicionando Syncplicity da Galeria
Para configurar a integração do Syncplicity com o Azure AD, terá de adicionar Syncplicity a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Syncplicity a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Syncplicity**.

    ![Criar um utilizador de teste do Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_search.png)

1. No painel de resultados, selecione **Syncplicity**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/syncplicity-tutorial/tutorial_syncplicity_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Syncplicity com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Syncplicity a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Syncplicity deve ser estabelecido.

Syncplicity, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Syncplicity, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Syncplicity](#creating-a-syncplicity-test-user)**  - para ter um equivalente da Eduarda Almeida na Syncplicity que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Syncplicity.

**Para configurar o Azure AD início de sessão único com Syncplicity, execute os seguintes passos:**

1. No portal do Azure, sobre o **Syncplicity** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/syncplicity-tutorial/tutorial_syncplicity_samlbase.png)

1. Sobre o **Syncplicity domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/syncplicity-tutorial/tutorial_syncplicity_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.syncplicity.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Syncplicity](https://www.syncplicity.com/contact-us) obter esses valores. 
 

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/syncplicity-tutorial/tutorial_syncplicity_certificate.png) 

  
1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/syncplicity-tutorial/tutorial_general_400.png)

1. Sobre o **Syncplicity configuração** secção, clique em **configurar Syncplicity** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/syncplicity-tutorial/tutorial_syncplicity_configure.png) 

1. Inicie sessão no seu **Syncplicity** inquilino.

1. No menu na parte superior, clique em **administrador**, selecione **definições**e, em seguida, clique em **domínio personalizado e início de sessão único**.
   
    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Sobre o **único início de sessão (SSO)** caixa de diálogo página, execute os seguintes passos:
   
    ![Início de sessão único \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")   

    a. Na **Custom Domain** caixa de texto, escreva o nome do seu domínio.
  
    b. Selecione **habilitado** como **único início de sessão em estado**.

    c. Na **Id de entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.

    d. Na **URL da página de início de sessão** caixa de texto, colar a **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.

    e. Na **URL de página de fim de sessão** caixa de texto, colar a **URL de fim de sessão** que copiou do portal do Azure.

    f. Na **certificado do fornecedor de identidade**, clique em **Escolher ficheiro**e, em seguida, carregue o certificado que transferiu do portal do Azure. 

    g. Clique em **guardar alterações**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/syncplicity-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/syncplicity-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/syncplicity-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/syncplicity-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-syncplicity-test-user"></a>Criar um utilizador de teste Syncplicity
Para utilizadores do AAD conseguir iniciar sessão, têm de ser aprovisionados para aplicação Syncplicity. Esta secção descreve como criar contas de utilizador do AAD no Syncplicity.

**Para Aprovisionar uma conta de utilizador para Syncplicity, execute os seguintes passos:**

1. Inicie sessão no seu **Syncplicity** inquilino (por exemplo: `https://company.Syncplicity.com`).

1. Clique em **administrador** e selecione **contas de utilizador**.

1. Clique em **adicionar um utilizador**.
   
    ![Gerir utilizadores](./media/syncplicity-tutorial/ic769764.png "gerir utilizadores")

1. Tipo de **endereços de E-Mail** de uma conta do AAD que pretende aprovisionar, selecione **utilizador** como **função**e, em seguida, clique em **seguinte**.
   
    ![Informações da conta](./media/syncplicity-tutorial/ic769765.png "informações da conta")
   
    >[!NOTE]
    >O titular da conta do AAD, obtém um e-mail, incluindo uma ligação para confirmar e ativar a conta. 
    > 

1. Selecione um grupo na sua empresa que deve se tornar um membro do seu novo utilizador e, em seguida, clique em **seguinte**.
   
    ![Associação de grupo](./media/syncplicity-tutorial/ic769772.png "associação de grupo")
   
    >[!NOTE]
    >Se não houver nenhum grupo listado, clique em **seguinte**. 
    > 

1. Selecione as pastas que pretende colocar sob o controle do Syncplicity no computador do usuário e, em seguida, clique em **seguinte**.
   
    ![Pastas de Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity pastas")

>[!NOTE]
>Pode utilizar quaisquer outras Syncplicity utilizador conta criação ferramentas ou APIs fornecidas pelo Syncplicity para aprovisionar contas de utilizador do AAD. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Syncplicity.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Syncplicity, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Syncplicity**.

    ![Configurar o início de sessão único](./media/syncplicity-tutorial/tutorial_syncplicity_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Syncplicity no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Syncplicity.
## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/syncplicity-tutorial/tutorial_general_01.png
[2]: ./media/syncplicity-tutorial/tutorial_general_02.png
[3]: ./media/syncplicity-tutorial/tutorial_general_03.png
[4]: ./media/syncplicity-tutorial/tutorial_general_04.png

[100]: ./media/syncplicity-tutorial/tutorial_general_100.png

[200]: ./media/syncplicity-tutorial/tutorial_general_200.png
[201]: ./media/syncplicity-tutorial/tutorial_general_201.png
[202]: ./media/syncplicity-tutorial/tutorial_general_202.png
[203]: ./media/syncplicity-tutorial/tutorial_general_203.png

