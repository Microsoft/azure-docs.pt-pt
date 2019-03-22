---
title: 'Tutorial: Integração do Active Directory do Azure com o Suite de vida de SilkRoad | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e SilkRoad vida Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7f047d784796b9e7c59d95863aa74b3f72da324
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995626"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Integração do Active Directory do Azure com o Suite de vida de SilkRoad

Neste tutorial, saiba como integrar SilkRoad vida Suite com o Azure Active Directory (Azure AD).

Integrar SilkRoad vida Suite no Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso ao conjunto de vida de SilkRoad.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada ao Suite de vida de SilkRoad (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Suite de vida de SilkRoad, terá dos seguintes itens:

- Uma subscrição do Azure
- Um conjunto de vida de SilkRoad início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar conjunto de vida de SilkRoad a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Adicionar conjunto de vida de SilkRoad a partir da Galeria
Para configurar a integração do SilkRoad vida Suite para o Azure AD, terá de adicionar SilkRoad vida Suite a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar SilkRoad vida Suite a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Suite de vida de SilkRoad**, selecione **SilkRoad vida Suite** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Conjunto de vida de SilkRoad na lista de resultados](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o Suite de vida de SilkRoad com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte no conjunto de vida de SilkRoad a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no pacote de vida de SilkRoad deve ser estabelecido.

Conjunto de vida de SilkRoad, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **nome de utilizador** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com SilkRoad vida Suite, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste do pacote de vida de SilkRoad](#create-a-silkroad-life-suite-test-user)**  - para ter um equivalente da Eduarda Almeida no conjunto de vida de SilkRoad que está ligada à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único em seu aplicativo SilkRoad vida Suite.

**Para configurar o Azure AD início de sessão único com o Suite de vida de SilkRoad, execute os seguintes passos:**

1. No portal do Azure, sobre o **SilkRoad vida Suite** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

1. Sobre o **SilkRoad vida Suite domínio e URLs** secção, execute os seguintes passos:

    ![SilkRoad vida Suite domínio e URLs únicas início de sessão em informações](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. Na **URL de início de sessão** caixa de texto, escreva um URL com o seguinte padrão: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: 

    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: 
    
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de início de sessão. Contacte [equipa de suporte de cliente de conjunto de vida de SilkRoad](https://www.silkroad.com/locations/) obter esses valores. 

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
1. Sobre o **SilkRoad vida Suite configuração** secção, clique em **configurar conjunto de vida de SilkRoad** para abrir **configurar início de sessão** janela. Cópia a **URL de fim de sessão, o ID de entidade de SAML e o SAML único início de sessão no URL do serviço** partir o **secção de referência rápida.**

    ![Configuração de conjunto de vida de SilkRoad](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

1. Início de sessão no site da sua empresa SilkRoad como administrador. 
 
    >[!NOTE] 
    > Para obter acesso à aplicação SilkRoad vida Suite autenticação para configurar a Federação com o Microsoft Azure AD, contacte o suporte de SilkRoad ou o seu representante de SilkRoad serviços.

1. Aceda a **fornecedor de serviços**e, em seguida, clique em **detalhes de Federação**. 
   
    ![O Azure AD Single Sign-On][10]

1. Clique em **transferir metadados de Federação**e, em seguida, guarde o ficheiro de metadados no seu computador.
   
    ![O Azure AD Single Sign-On][11] 

1. No seu **SilkRoad** aplicação, clique em **autenticação origens**.
   
    ![O Azure AD Single Sign-On][12] 

1. Clique em **Adicionar origem de autenticação**. 
   
    ![O Azure AD Single Sign-On][13] 

1. Na **Adicionar origem de autenticação** secção, execute os seguintes passos: 
   
    ![O Azure AD Single Sign-On][14]
  
    a. Sob **opção 2 - ficheiro de metadados**, clique em **procurar** para carregar o ficheiro de metadados baixado a partir do portal do Azure.
  
    b. Clique em **fornecedor de identidade criar utilizando dados de ficheiro**.

1. Na **origens de autenticação** secção, clique em **editar**. 
    
     ![O Azure AD Single Sign-On][15] 

1. Sobre o **Editar origem de autenticação** caixa de diálogo, execute os seguintes passos: 
    
     ![O Azure AD Single Sign-On][16] 

    a. Como **Enabled**, selecione **Sim**.

    b. Na **EntityId** caixa de texto, cole o valor de **ID de entidade de SAML** que copiou do portal do Azure.
   
    c. Na **IdP Descrição** caixa de texto, digite uma descrição para a sua configuração (por exemplo: *SSO do Azure AD*).

    d. Na **ficheiro de metadados** caixa de texto, carregamento a **metadados** ficheiro que transferiu a partir do portal do Azure.
  
    e. Na **IdP nome** caixa de texto, escreva um nome específico à sua configuração (por exemplo: *Azure SP*).
  
    f. Na **URL de serviço de fim de sessão** caixa de texto, cole o valor de **URL de fim de sessão** que copiou do portal do Azure.

    g. Na **início de sessão no URL do serviço** caixa de texto, cole o valor de **SAML único início de sessão no URL do serviço** que copiou do portal do Azure.

    h. Clique em **Guardar**.

1. Desative todas as outras origens de autenticação. 
    
     ![O Azure AD Single Sign-On][17]

> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Criar um utilizador de teste SilkRoad vida Suite

Nesta secção, vai criar um usuário chamado Eduarda Almeida no SilkRoad vida Suite. Trabalhar com [equipa de suporte de cliente de conjunto de vida de SilkRoad](https://www.silkroad.com/locations/) para adicionar os utilizadores na plataforma SilkRoad vida Suite. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. 

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso ao SilkRoad vida Suite.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida SilkRoad vida Suite, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Suite de vida de SilkRoad**.

    ![A ligação de SilkRoad vida Suite na lista de aplicações](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Ao clicar no mosaico SilkRoad vida Suite no painel de acesso, deve obter automaticamente sessão iniciada em seu aplicativo SilkRoad vida Suite.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
