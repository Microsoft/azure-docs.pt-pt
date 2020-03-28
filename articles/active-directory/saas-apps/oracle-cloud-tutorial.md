---
title: 'Tutorial: Integração de Diretório Ativo Azure com consola de infraestrutura oracle Cloud [ Microsoft Docs'
description: Saiba como configurar um único sign-on entre o Diretório Ativo Azure e a Consola de Infraestruturas Oracle Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: f045fe19-11f8-4ccf-a3eb-8495fdc8716f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64cae5812a380725d612d27190042797542ee255
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289106"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Tutorial: Integrar consola de infraestrutura oracle cloud com diretório ativo Azure

Neste tutorial, aprenderás a integrar a Consola de Infraestruturas Oracle Cloud com o Diretório Ativo Azure (Azure AD). Quando integrar a Consola de Infraestruturas Oracle Cloud com a AD Azure, pode:

* Controle em Azure AD que tem acesso à Consola de Infraestruturas Oracle Cloud.
* Ative que os seus utilizadores sejam automaticamente inscritos na Consola de Infraestruturas Oracle Cloud com as suas contas Azure AD.
* Gerencie as suas contas num local central - o portal Azure.

Para saber mais sobre a integração de apps SaaS com a Azure AD, consulte [o que é o acesso à aplicação e o único sign-on com o Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, precisa dos seguintes itens:

* Uma subscrição da AD Azure. Se não tiver uma subscrição, pode obter uma [conta gratuita.](https://azure.microsoft.com/free/)
* A assinatura ativada pela Consola de Infraestrutura Oracle Cloud (SSO) permitiu a subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configura e testa o Azure AD SSO num ambiente de teste.

* A Consola de Infraestruturas Oracle Cloud suporta **SP** iniciado SSO.
* Assim que configurar a Consola de Infraestruturas Oracle Cloud, pode impor controlos de sessão, que protegem a exfiltração e infiltração dos dados sensíveis da sua organização em tempo real. Os controlos de sessão estendem-se a partir do Acesso Condicional. [Saiba como impor o controlo de sessão com o Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Adicionar consola de infraestrutura oracle cloud da galeria

Para configurar a integração da Consola de Infraestruturas Oracle Cloud em Azure AD, precisa adicionar a Consola de Infraestruturas Oracle Cloud da galeria à sua lista de aplicações geridas saaS.

1. Inscreva-se no [portal Azure](https://portal.azure.com) usando uma conta de trabalho ou escola, ou uma conta pessoal da Microsoft.
1. No painel de navegação à esquerda, selecione o serviço **de Diretório Ativo Azure.**
1. Navegue para **Aplicações Empresariais** e, em seguida, selecione **Todas as Aplicações**.
1. Para adicionar nova aplicação, selecione **Nova aplicação**.
1. No Add da secção **galeria,** digite a Consola de **Infraestruturas Oracle Cloud** na caixa de pesquisa.
1. Selecione A Consola de **Infraestruturas Oracle Cloud** do painel de resultados e, em seguida, adicione a aplicação. Espere alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configure e teste Azure AD único signo

Configure e teste Azure AD SSO com consola de infraestrutura oracle cloud usando um utilizador de teste chamado **B. Simon**. Para que o SSO funcione, é necessário estabelecer uma relação de ligação entre um utilizador da AD Azure e o utilizador relacionado na Consola de Infraestruturas Oracle Cloud.

Para configurar e testar o Azure AD SSO com a Consola de Infraestruturas Oracle Cloud, complete os seguintes blocos de construção:

1. **[Configure O SSO AD Azure](#configure-azure-ad-sso)** para permitir que os seus utilizadores utilizem esta funcionalidade.
    1. **[Crie um utilizador de teste Azure AD](#create-an-azure-ad-test-user)** para testar o único sinal de Azure AD com B. Simon.
    1. **[Atribua o utilizador de teste Azure AD](#assign-the-azure-ad-test-user)** para permitir que b. Simon utilize um único sinal de AD Azure.
1. Configure a Consola de **[Infraestruturas](#configure-oracle-cloud-infrastructure-console)** Oracle Cloud para configurar as definições SSO no lado da aplicação.
    1. Crie o utilizador de teste da Consola de **[Infraestruturas Oracle Cloud](#create-oracle-cloud-infrastructure-console-test-user)** para ter uma contrapartida de B. Simon na Consola de Infraestruturas Oracle Cloud que está ligada à representação do utilizador da AD Azure.
1. **[Teste sSO](#test-sso)** para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para permitir o Azure AD SSO no portal Azure.

1. No [portal Azure,](https://portal.azure.com/)na página de integração de aplicações da Consola de **Infraestruturas Oracle Cloud,** encontre a secção **Gerir** e selecione **um único sinal.**
1. Na página **Select a Single sign-on,** selecione **SAML**.
1. Na configuração do Single Sign-On com a página **SAML,** clique no ícone de edição/caneta para **configuração Básica sAML** para editar as definições.

   ![Editar Configuração Básica do SAML](common/edit-urls.png)

1. Na página **basic SAML Configuração,** introduza os valores para os seguintes campos:

   > [!NOTE]
   > Obterá o ficheiro de metadados do Fornecedor de Serviços na secção de inscrição única da consola de **infraestrutura** da Nuvem de Configuração do Oracle Cloud do tutorial.
    
   1. Clique no **ficheiro de metadados de upload**.

   1. Clique no logotipo da **pasta** para selecionar o ficheiro de metadados e clicar em **Carregar**.

   1. Uma vez que o ficheiro de metadados é carregado com sucesso, os valores **de URL** do **Identificador** e da Resposta ficam povoados automaticamente na caixa de texto da secção de **configuração Básica SAML.**
    
      > [!NOTE]
      > Se os valores de URL do **Identificador** e **da Resposta** não forem automaticamente polerados, preencha os valores manualmente de acordo com a sua exigência.

      Na caixa de texto **de URL sign-on,** escreva um URL utilizando o seguinte padrão:`https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > O valor não é real. Atualize o valor com o URL de Sign-On real. Contacte a equipa de suporte do Cliente da Consola de [Infraestruturas Oracle Cloud](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) para obter o valor. Também pode consultar os padrões mostrados na secção **de Configuração SAML Básica** no portal Azure.

1. Na configuração de um único sign-on com a página **SAML,** na secção certificado de **assinatura SAML,** encontre **metadados da Federação XML** e selecione **Descarregar** para descarregar o certificado e guardá-lo no seu computador.

   ![O link de descarregamento do Certificado](common/metadataxml.png)

1. A aplicação Oracle Cloud Infrastructure Console espera as afirmações do SAML num formato específico, o que requer que adicione mapeamentos personalizados de atributos à configuração de atributos de token SAML. A imagem que se segue mostra a lista de atributos predefinidos. Clique no ícone **Editar** para abrir o diálogo de Atributos do Utilizador.

   ![image](common/edit-attribute.png)

1. Além de acima, a aplicação Oracle Cloud Infrastructure Console espera que poucos atributos sejam retransmitidos na resposta sAML. Na secção **de Atributos do Utilizador & Reclamações** no diálogo de reclamações do **grupo (pré-visualização),** execute os seguintes passos:

   1. Clique na **caneta** ao lado do valor do **identificador nome**.

   1. Selecione **Persistente** como **Escolher formato identificador**de nome .
 
   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Clique na **caneta** ao lado dos **Grupos devolvidos por reclamação**.

   1. Selecione **grupos de segurança** da lista de rádio.

   1. Selecione **Atributo fonte** do ID do **grupo**.

   1. Verifique **Personalizar o nome da reivindicação do grupo.**

   1. Na caixa de texto **Name,** digite **o nome do grupo**.

   1. Na caixa de texto **Namespace (opcional),** escreva `https://auth.oraclecloud.com/saml/claims`.

   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Na secção De configurar a **Oracle Cloud Infrastructure Console,** copie os URL(s) apropriados com base no seu requisito.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)



### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste Azure AD

Nesta secção, você vai criar um utilizador de teste no portal Azure chamado B. Simon.

1. A partir do painel esquerdo no portal Azure, **selecione Azure Ative Directory**, selecione **Utilizadores**e, em seguida, selecione **Todos os utilizadores**.
1. Selecione **Novo utilizador** na parte superior do ecrã.
1. Nas propriedades do **Utilizador,** siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. No campo de nome username@companydomain.extensiondo **Utilizador,** introduza o . Por exemplo, `B. Simon@contoso.com`.
   1. Selecione a caixa de verificação de **palavra-passe do Show** e, em seguida, escreva o valor que está apresentado na caixa **password.**
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o utilizador de teste Azure AD

Nesta secção, permitirá que B. Simon utilize um único sign-on Azure, concedendo acesso à Consola de Infraestruturas Oracle Cloud.

1. No portal Azure, selecione **Aplicações Empresariais,** e, em seguida, selecione **Todas as aplicações**.
1. Na lista de aplicações, selecione **Oracle Cloud Infrastructure Console**.
1. Na página geral da aplicação, encontre a secção **Gerir** e selecione **Utilizadores e grupos**.

   ![O link "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **Adicionar utilizador**e, em seguida, selecione **Utilizadores e grupos** no diálogo **'Atribuição adicionar'.**

   ![Ligação Adicionar Utilizador](common/add-assign-user.png)

1. No diálogo **de Utilizadores e grupos,** selecione **B. Simon** na lista de Utilizadores e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. Se estiver à espera de algum valor de papel na afirmação do SAML, no diálogo **Select Role,** selecione a função adequada para o utilizador da lista e, em seguida, clique no botão **Select** na parte inferior do ecrã.
1. No diálogo **Adicionar Atribuição,** clique no botão **Atribuir.**

## <a name="configure-oracle-cloud-infrastructure-console"></a>Configure consola de infraestrutura de nuvem oracle

1. Numa janela de navegador web diferente, inscreva-se na Consola de Infraestruturas Oracle Cloud como Administrador.

1. Clique no lado esquerdo do menu e clique em **Identidade** e depois navegue para a **Federação**.

   ![Configuração](./media/oracle-cloud-tutorial/config01.png)

1. Guarde o ficheiro de metadados do Fornecedor de **Serviços** clicando no **link de descarregue este link de documento** e carregue-o na secção básica de **configuração SAML** do portal Azure e, em seguida, clique em **Adicionar Fornecedor**de Identidade .

   ![Configuração](./media/oracle-cloud-tutorial/config02.png)

1. No pop-up do Fornecedor de **Identidade Adicionar,** execute os seguintes passos:

   ![Configuração](./media/oracle-cloud-tutorial/config03.png)

   1. Na caixa de texto **NAME,** insira o seu nome.

   1. Na caixa de texto **DESCRIÇÃO,** introduza a sua descrição.

   1. Selecione **MICROSOFT ATIVE DIRECTORY FEDERATION SERVICE (ADFS) OR SAML 2.0 COMPLIANT IDENTITY PROVIDER** como **TIPO**.

   1. Clique em **Navegar** para carregar os Metadados da Federação XML, que descarregou do portal Azure.

   1. Clique em **Continuar** e na secção **Editar Fornecedor** de Identidade executar os seguintes passos:

      ![Configuração](./media/oracle-cloud-tutorial/config09.png)

   1. O **GRUPO DE FORNECEDOR DE IDENTIDADE** deve ser selecionado como Grupo Personalizado. O GROUP ID deve ser o GUIA do grupo do Azure Ative Directory. O grupo precisa de ser mapeado com o grupo correspondente no campo **do Grupo OCI.**

   1. Pode mapear vários grupos de acordo com a sua configuração no portal Azure e a sua organização necessita. Clique em **+ Adicione mapeamento** para adicionar os grupos que precisar.

   1. Clique em **Submeter**.
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Criar o utilizador de teste de consola de infraestrutura oracle Cloud

 A Consola de Infraestruturas Oracle Cloud suporta o fornecimento just-in-time, que é por padrão. Não há nenhum item de ação para si nesta secção. Um novo utilizador não é criado durante uma tentativa de acesso e também não é necessário criar o utilizador.

### <a name="test-sso"></a>Teste SSO

Quando selecionar o azulejo da Consola de Infraestruturas Oracle Cloud no Painel de Acesso, será redirecionado para o sinal da Consola de Infraestruturas Oracle Cloud na página. Selecione o **FORNECEDOR DE IDENTIDADE** do menu suspenso e clique em **Continuar** como mostrado abaixo para iniciar sessão. Para mais informações sobre o Painel de Acesso, consulte [introdução ao Painel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)de Acesso .

![Configuração](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o Acesso Condicional no Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Como proteger a Consola de Infraestruturas Oracle Cloud com visibilidade e controlos avançados](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
