---
title: 'Tutorial: Integração do Azure Ative Directory com costpoint | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Costpoint.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: ed3a808085e09ec7b5989fc623cc9129244ca2a6
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559058"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrar Costpoint com Diretório Ativo Azure

Neste tutorial, você vai aprender a integrar Costpoint com Azure Ative Directory (Azure AD). Quando integrar o Costpoint com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Costpoint.
* Permitir que os seus utilizadores sejam automaticamente inscritos no Costpoint com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada por costpoint (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, irá configurar e testar o Azure AD SSO num ambiente de teste. 

* Costpoint suporta **SP e IDP** iniciado SSO.

## <a name="generate-costpoint-metadata"></a>Gerar metadados de ponto de custo

A configuração SSO de Costpoint SAML é explicada no guia **DeltekCostpoint711Security.pdf.** Faça o download deste guia a partir do site de suporte de Costpoint Deltek e consulte a **secção DE CONFIGURAÇÃO ÚNICA**  >  **SAML Configuração ÚNICA SAML entre Costpoint e Microsoft Azure.** Siga as instruções e gere um ficheiro **XML da Federação costpoint SP.** 

![Screenshot que mostra o "Utilitário de Configuração do Produto" com o separador "Weblogic - Security" selecionado.](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Adicione Costpoint da galeria

Para configurar a integração do Costpoint no Azure AD, é necessário adicionar o Costpoint da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Costpoint** na caixa de pesquisa.
1. Selecione **Costpoint** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Configure e teste Azure AD SSO para Costpoint

Configure e teste Azure AD SSO com Costpoint usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Costpoint.

Para configurar e testar a Azure AD SSO com costpoint, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure costpoint SSO](#configure-costpoint-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Costpoint test user](#create-costpoint-test-user)** - para ter uma contraparte de B.Simon em Costpoint que está ligada à representação AD AZure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir que o Azure AD SSO no portal Azure:

1. Na página de integração da aplicação **Costpoint,** selecione **Single sign-on**.

1. Na secção **de Configuração BÁSICA SAML,** se tiver o **ficheiro de metadados do Fornecedor de Serviços,** complete estes passos:

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

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Costpoint.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Costpoint**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-costpoint-sso"></a>Configurar costpoint SSO

1. Retorno ao Utilitário de Configuração de Costpoint. Na caixa de texto **IdP Federation Metadas XML,** cole o conteúdo do ficheiro *url de metadados da Federação de Aplicações.* 

   ![Utilitário de configuração de pontos de custo](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Continue as instruções do guia **DeltekCostpoint711Security.pdf** para terminar a configuração Costpoint SAML.

### <a name="create-costpoint-test-user"></a>Criar utilizador de teste Costpoint

Nesta secção, cria-se um utilizador em Costpoint. Assuma que o ID do utilizador é **B.SIMON** e o nome do utilizador é **B.Simon**. Trabalhe com a equipa de suporte do [Cliente Costpoint](https://www.deltek.com/about/contact-us) para adicionar o utilizador na plataforma Costpoint. O utilizador deve ser criado e ativado antes de poder utilizar uma única s ativação.

Após a criação do utilizador, a seleção do Método de **Autenticação** do utilizador deve ser **diretório ativo,** a caixa de verificação **de sinais de sinalização única SAML** deve ser selecionada e o nome de utilizador do Diretório Ativo Azure deve ser **Ative Directory ou Certificate ID** (mostrado na imagem seguinte).

![Utilizador de costpoint](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Costpoint Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição de Costpoint e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito no Costpoint para o qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo Costpoint nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito no Costpoint para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Costpoint, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
