---
title: 'Tutorial: Integração do Active Directory do Azure com fidelidade NetBenefits | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e NetBenefits fidelidade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5855a91873d75701748a454dc8bc83277802c57b
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57783250"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Tutorial: Integração do Active Directory do Azure com fidelidade NetBenefits

Neste tutorial, saiba como integrar NetBenefits de fidelidade com o Azure Active Directory (Azure AD).
Integrar NetBenefits de fidelidade com o Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao NetBenefits fidelidade.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para fidelidade NetBenefits (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com fidelidade NetBenefits, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Fidelidade NetBenefits único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Oferece suporte a fidelidade NetBenefits **IDP** iniciada SSO

* Oferece suporte a fidelidade NetBenefits **Just In Time** aprovisionamento de utilizadores

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Adicionando NetBenefits fidelidade da Galeria

Para configurar a integração do NetBenefits de fidelidade com o Azure AD, terá de adicionar fidelidade NetBenefits a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar fidelidade NetBenefits a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **fidelidade NetBenefits**, selecione **fidelidade NetBenefits** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![Fidelidade NetBenefits na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com NetBenefits de fidelidade com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no fidelidade NetBenefits deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com fidelidade NetBenefits, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar fidelidade NetBenefits início de sessão único](#configure-fidelity-netbenefits-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de fidelidade NetBenefits](#create-fidelity-netbenefits-test-user)**  - para ter um equivalente da Eduarda Almeida na NetBenefits fidelidade que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com fidelidade NetBenefits, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **fidelidade NetBenefits** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![Fidelidade NetBenefits domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL com o seguinte padrão:

    Para o ambiente de teste:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    Para o ambiente de produção:  `urn:sp:fidelity:geninbndnbparts20`

    b. Na **URL de resposta** caixa de texto, escreva um URL que, para ser fornecido pelo fidelidade no momento da implementação ou contacte o seu Gestor de serviço de cliente de fidelidade atribuído.

5. Aplicação de NetBenefits fidelidade espera que as asserções SAML num formato específico, o que requer a adição de mapeamentos de atributos personalizado à sua configuração de atributos de token SAML. Captura de ecrã seguinte mostra a lista de atributos predefinidos, em que **nameidentifier** está mapeada com **user.userprincipalname**. Fidelidade NetBenefits aplicativo espera **nameidentifier** seja mapeado com **employeeid** ou qualquer outra violação é aplicável à sua organização como **nameidentifier**, por isso, precisa editar o mapeamento do atributo ao clicar no **editar** ícone e altere o mapeamento do atributo.

    ![image](common/edit-attribute.png)

    >[!Note]
    >Fidelidade NetBenefits suporta estática e dinâmica de Federação. Estática significa que não utilizará SAML com base just-in-time utilizador aprovisionamento e dinâmica significa que ele oferece suporte apenas no tempo de aprovisionamento de utilizador. Para utilizar o JIT, os clientes de aprovisionamento com base tem de adicionar algumas declarações mais no Azure AD, como a data de nascimento do usuário etc. Esses detalhes são fornecidos pelo seu atribuído **Gestor de serviço de cliente de fidelidade** e têm de ativar esta federação dinâmica para a sua instância.

6. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

7. Sobre o **configurar fidelidade NetBenefits** secção, copie os URLs apropriados de acordo com seus requisitos.

    ![URLs de configuração de cópia](common/copy-configuration-urls.png)

    a. URL de início de sessão

    b. Azure Ad Identifier

    c. URL de fim de sessão

### <a name="configure-fidelity-netbenefits-single-sign-on"></a>Configurar fidelidade NetBenefits Single Sign-On

Para configurar o início de sessão único num **fidelidade NetBenefits** lado, terá de enviar o transferido **XML de metadados de Federação** e adequadas copiados URLs a partir do portal do Azure para [fidelidade Equipa de suporte de NetBenefits](mailto:SSOMaintenance@fmr.com). Se definir esta definição para que a ligação de SAML SSO definidas corretamente em ambos os lados.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso a fidelidade NetBenefits.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **fidelidade NetBenefits**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicações, selecione **fidelidade NetBenefits**.

    ![A ligação de fidelidade NetBenefits na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-fidelity-netbenefits-test-user"></a>Criar utilizador de teste de fidelidade NetBenefits

Nesta secção, vai criar um usuário chamado Eduarda Almeida no NetBenefits fidelidade. Se estiver a criar o Federação estática, trabalhe em conjunto com sua atribuído **Gestor de serviço de cliente de fidelidade** para criar os utilizadores na plataforma de fidelidade NetBenefits. Estes utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único.

Para a Federação dinâmica, os utilizadores são criados com o aprovisionamento de utilizador do Just In Time. Para utilizar o JIT, os clientes de aprovisionamento com base tem de adicionar algumas declarações mais no Azure AD, como a data de nascimento do usuário etc. Esses detalhes são fornecidos pelo seu atribuído **Gestor de serviço de cliente de fidelidade** e têm de ativar esta federação dinâmica para a sua instância.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de fidelidade NetBenefits no painel de acesso, deve ser automaticamente sessão iniciada no NetBenefits fidelidade para a qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

