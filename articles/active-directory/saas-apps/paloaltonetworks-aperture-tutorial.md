---
title: 'Tutorial: Integração do Active Directory do Azure com o da Palo Alto Networks - abertura | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e da Palo Alto Networks - abertura.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41c514706a671f755eb5b0efd5e3eefa5052c6e2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450027"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Tutorial: Integração do Active Directory do Azure com o da Palo Alto Networks - abertura

Neste tutorial, saiba como integrar da Palo Alto Networks - abertura com o Azure Active Directory (Azure AD).

Integração da Palo Alto Networks - abertura com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso da Palo Alto Networks - abertura.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Palo Alto Networks - abertura (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com da Palo Alto Networks - abertura, terá dos seguintes itens:

- Uma subscrição do Azure
- Da Palo Alto redes – abertura de logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adição da Palo Alto Networks - abertura da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Adição da Palo Alto Networks - abertura da Galeria
Para configurar a integração da Palo Alto Networks - abertura para o Azure AD, terá de adicionar da Palo Alto Networks - abertura da galeria, à sua lista de aplicações de SaaS geridas.

**Para adicionar da Palo Alto Networks - abertura a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Palo Alto Networks - abertura**, selecione **Palo Alto Networks - abertura** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Da Palo Alto Networks - abertura na lista de resultados](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único da Palo Alto Networks - abertura com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte da Palo Alto Networks - abertura para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado da Palo Alto Networks - abertura deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único da Palo Alto Networks - abertura, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar da Palo Alto Networks - utilizador de teste de abertura](#create-a-palo-alto-networks---aperture-test-user)**  - para ter um equivalente da Eduarda Almeida na Palo Alto Networks - abertura que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único nas redes da Palo Alto - aplicação de abertura.

**Para configurar o Azure AD início de sessão único da Palo Alto Networks - abertura, execute os seguintes passos:**

1. No portal do Azure, sobre o **Palo Alto Networks - abertura** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_samlbase.png)

1. Sobre o **da Palo Alto Networks - domínio de abertura e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Informações de início de sessão de único da Palo Alto Networks - domínio de abertura e URLs](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Informações de início de sessão de único da Palo Alto Networks - domínio de abertura e URLs](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [Palo Alto Networks - equipa de suporte de cliente de abertura](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/paloaltonetworks-aperture-tutorial/tutorial_general_400.png)


1. Sobre o **Palo Alto Networks - configuração de abertura** secção, clique em **configurar da Palo Alto Networks - abertura** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![O link configurar](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_configure.png)

1. Numa janela do browser web diferente, início de sessão para Palo Alto Networks - abertura como administrador.

1. Na barra de menus superior, clique em **definições**.

    ![O separador de definições](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

1. Navegue para **APLICATIVO** secção clique **autenticação** à esquerda do menu de formulário.

    ![O separador de autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
1. Sobre o **autenticação** página execute os seguintes passos:
    
    ![O separador de autenticação](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Verifique a **ativar único início de sessão-On(Supported SSP Providers are Okta, One login)** partir **Single Sign-On** campo.

    b. Na **ID do fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure.

    c. Clique em **Escolher ficheiro** para carregar o certificado transferido a partir do Azure AD no **certificado do fornecedor de identidade** campo.

    d. Na **URL de SSO de fornecedor de identidade** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    e. Reveja as informações do IdP partir **informações de abertura** secção e transfira o certificado a partir **chave de abertura** campo.

    f. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/paloaltonetworks-aperture-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/paloaltonetworks-aperture-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/paloaltonetworks-aperture-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/paloaltonetworks-aperture-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-palo-alto-networks---aperture-test-user"></a>Criar da Palo Alto Networks - utilizador de teste de abertura

Nesta secção, vai criar um usuário chamado Eduarda Almeida da Palo Alto Networks - abertura. Trabalhar com [Palo Alto Networks - equipa de suporte de cliente de abertura](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) para adicionar os utilizadores da Palo Alto Networks - plataforma de abertura. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Palo Alto Networks - abertura.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida para Palo Alto Networks - abertura, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Palo Alto Networks - abertura**.

    ![Da Palo Alto Networks - ligação de abertura na lista de aplicações](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica em da Palo Alto Networks - mosaico de abertura no painel de acesso, deve obter automaticamente com sessão iniciada para as suas redes da Palo Alto - aplicação de abertura.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_01.png
[2]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_02.png
[3]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_03.png
[4]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_04.png

[100]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_100.png

[200]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_200.png
[201]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_201.png
[202]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_202.png
[203]: ./media/paloaltonetworks-aperture-tutorial/tutorial_general_203.png

