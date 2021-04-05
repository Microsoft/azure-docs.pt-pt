---
title: 'Tutorial: Integração única de sign-on (SSO) do Azure Ative Directory com a Nitro Productivity Suite | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Nitro Productivity Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181922"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Tutorial: Azure Ative Directory integração única (SSO) com Nitro Productivity Suite

Neste tutorial, você vai aprender a integrar nitro Productivity Suite com Azure Ative Directory (Azure AD). Quando integrar a Suite de Produtividade Nitro com Azure AD, pode:

* Controlo em Azure AD que tem acesso à Suite de Produtividade Nitro.
* Permita que os seus utilizadores sejam automaticamente inscritos na Nitro Productivity Suite com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa do seguinte:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura Nitro Productivity Suite [Enterprise](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Nitro Productivity Suite suporta **SSO** iniciado SP e **IDP.**
* Nitro Productivity Suite suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Adicione a Suíte de Produtividade Nitro da galeria

Para configurar a integração da Nitro Productivity Suite em Azure AD, você precisa adicionar Nitro Productivity Suite da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione **Azure Ative Directory**.
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Nitro Productivity Suite** na caixa de pesquisa.
1. Selecione **Nitro Productivity Suite** a partir dos resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Configurar e testar a Azure AD um único sinal para a Suite de Produtividade Nitro

Configure e teste Azure AD SSO com Nitro Productivity Suite, utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação ligada entre um utilizador Azure AD e o utilizador relacionado na Suite de Produtividade Nitro.

Para configurar e testar o Azure AD SSO com a Suite de Produtividade Nitro, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.

    a. [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o Azure AD com B.Simon.
    
    b. [Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir que a B.Simon utilize um único sinal de Ad AD.
    
2. Crie um utilizador de [teste Nitro Productivity Suite](#create-a-nitro-productivity-suite-test-user) para ter uma contraparte de B.Simon em Nitro Productivity Suite, ligada à representação AD AZure do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Nitro Productivity Suite,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. Na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot da secção de Certificado de Assinatura SAML, com link de descarregamento em destaque](common/certificatebase64.png)
    
1. Na secção Configurar a **Suite de Produtividade Nitro,** selecione o ícone de cópia ao lado **do URL de login**.
    
    ![Screenshot da configuração da secção Nitro Productivity Suite, com URLs e ícones de cópia em destaque](common/copy-configuration-urls.png)
    
1. No [portal Nitro Admin](https://admin.gonitro.com/), na página **'Definições da Empresa',** encontre a secção **'Sign-On' único.** Selecione **Configurar SAML SSO**.

    a. Cole o URL de **login** do passo anterior para o campo Sign **In URL.**
    
    b. Faça o upload do **Certificado (Base64)** a partir do passo anterior no campo **certificado de assinatura X509.**
    
    c. Selecione **Submeter**.
    
    d. Selecione **Ative o único sinal de inscrição**.


1. Regresse ao [portal do Azure](https://portal.azure.com/). Na **configuração single Sign-On com** a página SAML, selecione o ícone de lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de Configurar single Sign-On com página SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** copie e cole o campo de ID da **Entidade SAML** a partir do [portal Nitro Admin](https://admin.gonitro.com/). Deve ter o seguinte padrão: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Na caixa de texto **URL de resposta,** copie e cole o campo **URL ACS** a partir do [portal Nitro Admin](https://admin.gonitro.com/). Deve ter o seguinte padrão: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecione **Definir URLs adicionais** e executar o seguinte passo se quiser configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://sso.gonitro.com/login`

1. Selecione **Guardar**.

1. A aplicação Nitro Productivity Suite espera que as afirmações DO SAML estejam num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot de atributos predefinidos](common/default-attributes.png)

1. Além dos atributos anteriores, a aplicação Nitro Productivity Suite espera que mais alguns atributos sejam repercutidos na resposta SAML. Estes atributos são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name  |  Atributo de origem|
    | ---------------| --------------- |
    | número de empregados |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa **de verificação de senha show** e, em seguida, escreva a palavra-passe para baixo.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permite que B.Simon use a Azure single sign-on, concedendo acesso à Suite De Produtividade Nitro.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Nitro Productivity Suite**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Criar um utilizador de teste Nitro Productivity Suite

Nitro Productivity Suite suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nenhuma ação adicional para tomar. Se um utilizador já não existir na Suite De Produtividade Nitro, uma nova é criada após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

1. Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o Signo da Suite De Produtividade Nitro no URL, onde pode iniciar o fluxo de login.  

2. Vá diretamente ao URL de inscrição da Suite Nitro Productivity e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Suite de Produtividade Nitro para a qual configura o SSO 

Também pode utilizar o Microsoft Access Panel para testar a aplicação em qualquer modo. Quando clicar no azulejo da Suite De Produtividade Nitro no Painel de Acesso, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Suite de Produtividade Nitro para a qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Nitro Productivity Suite, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).