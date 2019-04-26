---
title: 'Tutorial: Integração do Active Directory do Azure com o sistema de monitorização do SensoScientific sem fios temperatura | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o sistema de monitorização do SensoScientific sem fios temperatura.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a625e82f41bee1b8e3980192076d24a7471953
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60340720"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Integração do Active Directory do Azure com o sistema de monitorização do SensoScientific sem fios temperatura

Neste tutorial, saiba como integrar o sistema de monitorização do SensoScientific sem fios temperatura com o Azure Active Directory (Azure AD).

Integração de sistema de monitorização do SensoScientific sem fios temperatura com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao sistema de monitorização do SensoScientific sem fios temperatura
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o sistema de monitorização do SensoScientific sem fios temperatura (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o sistema de monitorização do SensoScientific sem fios temperatura, terá dos seguintes itens:

- Uma subscrição do Azure
- Um sistema de monitorização do SensoScientific sem fios temperatura início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando o sistema de monitorização do SensoScientific sem fios temperatura da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Adicionando o sistema de monitorização do SensoScientific sem fios temperatura da Galeria
Para configurar a integração do sistema de monitorização do SensoScientific sem fios temperatura para o Azure AD, terá de adicionar o sistema de monitorização do SensoScientific sem fios temperatura partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o sistema de monitorização do SensoScientific sem fios temperatura da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **sistema de monitorização do SensoScientific sem fios temperatura**.

    ![Criar um utilizador de teste do Azure AD](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

1. No painel de resultados, selecione **sistema de monitorização do SensoScientific sem fios temperatura**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com SensoScientific sem fios temperatura sistema de monitorização com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no sistema de monitorização do SensoScientific sem fios temperatura para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no sistema de monitorização do SensoScientific sem fios temperatura deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no sistema de monitorização do SensoScientific sem fios temperatura.

Para configurar e testar o Azure AD início de sessão único com o sistema de monitorização do SensoScientific sem fios temperatura, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de sistema de monitorização do SensoScientific sem fios temperatura](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)**  - para ter um equivalente da Eduarda Almeida na SensoScientific sem fios temperatura sistema de monitorização que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de sistema de monitorização do SensoScientific sem fios temperatura.

**Para configurar o Azure AD início de sessão único com o sistema de monitorização do SensoScientific sem fios temperatura, execute os seguintes passos:**

1. No portal do Azure, sobre o **sistema de monitorização do SensoScientific sem fios temperatura** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

1. Sobre o **SensoScientific sem fios temperatura monitorização do sistema de domínio e URLs** secção, sem a necessidade de efetuar outros passos de dado que a aplicação já está pré-integrado com o Azure:

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_general_400.png)

1. Sobre o **configuração do sistema de monitorização SensoScientific sem fios temperatura** secção, clique em **configurar SensoScientific sem fios temperatura sistema de monitorização** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML** e **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

1. Inicie sessão sua aplicação de sistema de monitorização do SensoScientific sem fios temperatura como administrador.

1. No menu de navegação na parte superior, clique em **Configuration** e goto **configurar** sob **início de sessão único** para abrir o início de sessão único em definições.

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

1. Na **início de sessão único em definições** formulário execute os seguintes passos:
 
    a. Selecione **nome do emissor** como do Azure AD.
    
    b. Colar o **ID de entidade de SAML** que copiou do portal do Azure na caixa de texto do URL do emissor.
    
    c. Colar o **SAML único início de sessão no URL do serviço** que copiou do portal do Azure na única URL de início de sessão no serviço de texto.

    d. Colar o **URL de fim de sessão** que copiou do portal do Azure na caixa de texto do URL do serviço de fim de sessão único.

    e. Procure o certificado que tenha transferido a partir do portal do Azure e carregue aqui.
    
    f. Clique em **Guardar**.
  
> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado](https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/sensoscientific-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/sensoscientific-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/sensoscientific-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/sensoscientific-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Criar um utilizador de teste de sistema de monitorização do SensoScientific sem fios temperatura

Para ativar a utilizadores do Azure AD iniciar sessão no sistema de monitorização do SensoScientific sem fios temperatura, tem de ser aprovisionados no sistema de monitorização do SensoScientific sem fios temperatura. Trabalhar com [equipa de suporte do sistema de monitorização do SensoScientific sem fios temperatura](https://www.sensoscientific.com/contact-us/) para adicionar os utilizadores na plataforma do sistema de monitorização do SensoScientific sem fios temperatura. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao sistema de monitorização do SensoScientific sem fios temperatura.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida ao sistema de monitorização do SensoScientific sem fios temperatura, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **sistema de monitorização do SensoScientific sem fios temperatura**.

    ![Configurar o início de sessão único](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso. Clique no mosaico de sistema de monitorização do SensoScientific sem fios temperatura no painel de acesso, irá ser automaticamente com sessão iniciada para a sua aplicação de sistema de monitorização do SensoScientific sem fios temperatura. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/sensoscientific-tutorial/tutorial_general_203.png

