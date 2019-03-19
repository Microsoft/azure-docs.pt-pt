---
title: 'Tutorial: Integração do Active Directory do Azure com o Suite de BPM Questetra | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78b069e6534d8394de1f9a9fcdf51b871441c386
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57838039"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Tutorial: Integração do Active Directory do Azure com o Suite de BPM Questetra

Neste tutorial, saiba como integrar Questetra BPM Suite com o Azure Active Directory (Azure AD).

Integrar Questetra BPM Suite no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao conjunto de BPM Questetra
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para Questetra Suite de BPM (Single Sign-On) com as suas contas do Azure AD
- Pode gerir as suas contas num local central – portal do Azure

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Suite de BPM Questetra, terá dos seguintes itens:

- Uma subscrição do Azure
- Um conjunto de BPM Questetra logon único habilitado subscrição

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar conjunto de BPM Questetra a partir da Galeria
1. Configurar e testar o Azure AD início de sessão único

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Adicionar conjunto de BPM Questetra a partir da Galeria
Para configurar a integração do Questetra BPM Suite para o Azure AD, terá de adicionar Questetra BPM Suite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar Questetra BPM Suite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![Aplicações][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![Aplicações][3]

1. Na caixa de pesquisa, escreva **Suite de BPM Questetra**, selecione **Questetra BPM Suite** do painel de resultados e clique em **Add** botão para adicionar a aplicação.

    ![Adicionar a partir da Galeria](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único
Nesta secção, configure e teste do Azure AD início de sessão único com o Suite de BPM Questetra com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no conjunto de BPM Questetra a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no pacote de BPM Questetra deve ser estabelecido.

Conjunto de BPM Questetra, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com Questetra BPM Suite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do conjunto de BPM Questetra](#create-a-questetra-bpm-suite-test-user)**  - para ter um equivalente da Eduarda Almeida no Questetra BPM Suite, que está ligado à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo Questetra BPM Suite.

**Para configurar o Azure AD início de sessão único com o Suite de BPM Questetra, execute os seguintes passos:**

1. No portal do Azure, sobre o **Suite de BPM Questetra** página de integração de aplicativo, clique em **início de sessão único**.

    ![Configurar o início de sessão único][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Inicio de sessão baseado em SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

1. Sobre o **Questetra BPM Suite domínio e URLs** secção, execute os seguintes passos:

    ![Secção Questetra BPM Suite domínio e URLs](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.questetra.net/`

    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o URL de início de sessão e o identificador real. Pode obter estes valores a partir **informações de SP** secção no seu **Questetra BPM Suite** site da empresa, que é explicado mais tarde no tutorial ou contacte [suporte Questetra BPM Suite cliente equipe](https://www.questetra.com/contact/). 
 
1. Sobre o **certificado de assinatura SAML** secção, clique em **certificado (Base 64)** e, em seguida, guarde o ficheiro de certificado no seu computador.

    ![Secção do certificado de assinatura SAML](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

1. Clique em **guardar** botão.

    ![Botão Guardar](./media/questetra-bpm-suite-tutorial/tutorial_general_400.png)

1. Sobre o **Questetra BPM Suite Configuration** secção, clique em **configurar conjunto de BPM Questetra** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Seção de configuração de conjunto de BPM Questetra](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

1. Numa janela do browser web diferente, inicie sessão no seu **Suite de BPM Questetra** site da empresa como administrador.

1. No menu na parte superior, clique em **definições do sistema**. 
   
    ![O Azure AD Single Sign-On][10]

1. Para abrir o **SingleSignOnSAML** página, clique em **SSO (SAML)**. 
   
    ![O Azure AD Single Sign-On][11]

1. No seu **Suite de BPM Questetra** da empresa site, no **SP informações** secção, execute os seguintes passos:

    a. Cópia a **URL de ACS**e, em seguida, cole-o no **URL de início de sessão** caixa de texto no **Questetra BPM Suite domínio e URLs** seção do portal do Azure.
    
    b. Copiar o **ID de entidade**e, em seguida, cole-o no **identificador** caixa de texto no **Questetra BPM Suite domínio e URLs** seção do portal do Azure.

1. No seu **Suite de BPM Questetra** da empresa site, execute os seguintes passos: 
   
    ![Configurar o início de sessão único][15]
   
    a. Selecione **ativar o início de sessão único**.
   
    b. Na **ID de entidade** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
    
    c. Na **URL da página de início de sessão** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.
    
    d. Na **URL de página de início de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.
    
    e. Na **formato NameID** caixa de texto, tipo `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Abra sua **Base-64** certificado codificado no bloco de notas transferido a partir do portal do Azure, copie o conteúdo do mesmo para a área de transferência e, em seguida, cole-o no **certificados de validação** caixa de texto. 

    g. Clique em **Guardar**.

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD
O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

![Criar utilizador do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. Na **portal do Azure**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![Criar um utilizador de teste do Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_01.png) 

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos** e clique em **todos os utilizadores**.
    
    ![Criar um utilizador de teste do Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_02.png) 

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior da caixa de diálogo.
 
    ![Criar um utilizador de teste do Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_03.png) 

1. Sobre o **utilizador** caixa de diálogo página, execute os seguintes passos:
 
    ![Criar um utilizador de teste do Azure AD](./media/questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. Na **Name** caixa de texto, tipo **BrittaSimon**.

    b. Na **nome de utilizador** caixa de texto, tipo a **endereço de e-mail** de BrittaSimon.

    c. Selecione **mostrar palavra-passe** e indique o valor da **palavra-passe**.

    d. Clique em **Criar**.
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Criar um utilizador de teste Questetra BPM Suite

O objetivo desta secção é criar um usuário chamado Eduarda Almeida no Questetra BPM Suite.

**Para criar um usuário chamado Eduarda Almeida no conjunto de BPM Questetra, execute os seguintes passos:**

1. Inicie sessão site da sua empresa Questetra BPM Suite como um administrador.
1. Aceda a **definições do sistema > lista de utilizadores > novo utilizador**. 
1. Na caixa de diálogo novo utilizador, execute os seguintes passos: 
   
    ![Criar utilizador de teste][300] 
   
    a. Na **Name** caixa de texto, tipo **nome** do utilizador **britta.simon\@contoso.com**.
   
    b. Na **E-Mail** caixa de texto, tipo **e-mail** do utilizador **britta.simon\@contoso.com**
   
    c. Na **palavra-passe** caixa de texto, escreva um **palavra-passe** do utilizador.
    
    d. Clique em **adicionar novo utilizador**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao Questetra BPM Suite.

![Atribuir utilizador][200] 

**Para atribuir a Eduarda Almeida Questetra BPM Suite, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Suite de BPM Questetra**.

    ![Conjunto de BPM Questetra na lista de aplicações](./media/questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![Atribuir utilizador][202] 

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![Atribuir utilizador][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico Questetra BPM Suite no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo Questetra BPM Suite.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

