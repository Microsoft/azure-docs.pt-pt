---
title: 'Tutorial: Integração do Active Directory do Azure com o Zscaler privada acesso (ZPA) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o acesso privado Zscaler (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984498a2b9d4d72ee6bb6b9f0a9e62636bf870bf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226442"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Tutorial: Integrar o Zscaler privada acesso (ZPA) com o Azure Active Directory

Neste tutorial, irá aprender como integrar o acesso privado Zscaler (ZPA) com o Azure Active Directory (Azure AD). Quando integrar o acesso privado Zscaler (ZPA) com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso para Zscaler privada acesso (ZPA).
* Permita que os utilizadores ser automaticamente sessão iniciada para Zscaler privada acesso (ZPA) com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Acesso privado Zscaler (ZPA) início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Acesso privado Zscaler (ZPA) suporta **SP** iniciada SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Adicionando o acesso privado Zscaler (ZPA) da Galeria

Para configurar a integração do Zscaler privada acesso (ZPA) para o Azure AD, terá de adicionar acesso privado Zscaler (ZPA) a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Zscaler privada acesso (ZPA)** na caixa de pesquisa.
1. Selecione **Zscaler privada acesso (ZPA)** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o Zscaler privada acesso (ZPA) com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Zscaler privada acesso (ZPA).

Para configurar e testar o SSO do Azure AD com o Zscaler privada acesso (ZPA), conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o acesso privado Zscaler (ZPA)](#configure-zscaler-private-access-zpa)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de acesso privado Zscaler (ZPA)](#create-zscaler-private-access-zpa-test-user)**  ter um equivalente da Eduarda Almeida na Zscaler privada acesso (ZPA) que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Zscaler privada acesso (ZPA)** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    1. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Na **identificador (ID de entidade)** caixa de texto, escreva um URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > O **iniciar sessão no URL** valor não é real. Atualize o valor com o início de sessão real no URL. Contacte [equipa de suporte de cliente de acesso privado Zscaler (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para obter o valor. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **XML de metadados de Federação** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/metadataxml.png)

1. Sobre o **definir a cópia de segurança Zscaler privada acesso (ZPA)** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Configurar o acesso privado do Zscaler (ZPA)

1. Para automatizar a configuração no Zscaler privada acesso (ZPA), tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **configuração Zscaler privada acesso (ZPA)** irá direcioná-lo para a aplicação de acesso privado Zscaler (ZPA). A partir daí, forneça as credenciais de administrador para iniciar sessão em Zscaler privada acesso (ZPA). A extensão do browser irá configurar o aplicativo para e automatizar passos 3 a 6 automaticamente.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o acesso privado Zscaler (ZPA), abra uma nova janela de browser e inicie sessão no site da sua empresa Zscaler acesso (ZPA privada) como administrador e execute os seguintes passos:

4. Do lado esquerdo do menu, clique em **Administration** e navegue até à **autenticação** secção clique **configuração do IdP**.

    ![Administração de administrador de acesso privado Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. No canto superior direito, clique em **adicionar a configuração do IdP**. 

    ![Idp de administrador de acesso privado Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Sobre o **adicionar a configuração do IdP** página execute os seguintes passos:
 
    ![Selecione de administrador de acesso privado Zscaler](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Clique em **selecionar ficheiro** para carregar o ficheiro de metadados baixado do Azure AD no **carregamento de ficheiros de metadados do IdP** campo.

    b. Ele lê a **metadados de IdP** do Azure AD e preenche todas as informações de campos, conforme mostrado abaixo.

    ![Configuração de administrador de acesso privado Zscaler](./media/zscalerprivateaccess-tutorial/config.png)

    c. Selecione o seu domínio a partir **domínios** campo.
    
    d. Clique em **Guardar**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para Zscaler privada acesso (ZPA).

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Zscaler privada acesso (ZPA)** .
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Criar utilizador de teste de acesso privado Zscaler (ZPA)

Nesta secção, vai criar um usuário chamado Eduarda Almeida na Zscaler privada acesso (ZPA). Trabalhe em conjunto com [equipa de suporte de acesso privado Zscaler (ZPA)](https://help.zscaler.com/zpa-submit-ticket) para adicionar os utilizadores na plataforma do Zscaler privada acesso (ZPA).

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico de acesso privado Zscaler (ZPA) no painel de acesso, deve ser automaticamente conectado para o Zscaler privada acesso (ZPA) para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)