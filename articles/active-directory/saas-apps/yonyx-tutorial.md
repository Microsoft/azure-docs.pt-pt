---
title: 'Tutorial: Integração do Active Directory do Azure com os guias de Yonyx interativa | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e guias de Yonyx interativo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 07db4e01-319b-4cb6-9b93-4577bffd3cbc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce7653770d0fb3134f266137e167125b9161865
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097598"
---
# <a name="tutorial-azure-active-directory-integration-with-yonyx-interactive-guides"></a>Tutorial: Integração do Active Directory do Azure com os guias de Yonyx interativa

Neste tutorial, saiba como integrar os guias de Yonyx interativa com o Azure Active Directory (Azure AD).

Integração de guias de Yonyx interativa com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso interativo Yonyx de guias de procedimentos
- Pode permitir que os utilizadores automaticamente obter sessão iniciada em Yonyx guias interativa (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com guias de Yonyx interativa, precisa do seguinte:

- Uma subscrição do Azure
- Um Yonyx guias de Interactive logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Yonyx guias de interativo da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-yonyx-interactive-guides-from-the-gallery"></a>Adicionando Yonyx guias de interativo da Galeria
Para configurar a integração de guias de Yonyx interativa com o Azure AD, terá de adicionar guias de Yonyx interativos a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar guias de Yonyx interativos a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **guias de interativo Yonyx**, selecione **guias de interativo Yonyx** no painel de resultados, em seguida, clique em **adicionar** botão para adicionar a aplicação.

    ![Guias de interativo Yonyx na lista de resultados](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com os guias de interativo Yonyx com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte nos guias de Yonyx interativo para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos guias de interativo Yonyx deve ser estabelecido.

Guias de Yonyx interativo, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com os guias de Yonyx interativo, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de guias de interativo Yonyx](#create-a-yonyx-interactive-guides-test-user)**  - para ter um equivalente da Eduarda Almeida Yonyx guias interativo que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar do Azure AD início de sessão único no portal do Azure e configurar o início de sessão único em seu aplicativo de guias de Yonyx interativo.

**Para configurar o Azure AD início de sessão único com os guias de Yonyx interativo, execute os seguintes passos:**

1. No portal do Azure, no **guias de interativo Yonyx** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_samlbase.png)

1. Sobre o **Yonyx interativo guias de domínio e URLs** secção, execute os seguintes passos:

    ![Yonyx interativo guias de domínio e URLs únicas início de sessão em informações](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.yonyx.com/y/conversation/?id=<guid number>`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.yonyx.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de guias interativo Yonyx](mailto:support@yonyx.com) obter esses valores. 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/yonyx-tutorial/tutorial_general_400.png)

1. Na **Yonyx interativo guias de configuração** secção, clique em **Configurar guias interativa de Yonyx** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de guias interativo Yonyx](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_configure.png) 

1. Para configurar o início de sessão único num **guias de interativo Yonyx** lado, terá de enviar o transferido **Certificate(Base64)**, **URL de fim de sessão**, **único SAML URL do serviço de início de sessão** **ID da entidade de SAML** para [equipa de suporte de guias de interativo Yonyx](mailto:support@yonyx.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

  ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/yonyx-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/yonyx-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/yonyx-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/yonyx-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-yonyx-interactive-guides-test-user"></a>Criar um utilizador de teste de guias de Yonyx interativa

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida nos guias de Yonyx interativo. Guias de interativo Yonyx suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder guias de interativo Yonyx se não existir ainda.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar a equipa de suporte de guias de interativo Yonyx por <mailto:support@yonyx.com>. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso interativo Yonyx de guias de procedimentos.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida interativo Yonyx de guias de procedimentos, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **guias de interativo Yonyx**.

    ![A ligação de guias de interativo Yonyx na lista de aplicações](./media/yonyx-tutorial/tutorial_yonyxinteractiveguides_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de guias de interativo Yonyx no painel de acesso, deve obter automaticamente sessão iniciada em à sua aplicação de guias de Yonyx interativo.

Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/yonyx-tutorial/tutorial_general_01.png
[2]: ./media/yonyx-tutorial/tutorial_general_02.png
[3]: ./media/yonyx-tutorial/tutorial_general_03.png
[4]: ./media/yonyx-tutorial/tutorial_general_04.png

[100]: ./media/yonyx-tutorial/tutorial_general_100.png

[200]: ./media/yonyx-tutorial/tutorial_general_200.png
[201]: ./media/yonyx-tutorial/tutorial_general_201.png
[202]: ./media/yonyx-tutorial/tutorial_general_202.png
[203]: ./media/yonyx-tutorial/tutorial_general_203.png

