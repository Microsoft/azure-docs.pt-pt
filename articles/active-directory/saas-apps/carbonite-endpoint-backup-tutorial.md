---
title: 'Tutorial: Integração do Diretório Ativo Azure com Reforço do Ponto final da Carbonite [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Carbonite Endpoint Backup.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68879683"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutorial: Integrar backup de ponto final carbonite com diretório ativo Azure

Neste tutorial, você vai aprender a integrar carbonite Endpoint Backup com Azure Ative Directory (Azure AD). Quando integrar o Carbonite Endpoint Backup com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Carbonite Endpoint Backup.
* Ative que os seus utilizadores sejam automaticamente inscritos na Carbonite Endpoint Backup com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A subscrição ativada por subscrição ativada por Carbonite Endpoint Backup (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* Carbonite Endpoint Backup suporta **SP e IDP** iniciado SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Adicionar reforço site endpoint carbonite da galeria

Para configurar a integração do Carbonite Endpoint Backup em Azure AD, você precisa adicionar Carbonite Endpoint Backup da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No **Add da** secção galeria, **digite Carbonite Endpoint Backup** na caixa de pesquisa.
1. Selecione **Carbonite Endpoint Backup** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com Cópia de Segurança do Ponto final carbonite utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado em Backup Carbonite Endpoint.

Para configurar e testar o Azure AD SSO com cópia de segurança do Ponto de apoio carbonite Endpoint, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
2. **[Configure o SSO](#configure-carbonite-endpoint-backup-sso)** de Backup de Ponto de Fim de Carbonite - para configurar as definições de entrada única no lado da aplicação.
3. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** - para testar o único sign-on da Azure AD com b.Simon.
4. Atribuir o utilizador de **[teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de AD Azure.
5. **[Crie o utilizador do teste de backup de endpoint carbonite](#create-carbonite-endpoint-backup-test-user)** - para ter uma contrapartida de B.Simon em Carbonite Endpoint Backup que está ligada à representação do utilizador da AD Azure.
6. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração de aplicações **carbonite Endpoint Backup,** encontre a secção **Gerir** e selecione **single sign-on**.
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na secção **Basic SAML Configuration,** caso deseje configurar a aplicação no modo iniciado do **IDP,** introduza os valores para os seguintes campos:

    a. Na caixa de texto **identificador,** escreva um dos seguintes URLs:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. Na caixa de texto **URL resposta,** escreva um dos seguintes URLs:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Clique em **Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação no modo iniciado **por SP:**

    Na caixa de texto **de URL sign-on,** escreva um dos seguintes URLs:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Na configuração de um único sinal com página **SAML,** na secção certificado de **assinatura SAML,** encontre **certificado (Base64)** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

    ![O link de descarregamento do Certificado](common/certificatebase64.png)

1. Na secção de **backup carbonite endpoint,** copie os URL(s) adequados com base na sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Configure Carbonite Endpoint Backup SSO

1. Para automatizar a configuração dentro da Cópia de Segurança do Ponto final da Carbonite, precisa de instalar a extensão de **navegador Secure-in das Minhas Aplicações** clicando em **instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar Carbonite Endpoint Backup** irá direcioná-lo para a aplicação De backup de ponto final carbonite endpoint. A partir daí, forneça as credenciais de administração para assinar em Carbonite Endpoint Backup. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-7.

    ![Configuração de configuração de configuração](common/setup-sso.png)

3. Se pretender configurar manualmente o Carbonite Endpoint Backup, abra uma nova janela do navegador web e inscreva-se no site da empresa Carbonite Endpoint Backup como administrador e execute os seguintes passos:

4. Clique na **Empresa** a partir do painel esquerdo.

    ![Configuração de backup de ponto final carbonite ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Clique no **único sinal.**

    ![Configuração de backup de ponto final carbonite ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Clique em **Ativar** e, em seguida, clique em **definições de Editar** para configurar.

    ![Configuração de backup de ponto final carbonite ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na página de definições **de inscrição única,** execute os seguintes passos:

    ![Configuração de backup de ponto final carbonite ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Na caixa de texto do **fornecedor de identidade,** cola o valor do **Identificador AD Azure,** que copiou do portal Azure.

    1. Na caixa de texto URL do **fornecedor de identidade,** colá o valor URL do **Login,** que copiou do portal Azure.

    1. Clique no **ficheiro Escolha** para fazer o upload do ficheiro Certificado descarregado **(Base64)** do portal Azure.

    1. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que b.Simon utilize um único sign-on Azure, concedendo acesso ao Carbonite Endpoint Backup.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Carbonite Endpoint Backup**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

    ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

### <a name="create-carbonite-endpoint-backup-test-user"></a>Criar o utilizador do teste de backup de ponto final carbonite

1. Numa janela de navegador web diferente, inscreva-se no site da sua empresa carbonite Endpoint Backup como administrador.

1. Clique nos **Utilizadores** a partir do painel esquerdo e, em seguida, clique em **Adicionar utilizador**.

    ![Adicionar utilizador na cópia de segurança do ponto de apoio do ponto de partida carbonite](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na página **de utilizador Adicionar,** execute os seguintes passos:

    ![Adicionar utilizador na cópia de segurança do ponto de apoio do ponto de partida carbonite](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Insira o **Email**, **Primeiro Nome,** **Último nome** do utilizador e forneça as permissões necessárias ao utilizador de acordo com os requisitos organizacionais.

    1. Clique em **Adicionar utilizador**.

### <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal do Azure AD utilizando o Painel de Acesso.

Quando clicar no azulejo de backup carbonite endpoint no Painel de Acesso, deve ser automaticamente inscrito na Cópia de Segurança do Ponto final carbonite para a qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)