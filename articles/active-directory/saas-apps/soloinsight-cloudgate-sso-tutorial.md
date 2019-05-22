---
title: 'Tutorial: Integração do Active Directory do Azure com o SSO Soloinsight CloudGate | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e Soloinsight CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c131f034b4a8b5afaa2556c5c8d053b3e6793b4d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987233"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: Integrar o SSO Soloinsight CloudGate com o Azure Active Directory

Neste tutorial, irá aprender como integrar o SSO Soloinsight CloudGate com o Azure Active Directory (Azure AD). Quando integrar o SSO Soloinsight CloudGate com o Azure AD, pode:

* Controlar no Azure AD que tenha acesso ao Soloinsight CloudGate SSO.
* Permita que os utilizadores ser automaticamente sessão iniciada para Soloinsight CloudGate SSO com as suas contas do Azure AD.
* Gira as suas contas num local central – portal do Azure.

Para saber mais sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Pré-requisitos

Para começar, terá dos seguintes itens:

* Uma subscrição do Azure AD. Se não tiver uma subscrição, pode obter a versão de avaliação gratuita de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).
* SSO Soloinsight CloudGate início de sessão único (SSO) ativado na subscrição.

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configurar e testar o SSO do Azure AD num ambiente de teste. Suporta SSO Soloinsight CloudGate **SP** iniciada SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Adicionando Soloinsight CloudGate SSO da Galeria

Para configurar a integração do Soloinsight CloudGate SSO para o Azure AD, terá de adicionar Soloinsight CloudGate SSO partir da galeria, à sua lista de aplicações de SaaS geridas.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) com uma conta profissional ou escolar ou uma conta pessoal da Microsoft.
1. No painel de navegação esquerdo, selecione o **do Azure Active Directory** serviço.
1. Navegue para **aplicações empresariais** e, em seguida, selecione **todos os aplicativos**.
1. Para adicionar nova aplicação, selecione **nova aplicação**.
1. Na **adicionar a partir da galeria** secção, escreva **Soloinsight CloudGate SSO** na caixa de pesquisa.
1. Selecione **Soloinsight CloudGate SSO** do painel de resultados e, em seguida, adicionar a aplicação. Aguarde alguns segundos enquanto a aplicação é adicionada ao seu inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Configurar e testar o SSO do Azure AD com o SSO de Soloinsight CloudGate com um utilizador de teste **Eduarda Almeida**. Para SSO para funcionar, tem de estabelecer uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no Soloinsight CloudGate SSO.

Para configurar e testar o SSO do Azure AD com o SSO Soloinsight CloudGate, conclua os seguintes blocos de construção:

1. **[Configurar o SSO do Azure AD](#configure-azure-ad-sso)**  para permitir aos utilizadores utilizar esta funcionalidade.
2. **[Configurar o SSO Soloinsight CloudGate](#configure-soloinsight-cloudgate-sso)**  para configurar as definições de SSO no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de SSO Soloinsight CloudGate](#create-soloinsight-cloudgate-sso-test-user)**  ter um equivalente da Eduarda Almeida na SSO Soloinsight CloudGate que está ligado à representação de utilizador do Azure AD.
6. **[Testar o SSO](#test-sso)**  para verificar se a configuração funciona.

### <a name="configure-azure-ad-sso"></a>Configurar o SSO do Azure AD

Siga estes passos para ativar o SSO do Azure AD no portal do Azure.

1. Na [portal do Azure](https://portal.azure.com/), na **Soloinsight CloudGate SSO** página de integração de aplicativo, encontrar o **gerir** secção e selecione **início de sessão único**.
1. Sobre o **selecionar um método de início de sessão único** , selecione **SAML**.
1. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique no ícone de edição/caneta para **configuração básica de SAML** para editar as definições.

   ![Editar a configuração SAML do básico](common/edit-urls.png)

1. Sobre o **configuração básica de SAML** página, introduza os valores para os seguintes campos:

    1. Na **iniciar sessão no URL** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Na **identificador (ID de entidade)** caixa de texto, escreva um URL com o seguinte padrão: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Estes valores não são reais. Atualize estes valores com o início de sessão real URL e o identificador que é explicado posteriormente no **configurar Soloinsight CloudGate SSO Single Sign-On** secção do tutorial.

1. No **definir a segurança de início de sessão único com o SAML** na página a **certificado de assinatura SAML** secção, encontrar **certificado (Base64)** e selecione **transferir** para transferir o certificado e guarde-o no seu computador.

   ![O link de download de certificado](common/certificatebase64.png)

1. Sobre o **configurar o SSO Soloinsight CloudGate** secção, copie o URL adequado com base nos seus requisitos.

   ![URLs de configuração de cópia](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Configure Soloinsight-CloudGate SSO

1. Para automatizar a configuração no Soloinsight CloudGate SSO, tem de instalar **segura de aplicações meu início de sessão da extensão de browser** ao clicar em **instalar a extensão**.

    ![Extensão My apps](common/install-myappssecure-extension.png)

2. Depois de adicionar a extensão para o navegador, clique em **configuração Soloinsight CloudGate SSO** irá direcioná-lo para a aplicação SSO Soloinsight CloudGate. A partir daí, forneça as credenciais de administrador a iniciar sessão em Soloinsight CloudGate SSO. A extensão de navegador automaticamente irá configurar o aplicativo para e automatizar passos 3-8.

    ![Configuração do programa de configuração](common/setup-sso.png)

3. Se quiser configurar o SSO Soloinsight CloudGate manualmente, abra uma nova janela de browser e inicie sessão no site da sua empresa Soloinsight CloudGate SSO como administrador e execute os seguintes passos:

4. Para obter os valores que estão a ser colado no portal do Azure durante a configuração básica de SAML, inicie sessão no Portal Web CloudGate com as suas credenciais, em seguida, acessar as configurações de SSO, que podem ser encontradas no caminho seguinte **home page > Administração > Definições do sistema > geral**.

    ![Definições de CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **URL de consumidor SAML**

    * Copie as ligações disponíveis em relação a **URL de consumidor de Saml** e o **URL de redirecionamento** campos e cole-os no portal do Azure **configuração básica de SAML** na secção  **Identificador (ID de entidade)** e **URL de resposta** campos, respetivamente.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certificado de assinatura SAML**

    * Vá para a origem do ficheiro de certificado (Base64) que foi transferido do portal do Azure certificado de assinatura SAML listas e com o botão direito no mesmo. Escolher **editar com o bloco de notas + +** opção na lista. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Copie o conteúdo no ficheiro de certificado (Base64) bloco de notas + +.

        ![Cópia do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Cole o conteúdo nas definições de SSO de Portal Web CloudGate **certificado** campo e clique no botão Save.

        ![Portal do certificado](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupo predefinido**

    * Selecione **administrador de negócios** na lista pendente da **grupo predefinido** opção no CloudGate Web Portal

        ![Grupo predefinido](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **URL de identificador do AD e o início de sessão**

    * O copiado **URL de início de sessão** do portal do Azure **configurar o SSO Soloinsight CloudGate** as configurações são introduzidos na secção de definições de SSO de Portal Web CloudGate.

    * Colar o **URL de início de sessão** ligação a partir do portal do Azure no CloudGate Web Portal **URL de início de sessão do AD** campo.

    * Colar o **do Azure AD identificador** ligação a partir do portal do Azure no CloudGate Web Portal **identificador AD** campo

        ![Início de sessão do AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Criar um utilizador de teste do Azure AD

Nesta secção, irá criar um utilizador de teste no portal do Azure chamado Eduarda Almeida.

1. No painel à esquerda no portal do Azure, selecione **do Azure Active Directory**, selecione **utilizadores**e, em seguida, selecione **todos os utilizadores**.
1. Selecione **novo utilizador** na parte superior do ecrã.
1. Na **utilizador** propriedades, siga estes passos:
   1. No campo **Nome**, introduza `Britta Simon`.  
   1. Na **nome de utilizador** , insira o username@companydomain.extension. Por exemplo, `BrittaSimon@contoso.com`.
   1. Selecione o **palavra-passe de Show** caixa de verificação e, em seguida, anote o valor que é apresentado na **palavra-passe** caixa.
   1. Clique em **Criar**.

### <a name="assign-the-azure-ad-test-user"></a>Atribua o utilizador de teste do Azure AD

Nesta secção, irá ativar a Eduarda Almeida utilizar o Azure início de sessão único ao conceder acesso para Soloinsight CloudGate SSO.

1. No portal do Azure, selecione **aplicações empresariais**e, em seguida, selecione **todos os aplicativos**.
1. Na lista de aplicações, selecione **Soloinsight CloudGate SSO**.
1. Na página de descrição geral da aplicação, localize a **Manage** secção e selecione **utilizadores e grupos**.

   ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

1. Selecione **adicionar utilizador**, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![A ligação de adicionar utilizador](common/add-assign-user.png)

1. Na **utilizadores e grupos** caixa de diálogo, selecione **Eduarda Almeida** a partir da lista de utilizadores, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Se estiver à espera de qualquer valor de função na asserção de SAML, no **selecionar função** caixa de diálogo, selecione a função adequada para o utilizador a partir da lista e, em seguida, clique nas **selecione** na parte inferior do ecrã.
1. Na **adicionar atribuição** caixa de diálogo, clique nas **atribuir** botão.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Criar utilizador de teste Soloinsight CloudGate SSO

Para criar um utilizador de teste, selecione **funcionários** no menu principal do seu Portal de Web de CloudGate e preencha o formulário de funcionário adicionar novo. O nível de autoridade que está a ser atribuída ao utilizador de teste é **administrador de negócios** clique em **criar** depois de todos os campos obrigatórios são preenchidos.

![Teste de funcionário](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Teste SSO

Ao selecionar o mosaico Soloinsight CloudGate SSO no painel de acesso, deve ser automaticamente sessão iniciada no SSO Soloinsight CloudGate para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)