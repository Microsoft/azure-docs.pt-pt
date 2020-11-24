---
title: 'Tutorial: Azure Ative Directy integração única (SSO) com NetSuite Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o NetSuite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: jeedes
ms.openlocfilehash: 9bebb11c3a1162a0a7b72140f7d3812f86f793ce
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95791374"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Tutorial: Integre o Azure AD single sign-on (SSO) com NetSuite

Neste tutorial, você vai aprender a integrar NetSuite com Azure Ative Directory (Azure AD). Quando integrar o NetSuite com Azure AD, pode:

* Controlo em Azure AD que tem acesso à NetSuite.
* Permita que os seus utilizadores sejam automaticamente inscritos no NetSuite com as suas contas AD Azure.
* Gerencie as suas contas numa localização central, o portal Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Uma assinatura ativada pela NetSuite (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste. 

Suportes NetSuite:

* SSO iniciado pelo IDP.
* JIT (just-in-time) fornecimento de utilizador.
* [Fornecimento automatizado de utilizadores](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Como o identificador desta aplicação é um valor fixo de cadeia, apenas um caso pode ser configurado em um inquilino.

## <a name="add-netsuite-from-the-gallery"></a>Adicione NetSuite da galeria

Para configurar a integração da NetSuite no AD Azure, adicione a NetSuite da galeria à sua lista de aplicações geridas saaS fazendo o seguinte:

1. Inscreva-se no portal Azure com uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel esquerdo, selecione o serviço **Azure Ative Directory.**
1. Vá a **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar uma nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite NetSuite** na caixa de pesquisa.
1. No painel de resultados, selecione **NetSuite** e, em seguida, adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Configurar e testar Azure AD único sinal para netSuite

Configure e teste Azure AD SSO com NetSuite utilizando um utilizador de teste chamado **B.Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador AZure AD e o utilizador relacionado na NetSuite.

Para configurar e testar o Azure AD SSO com a NetSuite, complete os seguintes blocos de construção:

1. [Configure Azure AD SSO](#configure-azure-ad-sso) para permitir que os seus utilizadores utilizem esta funcionalidade.
    * [Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user) para testar o único sinal de Azure AD com o utilizador B.Simon.  
    * [Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user) para permitir ao utilizador B.Simon utilizar um único sinal de Azure.
1. [Configure o NetSuite SSO](#configure-netsuite-sso) para configurar as definições de inscrição única no lado da aplicação.
    * [Crie o utilizador de teste NetSuite](#create-the-netsuite-test-user) para ter uma contrapartida do utilizador B.Simon na NetSuite que está ligada à representação AD Azure do utilizador.
1. [Teste SSO](#test-sso) para verificar se a configuração funciona.

## <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Para ativar o Azure AD SSO no portal Azure, faça o seguinte:

1. No portal Azure, na página de integração de aplicações **NetSuite,** procure a secção **Gestão** e, em seguida, selecione **Single sign-on**.
1. No **painel de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com painel SAML,** selecione o ícone **Edit** ("lápis") ao lado **da Configuração Básica SAML**.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na secção **configuração básica do SAML,** na caixa de texto **URL de resposta,** digite um URL num dos seguintes formatos:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Obtém-se o **<`Instance ID`>** valor na secção de configuração Netsuite, que é explicada mais tarde no tutorial no passo 8 em Configuração Netsuite. Encontrará o domínio exato (como system.na0.netsuite.com neste caso).

        ![A screenshot mostra a página de configuração SAML onde pode obter o domínio.](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Os valores nos URLs anteriores não são reais. Atualize-os com o URL de resposta real. Para obter o valor, contacte a equipa de suporte do [Cliente NetSuite.](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) Também pode consultar os formatos indicados na secção **Configuração Básica SAML** no portal Azure.

1. A aplicação NetSuite espera as afirmações DE SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos.

    ![image](common/default-attributes.png)

1. Além de acima, a aplicação NetSuite espera que alguns mais atributos sejam repercutidos na resposta SAML que são mostrados abaixo. Estes atributos também são pré-povoados, mas pode revê-los de acordo com os seus requisitos.

    | Nome | Atributo de origem |
    | ---------------| --------------- |
    | conta  | `account id` |

    > [!NOTE]
    > O valor do atributo da conta não é real. Irá atualizar este valor, como explicado mais tarde neste tutorial.

1. Na configuração de um único sessão de inscrição com a página SAML, na secção Certificado de Assinatura SAML, encontre o Metadados XML da Federação e selecione Descarregue para descarregar o certificado e guarde-o no seu computador.

    ![O link de descarregamento de certificado](common/metadataxml.png)

1. Na secção **Configuração NetSuite,** copie os URL ou URLs apropriados, dependendo da sua exigência.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, cria-se um utilizador de teste no portal Azure chamado B.Simon.

1. No painel esquerdo do portal Azure, selecione **Utilizadores do Diretório Ativo Azure**  >  **Users**  >  **Todos os utilizadores**.

1. Selecione **Novo utilizador** na parte superior do ecrã.

1. No painel de propriedades do **Utilizador,** siga estes passos:

   a. Na caixa **Nome,** insira **B.Simon**.  
   b. Na caixa **do nome do utilizador,** introduza a username@companydomain.extension (por exemplo, B.Simon@contoso.com ).  
   c. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**  
   d. Selecione **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, permite ao utilizador B.Simon utilizar o Azure single sign-on, permitindo o acesso ao NetSuite.

1. No portal Azure, selecione **Aplicações empresariais** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de candidaturas, selecione **NetSuite**.
1. No painel de visão geral, procure a secção **Gerir** e, em seguida, selecione a ligação **Utilizadores e grupos.**
1. **Selecione Adicionar utilizador** e, em seguida, no painel de atribuição de **adicionar,** selecione **Utilizadores e grupos**.
1. No painel **de Utilizadores e grupos,** na lista de **utilizadores,** selecione **B.Simon** e, em seguida, selecione o botão **Select** na parte inferior do ecrã.
1. Se espera algum valor de papel na afirmação da SAML, faça o seguinte:

   a. No painel **'Escolha' de Função,** na lista de espera, selecione a função adequada para o utilizador.  
   b. Na parte inferior do ecrã, selecione o botão **Select.**
1. No painel **de atribuição de adicionar,** selecione o botão **Atribuir.**

## <a name="configure-netsuite-sso"></a>Configure NetSuite SSO

1. Abra um novo separador no seu navegador e inscreva-se no site da empresa NetSuite como administrador.

2. Na barra de navegação superior, selecione **Configuração** e, em seguida, selecione **Company**  >  **Funcionalidades de Ativação** da Empresa .

    ![Screenshot mostra Enable Features selecionados da Empresa.](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Na barra de ferramentas no meio da página, selecione **SuiteCloud**.

    ![A screenshot mostra SuiteCloud selecionada.](./media/NetSuite-tutorial/ns-suitecloud.png)

4. No **Âmbito da Autenticação Gestão**, selecione a caixa de verificação **de sinais único SAML** para ativar a opção de inscrição única SAML no NetSuite.

    ![A screenshot mostra a autenticação de gestão onde pode selecionar O SEML Single Sign-on.](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Na barra de navegação superior, selecione **Configuração**.

    ![Screenshot mostra configuração selecionada a partir da barra de navegação NETSUITE.](./media/NetSuite-tutorial/ns-setup.png)

6. Na lista **de Tarefas de Configuração,** selecione **Integração**.

    ![Screenshot mostra integração selecionada a partir de TAREFAS DE CONFIGURAÇÃO.](./media/NetSuite-tutorial/ns-integration.png)

7. Em **Gestão de Autenticação**, selecione **SAML Single Sign-on**.

    ![Screenshot mostrar SINL Single Sign-on selecionado a partir do item de integração em TAREFAS DE CONFIGURAÇÃO.](./media/NetSuite-tutorial/ns-saml.png)

8. No painel **de configuração SAML,** em **Configuração NetSuite,** faça o seguinte:

    ![A screenshot mostra a Configuração SAML onde pode introduzir os valores descritos.](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecione a caixa **de verificação do método de autenticação primária.**

    b. Nos **metadados do Fornecedor de Identidade SAMLV2,** selecione **upload IDP Metadata File**, e, em seguida, selecione **Browse** para carregar o ficheiro de metadados que descarregou a partir do portal Azure.

    c. Selecione **Submeter**.

9. Na barra de navegação superior NetSuite, selecione **Configuração** e, em seguida, selecione **Company**  >  **Informações da Empresa**.

    ![A screenshot mostra informações da empresa selecionadas da Empresa.](./media/NetSuite-tutorial/ns-com.png)

    ![A imagem mostra o painel onde pode introduzir os valores descritos.](./media/NetSuite-tutorial/ns-account-id.png)

    b. No painel **de Informação da Empresa,** na coluna certa, copie o valor **de ID conta.**

    c. Cole o **ID da Conta** que copiou da conta NetSuite na caixa De Valor De **Atributo** em Azure AD.

    ![Screenshot mostra para adicionar o valor de id da conta](./media/netsuite-tutorial/attribute-value.png)

10. Antes de os utilizadores poderem realizar um único início de sessão no NetSuite, devem primeiro ser-lhes atribuídas as permissões adequadas no NetSuite. Para atribuir estas permissões, faça o seguinte:

    a. Na barra de navegação superior, selecione **Configuração**.

    ![Screenshot mostra configuração selecionada a partir da barra de navegação NETSUITE.](./media/NetSuite-tutorial/ns-setup.png)

    b. No painel esquerdo, selecione **Utilizadores/Funções** e, em seguida, **selecione Gerir Funções**.

    ![A screenshot mostra o painel 'Gerir papéis' onde pode selecionar New Role.](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecione **Novo Papel**.

    d. Insira um **Nome** para o novo papel.

    ![A screenshot mostra o Gestor de Configuração onde pode introduzir um nome para a função.](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selecione **Guardar**.

    f. Na barra de navegação superior, selecione **Permissões**. Em seguida, selecione **Configuração**.

    ![A screenshot mostra o separador Configuração onde pode introduzir os valores descritos.](./media/NetSuite-tutorial/ns-sso.png)

    exemplo, Selecione **O Sign-on Único SAML** e, em seguida, selecione **Adicionar**.

    h. Selecione **Guardar**.

    i. Na barra de navegação superior, selecione **Configuração** e, em seguida, selecione **Setup Manager**.

    ![Screenshot mostra configuração selecionada a partir da barra de navegação NETSUITE.](./media/NetSuite-tutorial/ns-setup.png)

    j. No painel esquerdo, selecione **Utilizadores/Funções** e, em seguida, **selecione Gerir utilizadores**.

    ![A screenshot mostra o painel de Utilizadores de Gestão onde pode selecionar a Equipa de Demonstração suite.](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecione um utilizador de teste, **selecione Editar** e, em seguida, selecione o **separador 'Saber'.**

    ![A screenshot mostra o painel de Utilizadores de Gestão onde pode selecionar Editar.](./media/NetSuite-tutorial/ns-edit-user.png)

    l. No painel **Roles,** atribua o papel apropriado que criou.

    ![A screenshot mostra administrador selecionado do Empregado.](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selecione **Guardar**.

### <a name="create-the-netsuite-test-user"></a>Criar o utilizador de teste NetSuite

Nesta secção, um utilizador chamado B.Simon é criado na NetSuite. O NetSuite suporta o fornecimento de utilizadores just-in-time, o que é ativado por padrão. Não há nada de ação para ti nesta secção. Se um utilizador já não existir no NetSuite, um novo é criado após a autenticação.

## <a name="test-sso"></a>Teste SSO

Nesta secção, testa a configuração de um único sinal de inscrição Azure AD com as seguintes opções.

- Clique em Testar esta aplicação no portal Azure e deverá ser automaticamente inscrito no NetSuite para o qual configura o SSO

- Pode utilizar as minhas apps do Microsoft. Quando clicar no azulejo NetSuite nas Minhas Apps, deverá ser automaticamente inscrito no NetSuite para o qual configura o SSO. Para obter mais informações sobre as Minhas Apps, consulte [Introdução às Minhas Aplicações.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)


## <a name="next-steps"></a>Passos seguintes

Uma vez configurado o NetSuite, pode impor controlos de sessão, o que protege a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)
