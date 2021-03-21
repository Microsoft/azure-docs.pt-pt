---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com JLL TRIRIGA | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o JLL TRIRIGA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/17/2020
ms.author: jeedes
ms.openlocfilehash: b33db1b766d99aeb8d0a08f6396b5384948f118e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736873"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jll-tririga"></a>Tutorial: Azure Ative Directory integração única (SSO) com JLL TRIRIGA

Neste tutorial, você vai aprender a integrar JLL TRIRIGA com Azure Ative Diretório (Azure AD). Quando integrar a JLL TRIRIGA com AZure AD, pode:

* Controlo em Azure AD que tem acesso à JLL TRIRIGA.
* Ative os seus utilizadores a serem automaticamente inscritos na JLL TRIRIGA com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* JLL TRIRIGA assinatura única ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* JLL TRIRIGA apoia **IDP** iniciado SSO

## <a name="adding-jll-tririga-from-the-gallery"></a>Adicionando JLL TRIRIGA da galeria

Para configurar a integração da JLL TRIRIGA em AD Azure, é necessário adicionar o JLL TRIRIGA da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** digite **JLL TRIRIGA** na caixa de pesquisa.
1. Selecione **JLL TRIRIGA** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-jll-tririga"></a>Configure e teste Azure AD SSO para JLL TRIRIGA

Configure e teste Azure AD SSO com JLL TRIRIGA usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na JLL TRIRIGA.

Para configurar e testar a Azure AD SSO com jll TRIRIGA, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure jll TRIRIGA SSO](#configure-jll-tririga-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie o utilizador de **[teste JLL TRIRIGA](#create-jll-tririga-test-user)** - para ter uma contraparte de B.Simon em JLL TRIRIGA que esteja ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **JLL TRIRIGA,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. No **set-on único com** a página SAML, insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando um dos seguintes padrões:
   
    | Identificador |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT` |
    | `https://<SUBDOMAIN>.jll.com` |
    |

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando um dos seguintes padrões:

    | URL de Resposta |
    |-----------|
    | `https://<SUBDOMAIN>.valudconsulting.com:PORT/samlsps/trisaml` |
    | `https://<SUBDOMAIN>.jll.com/samlsps/trisaml` |
    |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de identificação e resposta real. Contacte [a equipa de suporte ao cliente JLL TRIRIGA](https://www.us.jll.com/contact-us) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configurar JLL TRIRIGA,** copie os URL(s) apropriados com base na sua exigência.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a JLL TRIRIGA.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **JLL TRIRIGA**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-jll-tririga-sso"></a>Configure JLL TRIRIGA SSO

Para configurar um único sinal no lado **JLL TRIRIGA,** é necessário enviar o Certificado descarregado **(Base64)** e URLs copiados apropriados do portal Azure para a equipa de [suporte JLL TRIRIGA.](https://www.us.jll.com/contact-us) Eles definem esta definição para ter a ligação SSO SAML corretamente definida em ambos os lados.

### <a name="create-jll-tririga-test-user"></a>Criar utilizador de teste JLL TRIRIGA

Nesta secção, cria-se um utilizador chamado Britta Simon na JLL TRIRIGA. Trabalhe com a [equipa de suporte JLL TRIRIGA](https://www.us.jll.com/contact-us) para adicionar os utilizadores na plataforma JLL TRIRIGA. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no JLL TRIRIGA para o qual configura o SSO

* Pode utilizar o Microsoft Access Panel. Quando clicar no azulejo JLL TRIRIGA no Painel de Acesso, deverá ser automaticamente inscrito no JLL TRIRIGA para o qual configura o SSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado JLL TRIRIGA, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).