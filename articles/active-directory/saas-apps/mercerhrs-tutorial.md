---
title: 'Tutorial: Integração do Active Directory do Azure com Mercer BenefitsCentral (MBC) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Mercer BenefitsCentral (MBC).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3788b28c-49aa-4208-9acd-630362008e89
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: 9dde23d0d860e5777239cb9c51e96a8d46e67841
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983386"
---
# <a name="tutorial-azure-active-directory-integration-with-mercer-benefitscentral-mbc"></a>Tutorial: Integração do Active Directory do Azure com Mercer BenefitsCentral (MBC)

Neste tutorial, saiba como integrar Mercer BenefitsCentral (MBC) com o Azure Active Directory (Azure AD).
Integrar Mercer BenefitsCentral (MBC) no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso Mercer BenefitsCentral (MBC).
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para Mercer BenefitsCentral (MBC) (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Mercer BenefitsCentral (MBC), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Mercer BenefitsCentral (MBC) início de sessão único de subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Mercer BenefitsCentral (MBC) suporta **IDP** iniciada SSO

## <a name="adding-mercer-benefitscentral-mbc-from-the-gallery"></a>Adicionando Mercer BenefitsCentral (MBC) da Galeria

Para configurar a integração de Mercer BenefitsCentral (MBC) para o Azure AD, terá de adicionar Mercer BenefitsCentral (MBC) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Mercer BenefitsCentral (MBC) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **Mercer BenefitsCentral (MBC)**, selecione **Mercer BenefitsCentral (MBC)** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Mercer BenefitsCentral (MBC) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Mercer BenefitsCentral (MBC) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Mercer BenefitsCentral (MBC) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com Mercer BenefitsCentral (MBC), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar Mercer BenefitsCentral (MBC) início de sessão único](#configure-mercer-benefitscentral-mbc-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Mercer BenefitsCentral (MBC)](#create-mercer-benefitscentral-mbc-test-user)**  - para ter um equivalente da Eduarda Almeida na Mercer BenefitsCentral (MBC) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com Mercer BenefitsCentral (MBC), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **Mercer BenefitsCentral (MBC)** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![Mercer BenefitsCentral (MBC) domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL: `stg.mercerhrs.com/saml2.0`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://ssous-stg.mercerhrs.com/SP2/Saml2AssertionConsumer.aspx`

    > [!NOTE]
    > O valor de URL de resposta não é real. Atualize este valor com o URL de resposta real. Contacte [equipa de suporte de cliente Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para obter este valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

6. Sobre o **definir segurança Mercer BenefitsCentral (MBC)** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-mercer-benefitscentral-mbc-single-sign-on"></a>Configurar Mercer BenefitsCentral (MBC) início de sessão único

Para configurar o início de sessão único num **Mercer BenefitsCentral (MBC)** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [Mercer A equipa de suporte BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso Mercer BenefitsCentral (MBC).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **Mercer BenefitsCentral (MBC)**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **Mercer BenefitsCentral (MBC)**.

    ![A ligação de Mercer BenefitsCentral (MBC) na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-mercer-benefitscentral-mbc-test-user"></a>Criar utilizador de teste Mercer BenefitsCentral (MBC)

Nesta secção, vai criar um usuário chamado Eduarda Almeida na Mercer BenefitsCentral (MBC). Trabalhar com [equipa de suporte de Mercer BenefitsCentral (MBC)](https://www.mercer.com/contact-us.html) para adicionar os utilizadores na plataforma Mercer BenefitsCentral (MBC). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Mercer BenefitsCentral (MBC) no painel de acesso, deve ser automaticamente conectado para o Mercer BenefitsCentral (MBC) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

