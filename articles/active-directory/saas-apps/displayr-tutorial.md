---
title: 'Tutorial: Integração do Active Directory do Azure com Displayr | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Displayr.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7a2d793a1fbd68d6a71f48b556a77ddcaaaf111
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66112159"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Tutorial: Integrar Displayr com o Azure Active Directory

Neste tutorial, irá aprender como integrar Displayr com o Azure Active Directory (Azure AD). Quando integrar Displayr com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Displayr.
* Permita que os utilizadores ser automaticamente sessão iniciada para Displayr com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter um [conta gratuita](https://azure.microsoft.com/free/).
* Displayr início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta Displayr **SP** iniciada SSO.

## <a name="adding-displayr-from-the-gallery"></a>Adicionando Displayr da Galeria

Para configurar a integração do Displayr com o Azure AD, terá de adicionar Displayr a partir da Galeria à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Displayr** na caixa de pesquisa.
1. Selecione **Displayr** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com Displayr com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Displayr.

Para configurar e testar o SSO do Azure AD com Displayr, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar Displayr](#configure-displayr)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste Displayr](#create-displayr-test-user)**  ter um equivalente da Eduarda Almeida na Displayr que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Displayr** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **configuração de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** secção, executar o passo seguinte:

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<YOURDOMAIN>.displayr.com`

    b. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador. Contacte [equipa de suporte de cliente Displayr](mailto:support@displayr.com) obter esses valores. Também pode consultar os padrões mostrados na seção de configuração de SAML básica no portal do Azure.

1. Na **configuração de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Aplicação de Displayr espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos. Clique em **edite** ícone para abrir a caixa de diálogo de atributos do utilizador.

    ![image](common/edit-attribute.png)

1. Além dos acima, o aplicativo Displayr espera mais alguns atributos a serem passados na resposta SAML. No **atributos de utilizador e afirmações** secção sobre o **afirmações de grupo (pré-visualização)** caixa de diálogo, execute os seguintes passos:

    a. Clique nas **caneta** junto a **grupos devolvido na afirmação**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Selecione **todos os grupos** na lista de rádio.

    c. Selecione **atributo de origem** dos **ID de grupo**.

    d. Verifique **personalizar o nome da afirmação de grupo**.

    e. Verifique **emitir grupos como declarações de função**.

    f. Clique em **Guardar**.

1. Sobre o **Displayr configuração** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Configurar Displayr

1. Para automatizar a configuração no Displayr, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **Displayr configuração** irá direcioná-lo para o aplicativo Displayr. A partir daí, forneça as credenciais de administrador a iniciar sessão em Displayr. A extensão do browser irá configurar o aplicativo para e automatizar passos 3 a 6 automaticamente.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se desejar configurar manualmente o Displayr, abra uma nova janela de browser e inicie sessão no site da sua empresa Displayr como administrador e execute os seguintes passos:

4. Clique em **configurações** , em seguida, navegue até à **conta**.

    ![Configuração](./media/displayr-tutorial/config01.png)

5. Mude para o **configurações** no menu superior e desloque-se para baixo na página clicando **configurar único início de sessão (SAML)**.

    ![Configuração](./media/displayr-tutorial/config02.png)

6. Sobre o **único início de sessão (SAML)** página, execute os seguintes passos:

    ![Configuração](./media/displayr-tutorial/config03.png)

    a. Verifique os **ativar único início de sessão (SAML)** caixa.

    b. Copiar real **identificador** partir do **configuração básica de SAML** secção do Azure AD e cole-o para o **emissor** caixa de texto.

    c. Na **URL de início de sessão** texto caixa, cole o valor de **URL de início de sessão**, que copiou do portal do Azure.

    d. Na **URL de fim de sessão** texto caixa, cole o valor de **URL de fim de sessão**, que copiou do portal do Azure.

    e. Abrir o certificado (Base64) no bloco de notas, copiar o conteúdo e cole-o para o **certificado** caixa de texto.

    f. **Mapeamentos de grupo** são opcionais.

    g. Clique em **Guardar**.  

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

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Displayr.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Displayr**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-displayr-test-user"></a>Criar utilizador de teste Displayr

Para ativar os utilizadores do Azure AD, inicie sessão para Displayr, eles têm de ser aprovisionados em Displayr. Displayr, aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no Displayr como administrador.

2. Clique em **configurações** , em seguida, navegue até à **conta**.

    ![Configuração de Displayr](./media/displayr-tutorial/config01.png)

3. Mude para o **definições** no menu superior e desloque-se para baixo na página, até **utilizadores** secção, em seguida, clique em **novo utilizador**.

    ![Configuração de Displayr](./media/displayr-tutorial/config07.png)

4. Sobre o **novo utilizador** página, execute os seguintes passos:

    ![Configuração de Displayr](./media/displayr-tutorial/config06.png)

    a. Na **Name** texto, introduza o nome de utilizador, como **Brittasimon**.

    b. Na **E-Mail** texto, introduza o e-mail do utilizador, como `Brittasimon@contoso.com`.

    c. Selecione seu apropriado **associação de grupo**.

    d. Clique em **Guardar**.

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Displayr no painel de acesso, deve ser automaticamente sessão iniciada no Displayr para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)