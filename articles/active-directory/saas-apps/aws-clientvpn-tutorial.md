---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com AWS ClientVPN Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o AWS ClientVPN.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/29/2020
ms.author: jeedes
ms.openlocfilehash: 11aac69816714611c168cd05c2828dd1ae85786e
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813654"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-clientvpn"></a>Tutorial: Azure Ative Directy integração única (SSO) com AWS ClientVPN

Neste tutorial, você vai aprender a integrar AWS ClientVPN com Azure Ative Directy (Azure AD). Quando integrar o AWS ClientVPN com Azure AD, pode:

* Controle em Azure AD que tem acesso a AWS ClientVPN.
* Capacitar os seus utilizadores a serem automaticamente inscritos no AWS ClientVPN com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* AWS ClientVPN única subscrição ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* AWS ClientVPN suporta **SSO** iniciado SP

* AWS ClientVPN suporta **provisão de** utilizadores just in time

## <a name="adding-aws-clientvpn-from-the-gallery"></a>Adicionar AWS ClientVPN da galeria

Para configurar a integração do AWS ClientVPN em Azure AD, é necessário adicionar o AWS ClientVPN da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite AWS ClientVPN** na caixa de pesquisa.
1. Selecione **AWS ClientVPN** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-aws-clientvpn"></a>Configurar e testar Azure AD SSO para AWS ClientVPN

Configure e teste Azure AD SSO com AWS ClientVPN usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no AWS ClientVPN.

Para configurar e testar a Azure AD SSO com a AWS ClientVPN, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure a AWS ClientVPN SSO](#configure-aws-clientvpn-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie o utilizador de teste AWS ClientVPN](#create-aws-clientvpn-test-user)** - para ter uma contraparte de B.Simon em AWS ClientVPN que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **AWS ClientVPN,** encontre a secção **Gerir** e selecione **um único login**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando o seguinte padrão: `https://<LOCALHOST>`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:

    | URL de Resposta |
    |------------|
    | `http://<LOCALHOST>` |
    | `https://self-service.clientvpn.amazonaws.com/api/auth/sso/saml` |
    |

    > [!NOTE]
    > Estes valores não são reais.  Atualize estes valores com o sinal real no URL e URL de resposta.  O URL de sinal de URL e resposta pode ter o mesmo valor http://127.0.0.1:35001) (.  Consulte a [Documentação VPN do Cliente AWS](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#ad) para obter mais detalhes.   Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure. Contacte [a equipa de suporte da AWS ClientVPN](https://aws.amazon.com/contact-us/) para qualquer problema de configuração. 

1. No serviço Azure Ative Directory, navegue para **as inscrições da App** e, em seguida, selecione Todas as **Aplicações**.

1. Digite **AWS ClientVPN** na caixa de pesquisa e selecione **AWS ClientVPN** a partir do painel de pesquisa.

1. Clique no **Manifesto** e precisa de manter o URL de resposta como **http** em vez de **https** para obter a integração a funcionar, clique em **Guardar**.

    ![página manifesto](./media/aws-clientvpn-tutorial/reply-url.png)

1. A aplicação AWS ClientVPN espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação AWS ClientVPN espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Nome |  Atributo de origem|
    | -------------- | --------- |
    | membroOf | utilizador.grupos |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração AWS ClientVPN,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)
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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao AWS ClientVPN.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **AWS ClientVPN**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-aws-clientvpn-sso"></a>Configurar a AWS ClientVPN SSO

Para configurar um único sign-on no lado **AWS ClientVPN,** você precisa enviar o **Metdata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para a equipa de [suporte AWS ClientVPN](https://aws.amazon.com/contact-us/). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-aws-clientvpn-test-user"></a>Criar utilizador de teste AWS ClientVPN

Nesta secção, um utilizador chamado Britta Simon é criado na AWS ClientVPN. O AWS ClientVPN suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no AWS ClientVPN, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para URL de entrada de assinatura do cliente AWS, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição do Cliente AWS e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo AWS ClientVPN nas Minhas Apps, este será redirecionado para URL de inscrição do cliente AWS. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado O ClienteVPN AWS pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


