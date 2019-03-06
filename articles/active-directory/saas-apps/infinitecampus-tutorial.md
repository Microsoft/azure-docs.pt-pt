---
title: 'Tutorial: Integração do Active Directory do Azure com o Campus infinito | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e no Campus infinito.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3995b544-e751-4e0f-ab8b-c9a3862da6ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c23f16f5d3c665cb74e9e0460d2e5658fa716d72
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57452866"
---
# <a name="tutorial-azure-active-directory-integration-with-infinite-campus"></a>Tutorial: Integração do Active Directory do Azure com o Campus infinita

Neste tutorial, saiba como integrar o Campus infinita com o Azure Active Directory (Azure AD).

Integrar o Campus infinita com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Campus infinito.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Campus infinito (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com infinito Campus, terá dos seguintes itens:

- Uma subscrição do Azure
- Um infinito Campus logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).
- No mínimo, tem de ser um administrador do Azure Active Directory e ter uma função de segurança de produto no Campus de "estudante informações SIS (sistema)" para concluir a configuração.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Campus infinito da Galeria
2. Configuração e teste do Azure AD início de sessão único

## <a name="adding-infinite-campus-from-the-gallery"></a>Adicionando Campus infinito da Galeria

Para configurar a integração do Campus infinita com o Azure AD, terá de adicionar Campus infinito a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Campus infinito a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

2. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

4. Na caixa de pesquisa, escreva **Campus infinito**, selecione **Campus infinito** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Campus infinito na lista de resultados](./media/infinitecampus-tutorial/tutorial_infinitecampus_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com infinito Campus, com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Campus infinita para um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Campus infinito deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com infinito Campus, terá de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
3. **[Criar um utilizador de teste do Campus infinito](#creating-a-infinite-campus-test-user)**  - para ter um equivalente da Eduarda Almeida no Campus infinito que está ligado à representação de utilizador do Azure AD.
4. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Campus infinito.

**Para configurar o Azure AD início de sessão único com Campus infinito, execute os seguintes passos:**

1. No portal do Azure, sobre o **Campus infinito** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, clique em **selecione** para **SAML** modo para ativar o início de sessão único.

    ![Configurar o início de sessão único](common/tutorial_general_301.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Configurar o início de sessão único](common/editconfigure.png)

4. Na **configuração básica de SAML** secção, se tiver um **ficheiro de metadados do fornecedor de serviços** exportados do Campus infinito, concluir 4.a por meio de 4.d os passos e, em seguida, avance para o passo 11.c. Se não tiver um ficheiro de metadados do fornecedor de serviço, avance para o passo 5.

    a. Clique em **carregamento de ficheiro de metadados**.

        ![image](common/b9_saml.png)

    b. Clique em **logótipo da pasta** para selecionar o ficheiro de metadados e clique em **carregar**.

    ![image](common/b9(1)_saml.png)

    c. Depois do ficheiro de metadados é carregado com êxito, o **identificador** e **URL de resposta** valores ficam automaticamente povoada na **configuração básica de SAML** secção caixa de texto, conforme mostrado abaixo :

    ![image](./media/infinitecampus-tutorial/tutorial_infinitecampus_url.png)

    d. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão (o domínio irão variar consoante o modelo de alojamento): `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

5. Se não tiver **ficheiro de metadados do fornecedor de serviços**, execute os seguintes passos (Observe que o domínio irão variar consoante o modelo de alojamento):

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>/SIS`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/<DISTRICTNAME>`

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<DOMAIN>.infinitecampus.com/campus/SSO/<DISTRICTNAME>`

    ![Infinito Campus domínio e URLs único informações de início de sessão](./media/infinitecampus-tutorial/tutorial_infinitecampus_url1.png)

6. No **certificado de assinatura SAML** página, além do **certificado de assinatura SAML** secção, clique na cópia **ícone** para copiar **Url de metadados de Federação de aplicação**  e cole-o no bloco de notas.

    ![O link de download de certificado](./media/infinitecampus-tutorial/tutorial_infinitecampus_certificate.png) 

7. Sobre o **configurar Campus infinito** secção, utilize os seguintes valores para validar a quando carregar ou utilizar o ficheiro de metadados do Azure/URL.

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

    ![Configuração do Campus infinita](common/configuresection.png)

8. Numa janela do browser web diferente, início de sessão ao Campus infinito como um administrador de segurança.

9. No lado esquerdo do menu, clique em **de administração do sistema**.

    ![O administrador](./media/infinitecampus-tutorial/tutorial_infinitecampus_admin.png)

10. Navegue para **segurança de usuário** > **gestão de SAML** > **configuração do fornecedor de serviço SSO**.

    ![O saml](./media/infinitecampus-tutorial/tutorial_infinitecampus_saml.png)

11. Sobre o **configuração do fornecedor de serviço de SSO** página, execute os seguintes passos:

    ![O sso](./media/infinitecampus-tutorial/tutorial_infinitecampus_sso.png)

    a. Selecione **ativar SAML início de sessão único**.
    
    b. Editar a **nome de atributo opcional** para conter **nome**
    
    c. Na **Selecione uma opção para recuperar dados de servidor do fornecedor de identidade (IDP)** secção, selecione **URL de metadados**, cole o **Url de metadados de Federação de aplicação** (a partir do passo 6 acima) em a caixa e, em seguida, clique **sincronização**.

    d. Clique em **metadados do fornecedor de serviço** link para salvar o **ficheiro de metadados do fornecedor de serviços** no seu computador e carregá-la no **configuração básica de SAML** secção automaticamente preencher o **identificador** e **URL de resposta** valores no portal do Azure (consulte a etapa 4 para o upload e a população automática de valores ou o passo 5 para entrada manual).

    e. Depois de clicar em **sincronização** os valores ficam preenchidos automaticamente na **configuração do fornecedor de serviço de SSO** página.

    f. Clique em **Guardar**.

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção consiste em criar uma _único_ utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No portal do Azure, no painel esquerdo, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.

    ![Criar utilizador do Azure AD][100]

2. Selecione **novo utilizador** na parte superior do ecrã.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_01.png) 

3. Nas propriedades do utilizador, execute os seguintes passos.

    ![Criar um utilizador de teste do Azure AD](common/create_aaduser_02.png)

    a. Na **Name** , insira **BrittaSimon**.
  
    b. Na **nome de utilizador** , digite **brittasimon@yourcompanydomain.extension**  
    Por exemplo, BrittaSimon@contoso.com

    c. Selecione **propriedades**, selecione a **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na caixa de palavra-passe.

    d. Selecione **Criar**.

### <a name="creating-a-infinite-campus-test-user"></a>Criar um utilizador de teste do Campus infinita

Campus infinito tem uma arquitetura de dados demográficos centralizados. Entre em contato com [equipa de suporte do Campus infinito](mailto:sales@infinitecampus.com) para adicionar os utilizadores na plataforma do Campus infinito.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Campus infinito.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**.

    ![Atribuir utilizador][201]

2. Na lista de aplicações, selecione **Campus infinito**.

    ![Configurar o início de sessão único](./media/infinitecampus-tutorial/tutorial_infinitecampus_app.png) 

3. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202]

4. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Na **adicionar atribuição** caixa de diálogo select a **atribuir** botão.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico do Campus infinito no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação do Campus infinito. Se está a iniciar sessão na aplicação Campus infinito no mesmo browser que está a administrar o Azure AD, certifique-se de que esteja conectado ao Azure AD como o utilizador de teste. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
