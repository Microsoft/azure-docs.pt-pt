---
title: 'Tutorial: Integração do Active Directory do Azure com o Console de infraestrutura de nuvem de Oracle | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e a consola de infraestrutura de nuvem do Oracle.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 456c984e577e3427ce8cd62d6f63987118f2c8ed
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164156"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Tutorial: Integrar o Console de infraestrutura de nuvem do Oracle com o Azure Active Directory

Neste tutorial, irá aprender como integrar a consola de infraestrutura de nuvem do Oracle no Azure Active Directory (Azure AD). Quando integrar o Console de infraestrutura de Cloud do Oracle no Azure AD, pode:

* Controlar no Azure AD que tenha acesso à consola de infraestrutura de nuvem do Oracle.
* Permita que os utilizadores ser automaticamente sessão iniciada a consola de infraestrutura de nuvem do Oracle com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Oracle Cloud infraestrutura Console início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Consola de infraestrutura de nuvem de Oracle suporta **SP** iniciada SSO.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Adicionando a consola de infraestrutura de nuvem do Oracle da Galeria

Para configurar a integração da consola de infraestrutura de nuvem do Oracle no Azure AD, terá de adicionar consola de infraestrutura de nuvem do Oracle a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Oracle Cloud infraestrutura Console** na caixa de pesquisa.
1. Selecione **consola de infraestrutura de nuvem do Oracle** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Console de infraestrutura de Cloud de Oracle através de um utilizador de teste **B. Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na consola de infraestrutura de nuvem do Oracle.

Para configurar e testar o SSO do Azure AD com o Console de infraestrutura de nuvem do Oracle, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
1. **[Configurar a consola de infraestrutura de nuvem do Oracle](#configure-oracle-cloud-infrastructure-console)**  para configurar as definições de SSO no lado do aplicativo.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Simon B.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Simon B. utilizar o Azure AD início de sessão único.
1. **[Criar utilizador de teste de Console de infraestrutura de nuvem do Oracle](#create-oracle-cloud-infrastructure-console-test-user)**  ter um equivalente de Simon B. na consola de infraestrutura de nuvem Oracle que esteja ligado a representação do Azure AD do utilizador.
1. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Oracle Cloud infraestrutura Console** página de integração de aplicativo, encontrar o **gerir** secção e selecione **único início de sessão**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

   > [!NOTE]
   > Obterá o ficheiro de metadados do fornecedor de serviços do **configurar o Oracle Cloud infraestrutura consola Single Sign-On** secção do tutorial.
    
   1. Clique em **carregamento de ficheiro de metadados**.

   1. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

   1. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção caixa de texto.
    
      > [!NOTE]
      > Se o **identificador** e **URL de resposta** valores não obter polulated automática, em seguida, preencha os valores manualmente de acordo com seus requisitos.

      Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > O valor não é real. Atualize o valor com o URL de início de sessão real. Contacte [equipa de suporte de cliente da consola do Oracle Cloud infraestrutura](https://www.oracle.com/support/advanced-customer-support/products/cloud.html) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Aplicação de consola de infraestrutura de nuvem da Oracle espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

   ![image](common/edit-attribute.png)

1. Além dos acima, a aplicação de consola de infraestrutura de nuvem do Oracle espera mais alguns atributos a serem passados na resposta SAML. No **atributos de utilizador e afirmações** secção sobre o **afirmações de grupo (pré-visualização)**  caixa de diálogo, execute os seguintes passos:

   1. Clique nas **caneta** junto a **o valor do identificador de nome**.

   1. Selecione **persistente** como **formato de identificador de nome de escolha**.
 
   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config07.png)
    
      ![image](./media/oracle-cloud-tutorial/config11.png)

   1. Clique nas **caneta** junto a **grupos devolvido na afirmação**.

   1. Selecione **grupos de segurança** na lista de rádio.

   1. Selecione **atributo de origem** dos **ID de grupo**.

   1. Verifique **personalizar o nome da afirmação de grupo**.

   1. Na **Name** caixa de texto, escreva **groupName**.

   1. Na **(opcional) de espaço de nomes** caixa de texto, escreva `https://auth.oraclecloud.com/saml/claims`.

   1. Clique em **Guardar**.

      ![image](./media/oracle-cloud-tutorial/config08.png)

1. Sobre o **configurar a consola de infraestrutura de nuvem do Oracle** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-oracle-cloud-infrastructure-console"></a>Configurar a consola de infraestrutura de nuvem do Oracle

1. Numa janela do browser web diferente, iniciar sessão na consola de infraestrutura de nuvem do Oracle como administrador.

1. Clique no lado esquerdo do menu e clique em **identidade** , em seguida, navegue até à **Federação**.

   ![Configuração](./media/oracle-cloud-tutorial/config01.png)

1. Guardar o **ficheiro de metadados do fornecedor de serviços** clicando a **baixe este documento** ligar e carregue-o para o **configuração básica de SAML** seção do portal do Azure e, em seguida, Clique em **Add Identity Provider**.

   ![Configuração](./media/oracle-cloud-tutorial/config02.png)

1. Sobre o **Add Identity Provider** pop-up, execute os seguintes passos:

   ![Configuração](./media/oracle-cloud-tutorial/config03.png)

   1. Na **nome** texto, digite seu nome.

   1. Na **Descrição** texto, introduza a sua descrição.

   1. Selecione **serviço de Federação do MICROSOFT ACTIVE DIRECTORY (ADFS) ou o fornecedor de identidade em conformidade com o SAML 2.0** como **tipo**.

   1. Clique em **procurar** para carregar o XML de metadados de Federação, o que transferiu a partir do portal do Azure.

   1. Clique em **continuar** e, no **Editar fornecedor de identidade** secção execute os seguintes passos:

      ![Configuração](./media/oracle-cloud-tutorial/config09.png)

   1. Para **grupo de fornecedor de identidade** campos, introduza o nome do grupo e o ID de grupo que são configuradas no portal do Azure. O grupo precisa ser mapeado com grupo correspondente no **OCI grupo** campo.

   1. Pode mapear vários grupos de acordo com a sua configuração no portal do Azure e a sua organização necessitam. Clique em **+ adicionar mapeamento** para adicionar os grupos conforme necessário.

   1. Clique em **Submit** (Submeter).

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Simon B.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B. Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B. Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Simon B. utilizar o Azure início de sessão único ao conceder acesso à consola de infraestrutura de nuvem do Oracle.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **consola de infraestrutura de nuvem do Oracle**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

   ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B. Simon** a partir da lista de utilizadores, em seguida, clique no **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Criar utilizador de teste de Console de infraestrutura de nuvem do Oracle

 Consola de infraestrutura de nuvem do Oracle suporta o aprovisionamento de just-in-time, que está por predefinição. Não existe nenhum item de ação para nesta secção. Um novo utilizador não é criado durante uma tentativa de acesso e também não é necessário para criar o utilizador.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico da consola de infraestrutura de nuvem do Oracle no painel de acesso, será redirecionado para a página de início de sessão de consola de infraestrutura de nuvem do Oracle. Selecione o **fornecedor de identidade** no menu pendente e clique em **continuar** conforme mostrado abaixo para iniciar sessão. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

![Configuração](./media/oracle-cloud-tutorial/config10.png)

## <a name="additional-resources"></a>Recursos adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
