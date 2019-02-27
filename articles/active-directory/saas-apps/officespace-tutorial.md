---
title: 'Tutorial: Integração do Active Directory do Azure com o Software de OfficeSpace | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o Software de OfficeSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b791a2c42bad636a779ead75e73b978fbc4b7bf9
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872799"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Tutorial: Integração do Active Directory do Azure com o Software de OfficeSpace

Neste tutorial, saiba como integrar o Software de OfficeSpace com o Azure Active Directory (Azure AD).

Integrar o Software de OfficeSpace no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao OfficeSpace Software.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para o Software de OfficeSpace (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Software de OfficeSpace, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Software de OfficeSpace logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar OfficeSpace Software a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-officespace-software-from-the-gallery"></a>Adicionar OfficeSpace Software a partir da Galeria
Para configurar a integração de OfficeSpace Software com o Azure AD, terá de adicionar OfficeSpace Software a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar OfficeSpace Software a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **OfficeSpace Software**, selecione **OfficeSpace Software** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Software de OfficeSpace na lista de resultados](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Software de OfficeSpace com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no OfficeSpace Software a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no OfficeSpace Software deve ser estabelecido.

Em OfficeSpace Software, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com o Software de OfficeSpace, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de OfficeSpace Software](#create-a-officespace-software-test-user)**  - para ter um equivalente da Eduarda Almeida no OfficeSpace Software, que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo de OfficeSpace Software.

**Para configurar o Azure AD início de sessão único com o Software de OfficeSpace, execute os seguintes passos:**

1. No portal do Azure, sobre o **OfficeSpace Software** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

1. Sobre o **OfficeSpace Software domínio e URLs** secção, execute os seguintes passos:

    ![OfficeSpace Software domínio e URLs únicas início de sessão em informações](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente de Software OfficeSpace](mailto:support@officespacesoftware.com) obter esses valores. 

1. Aplicação de OfficeSpace Software espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos da "**atributos de utilizador**" secção na página de integração de aplicações. Captura de ecrã seguinte mostra um exemplo disso.
    
    ![Configurar o atributo](./media/officespace-tutorial/tutorial_officespace_attribute.png)

1. Na **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, selecione **user.mail** como **identificador de utilizador** e para cada linha, mostrado na a tabela a seguir, execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo |
    | --- | --- |    
    | e-mail | user.mail |
    | nome | user.displayname |
    | first_name | user.givenname |
    | last_name | user.surname |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar a adicionar](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Configurar o atributo](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.
    
    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **Ok**
 
1. Sobre o **certificado de assinatura SAML** secção, copie a **THUMBPRINT** valor do certificado.

    ![O link de download de certificado](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/officespace-tutorial/tutorial_general_400.png)

1. Na **configuração do Software de OfficeSpace** secção, clique em **configurar OfficeSpace Software** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão e SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de OfficeSpace Software](./media/officespace-tutorial/tutorial_officespace_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no seu inquilino OfficeSpace Software como um administrador.

1. Aceda a **configurações** e clique em **conectores**.

    ![Configurar o início de sessão único no lado de aplicação](./media/officespace-tutorial/tutorial_officespace_002.png)

1. Clique em **autenticação SAML**.

    ![Configurar o início de sessão único no lado de aplicação](./media/officespace-tutorial/tutorial_officespace_003.png)

1. Na **autenticação SAML** secção, execute os seguintes passos:

    ![Configurar o início de sessão único no lado de aplicação](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Na **url de fim de sessão do fornecedor** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    b. Na **url de destino do cliente idp** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.

    c. Colar o **Thumbprint** valor que copiou do portal do Azure, para o **impressão digital do certificado de cliente IDP** caixa de texto. 

    d. Clique em **guardar as definições de**.


> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/officespace-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/officespace-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/officespace-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/officespace-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-officespace-software-test-user"></a>Criar um utilizador de teste de OfficeSpace Software

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no OfficeSpace Software. Software de OfficeSpace suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Não existe nenhum item de ação para nesta secção. Durante uma tentativa de aceder OfficeSpace Software se não existir ainda será criado um novo utilizador.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contacto [equipa de suporte do Software de OfficeSpace](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao OfficeSpace Software.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a OfficeSpace Software, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **OfficeSpace Software**.

    ![A ligação de OfficeSpace Software na lista de aplicações](./media/officespace-tutorial/tutorial_officespace_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de OfficeSpace Software no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de OfficeSpace Software.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

