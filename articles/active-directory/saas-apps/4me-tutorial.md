---
title: 'Tutorial: Azure Ative Directy integração única de sign-on (SSO) com 4me | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o 4me.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: f2a171460defb3a8befba5a674a2f1aadde636af
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517832"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Tutorial: Azure Ative Directy integração única (SSO) com 4me

Neste tutorial, você vai aprender a integrar 4me com Azure Ative Direy (Azure AD). Quando integra 4me com AD Azure, pode:

* Controlo em Azure AD que tem acesso a 4me.
* Ative os seus utilizadores a serem automaticamente inscritos em 4me com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* 4me assinatura única (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* 4me suporta SSO iniciado **SP.**
* 4me suporta o fornecimento do utilizador **Just In Time.**

## <a name="add-4me-from-the-gallery"></a>Adicione 4me da galeria

Para configurar a integração de 4me em AD Azure, precisa adicionar 4me da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar da secção **da galeria,** escreva **4me** na caixa de pesquisa.
1. Selecione **4me** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-4me"></a>Configure e teste Azure AD SSO para 4me

Configure e teste Azure AD SSO com 4me usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em 4me.

Para configurar e testar o Azure AD SSO com 4me, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure 4me SSO](#configure-4me-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. Crie um utilizador de **[teste de 4m](#create-4me-test-user)** - para ter uma contraparte de B.Simon em 4me que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração de aplicações **de 4m,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** um URL utilizando um dos seguintes padrões:

    | Ambiente| URL|
    |---|---|
    | PRODUÇÃO | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando um dos seguintes padrões:

    | Ambiente| URL|
    |---|---|
    | PRODUÇÃO | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o sinal real no URL e no identificador. Contacte [a equipa de suporte do cliente 4me](mailto:support@4me.com) para obter estes valores. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação 4me espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação 4me espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | utilizador.sobrenome |

1. Na secção **Certificado de Assinatura SAML,** clique em Editar o botão **Editar** para abrir o diálogo **do Certificado de Assinatura SAML.**

    ![Editar certificado de assinatura SAML](common/edit-certificate.png)

1. Na secção **Certificado de Assinatura SAML,** copie a **IMPRESSÃO DIGITAL** e guarde-a no seu computador.

    ![Valor da impressão digital do polegar da cópia](common/copy-thumbprint.png)

1. Na secção **Configurar 4m,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a 4me.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **4me**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-4me-sso"></a>Configurar 4me SSO

1. Numa janela diferente do navegador web, inscreva-se em 4me como Administrador.

1. No superior esquerdo, clique no logótipo **Definições** e na barra lateral esquerda clique em **'Sign-On' único**.

    ![Configurações de 4m](./media/4me-tutorial/settings.png)

1. Na página **'Sign-On' único,** execute os seguintes passos:

    ![4me singleasignon](./media/4me-tutorial/single-sign-on.png)

    a. Selecione a opção **Ativado**.

    b. Na caixa de texto **url de logótipo remoto,** cole o valor do URL **logout,** que copiou a partir do portal Azure.

    c. Na secção **SAML,** na caixa de texto **URL SSO SAML,** cole o valor do URL de **Login,** que copiou a partir do portal Azure.

    d. Na caixa de texto **de impressão digital do Certificado,** cole o valor DE IMPRESSÃO **POLEGAR** separado por um cólon na ordem duplets (AA:BB:CC:DD:EE:FF:GG:HH:II), que copiou do portal Azure.

    e. Clique em **Guardar**.

### <a name="create-4me-test-user"></a>Criar utilizador de teste de 4m

Nesta secção, um utilizador chamado Britta Simon é criado em 4me. O 4me suporta o provisionamento do utilizador just-in-time, que é ativado por padrão. Não há nenhum item de ação para si nesta secção. Se um utilizador já não existir em 4me, um novo é criado após a autenticação.

> [!Note]
> Se necessitar de criar um utilizador manualmente, contacte a [equipa de suporte 4me](mailto:support@4me.com).

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para URL de inscrição de 4m onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição 4me e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo de 4me nas Minhas Apps, este será redirecionado para 4me URL de inscrição. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado 4me, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
