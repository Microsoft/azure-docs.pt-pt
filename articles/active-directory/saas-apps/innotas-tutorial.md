---
title: 'Tutorial: Integração do Active Directory do Azure com Innotas | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Innotas.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: eb9e9c2c-4b09-4177-bbab-423fd657448e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21056663252ac5a41a529c3d8443efa888fbde9d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60272326"
---
# <a name="tutorial-azure-active-directory-integration-with-innotas"></a>Tutorial: Integração do Active Directory do Azure com Innotas

Neste tutorial, saiba como integrar Innotas com o Azure Active Directory (Azure AD).

Integrar Innotas no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Innotas
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Innotas (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Innotas, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Innotas logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Innotas da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-innotas-from-the-gallery"></a>Adicionando Innotas da Galeria
Para configurar a integração do Innotas com o Azure AD, terá de adicionar Innotas a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Innotas a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Innotas**.

    ![Criar um utilizador de teste do Azure AD](./media/innotas-tutorial/tutorial_innotas_search.png)

1. No painel de resultados, selecione **Innotas**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/innotas-tutorial/tutorial_innotas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Innotas com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Innotas a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Innotas deve ser estabelecido.

Innotas, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Innotas, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Innotas](#creating-an-innotas-test-user)**  - para ter um equivalente da Eduarda Almeida na Innotas que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Innotas.

**Para configurar o Azure AD início de sessão único com Innotas, execute os seguintes passos:**

1. No portal do Azure, sobre o **Innotas** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/innotas-tutorial/tutorial_innotas_samlbase.png)

1. Sobre o **Innotas domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/innotas-tutorial/tutorial_innotas_url.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<tenant-name>.Innotas.com`

    > [!NOTE] 
    > Este valor não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente Innotas](https://www.innotas.com/contact) para obter este valor. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![Configurar o início de sessão único](./media/innotas-tutorial/tutorial_innotas_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/innotas-tutorial/tutorial_general_400.png)

1. Para configurar o início de sessão único num **Innotas** lado, terá de enviar o transferido **XML de metadados** para [equipa de suporte de Innotas](https://www.innotas.com/contact). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/innotas-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/innotas-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/innotas-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/innotas-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-an-innotas-test-user"></a>Criar um utilizador de teste Innotas

Não existe nenhum item de ação para configurar o aprovisionamento de utilizador para Innotas.  
Quando um utilizador atribuído tenta iniciar sessão no Innotas usando o painel de acesso, Innotas verifica se o usuário existe.  

>[!NOTE]
>Se nenhuma conta de utilizador disponível ainda existe, é criado automaticamente pelo Innotas.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Innotas.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Innotas, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Innotas**.

    ![Configurar o início de sessão único](./media/innotas-tutorial/tutorial_innotas_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Innotas no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Innotas.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/innotas-tutorial/tutorial_general_01.png
[2]: ./media/innotas-tutorial/tutorial_general_02.png
[3]: ./media/innotas-tutorial/tutorial_general_03.png
[4]: ./media/innotas-tutorial/tutorial_general_04.png

[100]: ./media/innotas-tutorial/tutorial_general_100.png

[200]: ./media/innotas-tutorial/tutorial_general_200.png
[201]: ./media/innotas-tutorial/tutorial_general_201.png
[202]: ./media/innotas-tutorial/tutorial_general_202.png
[203]: ./media/innotas-tutorial/tutorial_general_203.png

