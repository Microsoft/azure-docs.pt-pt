---
title: 'Tutorial: Integração do Diretório Ativo Azure com a Costpoint Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Costpoint.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9ecc5f58-4462-4ade-ab73-0a4f61027504
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01168540e27605db0d240c0774159a710b5d5254
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "71840106"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar costpoint com diretório ativo azure

Neste tutorial, você vai aprender a integrar costpoint com o Azure Ative Directory (Azure AD). Quando integrar a Costpoint com a Azure AD, pode:

* Controlo em Azure AD que tem acesso a Costpoint.
* Ative que os seus utilizadores sejam automaticamente inscritos na Costpoint com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura de inscrição única costpoint (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD SSO num ambiente de teste. Costpoint suporta **SP e IDP** iniciado SSO.

## <a name="generate-costpoint-metadata"></a>Gerar metadados costpoint

A configuração Costpoint SAML SSO é explicada no guia **DeltekCostpoint711Security.pdf.** Descarregue este guia do site de suporte de Deltek Costpoint e consulte o **SAML Single Sign-on Configuração** > De Configuração**SAML Single Sign-on Sign-on Single Sign-on Sign-on Individual Sign-on Entre costpoint e** a secção Microsoft Azure. Siga as instruções e gere um ficheiro **Costpoint SP Federation Metadata XML.** 

![Utilitário de configuração de costpoint](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adicione Costpoint da galeria

Para integrar a Costpoint com a Azure AD, adicione primeiro a Costpoint à sua lista de aplicações geridas saaS a partir da galeria no portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.

1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**

   ![O botão Azure Ative Directory](common/select-azuread.png)

1. Selecione **Aplicações Empresariais** > **Todas as Aplicações**.

   ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.

   ![O novo botão de aplicação](common/add-new-app.png)

1. No Add da secção **galeria,** introduza **Costpoint** na caixa de pesquisa.

   ![Ponto de custo na lista de resultados](common/search-new-app.png)

1. Na lista de resultados, selecione **Costpoint**e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Configure e teste Azure AD single sgn-on

Configure e teste Azure AD SSO com Costpoint utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Costpoint.

Para configurar e testar o Azure AD SSO com a Costpoint, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure o Costpoint](#configure-costpoint)** para configurar as definições SAML SSO no lado da aplicação.
1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Crie um utilizador](#create-a-costpoint-test-user)** de teste Costpoint para ter uma contraparte de B.Simon em Costpoint que esteja ligada à representação do utilizador da AD Azure.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure:

1. Na página de integração da aplicação **Costpoint,** selecione **Single sign-on**.

   ![Configure o único link de início de sessão](common/select-sso.png)

1. Na secção **de Configuração Básica do SAML,** se tiver o ficheiro de *metadados*do Prestador de Serviços, preencha estas etapas:

   > [!NOTE]
   > Obtém o ficheiro de metadados do Fornecedor de Serviços em [Metadados Costpoint.](#generate-costpoint-metadata) Como utilizar o ficheiro é explicado mais tarde no tutorial.
 
   1. Selecione o botão de **ficheiro de metadados de upload** e, em seguida, selecione o ficheiro **Costpoint SP Federation Metadata XML** anteriormente gerado pela Costpoint e, em seguida, selecione o botão **Adicionar** para carregar o ficheiro.

      ![Faça upload do ficheiro de metadados](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Quando o ficheiro de metadados é carregado com sucesso, os valores de URL do **Identificador** e **da Resposta** são autopovoados na secção Costpoint.

      > [!NOTE]
      > Se os valores **de URL** do **Identificador** e resposta não forem auto-polifados, introduza os valores manualmente de acordo com a sua exigência. Verifique se o **Identificador (ID da entidade)** e o URL de Resposta (URL do **Serviço de Consumidor de Afirmação)** estão corretamente definidos e que o URL **ACS** é um URL de Costpoint válido que termina com **/LoginServlet.cps**.

   1. Selecione **Definir URLs adicionais**. Para **o Estado de Retransmissão,** introduza um valor utilizando o seguinte padrão:`system=[your system]` (por exemplo, **system=DELTEKCP**).

1. Na configuração do Single Sign-On com a página **SAML,** na secção Certificado de **Assinatura SAML,** selecione o ícone **Copy** para copiar o Url de **Metadados da Federação de Aplicações** e guarde-o para o Bloco de Notas.

   ![Certificado de Assinatura SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Configure Costpoint

1. Volte ao Utilitário de Configuração Costpoint. Na caixa de texto **IdP Federation Metadata XML,** colá-cola o conteúdo do ficheiro Url de *Metadados da Federação de Aplicações.* 

   ![Utilitário de configuração de costpoint](./media/costpoint-tutorial/config-utility-idp.png)

1. Continue as instruções do guia **DeltekCostpoint711Security.pdf** para terminar a configuração Costpoint SAML.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, no painel esquerdo,**Users** > selecione Utilizadores de **Diretório** > Ativo Azure**Todos os utilizadores**.

   ![As ligações "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

1. Selecione **Novo utilizador**.

   ![Novo botão de utilizador](common/new-user.png)

1. Nas propriedades do **Utilizador,** preencha estes passos:

   ![A caixa de diálogo do Utilizador](common/user-properties.png)

   1. No campo **Nome,** insira **B.Simon.**
   
   1. No campo de **nome do Utilizador,** introduza `b.simon\@yourcompanydomain.extension` (por exemplo, B.Simon@contoso.com).
   
   1. Selecione a caixa de verificação **de palavra-passe do Show** e, em seguida, escreva o valor que está apresentado no campo **Password.**
   
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permite que b.Simon utilize um único sign-on Azure, concedendo acesso a B.Simon à Costpoint.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.

1. Na lista de aplicações, selecione **Costpoint**.

1. Na secção **Gerir** a página de visão geral da aplicação, selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

   ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. Na caixa de diálogo **de utilizadores e grupos,** na lista **de Utilizadores,** selecione **B.Simon**. Em seguida, escolha **Selecionar**.

1. Se estiver à espera de algum valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, escolha **Select**.

1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-a-costpoint-test-user"></a>Criar um utilizador de teste Costpoint

Nesta secção, cria-se um utilizador em Costpoint. Assuma que o id do utilizador é **B.SIMON** e o nome do utilizador é **B.Simon**. Trabalhe com a equipa de suporte do [Costpoint Client](https://www.deltek.com/about/contact-us) para adicionar o utilizador na plataforma Costpoint. O utilizador deve ser criado e ativado antes de poder utilizar um único sinal.

Após a criação do utilizador, a seleção do método de **autenticação** do utilizador deve ser **o Diretório Ativo,** a caixa de **verificação de sinal único SAML** deve ser selecionada e o nome de utilizador do Diretório Ativo azure deve ser **Diretório Ativo ou ID** de Certificado (mostrado na seguinte imagem).

![Utilizador de costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Costpoint no Painel de Acesso, deve ser automaticamente inscrito na aplicação Costpoint porque configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais para integrar aplicações saaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
