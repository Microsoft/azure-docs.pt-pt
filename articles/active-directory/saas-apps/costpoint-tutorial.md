---
title: 'Tutorial: Integração do Azure Ative Directory com Costpoint Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 16c9d64cc4dd49898245d74108cd6a4f2f0e4660
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92455175"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar Costpoint com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Costpoint com Azure Ative Directory (Azure AD). Quando integrar o Costpoint com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Costpoint.
* Permitir que os seus utilizadores sejam automaticamente inscritos no Costpoint com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por Costpoint (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD SSO num ambiente de teste. Costpoint suporta **SP e IDP** iniciado SSO.

## <a name="generate-costpoint-metadata"></a>Gerar metadados de ponto de custo

A configuração SSO de Costpoint SAML é explicada no guia **DeltekCostpoint711Security.pdf.** Faça o download deste guia a partir do site de suporte de Costpoint Deltek e consulte a **secção DE CONFIGURAÇÃO ÚNICA**  >  **SAML Configuração ÚNICA SAML entre Costpoint e Microsoft Azure.** Siga as instruções e gere um ficheiro **XML da Federação costpoint SP.** 

![Screenshot que mostra o "Utilitário de Configuração do Produto" com o separador "Weblogic - Security" selecionado.](./media/costpoint-tutorial/config-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adicione Costpoint da galeria

Para integrar o Costpoint com a Azure AD, adicione primeiro o Costpoint à sua lista de aplicações geridas do SaaS a partir da galeria no portal Azure:

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.

1. No painel de navegação esquerdo, selecione o serviço **Azure Ative Directory.**

   ![O botão Azure Ative Directory](common/select-azuread.png)

1. Selecione **Aplicações empresariais**  >  **todas as aplicações**.

   ![A lâmina de aplicações da Enterprise](common/enterprise-applications.png)

1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.

   ![O novo botão de aplicação](common/add-new-app.png)

1. Na secção Adicionar a partir da secção **da galeria,** insira **Costpoint** na caixa de pesquisa.

   ![Ponto de custo na lista de resultados](common/search-new-app.png)

1. Na lista de resultados, selecione **Costpoint**e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sgn-on"></a>Configure e teste Azure AD single sgn-on

Configure e teste Azure AD SSO com Costpoint utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Costpoint.

Para configurar e testar o Azure AD SSO com costpoint, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
1. **[Configure o Costpoint](#configure-costpoint)** para configurar as definições SAML SSO no lado da aplicação.
1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. Crie um utilizador de **[teste Costpoint](#create-a-costpoint-test-user)** para ter uma contraparte de B.Simon em Costpoint que esteja ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. Na página de integração da aplicação **Costpoint,** selecione **Single sign-on**.

   ![Configurar o link único de inscrição](common/select-sso.png)

1. Na secção **de Configuração BÁSICA SAML,** se tiver o *ficheiro de metadados do Fornecedor de Serviços,* complete estes passos:

   > [!NOTE]
   > Obtém o ficheiro de metadados do Fornecedor de Serviços em [Metadados de Gerada Costpoint](#generate-costpoint-metadata). Como usar o ficheiro é explicado mais tarde no tutorial.
 
   1. Selecione o botão **de ficheiro de metadados upload** e, em seguida, selecione o ficheiro **Metadados XML da Federação de Metadados de Costpoint** SP anteriormente gerado por Costpoint e, em seguida, selecione o botão **Adicionar** para carregar o ficheiro.

      ![Faça o upload do ficheiro de metadados](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Quando o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na secção Costpoint.

      > [!NOTE]
      > Se os valores de URL **de identificação** e **resposta** não forem polidos automaticamente, insira os valores manualmente de acordo com o seu requisito. Verifique se **o Identifier (Entity ID)** e **o URL de resposta (URL do Serviço de Apoio ao Consumidor de Afirmação)** estão corretamente definidos, e que **o URL ACS** é um URL de Costpoint válido que termina com **/LoginServlet.cps**.

   1. Selecione **Definir URLs adicionais**. Para **o Estado de Retransmissão**, introduza um valor utilizando o seguinte padrão: `system=[your system]` (por exemplo, **sistema=DELTEKCP**).

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** selecione o ícone **Copy** para copiar o Url **de Metadados da Federação de Aplicações** e guarde-o para o Bloco de Notas.

   ![Certificado de assinatura SAML](common/copy-metadataurl.png)

### <a name="configure-costpoint"></a>Configurar costpoint

1. Retorno ao Utilitário de Configuração de Costpoint. Na caixa de texto **IdP Federation Metadas XML,** cole o conteúdo do ficheiro *url de metadados da Federação de Aplicações.* 

   ![Utilitário de configuração de pontos de custo](./media/costpoint-tutorial/config-utility-idp.png)

1. Continue as instruções do guia **DeltekCostpoint711Security.pdf** para terminar a configuração Costpoint SAML.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

O objetivo desta secção é criar um utilizador de teste no portal Azure chamado B.Simon.

1. No portal Azure, no painel esquerdo, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

   ![Os links "Utilizadores e grupos" e "Todos os utilizadores"](common/users.png)

1. Selecione **Novo utilizador**.

   ![Novo botão de utilizador](common/new-user.png)

1. Nas propriedades do **Utilizador,** complete estes passos:

   ![A caixa de diálogo do utilizador](common/user-properties.png)

   1. No campo **Nome,** insira **B.Simon**.
   
   1. No campo **nome do utilizador,** insira `b.simon\@yourcompanydomain.extension` (por exemplo, ). B.Simon@contoso.com
   
   1. Selecione a caixa **de verificação 'Mostrar palavra-passe'** e, em seguida, anota o valor que é apresentado no campo **Palavra-passe.**
   
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon use a Azure single sign-on, concedendo a B.Simon acesso a Costpoint.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.

1. Na lista de candidaturas, selecione **Costpoint**.

1. Na secção **Gerir** a página geral da aplicação, selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **de atribuição de** adicionar, selecione **Utilizadores e grupos**.

   ![O link do utilizador adicionar](common/add-assign-user.png)

1. Na caixa de diálogo **de Utilizadores e grupos,** na lista **de Utilizadores,** selecione **B.Simon**. Em seguida, escolha **Select**.

1. Se estiver à espera de qualquer valor de função na afirmação SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, escolha **Select**.

1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-costpoint-test-user"></a>Criar um utilizador de teste Costpoint

Nesta secção, cria-se um utilizador em Costpoint. Assuma que o ID do utilizador é **B.SIMON** e o nome do utilizador é **B.Simon**. Trabalhe com a equipa de suporte do [Cliente Costpoint](https://www.deltek.com/about/contact-us) para adicionar o utilizador na plataforma Costpoint. O utilizador deve ser criado e ativado antes de poder utilizar uma única s ativação.

Após a criação do utilizador, a seleção do Método de **Autenticação** do utilizador deve ser **diretório ativo,** a caixa de verificação **de sinais de sinalização única SAML** deve ser selecionada e o nome de utilizador do Diretório Ativo Azure deve ser **Ative Directory ou Certificate ID** (mostrado na imagem seguinte).

![Utilizador de costpoint](./media/costpoint-tutorial/costpoint-user.png)

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo Costpoint no Painel de Acesso, deverá ser automaticamente inscrito na aplicação Costpoint porque configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais para integrar apps saaS com diretório ativo Azure](./tutorial-list.md)

- [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)