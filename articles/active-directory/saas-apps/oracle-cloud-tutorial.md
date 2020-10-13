---
title: 'Tutorial: Integração do Azure Ative Directory com consola de infraestruturas de nuvem oracle Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e a Oracle Cloud Infrastructure Console.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 7a0da85aeba1a9736884a6b8b345efd3f0a8544c
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996552"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Tutorial: Integrar consola de infraestruturas de nuvem oracle com diretório ativo Azure

Neste tutorial, você vai aprender a integrar a Consola de Infraestruturas da Oracle Cloud com O Diretório Ativo Azure (Azure AD). Quando integrar a Consola de Infraestruturas oracle Cloud com Ad AD Azure, pode:

* Control em Azure AD que tem acesso à Consola de Infraestruturas da Oracle Cloud.
* Permita que os seus utilizadores sejam automaticamente inscritos na Consola de Infraestruturas oracle Cloud com as suas contas AD Azure.
* Gerencie as suas contas numa localização central - o portal Azure.

Para saber mais sobre a integração da aplicação SaaS com a Azure AD, consulte o que é o acesso à [aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma assinatura AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* Consola de infraestrutura oracle Cloud única assinatura ativada (SSO).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você configura e testa Azure AD SSO em um ambiente de teste.

* Oracle Cloud Infrastructure Console suporta SSO iniciado **SP.**
* Assim que configurar a Consola de Infraestruturas da Nuvem oracle pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se desde o Acesso Condicional. [Saiba como impor o controlo da sessão com a Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Adicionar consola de infraestrutura oracle cloud da galeria

Para configurar a integração da Consola de Infraestruturas da Oracle Cloud em Azure AD, é necessário adicionar a Consola de Infraestruturas oracle Cloud da galeria à sua lista de aplicações geridas para o SaaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **Azure Ative Directory.**
1. Navegue para **aplicações empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. Na secção Adicionar a partir da secção **de galeria,** **digite consola de infraestruturas de nuvem oracle** na caixa de pesquisa.
1. Selecione **a Consola de Infraestruturas** oracle Cloud do painel de resultados e adicione a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar Azure AD único sinal de inscrição

Configure e teste Azure AD SSO com oracle Cloud Infrastructure Console usando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador Azure AD e o utilizador relacionado na Consola de Infraestruturas da Nuvem oracle.

Para configurar e testar o Azure AD SSO com consola oracle Cloud Infrastructure, complete os seguintes blocos de construção:

1. **[Configure Azure AD SSO](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste AD Azure](#create-an-azure-ad-test-user)** para testar o Azure AD com B. Simon.
    1. **[Atribua ao utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de Ad AD.
1. **[Configure a consola de infraestruturas da oracle cloud](#configure-oracle-cloud-infrastructure-console)** para configurar as definições SSO no lado da aplicação.
    1. **[Crie o utilizador](#create-oracle-cloud-infrastructure-console-test-user)** de teste de consola oracle Cloud Infrastructure para ter uma contrapartida de B. Simon na Consola de Infraestruturas da Nuvem Oracle que está ligada à representação AD Azure do utilizador.
1. **[Teste SSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o Azure AD SSO no portal Azure.

1. No [portal Azure](https://portal.azure.com/), na página de integração da aplicação **oracle Cloud Infrastructure Console,** encontre a secção **Gerir** e selecione **Single sign-on**.
1. Na página **de método de inscrição única,** selecione **SAML**.
1. Na **configuração single Sign-On com** a página SAML, clique no ícone edit/pen para **Configuração SAML Básica** para editar as definições.

   ![Editar Configuração BÁSICA SAML](common/edit-urls.png)

1. Na página **de Configuração Básica SAML,** insira os valores para os seguintes campos:

   > [!NOTE]
   > Obtém-se o ficheiro metadados do Fornecedor de Serviços na secção de assinatura única única do tutorial da consola de **infraestruturas de nuvem de oráculos.**
    
   1. Clique **em Carregar o ficheiro de metadados**.

   1. Clique no **logotipo da pasta** para selecionar o ficheiro de metadados e clique em **Upload**.

   1. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores de URL **de identificação** e **resposta** são preenchidos automaticamente na caixa de texto da secção **de configuração SAML básica.**
    
      > [!NOTE]
      > Se os valores de URL **de identificação** e **resposta** não forem automaticamente poluídos, preencha os valores manualmente de acordo com o seu requisito.

      Na caixa de texto **URL de entrada de inscrição,** digite um URL utilizando o seguinte padrão: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte [a equipa de suporte do cliente da Oracle Cloud Infrastructure Console](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) para obter o valor. Também pode consultar os padrões indicados na secção **de Configuração BÁSICA SAML** no portal Azure.

1. Na **configuração single Sign-On com** a página SAML, na secção **Certificado de Assinatura SAML,** encontre o **Metadados XML da Federação** e selecione **Descarregamento** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento de certificado](common/metadataxml.png)

1. A aplicação Oracle Cloud Infrastructure Console espera as afirmações SAML num formato específico, o que requer que adicione mapeamentos de atributos personalizados à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique **em Editar** o ícone para abrir o diálogo dos Atributos do Utilizador.

   ![Screenshot que mostra o diálogo "Atributos do Utilizador" com o ícone "Editar" no top-right selecionado.](common/edit-attribute.png)

1. Além de acima, a aplicação Oracle Cloud Infrastructure Console espera que alguns mais atributos sejam repercutidos na resposta SAML. Na secção **"Atributos & Reclamações** do Utilizador no diálogo **'Pré-visualização)** do Grupo, execute os seguintes passos:

   1. Clique na **caneta** ao lado **do valor do identificador Nome**.

   1. Selecione **Persistente** como **Escolher o formato do identificador de nomes**.
 
   1. Clique em **Guardar**.

      ![Screenshot que mostra a secção "Atributos do Utilizador & Reclamações" com os ícones "Editar" para "Valor identificador de nome" e "Grupos devolvidos em reivindicação" realçados.](./media/oracle-cloud-tutorial/config07.png)
    
      ![Screenshot que mostra o diálogo "Gerir as alegações do utilizador" com o botão "Persistant" e o botão "Save" selecionado.](./media/oracle-cloud-tutorial/config11.png)

   1. Clique na **caneta** ao lado **de Grupos devolvidos em reivindicação**.

   1. Selecione **grupos** de segurança da lista de rádio.

   1. Selecione **Atributo de Origem** do **ID do Grupo**.

   1. Verifique **personalizar o nome da reclamação do grupo**.

   1. Na caixa de texto **Name,** **escreva o nome do grupo**.

   1. Na caixa de texto **Namespace (opcional),** escreva `https://auth.oraclecloud.com/saml/claims` .

   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Na secção Configurar a consola **de infraestruturas da Oracle Cloud,** copie os URL(s) apropriados com base na sua exigência.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste AZure AD

Nesta secção, irá criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, selecione **Azure Ative Directory**, selecione **Utilizadores**, e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo **nome do utilizador,** insira o username@companydomain.extension . Por exemplo, `B. Simon@contoso.com`.
   1. Selecione a caixa **de verificação de palavra-passe Show** e, em seguida, anote o valor que é apresentado na caixa **palavra-passe.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste AZure AD

Nesta secção, você permitirá a B. Simon usar o Azure single sign-on, concedendo acesso à Consola de Infraestruturas da Nuvem oracle.

1. No portal Azure, selecione **Aplicações empresariais**e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Oracle Cloud Infrastructure Console**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. **Selecione Adicionar utilizador,** em seguida, selecione **Utilizadores e grupos** no diálogo **'Adicionar Atribuição'.**

   ![O link do utilizador adicionar](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** da lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na afirmação SAML, no diálogo **'Fun's Select,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Selecione** na parte inferior do ecrã.
1. No diálogo **'Adicionar Atribuição',** clique no botão **'Atribuir'.**

## <a name="configure-oracle-cloud-infrastructure-console"></a>Consola de infraestrutura de nuvem oracle configurada

1. Numa janela diferente do navegador web, inscreva-se na Consola de Infraestruturas da Oracle Cloud como Administrador.

1. Clique no lado esquerdo do menu e clique na **Identidade** e depois navegue para a **Federação.**

   ![Screenshot que mostra a "Consola de Infraestruturas da Nuvem de Oráculo" com "Identidade" e "Federação" selecionadas a partir do menu no topo esquerdo.](./media/oracle-cloud-tutorial/config01.png)

1. Guarde o **ficheiro de metadados do Fornecedor de Serviço** clicando no link de **descarregamento deste documento** e faça o upload para a secção de **Configuração SAML Básica** do portal Azure e, em seguida, clique no Fornecedor de Identidade **Adicionar**.

   ![Screenshot que mostra "Federação" selecionada a partir do menu do lado esquerdo e do botão "Adicionar Fornecedor de Identidade" e "Descarregue este documento" selecionado.](./media/oracle-cloud-tutorial/config02.png)

1. No pop-up do Fornecedor de **Identidade Adicionar,** execute os seguintes passos:

   ![Screenshot que mostra o pop-up "Add Identity Provider".](./media/oracle-cloud-tutorial/config03.png)

   1. Na caixa de texto **NAME,** insira o seu nome.

   1. Na caixa de texto **DESCRIÇÃO,** insira a sua descrição.

   1. Selecione **MICROSOFT ATIVE DIRECTORY FEDERATION SERVICE (ADFS) OU SAML 2.0 FORNECEDOR DE IDENTIDADE CONFORME** COMO **TIPO**.

   1. Clique **em Procurar** para carregar o MÍsamo de Metadados da Federação XML, que descarregou a partir do portal Azure.

   1. Clique **em Continuar** e na secção Fornecedor de Identidade de **Edição** execute os seguintes passos:

      ![Screenshot que mostra a secção "Editar Fornecedor de Identidade" com "Grupo fornecedor de identidade", "Grupo O C I", "Adicionar Mapeamento" e o botão "Enviar" em destaque.](./media/oracle-cloud-tutorial/config09.png)

   1. O **GRUPO FORNECEDOR DE IDENTIDADE** deve ser selecionado como Grupo Personalizado. O ID do grupo deve ser o guia do grupo do Azure Ative Directory. O grupo precisa de ser mapeado com o grupo correspondente no campo **OCI GROUP.**

   1. Pode mapear vários grupos de acordo com a sua configuração no portal Azure e a sua organização precisa. Clique em **+ Adicione mapeamento** para adicionar os grupos necessários.

   1. Clique **em Submeter.**
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Criar utilizador de teste de consola de infraestrutura de nuvem oracle

 A Oracle Cloud Infrastructure Console suporta o fornecimento just-in-time, que é por defeito. Não há nenhum item de ação para si nesta secção. Um novo utilizador não é criado durante uma tentativa de acesso e também não precisa de criar o utilizador.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo da Consola de Infraestruturas da Nuvem oracle no Painel de Acesso, será redirecionado para o sinal de Consola de Infraestruturas de Nuvem oracle na página. Selecione o **FORNECEDOR DE IDENTIDADE** no menu suspenso e clique em **Continuar** como mostrado abaixo para iniciar sposição. Para obter mais informações sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

![Configuração](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações saas com diretório ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Como proteger a Consola de Infraestruturas da Nuvem oracle com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
