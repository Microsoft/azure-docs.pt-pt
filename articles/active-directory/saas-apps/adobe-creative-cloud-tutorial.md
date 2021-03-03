---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com a Adobe Creative Cloud | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o Adobe Creative Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 553f77cdb42aa0adb230ee3efd7bec7e9fbfa972
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101653376"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutorial: Azure Ative Directory integração única (SSO) com a Adobe Creative Cloud

Neste tutorial, você vai aprender a integrar a Adobe Creative Cloud com o Azure Ative Directory (Azure AD). Quando integrar a Adobe Creative Cloud com AZure AD, pode:

* Controle em Azure AD que tem acesso à Adobe Creative Cloud.
* Permita que os seus utilizadores sejam automaticamente inscritos na Adobe Creative Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Subscrição ativada pela Adobe Creative Cloud (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Adobe Creative Cloud suporta **SSO** iniciado sp

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Adicione a Nuvem Criativa adobe da galeria

Para configurar a integração da Adobe Creative Cloud em Azure AD, precisa adicionar a Nuvem Criativa da Galeria à sua lista de aplicações geridas pelo SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite a Nuvem Criativa do Adobe** na caixa de pesquisa.
1. Selecione **Adobe Creative Cloud** do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Configure e teste Azure AD SSO para Adobe Creative Cloud

Configure e teste Azure AD SSO com a Adobe Creative Cloud usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na Adobe Creative Cloud.

Para configurar e testar O Azure AD SSO com a Adobe Creative Cloud, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o Adobe Creative Cloud SSO](#configure-adobe-creative-cloud-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Create Adobe Creative Cloud test user](#create-adobe-creative-cloud-test-user)** - para ter uma contrapartida de B.Simon em Adobe Creative Cloud que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página de integração da aplicação **Adobe Creative Cloud,** encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone de lápis para **configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** insira os valores para os seguintes campos:

    a. Na caixa de texto **URL, digite** o URL: `https://adobe.com`

    b. Na caixa de texto **identifier (Entity ID),** digite um URL utilizando o seguinte padrão: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > O valor do Identificador não é real. Siga as orientações no passo 4 da secção **Configure Adobe Cloud SSO.** Na medida em que pode abrir o **ficheiro XML dos metadados** da Federação e obter o valor de ID da Entidade a partir dele e colocá-lo como um valor identificador na configuração AD do Azure. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A aplicação Adobe Creative Cloud espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Adobe Creative Cloud espera que alguns mais atributos sejam repercutidos na resposta SAML, que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com o seu requisito.

    | Name | Atributo de origem|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | utilizador.sobrenome |
    | E-mail | user.mail |

    > [!NOTE]
    > Os utilizadores precisam de ter uma licença de ExO válido microsoft 365 para o valor de reclamação de e-mail a ser povoado na resposta SAML.

1. Na **configuração de um único sessão de inscrição com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre **dados da Federação XML** e, em seguida, selecione **Descarregue** para descarregar o ficheiro de metadados XML e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/certificatebase64.png)

1. Na secção Configurar a **Nuvem Criativa da Adobe,** copie os URL(s) apropriados com base no seu requisito.

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

Nesta secção, você permite que B.Simon use a Azure single sign-on, concedendo acesso à Adobe Creative Cloud.

1. No portal Azure, selecione **Aplicações empresariais**  >  **Todas as aplicações**.
1. Na lista de aplicações, selecione **Adobe Creative Cloud**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.
1. Selecione **Adicionar utilizador**. Em seguida, na caixa de diálogo **de atribuição de adicionar,** selecione **Utilizadores e grupos**.
1. Na caixa de diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de utilizadores. Em seguida, escolha **Selecione** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. Na caixa de diálogo **'Adicionar Atribuição',** selecione **Atribuir**.

## <a name="configure-adobe-creative-cloud-sso"></a>Configurar a Nuvem Criativa da Adobe SSO

1. Numa janela diferente do navegador web, inscreva-se na [Consola Adobe Admin](https://adminconsole.adobe.com) como administrador de sistema.

1. Vá às **Definições** na barra de navegação superior e, em seguida, escolha **Identidade**. A lista de diretórios abre. Selecione o diretório federado que deseja.

1. Na página 'Detalhes do **Diretório',** selecione **Configure**.

1. Copie o ID da Entidade e o URL ACS (URL de serviço ao consumidor de afirmação ou URL de resposta). Introduza os URLs nos campos apropriados no portal Azure.

    ![Configurar um único sinal no lado da aplicação](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Utilize o valor de ID da Entidade que a Adobe forneceu para **o Identificador** na caixa de diálogo **configuração de configurações** .

    b. Utilize o valor ACS URL (URL do Serviço ao Consumidor de Afirmação) que a Adobe forneceu para **URL de resposta** na caixa de diálogo **configurações de configuração.**

1. Perto da parte inferior da página, faça o upload do ficheiro **Dados XML** da Federação que descarregou a partir do portal Azure. 

    ![Arquivo XML de dados da Federação](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "Metadados IdP XML")

1. Selecione **Guardar**.

### <a name="create-adobe-creative-cloud-test-user"></a>Criar utilizador de teste de Nuvem Criativa Adobe

Para permitir que os utilizadores de Azure AD assinem na Adobe Creative Cloud, devem ser a provisionados na Adobe Creative Cloud. No caso da Adobe Creative Cloud, o provisionamento é uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para a disponibilização de uma conta de utilizador, execute os seguintes passos:

1. Inscreva-se no site [da Consola Adobe Admin](https://adminconsole.adobe.com) como administrador.

2. Adicione o utilizador na consola da Adobe como ID Federado e atribua-o a um Perfil de Produto. Para obter informações detalhadas sobre a adição de utilizadores, consulte [adicionar utilizadores na Consola Adobe Admin](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. Neste ponto, digite o seu endereço de e-mail/upn no sinal de Adobe no formulário, no separador de imprensa, e deverá ser federado de volta para Azure AD:
   * Acesso à Web: www \. adobe.com > iniciar sedução
   * Dentro do utilitário de aplicativo de ambiente de trabalho > de iniciar sção
   * Dentro da aplicação > ajudar > iniciar s inscrição

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções. 

* Clique em **Testar esta aplicação** no portal Azure. Isto irá redirecionar para o URL de inscrição da Nuvem Criativa da Adobe, onde pode iniciar o fluxo de login. 

* Vá diretamente ao URL de inscrição da Nuvem Criativa da Adobe e inicie o fluxo de login a partir daí.

* Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo da Nuvem Criativa do Adobe nas Minhas Apps, este será redirecionado para o URL de sinal de cloud criativa da Adobe. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

## <a name="next-steps"></a>Passos seguintes

Assim que configurar a Adobe Creative Cloud, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se a partir do Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)