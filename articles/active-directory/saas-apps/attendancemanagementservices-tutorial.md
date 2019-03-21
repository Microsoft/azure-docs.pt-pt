---
title: 'Tutorial: Integração do Active Directory do Azure com serviços de gestão de participação | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e dos serviços de gestão de participação.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3642bea878ca4d1582319e5e1d964dfa43ff061
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57893514"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Tutorial: Integração do Active Directory do Azure com serviços de gestão de participação

Neste tutorial, saiba como integrar os serviços de gestão de participação no Azure Active Directory (Azure AD).

Integração de serviços de gestão de participação com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso aos serviços de gestão de participação.
- Pode permitir que os utilizadores automaticamente obter sessão iniciada em serviços de gestão de participação (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com os serviços de gestão de participação, precisa do seguinte:

- Uma subscrição do Azure
- Dos serviços de gestão de participação no início de sessão único de subscrição ativada

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando os serviços de gestão de participação da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-attendance-management-services-from-the-gallery"></a>Adicionando os serviços de gestão de participação da Galeria
Para configurar a integração dos serviços de gestão de participação no Azure AD, terá de adicionar serviços de gestão de participação na partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar serviços de gestão de participação da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **dos serviços de gestão de participação**, selecione **dos serviços de gestão de participação** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Serviços de gestão de participação na lista de resultados](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com os serviços de gestão de presença com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte nos serviços de gestão de participação para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos serviços de gestão de participação deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com os serviços de gestão de participação, precisa concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **Criar um utilizador de teste de serviços de gestão de participação** - para ter um equivalente da Eduarda Almeida na participação dos serviços de gestão que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de serviços de gestão de participação.

**Para configurar o Azure AD início de sessão único com os serviços de gestão de participação, execute os seguintes passos:**

1. No portal do Azure, no **dos serviços de gestão de participação** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

1. Sobre o **participação no gerenciamento de serviços de domínio e URLs** secção, execute os seguintes passos:

    ![Participação no gerenciamento de serviços de domínio e URLs únicas início de sessão em informações](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de serviços de gestão de participação](https://www.obcnet.jp/) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

1. Na **configuração de serviços de gestão de participação** secção, clique em **configurar serviços de gestão de participação** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de serviços de gestão de participação](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

1. Numa janela do browser diferente, início de sessão para o site da sua empresa de serviços de gestão de participação como administrador.

1. Clique em **autenticação SAML** sob a **seção de gerenciamento de segurança**.

    ![Configuração de serviços de gestão de participação](./media/attendancemanagementservices-tutorial/user1.png)

1. Execute os seguintes passos:

    ![Configuração de serviços de gestão de participação](./media/attendancemanagementservices-tutorial/user2.png)

    a. Selecione **autenticação de utilização SAML**.

    b. Na **identificador** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 

    c. Na **URL de ponto final de autenticação** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    d. Clique em **selecionar um ficheiro** para carregar o certificado que transferiu a partir do Azure AD.

    e. Selecione **desative a autenticação de palavra-passe**.

    f. Clique em **registo**

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação! Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-attendance-management-services-test-user"></a>Criar um utilizador de teste de serviços de gestão de participação

Para ativar a utilizadores do Azure AD iniciar sessão serviços de gestão de participação, tem de ser aprovisionados para os serviços de gestão de participação. No caso dos serviços de gestão de participação, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no site da sua empresa dos serviços de gestão de participação como administrador.

1. Clique em **gestão de utilizadores** sob a **seção de gerenciamento de segurança**.

    ![Adicionar o funcionário](./media/attendancemanagementservices-tutorial/user5.png)

1. Clique em **novo início de sessão de regras**.

    ![Adicionar o funcionário](./media/attendancemanagementservices-tutorial/user3.png)

1. Na **OBCiD informações** secção, execute os seguintes passos:

    ![Adicionar o funcionário](./media/attendancemanagementservices-tutorial/user4.png)

    a. Na **OBCiD** caixa de texto, como o tipo de e-mail do utilizador **BrittaSimon\@contoso.com**.

    b. Na **palavra-passe** caixa de texto, escreva a palavra-passe do utilizador.

    c. Clique em **registo**


### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único, concedendo acesso aos serviços de gestão de participação.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a participação no Serviços de gestão, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **dos serviços de gestão de participação**.

    ![A ligação de serviços de gestão de participação na lista de aplicações](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de serviços de gestão de participação no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de serviços de gestão de participação.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png

