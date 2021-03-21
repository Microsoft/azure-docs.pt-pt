---
title: 'Tutorial: Integração do Diretório Ativo Azure com | de backup de pontos de terminação carbonita Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Carbonite Endpoint Backup.
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
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456488"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutorial: Integrar a Cópia de Segurança do Ponto Final de Carbonite com o Azure Ative Directory

Neste tutorial, você vai aprender a integrar carbonite Endpoint Backup com Azure Ative Directory (Azure AD). Quando integrar a Cópia de Segurança De Ponto De Carbonite com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Carbonite Endpoint Backup.
* Permita que os seus utilizadores sejam automaticamente inscritos na Carbonite Endpoint Backup com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Assinatura ativada por Carbonite Endpoint Backup (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Carbonite Endpoint Backup suporta **SP e IDP** iniciado SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Adicionar backup carbonite endpoint da galeria

Para configurar a integração do Carbonite Endpoint Backup em Azure AD, precisa adicionar o Carbonite Endpoint Backup da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite Carbonite Endpoint Backup** na caixa de pesquisa.
1. Selecione **Carbonite Endpoint Backup** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com Carbonite Endpoint Backup usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Carbonite Endpoint Backup.

Para configurar e testar o Azure AD SSO com a Carbonite Endpoint Backup, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO de backup de ponto final carbonite](#configure-carbonite-endpoint-backup-sso)** - para configurar as definições de Sign-On única no lado da aplicação.
3. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
4. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
5. **[Create Carbonite Endpoint Test user](#create-carbonite-endpoint-backup-test-user)** - para ter uma contraparte de B.Simon in Carbonite Endpoint Backup que está ligada à representação AD AZure do utilizador.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **Carbonite Endpoint Backup,** encontre a secção **'Gerir'** e selecione **'Único sinal de sismo'.**
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **identifier,** digite um dos seguintes URLs:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. Na caixa de texto **URL de resposta,** digite um dos seguintes URLs:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de assinatura,** digite um dos seguintes URLs:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Na **configuração single Sign-On com página SAML,** na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção **Configuração Carbonite Endpoint Backup,** copie os URL(s) apropriados com base no seu requisito.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Configurar carbonite endpoint backup SSO

1. Para automatizar a configuração dentro do Carbonite Endpoint Backup, é necessário instalar a **extensão do navegador 'As aplicações' Secure Sign-in** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Setup Carbonite Endpoint Backup** irá direcioná-lo para a aplicação De backup de ponto final carbonita. A partir daí, forneça as credenciais de administração para assinar na Carbonite Endpoint Backup. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração](common/setup-sso.png)

3. Se pretender configurar o Carbonite Endpoint Backup manualmente, abra uma nova janela do navegador web e inscreva-se no site da empresa Carbonite Endpoint Backup como administrador e execute os seguintes passos:

4. Clique na **Empresa** a partir do painel esquerdo.

    ![A screenshot mostra Carbonite Endpoint com a Empresa selecionada.](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Clique em **'S'S-on' Único**.

    ![A screenshot mostra a Empresa com um único sinal de s-on selecionado.](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Clique em **Ativar** e, em seguida, clique em **Editar as definições** para configurar.

    ![A screenshot mostra o separador único de sinal de inscrição com definições de Ativação e Edição chamadas.](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na página de definições **de inscrição única,** execute os seguintes passos:

    ![A screenshot mostra o separador único de assinatura com as informações descritas neste passo.](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Na caixa de texto **do nome do fornecedor de identidade,** cole o valor do **identificador Azure AD,** que copiou do portal Azure.

    1. Na caixa de texto **URL do fornecedor de identidade,** cole o valor URL de **login,** que copiou a partir do portal Azure.

    1. Clique em **Escolher o ficheiro** para fazer o upload do ficheiro Certificado **(Base64)** descarregado a partir do portal Azure.

    1. Clique em **Guardar**.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso ao Carbonite Endpoint Backup.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Carbonite Endpoint Backup**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

    ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

### <a name="create-carbonite-endpoint-backup-test-user"></a>Criar utilizador de teste de backup de ponto final de Carbonite

1. Numa janela diferente do navegador web, inscreva-se no site da empresa Carbonite Endpoint Backup como administrador.

1. Clique nos **Utilizadores** a partir do painel esquerdo e, em seguida, clique em **Adicionar utilizador**.

    ![O Screenshot mostra a página de Ponto final de Carbonite com os utilizadores e adicionar utilizadores selecionados.](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na página de **utilizador Adicionar,** execute os seguintes passos:

    ![A screenshot mostra a página do utilizador Adicionar onde pode executar os passos descritos aqui.](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Introduza o **Email**, **Nome próprio**, **Apelido** do utilizador e forneça as permissões necessárias ao utilizador de acordo com os requisitos da Organização.

    1. Clique **em Adicionar utilizador**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de inscrição única AZure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de backup carbonite endpoint no Painel de Acesso, deverá ser automaticamente inscrito na Cópia de Segurança Final carbonita para a qual configura sSO. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](../user-help/my-apps-portal-end-user-access.md)

## <a name="additional-resources"></a>Recursos adicionais

- [ Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure ](./tutorial-list.md)

- [O que é o acesso à aplicação e um único acesso ao Azure Ative Directory? ](../manage-apps/what-is-single-sign-on.md)

- [O que é o acesso condicional no Azure Active Directory?](../conditional-access/overview.md)