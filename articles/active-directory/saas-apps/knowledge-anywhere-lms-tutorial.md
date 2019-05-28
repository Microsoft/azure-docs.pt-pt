---
title: 'Tutorial: Integração do Active Directory do Azure com dados de conhecimento em qualquer lugar LMS | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e LMS do conhecimento em qualquer lugar.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34553c92f54992698a7e3930ac302ef970edd7c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236671"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Tutorial: Integrar dados de conhecimento em qualquer lugar LMS com o Azure Active Directory

Neste tutorial, irá aprender como integrar LMS do conhecimento em qualquer lugar com o Azure Active Directory (Azure AD). Quando integrar LMS do conhecimento em qualquer lugar com o Azure AD, pode:

* Controlar no Azure AD, quem tem acesso a dados de conhecimento em qualquer lugar LMS.
* Permita que os utilizadores ser automaticamente sessão iniciada para LMS do conhecimento em qualquer lugar com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Dados de conhecimento em qualquer lugar LMS início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporte de dados de conhecimento em qualquer lugar LMS **SP** iniciou o SSO e suporta **Just In Time** aprovisionamento de utilizadores.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Adicionando LMS do conhecimento em qualquer lugar da Galeria

Para configurar a integração de LMS do conhecimento em qualquer lugar com o Azure AD, terá de adicionar LMS do conhecimento em qualquer lugar a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **LMS do conhecimento em qualquer lugar** na caixa de pesquisa.
1. Selecione **LMS do conhecimento em qualquer lugar** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com dados de conhecimento em qualquer lugar LMS com um utilizador de teste **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no LMS do conhecimento em qualquer lugar.

Para configurar e testar o SSO do Azure AD com LMS do conhecimento em qualquer lugar, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar dados de conhecimento em qualquer lugar LMS](#configure-knowledge-anywhere-lms)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de dados de conhecimento em qualquer lugar LMS](#create-knowledge-anywhere-lms-test-user)**  ter um equivalente de B. Simon em dados de conhecimento em qualquer lugar LMS que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **LMS do conhecimento em qualquer lugar** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, se desejar configurar a aplicação no **IDP** iniciada pelo modo, execute os seguintes passos:

    1. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta, o que é explicado mais tarde no tutorial.

1. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > O valor de URL de início de sessão não é real. Atualize este valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente de LMS do conhecimento em qualquer lugar](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar dados de conhecimento em qualquer lugar LMS** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Configurar dados de conhecimento em qualquer lugar LMS

1. Para automatizar a configuração no LMS do conhecimento em qualquer lugar, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **configuração do conhecimento em qualquer lugar LMS** irá direcioná-lo para a aplicação de LMS do conhecimento em qualquer lugar. A partir daí, forneça as credenciais de administrador a iniciar sessão em LMS do conhecimento em qualquer lugar. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3 a 7.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o LMS do conhecimento em qualquer lugar, abra uma nova janela de browser e inicie sessão no site da sua empresa LMS do conhecimento em qualquer lugar como administrador e execute os seguintes passos:

4. Selecione sobre o **Site** separador.

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selecione sobre o **definições de SAML** separador.

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Clique nas **adicionar novo**.

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Sobre o **adicionar/atualizar definições de SAML** página, execute os seguintes passos:

    ![Dados de conhecimento em qualquer lugar LMS configuração](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Introduza o nome de IDP de acordo com a sua organização. Para ex:- `Azure`.

    b. Na **ID de entidade do IDP** caixa de texto, colar **do Azure AD identificador** valor, que copiou do portal do Azure.

    c. Na **URL de IDP** caixa de texto, colar **URL de início de sessão** valor, que copiou do portal do Azure.

    d. Abra o ficheiro de certificado transferido a partir do portal do Azure no bloco de notas, copie o conteúdo do certificado e colá-lo no **certificado** caixa de texto.

    e. Na **URL de fim de sessão** caixa de texto, colar **URL de fim de sessão** valor, que copiou do portal do Azure.

    f. Selecione **Site de Main** na lista pendente para o **domínio**.

    g. Cópia a **ID de entidade de SP** valor e cole-a na **identificador** caixa de texto **configuração básica de SAML** secção no portal do Azure.

    h. Copiar o **SP Response(ACS) URL** valor e cole-a na **URL de resposta** caixa de texto **configuração básica de SAML** secção no portal do Azure.

    i. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Simon B.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Simon B. utilizar o Azure início de sessão único ao conceder acesso a dados de conhecimento em qualquer lugar LMS.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **LMS do conhecimento em qualquer lugar**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Criar utilizador de teste de LMS do conhecimento em qualquer lugar

Nesta secção, um usuário chamado Simon B. é criado na LMS do conhecimento em qualquer lugar. Dados de conhecimento em qualquer lugar LMS suporta o aprovisionamento de utilizadores de just-in-time, que está ativado por predefinição. Não existe nenhum item de ação para nesta secção. Se um utilizador já não existir no LMS do conhecimento em qualquer lugar, é criado um novo após a autenticação.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de LMS do conhecimento em qualquer lugar no painel de acesso, deve ser automaticamente conectado para os dados de conhecimento em qualquer lugar LMS para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)