---
title: 'Tutorial: Integração do Active Directory do Azure com o administrador de acesso do Zscaler privada | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o administrador de acesso do Zscaler privada.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9cdb842c4cebe48f2f6534417a7fe6aa4686d1e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894687"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: Integração do Active Directory do Azure com o administrador de acesso do privada Zscaler

Neste tutorial, saiba como integrar o administrador de acesso do Zscaler privada com o Azure Active Directory (Azure AD).

Integrar o administrador de acesso do Zscaler privado no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso para o administrador de acesso do Zscaler privada.
- Pode permitir que os utilizadores automaticamente obter sessão iniciada em Zscaler privada o administrador de acesso (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o administrador de acesso do Zscaler privada, precisa do seguinte:

- Uma subscrição do Azure
- Um administrador de acesso do Zscaler privada logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o administrador de acesso do Zscaler privada da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Adicionando o administrador de acesso do Zscaler privada da Galeria
Para configurar a integração do administrador de acesso do Zscaler privada com o Azure AD, terá de adicionar administrador de acesso do Zscaler privada a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar administrador de acesso do Zscaler privada a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **administrador de acesso do Zscaler privada**, selecione **administrador de acesso do Zscaler privada** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Administrador de acesso do Zscaler privada na lista de resultados](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Zscaler privada administrador de acesso com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no administrador de acesso do Zscaler privada para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no administrador de acesso do Zscaler privada tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o administrador de acesso do Zscaler privada, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do administrador de acesso privado Zscaler](#create-a-zscaler-private-access-administrator-test-user)**  - para ter um equivalente da Eduarda Almeida no administrador de acesso de Zscaler privada que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de administrador de acesso do Zscaler privada.

**Para configurar o Azure AD início de sessão único com o administrador de acesso do Zscaler privada, execute os seguintes passos:**

1. No portal do Azure, sobre o **administrador de acesso do Zscaler privada** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

1. Na **Zscaler privada ao administrador de acesso de domínio e URLs** secção se desejar configurar a aplicação na **IDP** iniciada pelo modo:

    ![Administrador de acesso do Zscaler privada domínio e URLs únicas início de sessão em informações](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Verificar **Mostrar definições de URL avançadas**

    d. Na **RelayState** caixa de texto, escreva um valor: `idpadminsso`

1.  Se desejar configurar a aplicação no **SP** modo iniciado execute os seguintes passos:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de identificador, o URL de resposta e início de sessão real. Contacte [equipa de suporte de administrador de acesso do Zscaler privada](https://help.zscaler.com/zpa-submit-ticket) obter esses valores.
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

1. Numa janela do browser web diferente, início de sessão privada Zscaler o administrador de acesso como administrador.

1. Na parte superior, clique em **Administration** e navegue até à **autenticação** secção clique **configuração do IdP**.

    ![Administrador de administrador de acesso privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. No canto superior direito, clique em **adicionar a configuração do IdP**. 

    ![Administrador de acesso do Zscaler privada addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

1. Sobre o **adicionar a configuração do IdP** página execute os seguintes passos:
 
    ![Administrador de acesso do Zscaler privada idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Clique em **selecionar ficheiro** para carregar o ficheiro de metadados baixado do Azure AD no **carregamento de ficheiros de metadados do IdP** campo.

    b. Ele lê a **metadados de IdP** do Azure AD e preenche todas as informações de campos, conforme mostrado abaixo.

    ![Administrador de acesso do Zscaler privada idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Selecione **início de sessão único** como **administrador**.

    d. Selecione o seu domínio a partir **domínios** campo.
    
    e. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Criar um utilizador de teste do administrador de acesso privado Zscaler

Para permitir que utilizadores do Azure AD iniciar sessão para o administrador de acesso do Zscaler privada, eles têm aprovisionados para o administrador de acesso do Zscaler privada. No caso do Zscaler privada ao administrador de acesso, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa Zscaler privada ao administrador de acesso como administrador.

1. Na parte superior, clique em **Administration** e navegue até à **autenticação** secção clique **configuração do IdP**.

    ![Administrador de administrador de acesso privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. Clique em **administradores** do lado esquerdo do menu.

    ![Administrador de administrador de acesso privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

1. No canto superior direito, clique em **Adicionar administrador**:

    ![Administrador de acesso do Zscaler privada Adicionar administrador](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

1. Na **Adicionar administrador** página, execute os seguintes passos:

    ![Administrador de utilizador de administrador de acesso privado Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Na **nome de utilizador** caixa de texto, introduza o e-mail do utilizador, como **BrittaSimon\@contoso.com**.

    b. Na **palavra-passe** caixa de texto, escreva a palavra-passe.

    c. Na **Confirmar palavra-passe** caixa de texto, escreva a palavra-passe.

    d. Selecione **função** como **administrador de acesso do Zscaler privada**.

    e. Na **E-Mail** caixa de texto, introduza o e-mail do utilizador, como **BrittaSimon\@contoso.com**.

    f. Na **Phone** caixa de texto, escreva o número de telefone.

    g. Na **fuso horário** caixa de texto, selecione o fuso horário.

    h. Clique em **Guardar**.  

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para o administrador de acesso do Zscaler privada.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida para o administrador de acesso do Zscaler privada, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **administrador de acesso do Zscaler privada**.

    ![A ligação de administrador de acesso do Zscaler privada na lista de aplicações](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de administrador de acesso do Zscaler privada no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de administrador de acesso do Zscaler privada.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

