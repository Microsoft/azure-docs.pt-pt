---
title: 'Tutorial: Integração do Active Directory do Azure com o serviço de segurança de Web do Symantec (WSS) | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o serviço de segurança Web (WSS) da Symantec.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9fdc6bf46cff1f3a38d40a4e7abad5bfe56c47
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877841"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Tutorial: Integração do Active Directory do Azure com o serviço de segurança de Web do Symantec (WSS)

Neste tutorial, ficará a saber como integrar a sua conta de serviço de segurança Web (WSS) da Symantec com a sua conta do Azure Active Directory (Azure AD) para que o WSS pode autenticar um utilizador final aprovisionado no Azure AD através da autenticação SAML e impor o utilizador ou regras de política de nível de grupo.

Integrar o serviço de segurança Web (WSS) da Symantec com o Azure AD fornece as seguintes vantagens:

- Gerir todos os utilizadores finais e grupos utilizados pela sua conta WSS a partir do portal do Azure AD.

- Permitir que os utilizadores finais para se autenticar no WSS através das respetivas credenciais do Azure AD.

- Ativar a imposição de utilizador e grupo de regras de política de nível definidas na sua conta do WSS.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o serviço de segurança de Web do Symantec (WSS), terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* Serviço de segurança Web (WSS) da Symantec único início de sessão na subscrição ativada

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o serviço de segurança Web (WSS) da Symantec **IDP** iniciada SSO

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>Adicionando o serviço de segurança Web (WSS) da Symantec da Galeria

Para configurar a integração do Symantec Web Security Service (WSS) com o Azure AD, terá de Adicionar serviço de segurança de Web do Symantec (WSS) a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar serviço de segurança de Web do Symantec (WSS) a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **serviço de segurança Web (WSS) da Symantec**, selecione **serviço de segurança Web (WSS) da Symantec** no painel de resultados, em seguida, clique em **adicionar** botão para adicionar o aplicação.

     ![Symantec Web Security Service (WSS) na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com Symantec Web Security Service (WSS) com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no serviço de segurança de Web do Symantec (WSS) deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o serviço de segurança de Web do Symantec (WSS), tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **Configurar o serviço de segurança Web (WSS) da Symantec Single Sign-On** - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste do serviço de segurança Web (WSS) da Symantec](#create-symantec-web-security-service-wss-test-user)**  - para ter um equivalente da Eduarda Almeida na segurança de Web da Symantec Service (WSS) que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o serviço de segurança de Web do Symantec (WSS), execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **serviço de segurança Web (WSS) da Symantec** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **configuração básica de SAML** caixa de diálogo, execute os seguintes passos:

    ![URLs de domínio do serviço de segurança Web (WSS) da Symantec e únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** caixa de texto, escreva um URL: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Na **URL de resposta** caixa de texto, escreva um URL: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Contacte [equipa de suporte de cliente do serviço de segurança Web (WSS) da Symantec](https://www.symantec.com/contact-us) f os valores para o **identificador** e **URL de resposta** não estão a funcionar por algum motivo.... Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Configurar o Symantec Security Service (WSS) início de sessão único da Web

Para configurar o início de sessão único no lado do serviço de segurança Web (WSS) da Symantec, consulte a documentação online do WSS. O transferido **XML de metadados de Federação** tem de ser importadas para o portal do WSS. Contacte os [equipa de suporte do serviço de segurança Web (WSS) da Symantec](https://www.symantec.com/contact-us) se precisar de assistência com a configuração no portal do WSS.

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao serviço de segurança de Web da Symantec (WSS).

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **serviço de segurança Web (WSS) da Symantec**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **Symantec Web Security Service (WSS)**.

    ![A ligação de serviço de segurança Web (WSS) da Symantec na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Criar utilizador de teste do serviço de segurança Web (WSS) da Symantec

Nesta secção, vai criar um usuário chamado Eduarda Almeida no serviço de segurança de Web do Symantec (WSS). O nome de utilizador final correspondente pode ser criado manualmente no portal do WSS ou pode aguardar que os utilizadores/grupos aprovisionados no Azure AD para ser sincronizadas com o portal WSS após alguns minutos (~ 15 minutos). Os utilizadores tem de ser criados e ativados antes de utilizar o início de sessão único. O endereço IP público da máquina do usuário final, que será utilizada para procurar os Web sites também tem de ser aprovisionado no portal do serviço de segurança Web (WSS) da Symantec.

> [!NOTE]
> Clique [aqui](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) para obter a sua máquina do IPaddress pública.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, irá testar a funcionalidade de início de sessão única, agora que configurou a sua conta do WSS para utilizar o Azure AD para autenticação de SAML.

Depois de ter configurado seu navegador da web para o tráfego de proxy para o WSS, quando abra o browser e tente navegar para um site, em seguida, será redirecionado para a página de início de sessão do Azure. Introduza as credenciais do utilizador final de teste que foi provisionado no Azure AD (ou seja, BrittaSimon) e associadas a palavra-passe. Uma vez autenticado, poderá visitar o Web site que escolheu. Deve criar uma regra de política no lado do WSS para bloquear BrittaSimon de navegar para um site específico, em seguida, verá a página de bloco do WSS, quando está tentando navegar para esse site como utilizador BrittaSimon.

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

