---
title: 'Tutorial: Integração do Active Directory do Azure com iPass SmartConnect | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b5b39a436ae356cf2543f03db3d6a84d1daa936a
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189500"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Tutorial: Integração do Active Directory do Azure com iPass SmartConnect

Neste tutorial, saiba como integrar iPass SmartConnect com o Azure Active Directory (Azure AD).
Integrar iPass SmartConnect no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao iPass SmartConnect.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para iPass SmartConnect (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com iPass SmartConnect, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* iPass SmartConnect único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* iPass suporta SmartConnect **SP e IDP** iniciada SSO
* iPass suporta SmartConnect **Just In Time** aprovisionamento de utilizadores

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Adicionando iPass SmartConnect da Galeria

Para configurar a integração do iPass SmartConnect com o Azure AD, terá de adicionar iPass SmartConnect a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar iPass SmartConnect a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **iPass SmartConnect**, selecione **iPass SmartConnect** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![iPass SmartConnect na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configurar e testar o Azure AD início de sessão único com iPass SmartConnect com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no iPass SmartConnect deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com iPass SmartConnect, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar iPass SmartConnect Single Sign-On](#configure-ipass-smartconnect-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar iPass utilizador de teste SmartConnect](#create-ipass-smartconnect-test-user)**  - para ter um equivalente da Eduarda Almeida na iPass SmartConnect que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com iPass SmartConnect, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **iPass SmartConnect** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** secção, o utilizador não tem de realizar qualquer passo como a aplicação já está pré-integrada com o Azure.

    ![informações de início de sessão de único iPass SmartConnect domínio e URLs](common/preintegrated.png)

5. Clique em **definir URLs adicionais** e executar o passo seguinte, se desejar configurar a aplicação na **SP** iniciada pelo modo:

    ![informações de início de sessão de único iPass SmartConnect domínio e URLs](common/metadata-upload-additional-signon.png)

    Na **URL de início de sessão** caixa de texto, escreva um URL:  `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. iPass SmartConnect aplicativo espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

7. No **afirmações de utilizador** secção sobre o **atributos de utilizador** caixa de diálogo, editar as afirmações utilizando **ícone de edição** ou adicionar as afirmações utilizando **Adicionar nova afirmação**para configurar o atributo de token SAML conforme mostrado na imagem acima e execute os seguintes passos:

    | Name |  Atributo de origem|
    | ---------------| ----------|
    | firstName | user.givenname |
    | lastName | user.surname |
    | e-mail | user.userprincipalname |
    | o nome de utilizador | user.userprincipalname |
    | | |

    a. Clique em **Adicionar nova afirmação** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Na **nome** caixa de texto, escreva o nome de atributo apresentado para essa linha.

    c. Deixe o **espaço de nomes** em branco.

    d. Selecione a origem de dado **atributo**.

    e. Partir do **atributo de origem** lista, digite o valor de atributo apresentado para essa linha.

    f. Clique em **Ok**

    g. Clique em **Guardar**.

8. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

9. Sobre o **configurar iPass SmartConnect** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Identificador do Azure AD

    c. URL de fim de sessão

### <a name="configure-ipass-smartconnect-single-sign-on"></a>Configurar iPass SmartConnect Single Sign-On

Para configurar o início de sessão único num **iPass SmartConnect** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [iPass SmartConnect equipa de suporte](mailto:help@ipass.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso aos iPass SmartConnect.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **iPass SmartConnect**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **iPass SmartConnect**.

    ![O iPass SmartConnect link na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-ipass-smartconnect-test-user"></a>Criar iPass SmartConnect utilizador de teste

Nesta secção, vai criar um usuário chamado Eduarda Almeida no iPass SmartConnect. Trabalhar com [equipa de suporte de iPass SmartConnect](mailto:help@ipass.com) para adicionar os utilizadores ou o domínio que é necessário para estar incluído na plataforma de SmartConnect iPass. Se o domínio for adicionado pela equipe, os utilizadores serão automaticamente aprovisionados para a plataforma de SmartConnect iPass. Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

### <a name="test-single-sign-on"></a>Testar o início de sessão único

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

**Para testar a aplicação no fluxo SP iniciada, execute os seguintes passos:**

a. Baixe o windows iPass cliente SmartConnect [aqui](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![O iPass SmartConnect link na lista de aplicações](./media/ipasssmartconnect-tutorial/testing3.png)

b. Instale o cliente e o lançamento.

c. Clique em **começar**.

![O iPass SmartConnect link na lista de aplicações](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Introduza o nome de utilizador do Azure com o domínio. Clique em **continuar**. Isso será redirecionado à página de início de sessão do Azure

![O iPass SmartConnect link na lista de aplicações](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Após a autenticação com êxito, será possível iniciar a ativação do cliente. Cliente vai ser ativado.

**Para testar a aplicação no fluxo IdP iniciada, execute os seguintes passos:**

a. Inicie sessão no [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Clique no iPass SmartConnect aplicação.

c. Ele inicia a página de SSA, clique em **transferir aplicações para Windows** instalar iPass SmartConnect cliente.

![O iPass SmartConnect link na lista de aplicações](./media/ipasssmartconnect-tutorial/testing4.png)

d. Depois da instalação, o cliente no primeiro lançamento será automaticamente inicia a ativação depois de aceitar os termos e condições.

e. Se a ativação não for iniciado, clique no botão Ativar na página de SSA para iniciar a ativação.

f. Cliente vai ser ativado.

## <a name="additional-resources"></a>Recursos Adicionais

- [ Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [O que é o acesso a aplicações e início de sessão único com o Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)