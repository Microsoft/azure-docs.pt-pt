---
title: 'Tutorial: Integração do Active Directory do Azure com o ciclo de vida de SCC | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o ciclo de vida de SCC.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6376a479d2e6969505310de0e8e73e74738a04c0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881336"
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Tutorial: Integração do Active Directory do Azure com o ciclo de vida de SCC

Neste tutorial, saiba como integrar o ciclo de vida de SCC com o Azure Active Directory (Azure AD).

Integrar o ciclo de vida de SCC no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ciclo de vida de SCC
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o ciclo de vida de SCC (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ciclo de vida do SCC, terá dos seguintes itens:

- Uma subscrição do Azure
- Um ciclo de vida de SCC logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o ciclo de vida de SCC da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-scc-lifecycle-from-the-gallery"></a>Adicionando o ciclo de vida de SCC da Galeria
Para configurar a integração do ciclo de vida de SCC com o Azure AD, terá de adicionar o ciclo de vida de SCC partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o ciclo de vida de SCC da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **ciclo de vida de SCC**.

    ![Criar um utilizador de teste do Azure AD](./media/scclifecycle-tutorial/tutorial_scclifecycle_search.png)

1. No painel de resultados, selecione **ciclo de vida de SCC**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/scclifecycle-tutorial/tutorial_scclifecycle_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o ciclo de vida de SCC com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no ciclo de vida de SCC a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no ciclo de vida de SCC deve ser estabelecido.

No ciclo de vida de SCC, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o ciclo de vida de SCC, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do ciclo de vida de SCC](#creating-an-scc-lifecycle-test-user)**  - para ter um equivalente da Eduarda Almeida no ciclo de vida de SCC que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de ciclo de vida de SCC.

**Para configurar o Azure AD início de sessão único com o ciclo de vida de SCC, execute os seguintes passos:**

1. No portal do Azure, sobre o **ciclo de vida de SCC** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/scclifecycle-tutorial/tutorial_scclifecycle_samlbase.png)

1. Sobre o **SCC ciclo de vida do domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/scclifecycle-tutorial/tutorial_scclifecycle_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<sub-domain>.scc.com/ic7/welcome/customer/PICTtest.aspx`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|--|
    | `https://bs1.scc.com/<entity>`|
    | `https://lifecycle.scc.com/<entity>`|
    
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de ciclo de vida de SCC](mailto:lifecycle.support@scc.com) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/scclifecycle-tutorial/tutorial_scclifecycle_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/scclifecycle-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **ciclo de vida de SCC** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte do ciclo de vida de SCC](mailto:lifecycle.support@scc.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

   >[!NOTE]
   >Início de sessão único tem de ser ativada pela equipa de suporte do ciclo de vida de SCC.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/scclifecycle-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/scclifecycle-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/scclifecycle-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/scclifecycle-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-scc-lifecycle-test-user"></a>Criar um utilizador de teste do ciclo de vida de SCC

Para habilitar os utilizadores do Azure AD iniciar sessão no ciclo de vida de SCC, tem de ser aprovisionados no ciclo de vida de SCC. Não existe nenhum item de ação para configurar o aprovisionamento de utilizadores para o ciclo de vida de SCC.

Quando um utilizador atribuído tenta iniciar sessão no ciclo de vida de SCC, uma conta de ciclo de vida de SCC é criada automaticamente se necessário.

> [!NOTE]
> O titular da conta do Azure Active Directory recebe uma mensagem de e-mail e segue uma ligação para confirmar a respetiva conta até se tornar Active Directory.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para o ciclo de vida de SCC.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida ao ciclo de vida de SCC, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda à **aplicações empresariais** , em seguida, clique em **todas as aplicações.**

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **ciclo de vida de SCC**.

    ![Configurar o início de sessão único](./media/scclifecycle-tutorial/tutorial_scclifecycle_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de ciclo de vida de SCC no painel de acesso, deve obter automaticamente com sessão iniciada a aplicação de ciclo de vida de SCC.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/scclifecycle-tutorial/tutorial_general_01.png
[2]: ./media/scclifecycle-tutorial/tutorial_general_02.png
[3]: ./media/scclifecycle-tutorial/tutorial_general_03.png
[4]: ./media/scclifecycle-tutorial/tutorial_general_04.png

[100]: ./media/scclifecycle-tutorial/tutorial_general_100.png

[200]: ./media/scclifecycle-tutorial/tutorial_general_200.png
[201]: ./media/scclifecycle-tutorial/tutorial_general_201.png
[202]: ./media/scclifecycle-tutorial/tutorial_general_202.png
[203]: ./media/scclifecycle-tutorial/tutorial_general_203.png

