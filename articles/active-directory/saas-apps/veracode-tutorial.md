---
title: 'Tutorial: Integração do Active Directory do Azure com Veracode | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e80a2e554d31ad85ddb1111f84a96e1814c3969f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638825"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Tutorial: Integração do Active Directory do Azure com Veracode

Neste tutorial, saiba como integrar Veracode com o Azure Active Directory (Azure AD).

Integrar Veracode no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Veracode.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Veracode (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Veracode, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Veracode logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Veracode a partir da Galeria
1. Configurar e testar o Azure AD início de sessão único

## <a name="add-veracode-from-the-gallery"></a>Adicionar Veracode a partir da Galeria
Para configurar a integração do Veracode com o Azure AD, terá de adicionar Veracode a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Veracode a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Veracode**, selecione **Veracode** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Veracode na lista de resultados](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Veracode com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Veracode a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Veracode deve ser estabelecido.

Veracode, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Veracode, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Veracode](#create-a-veracode-test-user)**  - para ter um equivalente da Eduarda Almeida na Veracode que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Veracode.

**Para configurar o Azure AD início de sessão único com Veracode, execute os seguintes passos:**

1. No portal do Azure, sobre o **Veracode** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. Sobre o **Veracode domínio e URLs** secção, o utilizador não tem de efetuar outros passos de como a aplicação já está pré-integrada com o Azure. 

    ![Configurar o início de sessão único](./media/veracode-tutorial/tutorial_veracode_url.png)

1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. É o objetivo desta secção descrevem como permitir que os utilizadores sejam autenticados Veracode com suas contas no Azure AD com Federação com base no protocolo SAML.

    Seu aplicativo Veracode espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizados para sua **atributos de token saml** configuração. Captura de ecrã seguinte mostra um exemplo disso.
    
    ![Atributos](./media/veracode-tutorial/tutorial_veracode_attr.png "atributos")

1. Para adicionar os mapeamentos de atributo necessário, execute os seguintes passos:

    | Nome do Atributo | Valor do Atributo |
    |--- |--- |
    | FirstName |User.givenname |
    | Apelido |User.surname |
    | e-mail |User.mail |
    
    a. Para cada linha de dados na tabela acima, clique em **adicione o atributo de utilizador**.
    
    ![Atributos](./media/veracode-tutorial/tutorial_veracode_addattr.png "atributos")
    
    ![Atributos](./media/veracode-tutorial/tutorial_veracode_addattr1.png "atributos")
    
    b. Na **nome de atributo** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Na **valor do atributo** caixa de texto, selecione o valor do atributo apresentado para essa linha.
    
    d. Clique em **OK**.

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/veracode-tutorial/tutorial_general_400.png)

1. Sobre o **Veracode configuração** secção, clique em **configurar Veracode** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML** partir o **secção de referência rápida.**

    ![Configuração de Veracode](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no site da sua empresa Veracode como um administrador.

1. No menu na parte superior, clique em **configurações**e, em seguida, clique em **administrador**.
   
    ![Administração](./media/veracode-tutorial/ic802911.png "administração")

1. Clique nas **SAML** separador.

1. Na **definições de SAML de organização** secção, execute os seguintes passos:
   
    ![Administração](./media/veracode-tutorial/ic802912.png "administração")
   
    a.  Na **emissor** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    b. Para carregar o certificado transferido a partir do portal do Azure, clique em **Escolher ficheiro**.
   
    c. Selecione **ativar o registo automático**.

1. Na **definições de registo de reposição personalizada** secção, execute os seguintes passos e, em seguida, clique em **guardar**:
   
    ![Administração](./media/veracode-tutorial/ic802913.png "administração")
   
    a. Como **nova ativação do usuário**, selecione **sem ativação necessária**.
   
    b. Como **atualizações de dados de utilizador**, selecione **dados de utilizador de Veracode preferência**.
   
    c. Para **detalhes do atributo de SAML**, selecione o seguinte:
      * **Funções de utilizador**
      * **Administrador de diretiva**
      * **Revisor**
      * **Gerente de segurança**
      * **Executivo**
      * **Submissor**
      * **Creator**
      * **Todos os tipos de análise**
      * **Membros de equipe**
      * **Equipe de predefinição**

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/veracode-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/veracode-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/veracode-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/veracode-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-veracode-test-user"></a>Criar um utilizador de teste Veracode
Para habilitar logon Veracode de utilizadores do Azure AD, tem de ser aprovisionados em Veracode. No caso de Veracode, o aprovisionamento é uma tarefa automatizada. Não existe nenhum item de ação para. Os utilizadores são criados automaticamente se for necessário durante a primeira única início de sessão tentativa.

> [!NOTE]
> Pode utilizar quaisquer outras Veracode utilizador conta criação ferramentas ou APIs fornecidas pelo Veracode para aprovisionar contas de utilizador do Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Veracode.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Veracode, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Veracode**.

    ![A ligação de Veracode na lista de aplicações](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Veracode no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Veracode.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

