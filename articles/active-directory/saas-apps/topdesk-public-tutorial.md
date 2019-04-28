---
title: 'Tutorial: Integração do Active Directory do Azure com TOPdesk - público | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e TOPdesk - público.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 0873299f-ce70-457b-addc-e57c5801275f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89412040fdea32746574d8ae5bada9c017617b80
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129297"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Integração do Active Directory do Azure com TOPdesk - público

Neste tutorial, saiba como integrar TOPdesk - público com o Azure Active Directory (Azure AD).

Integrar TOPdesk - público com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao TOPdesk - público.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para TOPdesk - público (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com TOPdesk - público, precisa dos seguintes itens:

- Uma subscrição do Azure
- Um TOPdesk - público início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar TOPdesk - público a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-topdesk---public-from-the-gallery"></a>Adicionar TOPdesk - público a partir da Galeria
Para configurar a integração do TOPdesk - público para o Azure AD, terá de adicionar TOPdesk - público a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar TOPdesk - público a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **TOPdesk - Public**, selecione **TOPdesk - público** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![TOPdesk - público na lista de resultados](./media/topdesk-public-tutorial/tutorial_topdesk-public_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com TOPdesk - público com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único trabalhar, o Azure AD precisa de saber o que o utilizador de contraparte TOPdesk - público for para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no TOPdesk - público deve ser estabelecido.

No TOPdesk - públicos, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com TOPdesk - público, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um TOPdesk - utilizador de teste público](#create-a-topdesk---public-test-user)**  - para ter um equivalente da Eduarda Almeida na TOPdesk - público que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão único no portal do Azure e configurar o início de sessão único na sua TOPdesk - aplicativos pública.

**Para configurar o Azure AD início de sessão único com TOPdesk - público, execute os seguintes passos:**

1. No portal do Azure, sobre o **TOPdesk - Public** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/topdesk-public-tutorial/tutorial_topdesk-public_samlbase.png)

1. Sobre o **TOPdesk - domínio público e URLs** secção, execute os seguintes passos:

    ![Informações de início de sessão de único TOPdesk - domínio público e URLs](./media/topdesk-public-tutorial/tutorial_topdesk-public_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net`
    
    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/verify`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.topdesk.net/tas/public/login/saml`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. URL de resposta é explaned mais tarde no tutorial. Contacte [TOPdesk - equipa de suporte de cliente público](https://help.topdesk.com/saas/enterprise/user/) obter esses valores.  

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/topdesk-public-tutorial/tutorial_topdesk-public_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/topdesk-public-tutorial/tutorial_general_400.png)
    
1. Sobre o **TOPdesk - configuração pública** secção, clique em **configurar TOPdesk - público** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![TOPdesk - configuração pública](./media/topdesk-public-tutorial/tutorial_topdesk-public_configure.png) 

1. Inicie sessão no seu **TOPdesk - público** site da empresa como administrador.

1. Na **TOPdesk** menu, clique em **definições**.
   
    ![As definições](./media/topdesk-public-tutorial/ic790598.png "definições")

1. Clique em **definições de início de sessão**.
   
    ![Definições de início de sessão](./media/topdesk-public-tutorial/ic790599.png "definições de início de sessão")

1. Expanda a **definições de início de sessão** e, em seguida, clique **geral**.
   
    ![Gerais](./media/topdesk-public-tutorial/ic790600.png "geral")

1. Na **pública** secção a **início de sessão SAML** configuração secção, execute os seguintes passos:
   
    ![Definições técnicas](./media/topdesk-public-tutorial/ic790601.png "definições técnicas")
   
    a. Clique em **transferir** para transferir o ficheiro de metadados públicos e, em seguida, guarde-o localmente no seu computador.
   
    b. Abra o ficheiro de metadados baixado e, em seguida, localize a **AssertionConsumerService** nó.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/ic790619.png "AssertionConsumerService")
   
    c. Copiar o **AssertionConsumerService** valor, cole este valor na **URL de resposta** caixa de texto no **TOPdesk - domínio público e URLs** secção.      
   
1. Para criar um ficheiro de certificado, execute os seguintes passos:
    
    ![Certificado](./media/topdesk-public-tutorial/ic790606.png "certificado")
    
    a. Abra o ficheiro de metadados baixado a partir do portal do Azure.
    
    b. Expanda a **RoleDescriptor** nó que tem um **xsi: type** de **inseridas: ApplicationServiceType**.
    
    c. Copie o valor do **X509Certificate** nó.
    
    d. Guardar o copiado **X509Certificate** valor localmente no seu computador num arquivo.

1. Na **pública** secção, clique em **Add**.
    
    ![Início de sessão SAML](./media/topdesk-public-tutorial/ic790625.png "início de sessão SAML")

1. Sobre o **Assistente de configuração de SAML** caixa de diálogo página, execute os seguintes passos:
    
    ![Assistente de configuração de SAML](./media/topdesk-public-tutorial/ic790608.png "Assistente de configuração de SAML")
    
    a. Para carregar o ficheiro de metadados baixado no portal do Azure, em **metadados de Federação**, clique em **procurar**.

    b. Para carregar o ficheiro de certificado, em **certificados RSA ()**, clique em **procurar**.

    c. Para carregar o ficheiro do logótipo da equipe de suporte do TOPdesk, tem em **ícone de logótipo**, clique em **procurar**.

    d. Na **atributo de nome de utilizador** caixa de texto, tipo `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. Na **nome a apresentar** caixa de texto, escreva um nome para a sua configuração.

    f. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/topdesk-public-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/topdesk-public-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/topdesk-public-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/topdesk-public-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-topdesk---public-test-user"></a>Criar um TOPdesk - utilizador de teste público

Para permitir que os utilizadores do Azure AD logon TOPdesk - público, tem de ser aprovisionados em TOPdesk - público.  
No caso de TOPdesk - públicos, o aprovisionamento é uma tarefa manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:
1. Inicie sessão no seu **TOPdesk - público** site da empresa como administrador.

1. No menu na parte superior, clique em **TOPdesk \> New \> ficheiros de suporte \> pessoa**.
   
    ![Pessoa](./media/topdesk-public-tutorial/ic790628.png "pessoa")

1. Na caixa de diálogo nova pessoa, execute os seguintes passos:
   
    ![Nova pessoa](./media/topdesk-public-tutorial/ic790629.png "nova pessoa")
   
    a. Clique na guia Geral.

    b. Na **Apelido** caixa de texto, digite o apelido do utilizador, como Simon
 
    c. Selecione um **Site** para a conta.
 
    d. Clique em **Guardar**.

> [!NOTE]
> Pode utilizar qualquer outro TOPdesk - ferramentas de criação de conta de utilizador público ou APIs fornecidas pelo TOPdesk - público para aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para TOPdesk - público.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a TOPdesk - público, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **TOPdesk - público**.

    ![TOPdesk - ligação pública na lista de aplicações](./media/topdesk-public-tutorial/tutorial_topdesk-public_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clicar o TOPdesk - mosaico de público no painel de acesso, deve obter automaticamente com sessão iniciada para sua TOPdesk - aplicativos pública.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/topdesk-public-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-public-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-public-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-public-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-public-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-public-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-public-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-public-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-public-tutorial/tutorial_general_203.png

