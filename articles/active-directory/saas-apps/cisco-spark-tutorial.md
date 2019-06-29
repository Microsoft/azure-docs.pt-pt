---
title: 'Tutorial: Integração do Active Directory do Azure com o Cisco Webex | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc537a631cd083da0f902fb4fcd44d47756eeba
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471782"
---
# <a name="tutorial-integrate-cisco-webex-with-azure-active-directory"></a>Tutorial: Integrar o Cisco Webex com o Azure Active Directory

Neste tutorial, irá aprender como integrar o Cisco Webex com o Azure Active Directory (Azure AD). Quando integrar o Cisco Webex com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Cisco Webex.
* Permita que os utilizadores ser automaticamente sessão iniciada para Cisco Webex com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Cisco Webex início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Cisco Webex **SP e IDP** iniciou o SSO e suporta **automatizada** aprovisionamento de utilizadores.

## <a name="adding-cisco-webex-from-the-gallery"></a>Adicionando Cisco Webex da Galeria

Para configurar a integração do Cisco Webex com o Azure AD, terá de adicionar Cisco Webex a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Cisco Webex** na caixa de pesquisa.
1. Selecione **Cisco Webex** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Cisco Webex com um utilizador de teste **B.Simon**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na Cisco Webex.

Para configurar e testar o SSO do Azure AD com o Cisco Webex, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o Cisco Webex](#configure-cisco-webex)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com B.Simon.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar B.Simon utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do Cisco Webex](#create-cisco-webex-test-user)**  para ter um equivalente de B.Simon no Cisco Webex que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Cisco Webex** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Na **configuração básica de SAML** secção, carregue o transferido **metadados do fornecedor de serviços** de ficheiros e configurar a aplicação, efetuando os seguintes passos:

    >[!Note]
    >Obterá o ficheiro de metadados do fornecedor de serviço a partir da **Cisco Webex** Portal. 

    a. Clique em **carregamento de ficheiro de metadados**.

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    c. Após a conclusão com êxito de carregar o ficheiro de metadados do fornecedor de serviços do **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção:

    Na **iniciar sessão no URL** caixa de texto, cole o valor de **URL de resposta**, que obtém autofilled SP pelo carregamento do ficheiro de metadados.

5. Aplicação do Cisco Webex espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **editar** ícone para adicionar os atributos.

    ![image](common/edit-attribute.png)

6. Além disso, anteriormente, a aplicação Cisco Webex espera mais alguns atributos a serem passados na resposta SAML. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, execute os seguintes passos para adicionar o atributo de token de SAML conforme mostrado na tabela a seguir:
    
    | Name |  Atributo de origem|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **configurar Cisco Webex** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-cisco-webex"></a>Configure Cisco Webex

1. Inicie sessão no [gerenciamento de colaboração de nuvem do Cisco](https://admin.ciscospark.com/) com as suas credenciais de administrador completo.

2. Selecione **configurações** e, no **autenticação** secção, clique em **modificar**.

    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Selecione **integrar um fornecedor de identidade 3rd party. (Avançado)**  e vá para a próxima tela.

4. Sobre o **importar metadados de Idp** página, qualquer um dos arrastar e soltar o arquivo de metadados do Azure AD para a página ou utilize a opção de browser de ficheiro para localizar e carregar o ficheiro de metadados do Azure AD. Em seguida, selecione **necessita de certificado assinado por uma autoridade de certificado nos metadados (mais seguro)** e clique em **próxima**.

    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Selecione **Testar ligação de SSO**e quando abre um novo separador do browser, autenticar com o Azure AD ao iniciar sessão.

6. Retorno para o **gerenciamento de colaboração de nuvem do Cisco** separador do browser. Se o teste foi concluída com êxito, selecione **esse teste foi concluída com êxito. Ativar a opção de início de sessão único** e clique em **próxima**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado B.Simon.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `B.Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `B.Simon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar B.Simon utilizar o Azure início de sessão único, concedendo acesso para Cisco Webex.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Cisco Webex**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **B.Simon** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-cisco-webex-test-user"></a>Criar utilizador de teste do Cisco Webex

Nesta secção, vai criar um usuário chamado Eduarda Almeida na Cisco Webex. Nesta secção, vai criar um usuário chamado Eduarda Almeida na Cisco Webex.

1. Vá para o [gerenciamento de colaboração de nuvem do Cisco](https://admin.ciscospark.com/) com as suas credenciais de administrador completo.

2. Clique em **usuários** e, em seguida **gerir utilizadores**.
   
    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Na **gerir utilizadores** janela, selecione **manualmente adicionar ou modificar utilizadores** e clique em **seguinte**.

4. Selecione **nomes e o endereço de E-Mail**. Em seguida, preencha a caixa de texto da seguinte forma:

    ![Configurar o início de sessão único](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. Na **nome próprio** caixa de texto, nome de utilizador, como o tipo **B**.

    b. Na **sobrenome** caixa de texto, tipo último nome de utilizador, como **Simon**.

    c. Na **endereço de E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador b.simon@contoso.com.

5. Clique no sinal para adicionar a Eduarda Almeida. Clique depois em **Seguinte**.

6. Na **adicionar serviços para os utilizadores** janela, clique em **guardar** e, em seguida **concluir**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de Cisco Webex no painel de acesso, deve ser automaticamente conectado para o Cisco Webex para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)