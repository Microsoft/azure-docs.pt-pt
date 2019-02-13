---
title: 'Tutorial: Integração do Active Directory do Azure com Lecorpio | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Lecorpio.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09693d17f5eca1b5e4597c522903c3f9148bc74
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178894"
---
# <a name="tutorial-azure-active-directory-integration-with-lecorpio"></a>Tutorial: Integração do Active Directory do Azure com Lecorpio

Neste tutorial, saiba como integrar Lecorpio com o Azure Active Directory (Azure AD).

Integrar Lecorpio no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Lecorpio
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Lecorpio (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Lecorpio, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Lecorpio início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Lecorpio da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-lecorpio-from-the-gallery"></a>Adicionando Lecorpio da Galeria
Para configurar a integração do Lecorpio com o Azure AD, terá de adicionar Lecorpio a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Lecorpio a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **nova aplicação** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Lecorpio**.

    ![Criar um utilizador de teste do Azure AD](./media/lecorpio-tutorial/tutorial_lecorpio_search.png)

1. No painel de resultados, selecione **Lecorpio**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/lecorpio-tutorial/tutorial_lecorpio_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Lecorpio com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Lecorpio a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Lecorpio deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor da **Username** no Lecorpio.

Para configurar e testar o Azure AD início de sessão único com Lecorpio, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Lecorpio](#creating-a-lecorpio-test-user)**  - para ter um equivalente da Eduarda Almeida na Lecorpio que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Lecorpio.

**Para configurar o Azure AD início de sessão único com Lecorpio, execute os seguintes passos:**

1. No portal do Azure, sobre o **Lecorpio** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/lecorpio-tutorial/tutorial_lecorpio_samlbase.png)

1. Sobre o **Lecorpio domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/lecorpio-tutorial/tutorial_lecorpio_url.png)

    a. Na **URL de início de sessão** caixa de texto, digite o valor usando o seguinte padrão: `https://<instance name>.lecorpio.com/<customer name>`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<instance name>.lecorpio.com/<customer name>`

    > [!NOTE] 
    > Estes valores não são o real. Atualize estes valores com o URL de início de sessão real e o identificador. Aqui iremos sugerir-lhe utilizar o valor único de cadeia de caracteres no identificador de. Contacte [equipa de suporte de cliente Lecorpio](mailto:info@lecorpio.com) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/lecorpio-tutorial/tutorial_lecorpio_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/lecorpio-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Lecorpio** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte de Lecorpio](mailto:info@lecorpio.com).

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/lecorpio-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/lecorpio-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/lecorpio-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/lecorpio-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lecorpio-test-user"></a>Criar um utilizador de teste Lecorpio

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Lecorpio. 

Contacte [equipa de suporte de cliente Lecorpio](mailto:info@lecorpio.com) para adicionar os utilizadores na aplicação Lecorpio.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Lecorpio.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Lecorpio, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Lecorpio**.

    ![Configurar o início de sessão único](./media/lecorpio-tutorial/tutorial_lecorpio_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Lecorpio no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Lecorpio.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/lecorpio-tutorial/tutorial_general_01.png
[2]: ./media/lecorpio-tutorial/tutorial_general_02.png
[3]: ./media/lecorpio-tutorial/tutorial_general_03.png
[4]: ./media/lecorpio-tutorial/tutorial_general_04.png

[100]: ./media/lecorpio-tutorial/tutorial_general_100.png

[200]: ./media/lecorpio-tutorial/tutorial_general_200.png
[201]: ./media/lecorpio-tutorial/tutorial_general_201.png
[202]: ./media/lecorpio-tutorial/tutorial_general_202.png
[203]: ./media/lecorpio-tutorial/tutorial_general_203.png

