---
title: 'Tutorial: Integração do Azure Active Directory com Lessonly.com | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Lessonly.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 2a4bae196e956d92548944637509b23f78ceb5d2
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44348009"
---
# <a name="tutorial-azure-active-directory-integration-with-lessonlycom"></a>Tutorial: Integração do Azure Active Directory com Lessonly.com

Neste tutorial, saiba como integrar Lessonly.com com o Azure Active Directory (Azure AD).

Integrar Lessonly.com no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao Lessonly.com
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Lessonly.com (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com Lessonly.com, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Lessonly.com logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando Lessonly.com da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-lessonlycom-from-the-gallery"></a>Adicionando Lessonly.com da Galeria
Para configurar a integração do Lessonly.com com o Azure AD, terá de adicionar Lessonly.com a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Lessonly.com a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Lessonly.com**.

    ![Criar um utilizador de teste do Azure AD](./media/lessonly-tutorial/tutorial_lessonly.com_search.png)

1. No painel de resultados, selecione **Lessonly.com**e, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Criar um utilizador de teste do Azure AD](./media/lessonly-tutorial/tutorial_lessonly.com_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Configuração e teste do Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com Lessonly.com com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no Lessonly.com a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Lessonly.com deve ser estabelecido.

Lessonly.com, atribua o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Lessonly.com, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configuring-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#creating-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste Lessonly.com](#creating-a-lessonly-test-user)**  - para ter um equivalente da Eduarda Almeida na Lessonly.com que está ligado à representação de utilizador do Azure AD.
1. **[Atribuir o utilizador de teste do Azure AD](#assigning-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Teste de início de sessão único](#testing-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuração do Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Lessonly.com.

**Para configurar o Azure AD início de sessão único com Lessonly.com, execute os seguintes passos:**

1. No portal do Azure, sobre o **Lessonly.com** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_lessonly.com_samlbase.png)

1. Sobre o **Lessonly.com domínio e URLs** secção, execute os seguintes passos:

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_lessonly.com_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.lessonly.com/signin`|

    >[!NOTE]
    >Quando referenciar um genérico dê um nome que **companyname** tem de ser substituído por um nome real.
    
    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:
    | |
    |--|
    | `https://<companyname>.lessonly.com/auth/saml/metadata`|

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Contacte [equipa de suporte de cliente Lessonly.com](mailto:support@lessonly.com) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **Certificate(Base64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_lessonly.com_certificate.png)

1. O aplicativo Lessonly.com espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizados para sua **atributos de Token SAML** configuração. Captura de ecrã seguinte mostra um exemplo disso.

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_lessonly_06.png)
           
1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem anterior e execute os seguintes passos:

    | Nome do Atributo   | Valor do Atributo |
    | ---------------  | ----------------|
    | urn:oid:2.5.4.42 |User.givenName |
    | urn:oid:2.5.4.4  |User.Surname |
    | urn:oid:0.9.2342.19200300.100.1.3 |User.Mail |
    | urn:oid:1.3.6.1.4.1.5923.1.1.1.10 |User.ObjectId |

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_attribute_05.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.     

1. Clique em **guardar** botão.

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_general_400.png)

1. Sobre o **Lessonly.com configuração** secção, clique em **configurar Lessonly.com** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_lessonly.com_configure.png)

1. Para configurar o início de sessão único num **Lessonly.com** lado, terá de enviar o transferido **Certificate(Base64)** e **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** para [equipa de suporte de Lessonly.com](mailto:support@lessonly.com).

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [documentação do embedded do Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/lessonly-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/lessonly-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/lessonly-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/lessonly-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="creating-a-lessonlycom-test-user"></a>Criar um utilizador de teste Lessonly.com

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Lessonly.com. Lessonly.com suporta o aprovisionamento de just-in-time, que está por predefinição, ativada.

Não existe nenhum item de ação para nesta secção. Durante uma tentativa de aceder Lessonly.com se não existir ainda será criado um novo utilizador.

> [!NOTE]
> Se precisar de criar manualmente um utilizador, terá de contactar o [equipa de suporte de Lessonly.com](mailto:support@lessonly.com).

### <a name="assigning-the-azure-ad-test-user"></a>Atribuir o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Lessonly.com.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida a Lessonly.com, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Lessonly.com**.

    ![Configurar o início de sessão único](./media/lessonly-tutorial/tutorial_lessonly.com_app.png)

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="testing-single-sign-on"></a>Teste de início de sessão único

O objetivo desta secção é testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Lessonly.com no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Lessonly.com.

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

<!--Image references-->

[1]: ./media/lessonly-tutorial/tutorial_general_01.png
[2]: ./media/lessonly-tutorial/tutorial_general_02.png
[3]: ./media/lessonly-tutorial/tutorial_general_03.png
[4]: ./media/lessonly-tutorial/tutorial_general_04.png

[100]: ./media/lessonly-tutorial/tutorial_general_100.png

[200]: ./media/lessonly-tutorial/tutorial_general_200.png
[201]: ./media/lessonly-tutorial/tutorial_general_201.png
[202]: ./media/lessonly-tutorial/tutorial_general_202.png
[203]: ./media/lessonly-tutorial/tutorial_general_203.png

