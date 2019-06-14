---
title: 'Tutorial: Integração do Active Directory do Azure com o ScaleX Enterprise | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e ScaleX Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64edf2aa47211c1d2a598417a7b2edc00f260075
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60321388"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Tutorial: Integração do Active Directory do Azure com o ScaleX Enterprise

Neste tutorial, saiba como integrar ScaleX Enterprise com o Azure Active Directory (Azure AD).

Integrar ScaleX Enterprise no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao ScaleX Enterprise
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para a empresa de ScaleX (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, consulte o artigo. O que é o acesso a aplicações e início de sessão único com [do Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o ScaleX Enterprise, terá dos seguintes itens:

- Uma subscrição do Azure
- Um empresa ScaleX início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que isso é necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando propriedades ScaleX Enterprise da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Adicionando propriedades ScaleX Enterprise da Galeria
Para configurar a integração do ScaleX Enterprise para o Azure AD, terá de adicionar ScaleX empresarial a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar propriedades ScaleX empresarial a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)** , no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Clique em **adicionar** botão na parte superior da caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **ScaleX Enterprise**.

    ![Criar um utilizador de teste do Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. No painel de resultados, selecione **ScaleX Enterprise**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o ScaleX Enterprise com base num utilizador de teste chamado "Eduarda Almeida."

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador equivalente na empresa ScaleX a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado na empresa ScaleX deve ser estabelecido.

Esta relação de ligação é estabelecida atribuindo o valor do **nome de utilizador** no Azure AD como o valor do **Username** ScaleX empresa.

Para configurar e testar o Azure AD início de sessão único com o ScaleX Enterprise, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de ScaleX Enterprise](#creating-a-scalex-enterprise-test-user)**  - para ter um equivalente da Eduarda Almeida na empresa ScaleX que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação empresarial de ScaleX.

**Para configurar o Azure AD início de sessão único com o ScaleX Enterprise, execute os seguintes passos:**

1. No portal do Azure, sobre o **ScaleX Enterprise** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, como **modo** selecionar **baseado em SAML início de sessão** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. Sobre o **ScaleX Enterprise domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Na **identificador** caixa de texto, digite o valor usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Verifique **Mostrar definições de URL avançadas**, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Na **URL de início de sessão** caixa de texto, digite o valor usando o seguinte padrão: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Essas não são os valores reais. Atualize estes valores com o identificador real, o URL de resposta ou URL de início de sessão. Contacte [equipa de suporte de ScaleX Enterprise Client](https://info.rescale.com/contact_sales) obter esses valores. 

1. A aplicação de ScaleX espera que as asserções SAML num formato específico, o que exige que modificar mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Clique em **ver e editar todos os outros atributos de utilizador** as definições de atributos de caixa de verificação para abrir o personalizado.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Clique com o botão direito do rato no atributo **nome** e clique em eliminar.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Clique em **emailaddress** atributo para abrir a janela de Editar atributo. Altere-o partir **user.mail** ao **user.userprincipalname** e clique em Ok.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. Na **ScaleX Enterprise Configuration** secção, clique em **configurar empresarial de ScaleX** para abrir **configurar início de sessão** janela. Cópia a **ID de entidade de SAML** e **SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. Para configurar o início de sessão único num **ScaleX Enterprise** lado, o início de sessão para o site da empresa de ScaleX Enterprise como administrador.

1. Clique no menu na parte superior direita e selecione **Contoso administração**.

    > [!NOTE] 
    > A Contoso é apenas um exemplo. Isso deve ser o nome real da sua empresa. 

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. Selecione **integrações** no menu superior e selecione **Single Sign-On**.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/admin_sso.png) 

1. Preencha o formulário da seguinte forma:

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Selecione **"Criar qualquer utilizador que pode autenticar com o SSO".**

    b. **Saml do fornecedor de serviços**: Cole o valor ***urn: oasis: nomes: tc: SAML:2.0:nameid-formato: persistente***

    c. **Nome do campo de e-mail do fornecedor de identidade na resposta de ACS**: Cole o valor `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **ID de entidade EntityDescriptor do fornecedor de identidade:** Colar o **ID de entidade de SAML** valor copiado do portal do Azure.

    e. **URL de SingleSignOnService do fornecedor de identidade:** Colar o **SAML único início de sessão no URL do serviço** do portal do Azure.

    f. **Certificado X509 pública do fornecedor de identidade:** Abra o X509 certificado transferido a partir do Azure no bloco de notas e cole o conteúdo nesta caixa. Certifique-se de que há que não quebras de nenhuma linha no meio do conteúdo de certificado.
    
    g. Verifique as caixas de verificação seguintes: **Ativado, encriptar NameID e inicie sessão AuthnRequests.**

    h. Clique em **definições de atualização de SSO** para guardar as definições.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. Aceda a **utilizadores e grupos** e clique em **todos os utilizadores** para apresentar a lista de utilizadores.
    
    ![Criar um utilizador de teste do Azure AD](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. Na parte superior da caixa de diálogo, clique em **Add** para abrir o **utilizador** caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Criar um utilizador de teste de ScaleX Enterprise

Para ativar a utilizadores do Azure AD iniciar sessão no ScaleX Enterprise, eles têm de ser aprovisionados para ScaleX Enterprise. No caso de ScaleX Enterprise, o aprovisionamento é uma tarefa automática e não existem passos manuais são necessários. Qualquer utilizador que consegue autenticar com êxito com credenciais SSO será automaticamente aprovisionada no lado do ScaleX.

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar do Azure-início de sessão único ao conceder acesso de utilizador para ScaleX Enterprise.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida para ScaleX Enterprise, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **ScaleX Enterprise**.

    ![Configurar o início de sessão único](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.

### <a name="testing-single-sign-on"></a>Teste de início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Clique no mosaico propriedades ScaleX Enterprise no painel de acesso, irá obter automaticamente com sessão iniciada para a sua aplicação empresarial de ScaleX. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

