---
title: 'Tutorial: Azure Ative Diretório integração individual (SSO) com veracode [ Integração de um único sign-on do Azure Ative Diretório) com a Veracode [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73043565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutorial: Azure Ative Diretório integração individual (SSO) com veracode

Neste tutorial, você vai aprender a integrar Veracode com o Azure Ative Directory (Azure AD). Quando integrar o Veracode com o Azure AD, pode:

* Controlo em Azure AD que tem acesso a Veracode.
* Ative que os seus utilizadores sejam automaticamente inscritos no Veracode com as suas contas Azure AD.
* Gerencie as suas contas num local central: o portal Azure.

Para saber mais sobre software como uma integração de aplicações de serviço (SaaS) com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma subscrição ativada por um único sinal de Veracode (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste. A Veracode suporta o fornecedor de identidade iniciado sSO e o fornecimento de utilizadores just-in-time.

## <a name="add-veracode-from-the-gallery"></a>Adicione Veracode da galeria

Para configurar a integração do Veracode no Azure AD, adicione veracode da galeria à sua lista de aplicações saaS geridas.

1. Inscreva-se no [portal Azure](https://portal.azure.com) utilizando uma conta de trabalho ou de escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Vá a **Aplicações Empresariais**e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **da galeria,** digite "Veracode" na caixa de pesquisa.
1. Selecione **Veracode** a partir do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Configure e teste Azure AD único sign-on para Veracode

Configure e teste Azure AD SSO com Veracode utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma ligação entre um utilizador da AD Azure e o utilizador relacionado em Veracode.

Para configurar e testar o Azure AD SSO com veracode, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b.Simon utilize um único sinal de AD Azure.
1. **[Configure o Veracode SSO](#configure-veracode-sso)** para configurar as definições de inscrição únicas no lado da aplicação.
    * **[Crie um utilizador de teste Veracode](#create-veracode-test-user)** para ter uma contrapartida de B.Simon em Veracode ligada à representação da AD Azure do utilizador.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações **Veracode,** encontre a secção **Gerir.** Selecione **um único sinal.**
1. Na página **de método de inscrição, selecione** **SAML**.
1. No **set set single sign-on com** a página SAML, selecione o ícone do lápis para **configuração SAML básica** para editar as definições.

   ![Screenshot de configurar um único sign-on com SAML, com ícone de lápis em destaque](common/edit-urls.png)

1. Na secção **de Configuração Básica do SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com o Azure. Selecione **Guardar**.

1. No **set set single sign-on com** a página SAML, na secção Certificado de **Assinatura SAML,** encontre **certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot da secção de certificado de assinatura SAML, com link de descarregamento em destaque](common/certificatebase64.png)

1. O Veracode espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração dos seus atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot dos atributos do utilizador & secção de reclamações](common/default-attributes.png)

1. Veracode também espera que mais alguns atributos sejam retransmitidos na resposta SAML. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem|
    | ---------------| --------------- |
    | primeiro nome |User.givenname |
    | apelido |Utilizador.sobrenome |
    | e-mail |Utilizador.mail |

1. Na secção **Configurar veracode,** copie os URL(s) adequados com base no seu requisito.

    ![Screenshot da secção Veracode configurada, com URLs de configuração em destaque](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Configure Veracode SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Veracode como administrador.

1. A partir do menu em cima, selecione **Configurações** > **Administrador**.
   
    ![Screenshot da Administração Veracode, com ícone de Definições e Administrador destacado](./media/veracode-tutorial/ic802911.png "Administração")

1. Selecione o **separador SAML.**

1. Na secção **de Definições SAML** da Organização, execute os seguintes passos:

    ![Screenshot da secção de definições SAML da organização](./media/veracode-tutorial/ic802912.png "Administração")

    a.  Para **emitente,** colhe o valor do **Identificador AD Azure** que copiou do portal Azure.

    b. Para **obter certificado de assinatura de afirmação,** selecione Choose **File** para fazer o upload do seu certificado descarregado a partir do portal Azure.

    c. Para **auto-registo,** **selecione Ativar o auto-registo.**

1. Na secção Definições de **Auto-Registo,** execute os seguintes passos e, em seguida, selecione **Guardar:**

    ![Screenshot da secção de Definições de Auto-Registo, com várias opções em destaque](./media/veracode-tutorial/ic802913.png "Administração")

    a. Para **a ativação do novo utilizador,** selecione **nenhuma ativação necessária**.

    b. Para **atualizações de dados do utilizador,** selecione Dados do **Utilizador De Preferência veracode**.

    c. Para obter detalhes de **atributo SAML,** selecione o seguinte:
      * **Funções de Utilizador**
      * **Administrador de Política**
      * **Revisor**
      * **Chumbo de Segurança**
      * **Executivo**
      * **Submeter**
      * **Creator**
      * **Todos os tipos de digitalização**
      * **Membros da equipa**
      * **Equipa Padrão**

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B.Simon.

1. A partir do painel esquerdo no portal Azure,**Users** > selecione **Utilizadores de Diretório** >Ativo Azure**Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:

   1. Para **Nome,** insira `B.Simon`.  
   1. Para o nome username@companydomain.extensiondo **utilizador,** introduza o . Por exemplo, `B.Simon@contoso.com`.
   1. Selecione **mostrar palavra-passe**e, em seguida, anote o valor mostrado.
   1. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitir que b.Simon utilize um único sign-on Azure, concedendo acesso ao Veracode.

1. No portal Azure, selecione **Aplicações Empresariais** > **Todas as aplicações**.
1. Na lista de aplicações, selecione **Veracode**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![Screenshot da secção Gerir, com utilizadores e grupos em destaque](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**. Na caixa de diálogo **Adicionar Atribuição,** selecione **Utilizadores e grupos**.

    ![Screenshot de Utilizadores e página de grupos, com adicionar utilizador destacado](common/add-assign-user.png)

1. Na caixa de diálogo **de utilizadores e grupos,** a partir de **Utilizadores,** selecione **B.Simon**. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de papel na afirmação do SAML, na caixa de diálogo **Select Role,** selecione a função adequada para o utilizador da lista. Em seguida, escolha **Selecionar** na parte inferior do ecrã.
1. Na caixa de diálogo **Adicionar Atribuição,** selecione **Atribuir**.

### <a name="create-veracode-test-user"></a>Criar o utilizador de teste Veracode

Para iniciar sessão no Veracode, os utilizadores de Anúncios Azure devem ser aprovisionados no Veracode. Esta tarefa é automatizada e não precisa de fazer nada manualmente. Os utilizadores são automaticamente criados, se necessário, durante a primeira tentativa de início de sessão.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador veracode ou APIs fornecidas pela Veracode para fornecer contas de utilizador da AD Azure.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de acesso do Azure AD utilizando o Painel de Acesso.

Quando selecionar **o Veracode** no Painel de Acesso, deve ser automaticamente inscrito no Veracode para o qual configura o SSO. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso à aplicação e a inscrição única com o Azure Ative Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Experimente veracode com Azure AD](https://aad.portal.azure.com/)