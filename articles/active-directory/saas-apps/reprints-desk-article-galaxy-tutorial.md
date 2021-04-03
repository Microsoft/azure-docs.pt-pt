---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Reprints Desk - Artigo Galaxy | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Reprints Desk - Artigo Galaxy.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: e28281b783c66f8dbb0bc4842679eeec43755508
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515006"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Reprints Desk - Artigo Galaxy

Neste tutorial, você vai aprender a integrar Reprints Desk - Artigo Galaxy com Azure Ative Direy (Azure AD). Quando integra o Reprints Desk - Artigo Galaxy com Azure AD, pode:

* Control em Azure AD que tem acesso a Reprints Desk - Artigo Galaxy.
* Habilita os seus utilizadores a serem automaticamente inscritos no Reprints Desk - Artigo Galaxy com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Reimpressões Desk - Assinatura ativada por Article Galaxy single sign-on (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Reprints Desk - Artigo Galaxy suporta **IDP** iniciado SSO

* Reprints Desk - Artigo Galaxy suporta **provisão de** utilizador just in time

* [Uma vez configurado o Reprints Desk - Artigo Galaxy pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>Adicionar Reprints Desk - Artigo Galaxy da galeria

Para configurar a integração do Reprints Desk - Artigo Galaxy em Azure AD, precisa de adicionar Reprints Desk - Article Galaxy da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **escreva Reprints Desk - Artigo Galaxy** na caixa de pesquisa.
1. Selecione **Reprints Desk - Artigo Galaxy** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Configurar e testar Azure AD único sign-on para Reprints Desk - Artigo Galaxy

Configure e teste Azure AD SSO com Reprints Desk - Artigo Galaxy usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado no Reprints Desk - Artigo Galaxy.

Para configurar e testar O Azure AD SSO com Reprints Desk - Artigo Galaxy, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configurar Reiimpressões Desk Article Galaxy SSO](#configure-reprints-desk-article-galaxy-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Criar Reimpressões Desk Article Utilizador Galaxy test -](#create-reprints-desk-article-galaxy-test-user)** para ter uma contrapartida de B.Simon in Reprints Desk - Artigo Galaxy que está ligado à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), no **Balcão de Reimpressões -** página de integração de aplicações do Artigo Galaxy, encontre a secção **Gerir** e selecione um único sinal de **sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**


1. Reprints Desk - Aplicação De Artigo Galaxy espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, Reprints Desk - Aplicação de Artigo Galaxy espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ------------ | --------- |
    | nome de primeiro nome | user.givenname |
    | último nome | utilizador.sobrenome |

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. No **Balcão de Reimpressões Configurar - Secção De Galaxy de Artigos,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Reprints Desk - Artigo Galaxy.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Reprints Desk - Artigo Galaxy**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Configure Reimpressões Artigo de Mesa Galaxy SSO

Para configurar um único sign-on no **Reprints Desk - Artigo Galaxy** side, você precisa enviar o **metudata XML da Federação** descarregado e URLs copiados apropriados do portal Azure para [Reprints Desk - Equipe de suporte do Artigo Galaxy](mailto:customersupport@reprintsdesk.com). Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Criar Reimpressões Artigo Despa via Galaxy utilizador de teste

Nesta secção, um utilizador chamado B.Simon é criado no Reprints Desk - Artigo Galaxy. Reimpressões Desk - Artigo Galaxy suporta o fornecimento de utilizadores just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir no Reprints Desk - Artigo Galaxy, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no Balcão de Reimpressões - Artigo Galaxy azulejo no Painel de Acesso, deverá ser automaticamente inscrito no Balcão de Reimpressões - Artigo Galaxy para o qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)

- [Experimente o Balcão de Reimpressões - Artigo Galaxy com Azure AD](https://aad.portal.azure.com/)

- [O que é o controlo de sessão no Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Como proteger o Balcão de Reimpressões - Artigo Galaxy com visibilidade avançada e controlos](/cloud-app-security/proxy-intro-aad)