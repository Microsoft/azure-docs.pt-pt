---
title: 'Tutorial: Integração do Active Directory do Azure com o Infor revenda – gerenciamento de informação | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Infor revenda – gerenciamento de informações.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 5ff49168-ef81-4169-8e5e-dc86e24dd5e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7c4ac61caae371ebce7c273a4b48244a45c3519
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57837682"
---
# <a name="tutorial-azure-active-directory-integration-with-infor-retail--information-management"></a>Tutorial: Integração do Active Directory do Azure com o Infor revenda – gerenciamento de informações

Neste tutorial, saiba como integrar o Infor revenda – gerenciamento de informações com o Azure Active Directory (Azure AD).

Integração de varejo Infor – gerenciamento de informações com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Infor revenda – gerenciamento de informações.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada de revenda Infor – gerenciamento de informações (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Infor revenda – gerenciamento de informações, precisa do seguinte:

- Uma subscrição do Azure
- Uma revenda Infor – gerenciamento de informações início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Infor revenda – gerenciamento de informações a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-infor-retail--information-management-from-the-gallery"></a>Adicionar Infor revenda – gerenciamento de informações a partir da Galeria
Para configurar a integração do varejo Infor – gerenciamento de informações para o Azure AD, terá de adicionar Infor revenda – gerenciamento de informações da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Infor revenda – gerenciamento de informações a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Infor revenda – gerenciamento de informações**, selecione **Infor revenda – gerenciamento de informação** no painel de resultados, em seguida, clique em **Add** botão para adicionar o aplicação.

    ![Infor revenda – gerenciamento de informações na lista de resultados](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Infor revenda – gestão de informações com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente no varejo Infor – gerenciamento de informações a um utilizador no Azure AD. Em outras palavras, uma relação entre um utilizador do Azure AD e o utilizador relacionado no varejo Infor – gerenciamento de informações de ligação tem de ser estabelecida.

No varejo Infor – gerenciamento de informações, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Infor revenda – gerenciamento de informações, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar uma revenda Infor – o utilizador de teste de gerenciamento de informação](#create-an-infor-retail--information-management-test-user)**  - para ter um equivalente da Eduarda Almeida no varejo Infor – gerenciamento de informações que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único no seu varejo Infor – o aplicativo de gerenciamento de informações.

**Para configurar o Azure AD início de sessão único com o Infor revenda – gerenciamento de informações, execute os seguintes passos:**

1. No portal do Azure, sobre o **Infor revenda – gerenciamento de informações** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_samlbase.png)

1. Sobre o **Infor revenda – URLs de domínio de gestão de informações e** secção, execute os seguintes passos, se desejar configurar a aplicação no modo de IDP iniciado:

    ![Infor revenda – URLs de domínio de gestão de informações e único início de sessão informações IDP](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com os seguintes padrões: 
    
    |   |
    | -- |
    | `https://<company name>.mingle.infor.com` |
    | `http://<company name>.mingledev.infor.com` |

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.mingle.infor.com/sp/ACS.saml2`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Infor revenda – o domínio de gestão de informações e URLs únicas início de sessão informações SP](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.mingle.infor.com/<company code>`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [Infor revenda – a equipa de suporte do cliente de gestão de informações](mailto:innovate@infor.com) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/inforretailinformationmanagement-tutorial/tutorial_general_400.png)
    
1. Para configurar o início de sessão único num **Infor revenda – gerenciamento de informações** lado, terá de enviar o transferido **XML de metadados** para [Infor revenda – a equipa de suporte de gestão de informações](mailto:innovate@infor.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/inforretailinformationmanagement-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/inforretailinformationmanagement-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/inforretailinformationmanagement-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/inforretailinformationmanagement-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-an-infor-retail--information-management-test-user"></a>Criar uma revenda Infor – o utilizador de teste de gerenciamento de informações

Nesta secção, vai criar um usuário chamado Eduarda Almeida no varejo Infor – gerenciamento de informações. Trabalhe em conjunto com [Infor revenda – equipa de suporte de gestão de informações](mailto:innovate@infor.com) para adicionar os utilizadores no varejo Infor – plataforma de gestão de informações.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso de revenda Infor – gerenciamento de informações.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida de revenda Infor – gerenciamento de informações, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Infor revenda – gerenciamento de informações**.

    ![A revenda Infor – ligação de gerenciamento de informações na lista de aplicações](./media/inforretailinformationmanagement-tutorial/tutorial_inforretailinformationmanagement_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica do varejo Infor – mosaico de gerenciamento de informações no painel de acesso, deve obter automaticamente com sessão iniciada para o varejo Infor – aplicação de gestão de informações.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_01.png
[2]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_02.png
[3]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_03.png
[4]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_04.png

[100]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_100.png

[200]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_200.png
[201]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_201.png
[202]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_202.png
[203]: ./media/inforretailinformationmanagement-tutorial/tutorial_general_203.png

