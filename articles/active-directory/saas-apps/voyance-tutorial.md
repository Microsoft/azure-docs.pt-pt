---
title: 'Tutorial: Integração do Active Directory do Azure com Voyance | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Voyance.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 539dc1f9-64c9-4dce-b259-2b0b49dcf857
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1771fe1d94eb64cf1e5227953bdc5defa488e85f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639284"
---
# <a name="tutorial-azure-active-directory-integration-with-voyance"></a>Tutorial: Integração do Active Directory do Azure com Voyance

Neste tutorial, saiba como integrar Voyance com o Azure Active Directory (Azure AD).

Integrar Voyance no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Voyance
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Voyance (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Voyance, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Voyance logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Voyance da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-voyance-from-the-gallery"></a>Adicionando Voyance da Galeria
Para configurar a integração do Voyance com o Azure AD, terá de adicionar Voyance a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Voyance a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Voyance**, selecione **Voyance** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Voyance na lista de resultados](./media/voyance-tutorial/tutorial_voyance_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Voyance com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Voyance a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Voyance deve ser estabelecido.

Voyance, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Voyance, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Voyance](#create-a-voyance-test-user)**  - para ter um equivalente da Eduarda Almeida na Voyance que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Voyance.

**Para configurar o Azure AD início de sessão único com Voyance, execute os seguintes passos:**

1. No portal do Azure, sobre o **Voyance** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/voyance-tutorial/tutorial_voyance_samlbase.png)

1. Sobre o **Voyance domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Domínio Voyance URLs únicas início de sessão em informações e para o IDP](./media/voyance-tutorial/tutorial_voyance_url1.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.nyansa.com`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.nyansa.com/saml/create/`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Voyance domínio e URLs único informações de início de sessão de SP](./media/voyance-tutorial/tutorial_voyance_url2.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<companyname>.nyansa.com/`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente Voyance](mailto:support@nyansa.com) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/voyance-tutorial/tutorial_voyance_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/voyance-tutorial/tutorial_general_400.png)
    
1. Sobre o **Voyance configuração** secção, clique em **configurar Voyance** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de Voyance](./media/voyance-tutorial/tutorial_voyance_configure.png) 

1. Numa janela do browser web diferente, início de sessão no seu inquilino Voyance como administrador.

1. Vá para o canto superior direito da barra de navegação e clique no menu suspenso que diz "**Acme Universidade**".
    
    ![Configurar início de sessão único na aplicação do lado do Acme Universidade](./media/voyance-tutorial/tutorial_voyance_001.png) 

1. Clique em "**definições de administração**".

    ![Configurar início de sessão único em definições de administração do lado da aplicação](./media/voyance-tutorial/tutorial_voyance_002.png)

1. Clique em "**acesso de utilizador**" separador.

    ![Configurar início de sessão único em acesso de utilizador do lado da aplicação](./media/voyance-tutorial/tutorial_voyance_003.png)

1. Clique em de "**SSO está desabilitado**" botão para configurar o Azure AD como um IdP utilizando SAML 2.0.

    ![Configurar o único início de sessão na aplicação do lado do SSO está desabilitado botão](./media/voyance-tutorial/tutorial_voyance_004.png)

1. Aceda a **v2 SAML** secção e executar passos abaixo:

    ![Configurar o início de sessão único no lado de aplicação SAML v2](./media/voyance-tutorial/tutorial_voyance_005.png)
    
    a. Selecione **ativada**.
    
    b. Colar **SAML único início de sessão no URL do serviço**, que copiou do portal do Azure para o **URL de início de sessão do IdP** caixa de texto.

    c. Abra o seu certificado codificado em Base64 transferido no bloco de notas, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-os para o **IdP Cert** caixa de texto.
    
    d. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](./media/voyance-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/voyance-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Botão Adicionar](./media/voyance-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![A caixa de diálogo de utilizador](./media/voyance-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-voyance-test-user"></a>Criar um utilizador de teste Voyance

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Voyance. Voyance suporta o aprovisionamento de just-in-time, que está por predefinição, ativada. Não existe nenhum item de ação para nesta secção. Um novo utilizador é criado durante uma tentativa de aceder Voyance se não existir ainda.

>[!NOTE]
>Se precisar de criar manualmente um utilizador, terá de contactar [equipa de suporte de Voyance](maiLto:support@nyansa.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Voyance.

![Atribuir a função de utilizador][200]

**Para atribuir a Eduarda Almeida a Voyance, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Voyance**.

    ![A ligação de Voyance na lista de aplicações](./media/voyance-tutorial/tutorial_voyance_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Voyance no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Voyance.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/voyance-tutorial/tutorial_general_01.png
[2]: ./media/voyance-tutorial/tutorial_general_02.png
[3]: ./media/voyance-tutorial/tutorial_general_03.png
[4]: ./media/voyance-tutorial/tutorial_general_04.png

[100]: ./media/voyance-tutorial/tutorial_general_100.png

[200]: ./media/voyance-tutorial/tutorial_general_200.png
[201]: ./media/voyance-tutorial/tutorial_general_201.png
[202]: ./media/voyance-tutorial/tutorial_general_202.png
[203]: ./media/voyance-tutorial/tutorial_general_203.png

