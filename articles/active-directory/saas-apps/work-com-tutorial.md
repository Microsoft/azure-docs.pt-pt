---
title: 'Tutorial: Integração do Active Directory do Azure com Work.com | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Work.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a37a811c7944334571b4043d8ba937ffadc1e71
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57891574"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Integração do Active Directory do Azure com Work.com

Neste tutorial, saiba como integrar Work.com com o Azure Active Directory (Azure AD).

Integrar Work.com no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Work.com
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Work.com (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Work.com, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Work.com logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Work.com a partir da Galeria
1. Configurar e testar o Azure AD início de sessão único

## <a name="add-workcom-from-the-gallery"></a>Adicionar Work.com a partir da Galeria
Para configurar a integração do Work.com com o Azure AD, terá de adicionar Work.com a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Work.com a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Work.com**, selecione **Work.com** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Adicionar a partir da Galeria](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Work.com com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Work.com a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Work.com deve ser estabelecido.

Work.com, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Work.com, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Work.com](#create-a-workcom-test-user)**  - para ter um equivalente da Eduarda Almeida na Work.com que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Work.com.

>[!NOTE]
>Para configurar o início de sessão único, terá de configurar um nome de domínio personalizado Work.com ainda. Tem de definir, pelo menos, um nome de domínio, testar o seu nome de domínio e implementá-la em toda a organização.

**Para configurar o Azure AD início de sessão único com Work.com, execute os seguintes passos:**

1. No portal do Azure, sobre o **Work.com** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Inicio de sessão baseado em SAML](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

1. Sobre o **Work.com domínio e URLs** secção, efetue o seguinte:

    ![Secção Work.com domínio e URLs](./media/work-com-tutorial/tutorial_work-com_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Work.com](https://help.salesforce.com/articleView?id=000159855&type=3) para obter este valor. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Secção do certificado de assinatura SAML](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

1. Clique em **guardar** botão.

    ![Botão Guardar](./media/work-com-tutorial/tutorial_general_400.png)

1. Sobre o **Work.com configuração** secção, clique em **configurar Work.com** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Seção de configuração de Work.com](./media/work-com-tutorial/tutorial_work-com_configure.png) 
1. Inicie sessão no seu inquilino Work.com como administrador.

1. Aceda a **configuração**.
   
    ![Programa de configuração](./media/work-com-tutorial/ic794108.png "configuração")

1. No painel de navegação esquerdo, no **Administer** secção, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **Meu domínio** página. 
   
    ![Meu domínio](./media/work-com-tutorial/ic767825.png "meu domínio")

1. Para verificar que seu domínio foi configurado corretamente, certifique-se de que está a ser "**passo 4 implementadas para os utilizadores**" e rever seu "**minhas configurações de domínio**".
   
    ![Domínio implementado para o usuário](./media/work-com-tutorial/ic784377.png "implementado utilizador de domínio")

1. Inicie sessão no seu inquilino Work.com.

1. Aceda a **configuração**.
    
    ![Programa de configuração](./media/work-com-tutorial/ic794108.png "configuração")

1. Expanda a **controlos de segurança** e, em seguida, clique **configurações de logon único**.
    
    ![Único configurações de logon](./media/work-com-tutorial/ic794113.png "único configurações de logon")

1. Sobre o **definições de início de sessão único** caixa de diálogo página, execute os seguintes passos:
    
    ![SAML ativada](./media/work-com-tutorial/ic781026.png "SAML ativada")
    
    a. Selecione **SAML ativada**.
    
    b. Clique em **Novo**.

1. Na **SAML único configurações de logon** secção, execute os seguintes passos:
    
    ![SAML única início de sessão definição](./media/work-com-tutorial/ic794114.png "SAML única início de sessão na definição")
    
    a. Na **nome** caixa de texto, escreva um nome para a sua configuração.  
       
    > [!NOTE]
    > Fornecer um valor para **Name** preencher automaticamente o **nome da API** caixa de texto.
    
    b. Na **emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    c. Para carregar o certificado transferido a partir do portal do Azure, clique em **procurar**.
    
    d. Na **Id de entidade** caixa de texto, tipo `https://salesforce-work.com`.
    
    e. Como **tipo de identidade de SAML**, selecione **asserção contém o ID de Federação do objeto de utilizador**.
    
    f. Como **localização de identidade de SAML**, selecione **identidade é no elemento NameIdentfier da declaração de assunto**.
    
    g. Na **URL de início de sessão do fornecedor de identidade** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.

    h. Na **URL de fim de sessão do fornecedor de identidade** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.
    
    i. Como **fornecedor iniciada solicitar vínculo de serviço**, selecione **HTTP Post**.
    
    j. Clique em **Guardar**.

1. No portal clássico do Work.com, no painel de navegação esquerdo, clique em **gestão de domínios** para expandir a secção relacionada e, em seguida, clique em **meu domínio** para abrir o **meu domínio** página. 
    
    ![Meu domínio](./media/work-com-tutorial/ic794115.png "meu domínio")

1. No **meu domínio** página, além da **marca de página de início de sessão** secção, clique em **editar**.
    
    ![Página de início de sessão de imagem corporativa](./media/work-com-tutorial/ic767826.png "imagem corporativa da página início de sessão")

1. No **uma imagem corporativa de página de início de sessão** na página a **serviço de autenticação** secção, o nome do seu **as definições de SSO SAML** é apresentado. Selecione-o e, em seguida, clique em **guardar**.
    
    ![Página de início de sessão de imagem corporativa](./media/work-com-tutorial/ic784366.png "imagem corporativa da página início de sessão")

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/work-com-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Utilizadores e grupos -> todos os utilizadores](./media/work-com-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Adicionar](./media/work-com-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Página de diálogo de utilizador](./media/work-com-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-workcom-test-user"></a>Criar um utilizador de teste Work.com
Para utilizadores do Azure Active Directory poder iniciar sessão, tem de ser aprovisionados para Work.com. No caso de Work.com, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:
1. Inicie sessão site da sua empresa Work.com como um administrador.

1. Aceda a **configuração**.
   
    ![Programa de configuração](./media/work-com-tutorial/IC794108.png "configuração")
1. Aceda a **gerir utilizadores \> utilizadores**.
   
    ![Gerir utilizadores](./media/work-com-tutorial/IC784369.png "gerir utilizadores")

1. Clique em **novo utilizador**.
   
    ![Todos os utilizadores](./media/work-com-tutorial/IC794117.png "todos os utilizadores")

1. Na seção Editar utilizador, execute os seguintes passos, nos atributos de um Azure válido conta AD que pretende aprovisionar em caixas de texto relacionadas:
   
    ![Edição do utilizador](./media/work-com-tutorial/ic794118.png "edição do utilizador")
   
    a. Na **nome próprio** caixa de texto, tipo a **nome próprio** do utilizador **Eduarda**.
    
    b. Na **sobrenome** caixa de texto, tipo a **Apelido** do utilizador **Simon**.
    
    c. Na **Alias** caixa de texto, tipo a **nome** do utilizador **BrittaS**.
    
    d. Na **E-Mail** caixa de texto, tipo a **endereço de e-mail** do utilizador **Brittasimon\@contoso.com**.
    
    e. Na **nome de utilizador** caixa de texto, escreva um nome de utilizador do utilizador, como **Brittasimon\@contoso.com**.
    
    f. Na **nome Nick** caixa de texto, escreva um **nick nome** do utilizador **Simon**.
    
    g. Selecione **função**, **licença de utilizador**, e **perfil**.
    
    h. Clique em **Guardar**.  
      
    > [!NOTE]
    > O titular da conta do Azure AD irá receber um e-mail, incluindo uma ligação para confirmar a conta até se tornar Active Directory.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Work.com.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Work.com, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Work.com**.

    ![Work.com na lista da aplicação](./media/work-com-tutorial/tutorial_work-com_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Work.com no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Work.com.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

