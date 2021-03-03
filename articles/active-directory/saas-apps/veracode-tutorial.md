---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Veracode | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Veracode.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: f56f2dc974df58575c72c93a0609026cd7bbf88d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652628"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Veracode

Neste tutorial, você vai aprender a integrar o Veracode com o Azure Ative Directory (Azure AD). Quando integrar o Veracode com a AD Azure, pode:

* Controlo em Azure AD que tem acesso a Veracode.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Veracode com as suas contas AD Azure.
* Gerencie as suas contas num local central: o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada por Veracode (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. A Veracode suporta o fornecedor de identidade iniciado SSO e o fornecimento de utilizadores just-in-time.

## <a name="add-veracode-from-the-gallery"></a>Adicione Veracode da galeria

Para configurar a integração do Veracode no Azure AD, adicione a Veracode da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure utilizando uma conta de trabalho ou escola ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** escreva "Veracode" na caixa de pesquisa.
1. Selecione **Veracode** do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-veracode"></a>Configurar e testar Azure AD SSO para Veracode

Configure e teste Azure AD SSO com Veracode utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, deve estabelecer uma ligação entre um utilizador AZure AD e o utilizador relacionado em Veracode.

Para configurar e testar a Azure AD SSO com a Veracode, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    * **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B.Simon.
    * **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que a B.Simon utilize um único sinal de Ad AD.
1. **[Configure o Veracode SSO](#configure-veracode-sso)** para configurar as definições de inscrição única no lado da aplicação.
    * **[Crie um utilizador de teste Veracode](#create-veracode-test-user)** para ter uma contraparte de B.Simon em Veracode ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Veracode,** encontre a secção **Gerir.** Selecione **um único sinal de inscrição**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, selecione o ícone de lápis para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **de Configuração Básica SAML,** a aplicação está pré-configurada e os URLs necessários já estão pré-povoados com Azure. Selecione **Guardar**.

1. Na **configuração de um único sinal com** a página SAML, na secção Certificado de Assinatura **SAML,** encontre **o Certificado (Base64)**. Selecione **Baixar** para descarregar o certificado e guardá-lo no seu computador.

    ![Screenshot da secção de Certificado de Assinatura SAML, com link de descarregamento em destaque](common/certificatebase64.png)

1. A Veracode espera as afirmações DO SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![Screenshot da secção de atributos de & de créditos do utilizador](common/default-attributes.png)

1. A Veracode também espera que mais alguns atributos sejam repercutidos na resposta SAML. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Name | Atributo de origem|
    | ---------------| --------------- |
    | nome de primeiro nome |Nome de utilizador.given |
    | último nome |User.sobrenome |
    | e-mail |User.mail |

1. Na secção **Configurar Veracode,** copie os URL(s) apropriados com base no seu requisito.

    ![Screenshot da secção Configurar Veracode, com URLs de configuração em destaque](common/copy-configuration-urls.png)

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso a Veracode.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Veracode**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-veracode-sso"></a>Configurar Veracode SSO

1. Numa janela diferente do navegador web, inscreva-se no site da sua empresa Veracode como administrador.

1. A partir do menu em cima, selecione **Definições**  >  **Admin**.
   
    ![Screenshot da Administração Veracode, com ícone de Definições e Administrador em destaque](./media/veracode-tutorial/admin.png "Administração")

1. Selecione o separador **SAML.**

1. Na secção **Configurações SAML** da Organização, execute os seguintes passos:

    ![Screenshot da secção de configurações SAML da organização](./media/veracode-tutorial/saml.png "Administração")

    a.  Para **emitente,** cole o valor do **Identificador AD Azure** que copiou do portal Azure.

    b. Para **obter o Certificado de Assinatura de Afirmação**, **selecione Choose File** para fazer o upload do certificado descarregado a partir do portal Azure.

    c. Para **auto-registo,** selecione **Enable Self Registration**.

1. Na secção **Definições de Auto-Registo,** execute os seguintes passos e, em seguida, selecione **Guardar**:

    ![Screenshot da secção Definições de Auto-Registo, com várias opções em destaque](./media/veracode-tutorial/save.png "Administração")

    a. Para **nova ativação do utilizador**, selecione Não é necessária **ativação**.

    b. Para **atualizações de dados do utilizador**, selecione Preferência **Veracode User Data**.

    c. Para **detalhes de atributos SAML,** selecione o seguinte:
      * **Funções de Utilizador**
      * **Administrador de Política**
      * **Revisor**
      * **Chumbo de segurança**
      * **Executivo**
      * **Submetidar**
      * **Creator**
      * **Todos os tipos de digitalização**
      * **Membros da equipa**
      * **Equipa Predefinido**

### <a name="create-veracode-test-user"></a>Criar utilizador de teste veracode

Nesta secção, um utilizador chamado B.Simon é criado em Veracode. O Veracode suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nada de ação para ti nesta secção. Se um utilizador já não existir no Veracode, um novo é criado após a autenticação.

> [!NOTE]
> Pode utilizar quaisquer outras ferramentas de criação de conta de utilizador Veracode ou APIs fornecidas pela Veracode para fornecer contas de utilizador Azure AD.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

* Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no Código para o qual configura o SSO.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo veracode nas Minhas Apps, deverá ser automaticamente inscrito no Código para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o Código de Estado pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).