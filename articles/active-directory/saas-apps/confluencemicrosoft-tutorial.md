---
title: 'Tutorial: Integração do Active Directory do Azure com confluência SAML SSO pela Microsoft | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e confluência SAML SSO pela Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04f42d2a17c8694a59e388d090a48ea45840979
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868175"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Integração do Active Directory do Azure com confluência SAML SSO pela Microsoft

Neste tutorial, saiba como integrar confluência SAML SSO pela Microsoft no Azure Active Directory (Azure AD).
Integrar confluência SAML SSO pela Microsoft no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao confluência SAML SSO pela Microsoft.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para confluência SAML SSO pela Microsoft (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="description"></a>Descrição:

Utilize a sua conta do Microsoft Azure Active Directory com o servidor de confluência Atlassian para ativar o início de sessão único. Desta forma todos os seus utilizadores da organização podem utilizar as credenciais do Azure AD ao iniciar sessão na aplicação confluência. Este plug-in utiliza SAML 2.0 para a Federação.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com confluência SAML SSO pela Microsoft, precisa do seguinte:

- Uma subscrição do Azure
- Aplicação de servidor confluência instalada num servidor Windows de 64 bits (no local ou na cloud em infraestrutura IaaS)
- Servidor de confluência é ativadas por HTTPS
- Tenha em atenção de que as versões suportadas para o plug-in do confluência mencionadas abaixo de secção.
- Servidor de confluência está acessível na internet, especialmente para a página de início de sessão do Azure AD para autenticação e deve capaz de receber o token do Azure AD
- Credenciais de administrador são configuradas no confluência
- WebSudo está desativada na confluência
- Utilizador de teste criado no aplicativo de servidor confluência

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção de confluência. Teste a integração primeiro no desenvolvimento ou de ambiente de teste do aplicativo e, em seguida, utilize o ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês aqui: [Oferta de avaliação](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Versões suportadas do confluência

A partir de agora, os seguintes versões do confluência são suportadas:

- Confluência: 5.0 para 5.10
- Confluência: 6.0.1
- Confluência: 6.1.1
- Confluência: 6.2.1
- Confluência: 6.3.4
- Confluência: 6.4.0
- Confluência: 6.5.0
- Confluência: 6.6.2
- Confluência: 6.7.0
- Confluência: 6.8.1
- Confluência: 6.9.0
- Confluência: 6.10.0
- Confluência: 6.11.0
- Confluência: 6.12.0

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta confluência SAML SSO pela Microsoft **SP** iniciada SSO

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Adicionar confluência SAML SSO pela Microsoft a partir da Galeria

Para configurar a integração do confluência SAML SSO pela Microsoft para o Azure AD, terá de adicionar confluência SAML SSO pela Microsoft a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar confluência SAML SSO pela Microsoft a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **confluência SAML SSO pela Microsoft**, selecione **confluência SAML SSO pela Microsoft** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Confluência SAML SSO pela Microsoft na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com confluência SAML SSO pela Microsoft com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na confluência SAML SSO pela Microsoft deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com confluência SAML SSO pela Microsoft, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar confluência SAML SSO pelo Microsoft início de sessão único](#configure-confluence-saml-sso-by-microsoft-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar confluência SAML SSO por utilizador de teste da Microsoft](#create-confluence-saml-sso-by-microsoft-test-user)**  - para ter um equivalente da Eduarda Almeida na confluência SAML SSO pela Microsoft que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com confluência SAML SSO pela Microsoft, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **confluência SAML SSO pela Microsoft** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, execute os seguintes passos:

    ![Confluência SAML SSO pelo Microsoft Domain e URLs únicas início de sessão em informações](common/sp-identifier-reply.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Na **identificador** caixa, escreva um URL com o seguinte padrão: `https://<domain:port>/`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. A porta é opcional, caso seja um URL com nome. Estes valores são recebidos durante a configuração de confluência Plug-in do, que é explicado mais tarde no tutorial.

4. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, clique em botão Copiar para copiar **Url de metadados de Federação de aplicação** e guarde-o no seu computador.

    ![O link de download de certificado](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Configurar confluência SAML SSO pelo Microsoft Single Sign-On

1. Numa janela do browser web diferente, inicie sessão na sua instância de confluência como administrador.

2. Paire o rato sobre o ícone de roda dentada e clique nas **suplementos**.

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon1.png)

3. Transfira o plug-in do [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Carregar manualmente o plug-in fornecido pela Microsoft usando **carregar o suplemento** menu. A transferência de plug-in é coberta [contrato de serviço do Microsoft](https://www.microsoft.com/servicesagreement/).

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon12.png)

4. Assim que o plug-in estiver instalado, ele aparece na **utilizador instalado** secção de suplementos do **suplemento gerir** secção. Clique em **configurar** para configurar o plug-in de novo.

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon13.png)

5. Execute os seguintes passos na página de configuração:

    ![Configurar o início de sessão único](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Certifique-se de que existe apenas um certificado mapeado em relação a aplicação, para que não há nenhum erro na resolução de metadados. Se existirem vários certificados, o administrador obtém um erro ao resolver os metadados.

    a. Na **URL de metadados** caixa de texto, colar **Url de metadados de Federação de aplicação** valor que copiou do portal do Azure e clique nas **resolver** botão. Lê o URL de metadados de IdP e preenche a todas as informações de campos.

    b. Copiar o **identificador, o URL de resposta e o URL de início de sessão** valores e cole-os na **identificador, o URL de resposta e o URL de início de sessão** respectivamente em caixas de texto **configuração básica de SAML** secção no portal do Azure.

    c. Na **nome do botão de início de sessão** escreva o nome do botão, a organização quer que os utilizadores para ver no ecrã de início de sessão.

    d. Na **locais de ID de usuário de SAML**, selecione **ID de utilizador é no elemento NameIdentifier da declaração do requerente** ou **ID de utilizador está num elemento de atributo**.  Este ID deve ser o id de utilizador confluência. Se o id de utilizador não for encontrado, sistema não permitirá que os utilizadores iniciem sessão. 

    > [!Note]
    > Localização de ID de utilizador de SAML predefinida é o identificador de nome. Pode alterá-la para uma opção de atributo e introduza o nome do atributo adequado.
    
    e. Se selecionou **ID de utilizador está num elemento de atributo** opção, em seguida, no **nome do atributo** caixa de texto, escreva o nome do atributo onde é esperado o Id de utilizador. 

    f. Se estiver a utilizar o domínio Federado (como etc. do AD FS) com o Azure AD, em seguida, clique nas **ativar a deteção de Realm inicial** opção e configurar o **nome de domínio**.
    
    g. Na **nome de domínio** escreva o nome de domínio aqui em caso do início de sessão baseado no AD FS.

    h. Verifique **ativar o início de sessão único horizontalmente** se deseja terminar sessão do Azure AD quando um utilizador terminar a sessão de confluência. 

    i. Clique em **guardar** botão para guardar as definições.

    > [!NOTE]
    > Para obter mais informações sobre a instalação e resolução de problemas, visite [Guia do administrador do conector MS confluência SSO](../ms-confluence-jira-plugin-adminguide.md) e também há [FAQ](../ms-confluence-jira-plugin-faq.md) para sua assistência

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD 

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso para confluência SAML SSO pela Microsoft.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **confluência SAML SSO pela Microsoft**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **confluência SAML SSO pela Microsoft**.

    ![O SSO de SAML confluência pela ligação da Microsoft na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Criar confluência SAML SSO por utilizador de teste da Microsoft

Para ativar a utilizadores do Azure AD iniciar sessão no servidor no local de confluência, eles têm de ser aprovisionados na confluência SAML SSO pela Microsoft. Confluência SAML SSO pela Microsoft, o aprovisionamento é uma tarefa manual.

**Para Aprovisionar uma conta de utilizador, execute os seguintes passos:**

1. Inicie sessão no seu servidor no local de confluência como administrador.

2. Paire o rato sobre o ícone de roda dentada e clique nas **gestão de utilizadores**.

    ![Adicionar o funcionário](./media/confluencemicrosoft-tutorial/user1.png) 

3. Na secção utilizadores, clique em **adicionar utilizadores** separador. Sobre o **adicionar um utilizador** caixa de diálogo página, execute os seguintes passos:

    ![Adicionar o funcionário](./media/confluencemicrosoft-tutorial/user2.png) 

    a. Na **nome de utilizador** caixa de texto, escreva a mensagem de e-mail do utilizador, como a Eduarda Almeida.

    b. Na **FullName** caixa de texto, escreva o nome completo do utilizador, como a Eduarda Almeida.

    c. Na **E-Mail** caixa de texto, como o tipo de endereço de e-mail do utilizador Brittasimon@contoso.com.

    d. Na **palavra-passe** caixa de texto, escreva a palavra-passe para Eduarda Almeida.

    e. Clique em **Confirmar palavra-passe** Reintroduza a palavra-passe.

    f. Clique em **adicionar** botão.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar o confluência SAML SSO ao mosaico da Microsoft no painel de acesso, deve ser automaticamente sessão iniciada no SAML SSO a confluência pela Microsoft para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

