---
title: 'Tutorial: Integração do Active Directory do Azure com o Gestor de risco IMPAC | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Gestor de risco IMPAC.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f987c6803f6ca538f4ae7470caaff597c9596c2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900748"
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Tutorial: Integração do Active Directory do Azure com o Gestor de risco IMPAC

Neste tutorial, saiba como integrar o Gestor de risco IMPAC com o Azure Active Directory (Azure AD).

Integrar o Gestor de risco IMPAC no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Gestor de risco de IMPAC.
- Pode permitir que os utilizadores automaticamente obter sessão iniciada em IMPAC o Gestor de risco (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Gestor de risco IMPAC, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Gestor de risco IMPAC início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar IMPAC risco Manager a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-impac-risk-manager-from-the-gallery"></a>Adicionar IMPAC risco Manager a partir da Galeria
Para configurar a integração do Gestor de risco de IMPAC com o Azure AD, terá de adicionar IMPAC risco Manager a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar IMPAC risco Manager a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Gestor de risco IMPAC**, selecione **IMPAC risco Manager** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Gestor de risco de IMPAC na lista de resultados](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o IMPAC risco Manager com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Gestor de risco de IMPAC a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Gestor de risco de IMPAC deve ser estabelecido.

No Gestor de IMPAC risco, atribuir o valor do **nome de utilizador** no Azure AD como o valor da **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Gestor de risco IMPAC, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do Gestor de risco IMPAC](#create-a-impac-risk-manager-test-user)**  - para ter um equivalente da Eduarda Almeida no IMPAC risco Manager que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação do Gestor de risco de IMPAC.

**Para configurar o Azure AD início de sessão único com o Gestor de risco IMPAC, execute os seguintes passos:**

1. No portal do Azure, sobre o **IMPAC risco Manager** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

1. Sobre o **URLs de domínio do Gestor de risco IMPAC e** secção, execute os seguintes passos, se desejar configurar a aplicação no modo de IDP iniciado:

    ![URLs de domínio do Gestor de risco IMPAC e únicas início de sessão em informações](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. Na **identificador** caixa de texto, escreva um valor fornecido pelo IMPAC

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão:

    | Ambiente | Padrão de URL |
    | ---------------|--------------- |    
    | Para Produção |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para teste e treinamento  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para Desenvolvimento  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para o controle de qualidade |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Para teste |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![URLs de domínio do Gestor de risco IMPAC e únicas início de sessão em informações](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    
    | Ambiente | Padrão de URL |
    | ---------------|--------------- |    
    | Para Produção |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para teste e treinamento  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para Desenvolvimento  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para o controle de qualidade |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Para teste |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente do Gestor de risco IMPAC](mailto:rmsupport@Impac.co.nz) obter esses valores.

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![O link de download de certificado](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/impacriskmanager-tutorial/tutorial_general_400.png)
    
1. Na **configuração do Gestor de risco IMPAC** secção, clique em **configurar Gestor de risco de IMPAC** para abrir **configurar início de sessão** janela. Cópia a **SAML único início de sessão no URL do serviço, o ID de entidade de SAML** e **URL de fim de sessão** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

1. Para configurar o início de sessão único num **Gestor de risco IMPAC** lado, terá de enviar o transferido **Certificate(Base64)**, **URL de fim de sessão, o ID de entidade de SAML,** e  **SAML único início de sessão no URL do serviço** para [equipa de suporte do Gestor de risco IMPAC](mailto:rmsupport@Impac.co.nz). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/impacriskmanager-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/impacriskmanager-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/impacriskmanager-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/impacriskmanager-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-impac-risk-manager-test-user"></a>Criar um utilizador de teste do Gestor de risco IMPAC

Nesta secção, vai criar um usuário chamado Eduarda Almeida no Gestor de risco de IMPAC. Trabalhar com [equipa de suporte do Gestor de risco IMPAC](mailto:rmsupport@Impac.co.nz) para adicionar os utilizadores na plataforma do Gestor de risco IMPAC. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Gestor de risco de IMPAC.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida para IMPAC risco Manager, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **IMPAC risco Manager**.

    ![A ligação de Gestor de risco IMPAC na lista de aplicações](./media/impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o mosaico do Gestor de risco de IMPAC no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação do Gestor de risco IMPAC.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/impacriskmanager-tutorial/tutorial_general_203.png

