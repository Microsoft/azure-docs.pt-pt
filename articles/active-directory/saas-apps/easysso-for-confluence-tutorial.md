---
title: 'Tutorial: Azure Ative Directory integração única de sign-on (SSO) com easySSO para confluência | Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Azure Ative Directory e o EasySSO para Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 325f6ad7d9685fac17e17b28c4ffbe31b1245cca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734550"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Tutorial: Azure Ative Directory integração única de sign-on (SSO) com easySSO para confluência

Neste tutorial, você vai aprender a integrar easySSO para Confluence com Azure Ative Directory (Azure AD). Quando integra o EasySSO para Confluência com AZure AD, pode:

* Controlo em Azure AD que tem acesso à Confluência.
* Capacitar os seus utilizadores a serem automaticamente inscritos na Confluence com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* EasySSO para assinatura única de confluência (SSO) ativada.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* EasySSO para Confluência suporta **SP e IDP** iniciado SSO
* EasySSO para Confluência suporta provisão do utilizador **Just In Time**

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>Adicionar EasySSO para Confluência da galeria

Para configurar a integração da EasySSO para Confluence em AZure AD, precisa adicionar EasySSO para Confluência da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no portal Azure usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** escreva **EasySSO para Confluência** na caixa de pesquisa.
1. Selecione **EasySSO para Confluência** a partir do painel de resultados e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Configurar e testar Azure AD SSO para easySSO para confluência

Configure e teste Azure AD SSO com EasySSO para Confluência usando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na EasySSO para Confluence.

Para configurar e testar o Azure AD SSO com easySSO para confluência, execute os seguintes passos:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** - para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. Crie um utilizador de **[teste AD Azure](#create-an-azure-ad-test-user)** - para testar um único sinal de Azure com B.Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** - para permitir que b.Simon utilize um único sinal de Ad AD.
1. **[Configure o EasySSO para o Confluence SSO](#configure-easysso-for-confluence-sso)** - para configurar as definições de inscrição única no lado da aplicação.
    1. **[Crie easySSO para utilizador de teste de Confluência](#create-easysso-for-confluence-test-user)** - para ter uma contraparte de B.Simon na EasySSO para Confluence que está ligada à representação AD AD do utilizador.
1. **[Teste SSO](#test-sso)** - para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No portal Azure, na página **easySSO para** integração de aplicações confluência, encontre a secção **Gerir** e selecione um único sinal **de sação**.
1. Na página de método **de inscrição** única, selecione **SAML**.
1. No **set-on único com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **Configuração Básica SAML,** se pretender configurar a aplicação no modo iniciado pelo **IDP,** insira os valores para os seguintes campos:

    a. Na caixa de texto **do identificador,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Na caixa de texto **URL de resposta,** digite um URL utilizando o seguinte padrão: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Clique **em Definir URLs adicionais** e execute o seguinte passo se desejar configurar a aplicação **no** modo iniciado sp:

    Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real, URL de resposta e URL de inscrição. Contacte a [equipa de suporte da EasySSO](mailto:support@techtime.co.nz) para obter estes valores em caso de dúvida. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. A easySSO para aplicação Confluence espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação EasySSO for Confluence espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.
    
    | Name | Atributo de origem|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | utilizador.sobrenome |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Caso os utilizadores da Azure AD tenham **configurado o nome sAMAccountName,** teria de mapear **a urna:oid:0.9.2342.19200300.100.1.1 para** o atributo **sAMAccountName.**
    
1. Na **configuração de um único sessão de sessão com** a página SAML, na secção **Certificado de Assinatura SAML,** clique em **Baixar** links para **opções De Certificado (Base64)** ou **Federação De Metadados XML** e guardar qualquer um ou todos para o seu computador. Mais tarde, vai precisar dele para configurar a Confluence EasySSO.

    ![O link de descarregamento de certificado](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Se planeia executar o EasySSO para configuração de Confluência manualmente com certificado, também precisa copiar o **URL de Login** e o **Identificador AD Azure** da secção abaixo e guardá-los no seu computador.

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

Nesta secção, você permitirá que B.Simon use a Azure single sign-on, concedendo acesso à EasySSO para Confluence.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **EasySSO para Confluência**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

1. No diálogo **de Utilizadores e grupos,** selecione **B.Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera que uma função seja atribuída aos utilizadores, pode selecioná-la a partir do Dropdown de **função** Select. Se não tiver sido configurada qualquer função para esta aplicação, vê a função "Acesso Predefinido" selecionada.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-easysso-for-confluence-sso"></a>Configurar easySSO para A SSO de Confluência

1. Para automatizar a configuração no EasySSO para Confluence, é necessário instalar a **extensão do navegador 'As aplicações' Secure's,** clicando **em instalar a extensão**.

    ![Extensão das minhas aplicações](common/install-myappssecure-extension.png)

2. Depois de adicionar extensão ao navegador, clique em **Configurar o EasySSO para Confluence** irá direcioná-lo para a aplicação EasySSO para confluência. A partir daí, forneça as credenciais de administração para assinar na EasySSO para Confluence. A extensão do navegador configurará automaticamente a aplicação para si e automatizará os passos 3-9.

    ![Configuração de configuração](common/setup-sso.png)

1. Se pretender configurar o EasySSO para Confluência manualmente, inscreva-se na sua instância de Confluência Atlassiana com privilégios de Administrador e navegue para a secção **'Gerir aplicações'.** 

    ![Gerir Aplicações](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. No lado esquerdo, localize o **EasySSO** e clique nele. Em seguida, clique no **botão Configurar.**

    ![SSO fácil](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Selecione a opção **SAML.** Isto irá levá-lo à secção de configuração SAML.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Selecione o separador **Certificados** na parte superior e será apresentado com o seguinte ecrã: 

    ![URL de metadados](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Agora, localizar **Certificado (Base64)** ou **Metadados File** que guardou nos passos anteriores da configuração **AZure AD SSO.** Tem as seguintes opções sobre como proceder:

    a. Utilize o **Ficheiro de Metadados** da Federação de Aplicações que descarregou para o ficheiro local no seu computador. Selecione **carregar** o botão de rádio e siga o diálogo do ficheiro de upload específico para o seu sistema operativo

    **OR**

    b. Abra o **Ficheiro de Metadados** da Federação de Aplicações para ver o conteúdo (em qualquer editor de texto simples) do ficheiro e copiá-lo na área de transferência. Selecione a opção **entrada** e cole o conteúdo da área de transferência no campo de texto.
 
    **OR**

    c. Configuração totalmente manual. Abra o Certificado da Federação de Aplicações **(Base64)** para ver o conteúdo (em qualquer editor de texto simples) do ficheiro e copiá-lo na área de transferência. Cole-o no campo de texto **de assinatura de IdP Token.** Em seguida, navegue para separador **geral** e preencha os campos de URL e **ID de ID** **de ID de encadernação** post com os respetivos valores para **URL de Login** e **identificador AD AD que** guardou anteriormente.
 
6. Clique em **Guardar** o botão na parte inferior da página. Verá que o conteúdo dos ficheiros Metadados ou Certificado é analisado nos campos de configuração. A configuração EasySSO para confluência está completa.

7. Para obter a melhor experiência de teste, navegue no **separador Look & Feel** e consulte a opção **botão de login SAML.** Isto permitirá um botão separado no ecrã de login da Confluence especificamente para testar a sua integração Azure AD SAML de ponta a ponta. Pode deixar este botão ligado e configurar a sua colocação, cor e tradução para o modo de produção, também.

    ![Olhar & Sentir](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Se tiver algum problema, contacte a equipa de [suporte da EasySSO.](mailto:support@techtime.co.nz)

### <a name="create-easysso-for-confluence-test-user"></a>Criar EasySSO para utilizador de teste de Confluência

Nesta secção, um utilizador chamado Britta Simon é criado em Confluence. O EasySSO para Confluence suporta o fornecimento de utilizadores just-in-time, que é **desativado** por padrão. Para ativar o fornecimento do utilizador, tem de verificar explicitamente criar o utilizador na opção **de login bem sucedida** na secção geral da configuração do plugin EasySSO. Se um utilizador já não existir em Confluence, um novo é criado após a autenticação.

No entanto, se não pretender permitir o fornecimento automático de utilizadores no primeiro login do utilizador, os utilizadores devem existir em Diretórios de Utilizador backend que a confluência casos de Confluence fazem uso, como LDAP ou Atlassian Crowd.

![Aprovisionamento de utilizadores](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Teste SSO 

### <a name="idp-initiated-workflow"></a>Fluxo de trabalho iniciado pelo IdP

Nesta secção, você testa a sua configuração de sinal de Ad Ad única Azure usando as Minhas Apps.

Quando clicar no azulejo EasySSO para Confluence nas Minhas Apps, deverá ser automaticamente inscrito na instância Confluence para a qual configura sSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](../user-help/my-apps-portal-end-user-access.md)

### <a name="sp-initiated-workflow"></a>Fluxo de trabalho iniciado pela SP

Nesta secção, testa a configuração de login Azure AD com o botão de **Login Confluence SAML.**

![Login SAML do utilizador](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

Este cenário pressupõe que tenha ativado **o botão de login DO SAML** no **separador Olhar & Sinta o** separador na sua página de configuração Confluence EasySSO (ver acima). Abra o URL de login da Confluence no modo incógnito do navegador para evitar qualquer interferência nas sessões existentes. Clique no botão **DE Início de Sessão SAML** e será redirecionado para o fluxo de autenticação do utilizador Azure. Uma vez concluído com sucesso, será redirecionado de volta para a sua instância confluência como utilizador autenticado via SAML.

Há a possibilidade de encontrar o seguinte ecrã depois de ser redirecionado de volta do AZure AD

![Ecrã de falha EasySSO](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

Neste caso, tem de seguir as [instruções desta página]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) para ter acesso ao ficheiro **atlassian-confluence.log.** Os detalhes do erro estarão disponíveis pelo ID de referência encontrado na página de erro easySSO.

Se tiver algum problema em digerir as mensagens de registo, contacte a [equipa de suporte da EasySSO.](mailto:support@techtime.co.nz)

## <a name="next-steps"></a>Passos seguintes

Uma vez configurado EasySSO para Confluência, pode impor o controlo da sessão, que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. O controlo da sessão estende-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com o Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).