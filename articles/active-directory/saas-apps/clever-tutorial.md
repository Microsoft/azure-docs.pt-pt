---
title: 'Tutorial: Integração do Active Directory do Azure com Clever | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Clever.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 069ff13a-310e-4366-a147-d6ec5cca12a5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2018
ms.author: jeedes
ms.openlocfilehash: e65f0cb3ef30fb5b001acdb72481c1c3b55ca058
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197318"
---
# <a name="tutorial-azure-active-directory-integration-with-clever"></a>Tutorial: Integração do Active Directory do Azure com Clever

Neste tutorial, saiba como integrar Clever com o Azure Active Directory (Azure AD).

Integrar Clever no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Clever.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Clever (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Clever, terá dos seguintes itens:

- Uma subscrição do Azure
- Um inteligente logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Clever da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-clever-from-the-gallery"></a>Adicionando Clever da Galeria
Para configurar a integração do Clever com o Azure AD, terá de adicionar Clever a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Clever a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Clever**, selecione **Clever** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Inteligente, na lista de resultados](./media/clever-tutorial/tutorial_clever_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Clever com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Clever a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Clever deve ser estabelecido.

Clever, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Clever, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste inteligente](#create-a-clever-test-user)**  - para ter um equivalente da Eduarda Almeida na Clever que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação inteligente.

**Para configurar o Azure AD início de sessão único com Clever, execute os seguintes passos:**

1. No portal do Azure, sobre o **Clever** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.

    ![Caixa de diálogo de início de sessão único](./media/clever-tutorial/tutorial_clever_samlbase.png)

1. Sobre o **inteligente de domínio e URLs** secção, execute os seguintes passos:

    ![Inteligente URLs de domínio e única informações de início de sessão](./media/clever-tutorial/tutorial_clever_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://clever.com/in/<companyname>`

    b. Na **identificador** caixa de texto, escreva o URL: `https://clever.com/oauth/saml/metadata.xml`

    > [!NOTE]
    > Valor do URL de início de sessão não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente inteligente](https://clever.com/about/contact/) para obter este valor.

1. Sobre o **certificado de assinatura SAML** secção, clique no botão de cópia para copiar **Url de metadados de Federação de aplicação** e cole-o no bloco de notas.
    
    ![Configurar o início de sessão único](./media/clever-tutorial/tutorial_metadataurl.png)

1. A aplicação inteligente espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizados para sua **atributos de Token SAML** configuração.

    Captura de ecrã seguinte mostra um exemplo disso.

    ![Configurar o início de sessão único](./media/clever-tutorial/tutorial_clever_07.png)

1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem acima e execute os seguintes passos:
    
    | Nome do Atributo  | Valor do Atributo |
    | --------------- | -------------------- |
    | clever.teacher.credentials.district_username|user.userprincipalname|
    | clever.student.credentials.district_username| user.userprincipalname |
    | FirstName  | user.givenname |
    | Apelido  | user.surname |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/clever-tutorial/tutorial_attribute_04.png)
    
    ![Configurar o início de sessão único](./media/clever-tutorial/tutorial_attribute_05.png)
    
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.

    d. Deixe o **espaço de nomes** caixa de texto em branco.
    
    d. Clique em **OK**.
    
1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/clever-tutorial/tutorial_general_400.png)

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa inteligente como administrador.

1. Na barra de ferramentas, clique em **instantânea de início de sessão**.

    ![Início de sessão instantâneo](./media/clever-tutorial/ic798984.png "instantâneo início de sessão")

    > [!NOTE]
    > Antes de testar o início de sessão único, tem de contactar [equipa de suporte de cliente inteligente](https://clever.com/about/contact/) para ativar o SSO do Office 365 no back-end.

1. Sobre o **instantânea de início de sessão** página, execute os seguintes passos:
    
      ![Início de sessão instantâneo](./media/clever-tutorial/ic798985.png "instantâneo início de sessão")
    
      a. Tipo de **URL de início de sessão**.
    
      >[!NOTE]
      >O **URL de início de sessão** é um valor personalizado. Contacte [equipa de suporte de cliente inteligente](https://clever.com/about/contact/) para obter este valor.
    
      b. Como **sistema de identidade**, selecione **ADFS**.

      c. Na **URL de metadados** caixa de texto, colar **Url de metadados de Federação de aplicação** valor que copiou do portal do Azure.
    
      d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/clever-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/clever-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/clever-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/clever-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.

### <a name="create-a-clever-test-user"></a>Criar um utilizador de teste inteligente

Para ativar a utilizadores do Azure AD iniciar sessão no Clever, tem de ser aprovisionados em Clever.

Em caso de Clever, trabalhar com [equipa de suporte de cliente inteligente](https://clever.com/about/contact/) para adicionar os utilizadores na plataforma inteligente. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

>[!NOTE]
>Pode utilizar quaisquer outras ferramentas do utilizador inteligente conta criação ou APIs fornecidas pelo Clever para aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Clever.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a Clever, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201]

1. Na lista de aplicações, selecione **Clever**.

    ![O Clever ligação na lista de aplicativos](./media/clever-tutorial/tutorial_clever_app.png)

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico inteligente no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação inteligente.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/clever-tutorial/tutorial_general_01.png
[2]: ./media/clever-tutorial/tutorial_general_02.png
[3]: ./media/clever-tutorial/tutorial_general_03.png
[4]: ./media/clever-tutorial/tutorial_general_04.png

[100]: ./media/clever-tutorial/tutorial_general_100.png

[200]: ./media/clever-tutorial/tutorial_general_200.png
[201]: ./media/clever-tutorial/tutorial_general_201.png
[202]: ./media/clever-tutorial/tutorial_general_202.png
[203]: ./media/clever-tutorial/tutorial_general_203.png

