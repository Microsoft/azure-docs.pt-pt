---
title: 'Tutorial: Integração do Active Directory do Azure com sistemas de Pega | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e sistemas de Pega.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa3a3dbda3f184b0c2541dc9df6c39afa86458e4
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437637"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Integração do Active Directory do Azure com sistemas de Pega

Neste tutorial, saiba como integrar Pega sistemas com o Azure Active Directory (Azure AD).

Integrar sistemas Pega com o Azure AD fornece as seguintes vantagens:

- Pode controlar no Azure AD que tenha acesso aos sistemas de Pega.
- Pode permitir que os utilizadores automaticamente obter com sessão iniciada para sistemas de Pega (Single Sign-On) com as suas contas do Azure AD.
- Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com sistemas de Pega, terá dos seguintes itens:

- Uma subscrição do Azure
- Um Pega sistemas início de sessão único na subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, recomendamos que não utilize um ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode [obtenha uma avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, vai testar do Azure AD início de sessão único num ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionando sistemas de Pega a partir da Galeria
1. Configuração e teste do Azure AD início de sessão único

## <a name="adding-pega-systems-from-the-gallery"></a>Adicionando sistemas de Pega a partir da Galeria
Para configurar a integração de sistemas de Pega com o Azure AD, terá de adicionar sistemas Pega a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar sistemas Pega a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone. 

    ![O botão do Azure Active Directory][1]

1. Navegue para **aplicações empresariais**. Em seguida, aceda a **todos os aplicativos**.

    ![O painel de aplicações empresariais][2]
    
1. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo][3]

1. Na caixa de pesquisa, escreva **Pega sistemas**, selecione **Pega sistemas** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

    ![Sistemas de pega na lista de resultados](./media/pegasystems-tutorial/tutorial_pegasystems_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com sistemas de Pega com base num utilizador de teste chamado "Eduarda Almeida".

Para o início de sessão único funcione, o Azure AD precisa saber qual é o utilizador de contraparte em sistemas de Pega a um utilizador no Azure AD. Em outras palavras, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado nos sistemas de Pega deve ser estabelecido.

Em sistemas de Pega, atribuir o valor do **nome de utilizador** no Azure AD como o valor do **Username** para estabelecer a relação de ligação.

Para configurar e testar o Azure AD início de sessão único com sistemas de Pega, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
1. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
1. **[Criar um utilizador de teste de sistemas de Pega](#create-a-pega-systems-test-user)**  - para ter um equivalente da Eduarda Almeida em sistemas de Pega que está ligada à representação de utilizador do Azure AD.
1. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
1. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, pode ativar o Azure AD início de sessão único no portal do Azure e configurar início de sessão único na sua aplicação de sistemas de Pega.

**Para configurar o Azure AD início de sessão único com sistemas de Pega, execute os seguintes passos:**

1. No portal do Azure, sobre o **Pega sistemas** página de integração de aplicação, clique em **início de sessão único**.

    ![Configurar a ligação de início de sessão única][4]

1. Sobre o **início de sessão único** caixa de diálogo, selecione **modo** como **baseado em SAML logon** para ativar o início de sessão único.
 
    ![Caixa de diálogo de início de sessão único](./media/pegasystems-tutorial/tutorial_pegasystems_samlbase.png)

1. Sobre o **Pega sistemas de domínio e URLs** secção, execute os seguintes passos, se desejar configurar a aplicação no **IDP** iniciada pelo modo:

    ![Pega sistemas de domínio e URLs únicas início de sessão em informações](./media/pegasystems-tutorial/tutorial_pegasystems_url.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

1. Verifique **Mostrar definições de URL avançadas** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![Pega sistemas de domínio e URLs únicas início de sessão em informações](./media/pegasystems-tutorial/tutorial_pegasystems_url1.png)

    Na **estado de reencaminhamento** caixa de texto, escreva um URL com o seguinte padrão: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`
     
    > [!NOTE] 
    > Estes valores não são reais. Atualize estes valores com o identificador de real, a URL de resposta e o URL de estado de reencaminhamento. Pode encontrar os valores de identificador e o URL de resposta do aplicativo de Pega que é explicado posteriormente neste tutorial. Estado de reencaminhamento, entre em contato [equipa de suporte de cliente de sistemas de Pega](https://www.pega.com/contact-us) para obter o valor. 

1. A aplicação de sistemas de Pega espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Essas declarações são específico para o cliente e depende de seus requisitos. Afirmações opcionais seguintes são exemplo apenas que pode configurar para a sua aplicação. Pode gerir os valores destes atributos da "**atributos de utilizador**" secção na página de integração de aplicações. 

    ![Configurar o início de sessão único](./media/pegasystems-tutorial/tutorial_attribute.png)

1. No **atributos de utilizador** secção sobre o **início de sessão único** caixa de diálogo, configurar o atributo de token de SAML conforme mostrado na imagem anterior e execute os seguintes passos:
    
    | Nome do Atributo | Valor do Atributo |
    | ------------------- | -------------------- |    
    | uid | *********** |
    | CN  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organização | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Grupo de trabalho | *********** |
    | Telefone | *********** |

    > [!NOTE]
    > Estes são os valores específicos do cliente. Forneça os valores adequados.

    a. Clique em **adicionar atributo** para abrir o **adicionar atributo** caixa de diálogo.

    ![Configurar o início de sessão único](./media/pegasystems-tutorial/tutorial_attribute_04.png)

    ![Configurar o início de sessão único](./media/pegasystems-tutorial/tutorial_attribute_05.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Partir do **valor** lista, digite o valor de atributo apresentado para essa linha.
    
    d. Clique em **OK**.

1. Sobre o **certificado de assinatura SAML** secção, clique em **XML de metadados** e, em seguida, guarde o ficheiro de metadados no seu computador.

    ![O link de download de certificado](./media/pegasystems-tutorial/tutorial_pegasystems_certificate.png) 
1. Clique em **guardar** botão.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_general_400.png)
    
1. Para configurar o início de sessão único num **Pega sistemas** lado, abra o **Pega Portal** com a conta de administrador na outra janela do browser.

1. Selecione **crie** -> **SysAdmin** -> **serviço de autenticação**.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
1. Execute as ações seguintes no **criar serviço de autenticação** ecrã:

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Selecione **SAML 2.0** do tipo

    b. Na **nome** caixa de texto, introduza qualquer por exemplo, de nome SSO do Azure AD

    c. Na **breve descrição** caixa de texto, introduza qualquer descrição  

    d. Clique em **criar e abrir** 
    
1. Na **informações do fornecedor de identidade (IdP)** secção, clique em **IdP importar metadados** e procure o ficheiro de metadados que transferiu do portal do Azure. Clique em **submeter** ao carregar os metadados.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
1. Isto irá preencher os dados de IdP, conforme mostrado abaixo.

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
1. Execute as ações seguintes no **definições do fornecedor de serviços (SP)** secção:

    ![Configurar o botão único início de sessão em Guardar](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Copiar o **identificação de entidade** valor e cole do Portal do Azure **identificador** caixa de texto.

    b.  Copiar o **localização do serviço de consumidor de asserção (ACS)** valor e cole do Portal do Azure **URL de resposta** caixa de texto.

    c. Selecione **desativar a solicitação de assinatura**.

1. Clicar em **Guardar**
    
> [!TIP]
> Agora pode ler uma versão concisa destas instruções dentro do [portal do Azure](https://portal.azure.com), enquanto estiver a configurar a aplicação!  Depois de adicionar esta aplicação a partir da **do Active Directory > aplicações empresariais** secção, basta clicar o **Single Sign-On** separador e a documentação do embedded através de acesso a  **Configuração** seção na parte inferior. Pode ler mais sobre a funcionalidade de documentação do embedded aqui: [Documentação do Azure AD incorporado]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

O objetivo desta secção é criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

   ![Criar um utilizador de teste do Azure AD][100]

**Para criar um utilizador de teste no Azure AD, execute os seguintes passos:**

1. No portal do Azure, no painel esquerdo, clique nas **do Azure Active Directory** botão.

    ![O botão do Azure Active Directory](./media/pegasystems-tutorial/create_aaduser_01.png)

1. Para apresentar a lista de utilizadores, aceda a **utilizadores e grupos**e, em seguida, clique em **todos os utilizadores**.

    !["Os utilizadores e grupos" e os links de "Todos os utilizadores"](./media/pegasystems-tutorial/create_aaduser_02.png)

1. Para abrir o **usuário** caixa de diálogo, clique em **Add** na parte superior a **todos os utilizadores** caixa de diálogo.

    ![Botão Adicionar](./media/pegasystems-tutorial/create_aaduser_03.png)

1. Na **utilizador** diálogo caixa, execute os seguintes passos:

    ![A caixa de diálogo de utilizador](./media/pegasystems-tutorial/create_aaduser_04.png)

    a. Na **Name** , escreva **BrittaSimon**.

    b. Na **nome de utilizador** , escreva o endereço de e-mail do utilizador Eduarda Almeida.

    c. Selecione o **mostrar palavra-passe** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.

    d. Clique em **Criar**.
 
### <a name="create-a-pega-systems-test-user"></a>Criar um utilizador de teste de sistemas de Pega

O objetivo desta secção é criar um utilizador chamado Eduarda Almeida em sistemas de Pega. Trabalhe em conjunto com [equipa de suporte de cliente de sistemas de Pega](https://www.pega.com/contact-us) criar utilizadores no Pega Sysyems.


### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos sistemas de Pega.

![Atribuir a função de utilizador][200] 

**Para atribuir a Eduarda Almeida a sistemas de Pega, execute os seguintes passos:**

1. No portal do Azure, abra a vista de aplicativos e, em seguida, navegue para a vista de diretório e aceda a **aplicações empresariais** , em seguida, clique em **todos os aplicativos**.

    ![Atribuir utilizador][201] 

1. Na lista de aplicações, selecione **Pega sistemas**.

    ![A ligação de sistemas de Pega na lista de aplicações](./media/pegasystems-tutorial/tutorial_pegasystems_app.png)  

1. No menu à esquerda, clique em **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"][202]

1. Clique em **adicionar** botão. Em seguida, selecione **utilizadores e grupos** nos **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição][203]

1. No **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** na lista utilizadores.

1. Clique em **selecionar** botão **utilizadores e grupos** caixa de diálogo.

1. Clique em **atribua** botão **adicionar atribuição** caixa de diálogo.
    
### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de sistemas de Pega no painel de acesso, deve obter automaticamente com sessão iniciada para a sua aplicação de sistemas de Pega.
Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)



<!--Image references-->

[1]: ./media/pegasystems-tutorial/tutorial_general_01.png
[2]: ./media/pegasystems-tutorial/tutorial_general_02.png
[3]: ./media/pegasystems-tutorial/tutorial_general_03.png
[4]: ./media/pegasystems-tutorial/tutorial_general_04.png

[100]: ./media/pegasystems-tutorial/tutorial_general_100.png

[200]: ./media/pegasystems-tutorial/tutorial_general_200.png
[201]: ./media/pegasystems-tutorial/tutorial_general_201.png
[202]: ./media/pegasystems-tutorial/tutorial_general_202.png
[203]: ./media/pegasystems-tutorial/tutorial_general_203.png

