---
title: 'Tutorial: Integração do Active Directory do Azure com Workstars | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Workstars.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a9c1d13c1d51eeadae3ef9dad71e64618d1a5fe
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444706"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Tutorial: Integração do Active Directory do Azure com Workstars

Neste tutorial, saiba como integrar Workstars com o Azure Active Directory (Azure AD).

Integrar Workstars no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Workstars.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Workstars (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Workstars, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Workstars logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Workstars da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-workstars-from-the-gallery"></a>Adicionando Workstars da Galeria
Para configurar a integração do Workstars com o Azure AD, terá de adicionar Workstars a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Workstars a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Workstars**, selecione **Workstars** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Workstars na lista de resultados](./media/workstars-tutorial/tutorial_workstars_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Workstars com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Workstars a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Workstars deve ser estabelecido.

Workstars, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Workstars, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Workstars](#create-a-workstars-test-user)**  - para ter um equivalente da Eduarda Almeida na Workstars que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Workstars.

**Para configurar o Azure AD início de sessão único com Workstars, execute os seguintes passos:**

1. No portal do Azure, sobre o **Workstars** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/workstars-tutorial/tutorial_workstars_samlbase.png)

1. Sobre o **Workstars domínio e URLs** secção, execute os seguintes passos:

    ![Workstars domínio e URLs únicas início de sessão em informações](./media/workstars-tutorial/tutorial_workstars_url.png)

    a. Na **identificador** caixa de texto, escreva o URL: `https://workstars.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE] 
    > O valor não é real. Atualize o valor com o URL de resposta real. Contacte [equipa de suporte de Workstars](https://support.workstars.com) para obter o valor.
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/workstars-tutorial/tutorial_workstars_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/workstars-tutorial/tutorial_general_400.png)

1. Sobre o **Workstars configuração** secção, clique em **configurar Workstars** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Workstars](./media/workstars-tutorial/tutorial_workstars_configure.png) 

1. Na outra janela do browser, inicie sessão site da sua empresa Workstars como um administrador.

1. Na barra de ferramentas principal, clique em **definições**.

    ![Definições de Workstars](./media/workstars-tutorial/tutorial_workstars_sett.png)

1. Aceda a **iniciar sessão** > **definições**.

    ![Workstars início de sessão](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Definições de Workstars](./media/workstars-tutorial/tutorial_workstars_settings.png)

1. Sobre o **único início de sessão (SAML) - definições** página, execute os seguintes passos:
    
    ![Workstars saml](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Na **nome do fornecedor de identidade** caixa de texto, tipo **do Office 365**.

    b. Na **ID de entidade do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    c. Copie o conteúdo do ficheiro de certificado transferido no bloco de notas e, em seguida, cole-o para o **x509 certificado** caixa de texto. 

    d. Na **URL de SSO SAML** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.
    
    e. Na **URL de fim de sessão remoto** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure. 

    f. Selecione **ID de nome** como **E-Mail (predefinição)**.

    g. Clique em **Confirmar**.
    
> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/workstars-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/workstars-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/workstars-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/workstars-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
  
### <a name="create-a-workstars-test-user"></a>Criar um utilizador de teste Workstars

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Workstars. Trabalhar com [equipa de suporte de Workstars](https://support.workstars.com) para adicionar os utilizadores na plataforma Workstars.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Workstars.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Workstars, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Workstars**.

    ![A ligação de Workstars na lista de aplicações](./media/workstars-tutorial/tutorial_workstars_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Workstars no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Workstars.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/workstars-tutorial/tutorial_general_01.png
[2]: ./media/workstars-tutorial/tutorial_general_02.png
[3]: ./media/workstars-tutorial/tutorial_general_03.png
[4]: ./media/workstars-tutorial/tutorial_general_04.png

[100]: ./media/workstars-tutorial/tutorial_general_100.png

[200]: ./media/workstars-tutorial/tutorial_general_200.png
[201]: ./media/workstars-tutorial/tutorial_general_201.png
[202]: ./media/workstars-tutorial/tutorial_general_202.png
[203]: ./media/workstars-tutorial/tutorial_general_203.png

