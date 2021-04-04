---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Mapbox | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Mapbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e74018d4b145f8dec44137ca1a3b0713b1b5cadd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728612"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Tutorial: Azure Ative Directory integração única (SSO) com Mapbox

Neste tutorial, você vai aprender a integrar mapbox com Azure Ative Directory (Azure AD). Quando integrar a Mapbox com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Mapbox.
* Permita que os seus utilizadores sejam automaticamente inscritos na Mapbox com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por uma única caixa de mapas (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Mapbox suporta SSO iniciado no **IDP**

> [!NOTE]
> O identificador desta aplicação é um valor fixo de cadeia para que apenas um caso possa ser configurado em um inquilino.

## <a name="adding-mapbox-from-the-gallery"></a>Adicionar Mapbox da galeria

Para configurar a integração da Mapbox no AZure AD, é necessário adicionar a Mapbox da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Mapbox** na caixa de pesquisa.
1. Selecione **Mapbox** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Configure e teste Azure AD SSO para mapbox

Configure e teste Azure AD SSO com Mapbox usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado no Mapbox.

Para configurar e testar o Azure AD SSO com a Mapbox, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Mapbox SSO](#configure-mapbox-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Mapbox test user](#create-mapbox-test-user)** - para ter uma contraparte de B.Simon na Mapbox que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Mapbox,** encontre a secção **Gerir** e selecione **um único sinal de sing.**
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. O utilizador precisa de guardar a configuração clicando no botão **Guardar.**

1. A aplicação Mapbox espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Mapbox espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name   |  Atributo de origem|
    | -----|--------- |
    | papel | user.assignedroles |
    | | |

    > [!NOTE]
    > Para entender como configurar papéis em Azure AD, consulte [aqui.](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o Certificado **(Raw)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificateraw.png)

1. Na secção **Configurar mapbox,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Mapbox.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Mapbox**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se tiver configurado as funções como explicado no acima, pode selecioná-lo a partir do Dropdown de **função Select.**
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-mapbox-sso"></a>Configurar mapbox SSO

1. Numa janela diferente do navegador web, inscreva-se no Mapbox como administrador.

1. Clique no separador **Definições**.

    ![Separador de Definições de Mapbox](./media/mapbox-tutorial/configure1.png)

1. Clique no separador **Segurança** a partir do painel de navegação à esquerda.

    ![Separador de segurança mapbox](./media/mapbox-tutorial/configure2.png)

1. Clique **em Editar um único sinal de sessão.**

    ![Mapbox Editar um único sinal](./media/mapbox-tutorial/configure3.png)

1. Desloque-se até ao **passo 3: Configurar o único sinal de saml para a Mapbox** e executar os seguintes passos:

    ![Configuração da caixa de mapas](./media/mapbox-tutorial/configure4.png)

    1. Na caixa de texto url de inscrição do **Idp,** cole o valor URL do **Login,** que copiou a partir do portal Azure.

    1. Na caixa de texto do **ID do emitente,** cole o valor **do identificador Azure AD,** que copiou a partir do portal Azure.

    1. Abra o ficheiro **Certificado (Raw)** descarregado do portal Azure para o Bloco de Notas e copie o conteúdo do ficheiro de certificado e cole-o na caixa de texto do **certificado X.509.**

    1. Clique **em Guardar as definições de inscrição única .**

### <a name="create-mapbox-test-user"></a>Criar utilizador de teste Mapbox

Nesta secção, cria-se um utilizador chamado Britta Simon na Mapbox. Trabalhe com a [equipa de suporte da Mapbox](mailto:help@mapbox.com) para adicionar os utilizadores na plataforma Mapbox. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO 

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito na Mapbox para a qual configura o SSO

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Mapbox nas Minhas Apps, deverá ser automaticamente inscrito na Mapbox para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado mapbox, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
