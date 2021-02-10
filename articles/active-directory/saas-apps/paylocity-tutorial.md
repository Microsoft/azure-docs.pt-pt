---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com Paylocity | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Paylocity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: 847d129c2a3e3426888010df8cf97dfd386f1fd0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090739"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Tutorial: Azure Ative Directory integração única (SSO) com Paylocity

Neste tutorial, você vai aprender a integrar Paylocity com Azure Ative Directory (Azure AD). Quando integrar paylocity com Azure AD, pode:

* Controlo em Azure AD que tem acesso a Paylocity.
* Permitir que os seus utilizadores sejam automaticamente inscritos na Paylocity com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição única de paylocity (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Paylocity apoia **SP e IDP** iniciado SSO

## <a name="add-paylocity-from-the-gallery"></a>Adicionar Paylocity da galeria

Para configurar a integração da Paylocity no AD Azure, é necessário adicionar Paylocity da galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **da galeria,** **digite Paylocity** na caixa de pesquisa.
1. Selecione **Paylocity** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-paylocity"></a>Configure e teste Azure AD SSO para paylocity

Configure e teste Azure AD SSO com Paylocity usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado em Paylocity.

Para configurar e testar a Azure AD SSO com Paylocity, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    * Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    * **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure paylocity SSO](#configure-paylocity-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    * **[Create Paylocity test user](#create-paylocity-test-user)** - para ter uma contraparte de B.Simon em Paylocity que está ligada à representação AZure AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Paylocity,** encontre a secção **Gerir** e selecione **um único sinal de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **De Configuração Básica SAML,** o utilizador não tem de realizar qualquer passo, uma vez que a aplicação já está pré-integrada com o Azure.

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite o URL:  `https://access.paylocity.com/`

1. Clique em **Guardar**.

1. A aplicação Paylocity espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação Paylocity espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas você tem que atualizar estes atributos com os valores reais.

    | Name |  Atributo de origem|
    | ---------------| --------------- |
    | PartnerID | `P8000010` |
    | PaylocityUser | `user.mail`|
    | Entidade de Paylocity | < `PaylocityEntity` > |

    > [!NOTE]
    > A Entidade Paylocity é o ID da Empresa paylocity.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregue** para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na **configuração de um único sinal de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Editar Ícone**.

    ![Screenshot que mostra o "S A M L Signing Certificate" com a ação "Download" para "Federation Metadata X M L" selecionado.](./media/paylocity-tutorial/edit-samlassertion.png)

1. Selecione **a Opção de Assinatura** como Sinal de resposta e **afirmação SAML** e clique em **Guardar**.

    ![A Edição de Certificado de Assinatura SAML](./media/paylocity-tutorial/saml-assertion.png)

1. Na secção **De Locação De Pagamento,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à Paylocity.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **Paylocity**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**
1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-paylocity-sso"></a>Configurar paylocity SSO

Para configurar um único sinal no lado **paylocity,**

1. Descarregue o **Metadados XML da Federação.**
1. Na Paylocity, navegue para **HR &**  >    >  **Configuração SSO** de Acesso ao Utilizador de Pagamento .
1. Selecione **Adicionar Integração SSO** em **integrações SSO**. Uma gaveta nova abre.
1. Selecione **o Microsoft Azure** como o Fornecedor SSO do dropdown.
1. Selecione **Status** de dropdown.
1. Arraste e deixe cair o ficheiro de metadados na área de entrega. A paylocity tenta analisar os emissores, os URLs de redireccionamento e de ligação e os certificados de segurança.
1. **Selecione Guardar** para confirmar as alterações. A integração deve ser exibida no âmbito **das Integrações SSO**.

### <a name="create-paylocity-test-user"></a>Criar utilizador de teste paylocity

Nesta secção, cria-se um utilizador chamado B.Simon in Paylocity. Trabalhe com [a equipa de suporte paylocity](mailto:service@paylocity.com) para adicionar os utilizadores na plataforma Paylocity. Os utilizadores devem ser criados e ativados antes de utilizar uma única s ativação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

#### <a name="sp-initiated"></a>SP iniciado:

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para Paylocity Sign no URL onde pode iniciar o fluxo de login.  

* Vá diretamente ao URL de inscrição de Paylocity e inicie o fluxo de login a partir daí.

#### <a name="idp-initiated"></a>IDP iniciado:

* Clique em **Testar esta aplicação** no portal Azure e deverá ser automaticamente inscrito na Paylocity para a qual configura o SSO. 

Também pode utilizar o Microsoft My Apps para testar a aplicação em qualquer modo. Quando clicar no azulejo paylocity nas Minhas Apps, se configurado no modo SP, será redirecionado para o sinal de aplicação na página para iniciar o fluxo de login e se configurado no modo IDP, deverá ser automaticamente inscrito na Paylocity para a qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado Paylocity, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
