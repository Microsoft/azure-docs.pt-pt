---
title: 'Tutorial: Integração do Active Directory do Azure com Leapsome | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37549cc76e1490b0758de8e296523b0e70c98dbf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60260101"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Tutorial: Integração do Active Directory do Azure com Leapsome

Neste tutorial, saiba como integrar Leapsome com o Azure Active Directory (Azure AD).

Integrar Leapsome no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Leapsome.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Leapsome (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Leapsome, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Leapsome logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Leapsome da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-leapsome-from-the-gallery"></a>Adicionando Leapsome da Galeria
Para configurar a integração do Leapsome com o Azure AD, terá de adicionar Leapsome a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Leapsome a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Leapsome**, selecione **Leapsome** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Leapsome na lista de resultados](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Leapsome com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Leapsome a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Leapsome deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Leapsome, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Leapsome](#create-a-leapsome-test-user)**  - para ter um equivalente da Eduarda Almeida na Leapsome que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Leapsome.

**Para configurar o Azure AD início de sessão único com Leapsome, execute os seguintes passos:**

1. No portal do Azure, sobre o **Leapsome** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

1. Sobre o **Leapsome domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Leapsome domínio e URLs únicas início de sessão em informações](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://www.leapsome.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Leapsome domínio e URLs únicas início de sessão em informações](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > O valor de URL de URL de resposta e início de sessão anterior não é o valor real. Irá atualizá-los com os valores reais, que é explicado mais tarde no tutorial.

1. Aplicação de Leapsome espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Captura de ecrã seguinte mostra um exemplo.
    
    ![Configurar o início de sessão único](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo | Espaço de nomes |
    | ---------------| --------------- | --------- |   
    | FirstName | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Apelido | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | título | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | imagem | URL para imagem o funcionário | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > O valor do atributo de imagem não é real. Atualize este valor com o URL da imagem real. Para obter este valor de contacto [equipa de suporte de cliente Leapsome](mailto:support@leapsome.com).
    
    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d. Na **espaço de nomes** caixa de texto, digite o uri de espaço de nomes para essa linha.
    
    e. Clique em **Ok**

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/leapsome-tutorial/tutorial_general_400.png)
    
1. Sobre o **Leapsome configuração** secção, clique em **configurar Leapsome** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

1. Numa janela do browser web diferente, inicie sessão no Leapsome como um administrador de segurança.

1. No canto superior direito, clique no logótipo de definições e, em seguida, clique em **definições de administração**. 

    ![Conjunto de Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Na barra de menu à esquerda, clique em **único início de sessão (SSO)** e, no **baseado em SAML início de sessão único (SSO)** página execute os seguintes passos:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecione **baseado em SAML ativar início de sessão único**.

    b. Copiar o **URL de início de sessão (indicar a seus usuários aqui para iniciar o início de sessão)** valor e cole-o no **URL de início de sessão** caixa de texto no **Leapsome domínio e URLs** secção no portal do Azure.

    c. Copiar o **URL de resposta (recebe a resposta do seu fornecedor de identidade)** valor e cole-o no **URL de resposta** caixa de texto no **Leapsome domínio e URLs** secção no portal do Azure.

    d. Na **URL de início de sessão de SSO (fornecido pelo fornecedor de identidade)** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure.

    e. Copiar o certificado que tenha transferido a partir do portal do Azure sem - BEGIN CERTIFICATE e END certificado-- comentários e cole-a no **certificado (fornecido pelo fornecedor de identidade)** caixa de texto.

    f. Clique em **definições de ATUALIZAÇÃO de SSO**.
    
### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/leapsome-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/leapsome-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/leapsome-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/leapsome-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-leapsome-test-user"></a>Criar um utilizador de teste Leapsome

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Leapsome. Trabalhar com [equipa de suporte de cliente Leapsome](mailto:support@leapsome.com) para adicionar os utilizadores ou o domínio, o qual tem de estar na lista de permissões na plataforma Leapsome. Se o domínio for adicionado pela equipe, os utilizadores serão automaticamente aprovisionados para a plataforma de Leapsome. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Leapsome.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Leapsome, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Leapsome**.

    ![A ligação de Leapsome na lista de aplicações](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Leapsome no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Leapsome.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

