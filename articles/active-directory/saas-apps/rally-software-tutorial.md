---
title: 'Tutorial: Integração do Active Directory do Azure com Rally Software | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7264792a123a8f8ce6c4a08a6c502de578d374c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57891965"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Tutorial: Integração do Active Directory do Azure com Rally Software

Neste tutorial, saiba como integrar Rally Software com o Azure Active Directory (Azure AD).

Integrar o Rally Software no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Rally Software.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Software Rally (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Rally Software, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Rally Software logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar Rally Software a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-rally-software-from-the-gallery"></a>Adicionar Rally Software a partir da Galeria
Para configurar a integração da Rally Software para o Azure AD, terá de adicionar Rally Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Rally Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Rally Software**, selecione **Rally Software** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Rally Software na lista de resultados](./media/rally-software-tutorial/tutorial_rallysoftware_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Software Rally com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Rally Software a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Rally Software deve ser estabelecido.

Em Rally Software, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Software da Rally, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de Rally Software](#create-a-rally-software-test-user)**  - para ter um equivalente da Eduarda Almeida no Software Rally, que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de Rally Software.

**Para configurar o Azure AD início de sessão único com o Software de Rally, execute os seguintes passos:**

1. No portal do Azure, sobre o **Rally Software** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/rally-software-tutorial/tutorial_rallysoftware_samlbase.png)

1. Sobre o **Rally domínio de Software e URLs** secção, execute os seguintes passos:

    ![Rally Software domínio e URLs únicas início de sessão em informações](./media/rally-software-tutorial/tutorial_rallysoftware_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.rally.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.rally.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de Software Rally](https://help.rallydev.com/) obter esses valores. 
 


1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/rally-software-tutorial/tutorial_rallysoftware_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/rally-software-tutorial/tutorial_general_400.png)

1. Sobre o **Rally configuração de Software** secção, clique em **configurar Software Rally** para abrir **configurar início de sessão** janela. Copiar o **URL de fim de sessão e ID de entidade de SAML** partir o **secção de referência rápida.**

    ![Rally configuração de Software](./media/rally-software-tutorial/tutorial_rallysoftware_configure.png) 

1. Inicie sessão no seu **Rally Software** inquilino.

1. Na barra de ferramentas na parte superior, clique em **programa de configuração**e, em seguida, selecione **subscrição**.
   
    ![Subscrição](./media/rally-software-tutorial/ic769531.png "subscrição")

1. Clique nas **ação** botão. Selecione **editar subscrição** no lado superior direito da barra de ferramentas.

1. Sobre o **subscrição** página de diálogo, execute os seguintes passos e, em seguida, clique em **guardar e fechar**:
   
    ![Autenticação](./media/rally-software-tutorial/ic769542.png "autenticação")
   
    a. Selecione **autenticação Rally ou SSO** no menu pendente de autenticação.

    b. Na **URL de fornecedor de identidade** caixa de texto, cole o valor de **ID de entidade de SAML**, que copiou do portal do Azure. 

    c. Na **fim de sessão de SSO** caixa de texto, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/rally-software-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/rally-software-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/rally-software-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/rally-software-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-rally-software-test-user"></a>Criar um utilizador de teste de Rally Software

Para utilizadores do Azure AD possam iniciar sessão, tem de ser aprovisionados para a aplicação de Rally Software com seus nomes de utilizador do Azure Active Directory.

**Para configurar o aprovisionamento de utilizadores, execute os seguintes passos:**

1. Inicie sessão no seu inquilino Rally Software.

1. Aceda a **programa de configuração \> USUÁRIOS**e, em seguida, clique em **+ adicionar novo**.
   
    ![Os utilizadores](./media/rally-software-tutorial/ic781039.png "utilizadores")

1. Escreva o nome na caixa de texto novo utilizador e, em seguida, clique em **adicionar com detalhes**.

1. Na **criar utilizador** secção, execute os seguintes passos:
   
    ![Criar utilizador](./media/rally-software-tutorial/ic781040.png "criar utilizador")

    a. Na **nome de utilizador** como a caixa de texto, escreva o nome de utilizador **Brittsimon**.
   
    b. Na **endereço de email** caixa de texto, introduza o e-mail do utilizador, como **brittasimon\@contoso.com**.

    c. Na **nome próprio** texto, introduza o nome de utilizador, como **Eduarda**.

    d. Na **sobrenome** texto, digite o apelido do utilizador, como **Simon**.

    e. Clique em **guardar e fechar**.

   >[!NOTE]
   >Pode utilizar quaisquer outras ferramentas de criação da conta de utilizador de Rally Software ou APIs fornecidas pelo Rally Software para aprovisionar contas de utilizador do Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Rally Software.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a Rally Software, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Rally Software**.

    ![A ligação da Rally Software na lista de aplicações](./media/rally-software-tutorial/tutorial_rallysoftware_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico de Rally Software no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo de Rally Software.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/rally-software-tutorial/tutorial_general_01.png
[2]: ./media/rally-software-tutorial/tutorial_general_02.png
[3]: ./media/rally-software-tutorial/tutorial_general_03.png
[4]: ./media/rally-software-tutorial/tutorial_general_04.png

[100]: ./media/rally-software-tutorial/tutorial_general_100.png

[200]: ./media/rally-software-tutorial/tutorial_general_200.png
[201]: ./media/rally-software-tutorial/tutorial_general_201.png
[202]: ./media/rally-software-tutorial/tutorial_general_202.png
[203]: ./media/rally-software-tutorial/tutorial_general_203.png

