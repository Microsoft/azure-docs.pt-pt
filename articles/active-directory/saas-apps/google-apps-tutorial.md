---
title: 'Tutorial: Integração do Active Directory do Azure com o G Suite | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78e395db2f8ccaf4fbe9fb64b0faf9ac70c1b21c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839039"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Tutorial: Integração do Active Directory do Azure com o G Suite

Neste tutorial, saiba como integrar o G Suite no Azure Active Directory (Azure AD).
Integrar o G Suite no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao G Suite.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada ao G Suite (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com G Suite, terá dos seguintes itens:

- Uma subscrição do Azure
- Um G Suite logon único habilitado subscrição
- Uma subscrição do Google Apps ou a subscrição de Google Cloud Platform.

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção. Este documento foi criado com o utilizador novo experiência de início de sessão único. Se ainda estiver a utilizar o antigo, a configuração terá uma aparência diferente. Pode ativar a nova experiência nas definições de início de sessão único da aplicação do G Suite. Aceda a **do Azure AD, aplicações empresariais**, selecione **G Suite**, selecione **início de sessão único** e, em seguida, clique em **Experimente a nossa nova experiência**.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

1. **P: Esta integração suporta a integração de Google Cloud Platform SSO com o Azure AD?**

    R: Sim. Google Cloud Platform e o Google Apps compartilham a mesma plataforma de autenticação. Portanto, para fazer a integração de GCP terá de configurar o SSO com o Google Apps.

2. **P: São Chromebooks e outros dispositivos Chrome compatíveis com o Azure AD início de sessão único?**
  
    R: Sim, os utilizadores conseguem iniciar sessão nos respetivos dispositivos Chromebook através das respetivas credenciais do Azure AD. Ver isso [artigo de suporte do G Suite](https://support.google.com/chrome/a/answer/6060880) para obter informações sobre por que os utilizadores podem recebe o pedido de credenciais duas vezes.

3. **P: Se eu ativar o início de sessão único, os utilizadores poderão utilizar as credenciais do Azure AD para iniciar sessão em qualquer produto do Google, tais como a sala de aulas do Google, GMail, Google Drive, YouTube e assim por diante?**

    R: Sim, dependendo no [quais G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) optar por ativar ou desativar para a sua organização.

4. **P: Pode ativar início de sessão único para apenas um subconjunto dos meus utilizadores do G Suite?**

    R: Não, ativando o início de sessão único imediatamente requer que todos os seus utilizadores do G Suite autenticar com as credenciais do Azure AD. Porque o G Suite não suporta a ter vários fornecedores de identidade, o fornecedor de identidade para o seu ambiente do G Suite pode ser do Azure AD ou Google – mas não ambos simultaneamente.

5. **P: Se um usuário está conectado por meio do Windows, são autenticam automaticamente ao G Suite sem introdução pedida uma palavra-passe?**

    R: Existem duas opções para ativar este cenário. Em primeiro lugar, os utilizadores podem iniciar sessão em dispositivos Windows 10 através da [Azure Active Directory Join](../device-management-introduction.md). Em alternativa, os utilizadores foi possível iniciar sessão em dispositivos Windows que estão associados a um domínio para um diretório de Active Directory no local que tenha sido ativada para início de sessão único para o Azure AD através de um [serviços de Federação do Active Directory (AD FS)](../hybrid/plan-connect-user-signin.md) implementação. Ambas as opções exigem que execute os passos no tutorial seguinte para ativar o início de sessão único entre o Azure AD e G Suite.

6. **P: O que devo fazer quando receber uma mensagem de erro "email inválido"?**

    R: Para esta configuração, o atributo de correio eletrónico é necessário para os utilizadores ser capazes de início de sessão. Este atributo não pode ser definido manualmente.

    O atributo de correio eletrónico é preenchido automaticamente para qualquer utilizador com uma licença válida do Exchange. Se o utilizador não está ativada por e-mail, este erro será recebido como a aplicação tem de obter este atributo para dar acesso.

    Pode ir para portal.office.com com uma conta de administrador, em seguida, clique no Centro de administração, faturação, as subscrições, selecione a subscrição do Office 365 e, em seguida, clique em atribuir para utilizadores, selecione os utilizadores que pretende verificar a sua subscrição e no painel da direita, clique em Edite licenças.

    Assim que é atribuída a licença do Office 365, poderá demorar alguns minutos a ser aplicado. Depois disso, o atributo user.mail será preenchido automaticamente e o problema deve ser resolvido.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* G Suite suporta **SP** iniciada SSO
* G Suite suporta  **[aprovisionamento automático de utilizadores](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Adicionando o G Suite da Galeria

Para configurar a integração do G Suite para o Azure AD, terá de adicionar G Suite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o G Suite da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **G Suite**, selecione **G Suite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com G Suite com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no G Suite tem de ser estabelecida.

Para configurar e testar o Azure AD início de sessão único com o G Suite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o G Suite início de sessão único](#configure-g-suite-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do G Suite](#create-g-suite-test-user)**  - para ter um equivalente da Eduarda Almeida no G Suite, que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o G Suite, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **G Suite** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, se pretende configurar para o **Gmail** execute os seguintes passos:

    ![URLs de domínio do G Suite e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente do G Suite](https://www.google.com/contact/) obter esses valores.

5. Sobre o **configuração básica de SAML** secção, se pretende configurar para o **Google Cloud Platform** execute os seguintes passos:

    ![URLs de domínio do G Suite e únicas início de sessão em informações](common/sp-identifier.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com `

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente do G Suite](https://www.google.com/contact/) obter esses valores.

6. A aplicação do G Suite espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra um exemplo disso. O valor predefinido **identificador de utilizador exclusivo** é **user.userprincipalname** mas G Suite espera que isso seja mapeado com o endereço de e-mail do utilizador. Para que pode usar **user.mail** atributo da lista ou utilize o valor do atributo adequado com base na configuração da sua organização.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name | Atributo de origem |
    | ---------------| --------------- |
    | Identificador de Utilizador Exclusivo | User.mail |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **certificado (Base64)** entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/certificatebase64.png)

9. Sobre o **configurar o G Suite** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-g-suite-single-sign-on"></a>Configurar o G Suite início de sessão único

1. Abra um novo separador no seu browser e inicie sessão no [consola de administração do G Suite](https://admin.google.com/) com a sua conta de administrador.

2. Clique em **segurança**. Se não vir a ligação, podem estar ocultos sob o **More Controls** menu na parte inferior do ecrã.

    ![Clique em Security.][10]

3. Sobre o **Security** página, clique em **configurar início de sessão único (SSO).**

    ![Clique em SSO.][11]

4. Execute as seguintes alterações de configuração:

    ![Configurar o SSO][12]

    a. Selecione **SSO de configuração com o fornecedor de identidade de terceiros**.

    b. Na **URL da página de início de sessão** campo no G Suite, cole o valor de **URL de início de sessão** que copiou do portal do Azure.

    c. Na **URL de página de início de sessão** campo no G Suite, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    d. Na **alterar o URL de palavra-passe** campo no G Suite, cole o valor de **alterar a URL da palavra-passe** que copiou do portal do Azure.

    e. No G Suite, para o **certificado de verificação**, carregue o certificado que transferiu a partir do portal do Azure.

    f. Selecione **utilizar um emissor específico do domínio**.

    g. Clique em **guardar alterações**.

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](common/users.png)

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Novo utilizador botão](common/new-user.png)

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![A caixa de diálogo de utilizador](common/user-properties.png)

    a. Na **Name** campo introduza **BrittaSimon**.
  
    b. Na **nome de utilizador** tipo de campo **brittasimon\@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao G Suite.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **G Suite**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **G Suite**.

    ![A ligação do G Suite na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-g-suite-test-user"></a>Criar utilizador de teste do G Suite

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Software do G Suite. G Suite suporta o aprovisionamento automático, que está por predefinição, ativada. Não existe nenhuma ação para nesta secção. Se um utilizador já não existir no Software do G Suite, uma nova é criada quando tentar acessar o Software do G Suite.

> [!NOTE]
> Certifique-se de que os seus utilizadores são já existe no G Suite se aprovisionamento no Azure AD não foi ativado antes de testar o início de sessão único.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, entre em contato com o [equipa de suporte do Google](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do G Suite no painel de acesso, deve ser automaticamente conectado para o G Suite para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Configurar o aprovisionamento do utilizador](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
