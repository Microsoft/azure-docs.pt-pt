---
title: 'Tutorial: Integração do Active Directory do Azure com o SAP HANA | Documentos da Microsoft'
description: Saiba como configurar o início de sessão único entre o Azure Active Directory e o SAP HANA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8def13360bba2f82d5adf315503dc7d655f490d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57899832"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Tutorial: Integração do Active Directory do Azure com o SAP HANA

Neste tutorial, saiba como integrar o SAP HANA no Azure Active Directory (Azure AD).
Integrar o SAP HANA no Azure AD fornece as seguintes vantagens:

* Pode controlar no Azure AD que tenha acesso ao SAP HANA.
* Pode permitir que os utilizadores ser automaticamente sessão iniciada para o SAP HANA (Single Sign-On) com as suas contas do Azure AD.
* Pode gerir as suas contas num local central – portal do Azure.

Se quiser saber mais detalhes sobre a integração de aplicações SaaS com o Azure AD, veja [o que é o acesso a aplicações e início de sessão único com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Se não tiver uma subscrição do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o SAP HANA, terá dos seguintes itens:

- Uma subscrição do Azure
- Uma subscrição de SAP HANA que é o início de sessão único (SSO) ativado
- Uma instância HANA em execução no IaaS qualquer pública, no local, VMS do Azure ou instâncias grandes do SAP no Azure
- A interface da web de administração de XSA, bem como HANA Studio instalado na instância HANA

> [!NOTE]
> Recomendamos que não utilize um ambiente de produção do SAP HANA para testar os passos neste tutorial. Teste a integração primeiro no desenvolvimento ou ambiente de teste do aplicativo e, em seguida, utilize o ambiente de produção.

Para testar os passos neste tutorial, siga as seguintes recomendações:

* Uma subscrição do Azure AD. Se não tiver um ambiente do Azure AD, pode obter a versão de avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/)
* SAP HANA logon único habilitado subscrição

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, configure e teste do Azure AD início de sessão único num ambiente de teste.

* Suporta o SAP HANA **IDP** iniciada SSO
* Suporta o SAP HANA **just-in-time** aprovisionamento de utilizadores

## <a name="adding-sap-hana-from-the-gallery"></a>Adicionando o SAP HANA da Galeria

Para configurar a integração do SAP HANA no Azure AD, terá de adicionar SAP HANA a partir da Galeria à sua lista de aplicações de SaaS geridas.

**Para adicionar o SAP HANA a partir da galeria, execute os seguintes passos:**

1. Na **[portal do Azure](https://portal.azure.com)**, no painel de navegação esquerdo, clique em **Azure Active Directory** ícone.

    ![O botão do Azure Active Directory](common/select-azuread.png)

2. Navegue para **aplicações empresariais** e, em seguida, selecione a **todos os aplicativos** opção.

    ![O painel de aplicações empresariais](common/enterprise-applications.png)

3. Para adicionar nova aplicação, clique em **nova aplicação** botão na parte superior de caixa de diálogo.

    ![O novo botão de aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, escreva **SAP HANA**, selecione **SAP HANA** no painel de resultados, em seguida, clique em **Add** botão para adicionar a aplicação.

     ![SAP HANA na lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar o Azure AD início de sessão único

Nesta secção, configure e teste do Azure AD início de sessão único com o SAP HANA com base num utilizador de teste **Eduarda Almeida**.
Para o início de sessão único funcionar, uma relação de ligação entre um utilizador do Azure AD e o utilizador relacionado no SAP HANA deve ser estabelecido.

Para configurar e testar o Azure AD início de sessão único com o SAP HANA, tem de concluir os seguintes blocos de construção:

1. **[Configurar o Azure AD início de sessão único](#configure-azure-ad-single-sign-on)**  - para permitir que os utilizadores utilizar esta funcionalidade.
2. **[Configurar o SAP HANA início de sessão único](#configure-sap-hana-single-sign-on)**  - para configurar as definições de início de sessão único no lado do aplicativo.
3. **[Criar um utilizador de teste do Azure AD](#create-an-azure-ad-test-user)**  - para testar o Azure AD início de sessão único com Eduarda Almeida.
4. **[Atribua o utilizador de teste do Azure AD](#assign-the-azure-ad-test-user)**  - para ativar a Eduarda Almeida utilizar o Azure AD início de sessão único.
5. **[Criar utilizador de teste de SAP HANA](#create-sap-hana-test-user)**  - para ter um equivalente da Eduarda Almeida no SAP HANA que está ligado à representação de utilizador do Azure AD.
6. **[Testar início de sessão único](#test-single-sign-on)**  - para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o Azure AD início de sessão único

Nesta secção, vai ativar o Azure AD início de sessão único no portal do Azure.

Para configurar o Azure AD início de sessão único com o SAP HANA, execute os seguintes passos:

1. Na [portal do Azure](https://portal.azure.com/), na **SAP HANA** página de integração de aplicações, selecione **início de sessão único**.

    ![Configurar a ligação de início de sessão única](common/select-sso.png)

2. Sobre o **selecionar um método de início de sessão único** caixa de diálogo, selecione **SAML/WS-Fed** modo para ativar o início de sessão único.

    ![Único início de sessão em modo de seleção](common/select-saml-option.png)

3. Sobre o **definir a segurança de início de sessão único com o SAML** página, clique em **editar** ícone para abrir **configuração básica de SAML** caixa de diálogo.

    ![Editar a configuração SAML do básico](common/edit-urls.png)

4. Sobre o **definir a segurança de início de sessão único com o SAML** página, execute os seguintes passos:

    ![SAP HANA domínio e URLs únicas início de sessão em informações](common/idp-intiated.png)

    a. Na **identificador** texto, escreva o seguinte: `HA100`

    b. Na **URL de resposta** caixa de texto, escreva um URL com o seguinte padrão: `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Estes valores não são reais. Atualize estes valores com o identificador real e o URL de resposta. Contacte [equipa de suporte do SAP HANA Client](https://cloudplatform.sap.com/contact.html) obter esses valores. Também pode consultar os padrões mostrados a **configuração básica de SAML** secção no portal do Azure.

5. Aplicação de SAP HANA espera que as asserções SAML num formato específico. Configure as seguintes declarações para esta aplicação. Pode gerir os valores destes atributos do **atributos de utilizador** secção na página de integração de aplicações. Sobre o **definido no início de sessão único com o SAML** página, clique em **editar** botão para abrir **atributos de utilizador** caixa de diálogo.

    ![image](common/edit-attribute.png)

6. No **atributos de utilizador** secção sobre o **atributos de utilizador e afirmações** caixa de diálogo, execute os seguintes passos:
 
    a. Clique em **ícone de edição** para abrir o **afirmações de utilizador de gerir** caixa de diálogo.

    ![image](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Partir do **transformação** lista, selecione **ExtractMailPrefix()**.

    c. Partir do **parâmetro 1** lista, selecione **user.mail**.

    d. Clique em **Guardar**.

7. No **definido no início de sessão único com o SAML** página, além do **certificado de assinatura SAML** secção, clique em **transferir** para transferir o **XML de metadados de Federação**  entre as opções de determinado de acordo com seus requisitos e guarde-o no seu computador.

    ![O link de download de certificado](common/metadataxml.png)

### <a name="configure-sap-hana-single-sign-on"></a>Configurar o SAP HANA início de sessão único

1. Para configurar o início de sessão único no lado do SAP HANA, inicie sessão na sua **consola de Web do HANA XSA** indo para o respetivo ponto final de HTTPS.

    > [!NOTE]
    > Na configuração predefinida, o URL redireciona o pedido para um início de sessão no ecrã, que requer as credenciais de um utilizador de base de dados do SAP HANA autenticado. O utilizador que inicia sessão tem de ter permissões para efetuar tarefas de administração de SAML.

2. Na Interface de Web XSA, aceda a **fornecedor de identidade de SAML**. Em seguida, selecione o **+** botão na parte inferior do ecrã, para apresentar o **adicionar informações do fornecedor de identidade** painel. Em seguida, siga os passos seguintes:

    ![Adicionar fornecedor de identidade](./media/saphana-tutorial/sap1.png)

    a. Na **adicionar informações do fornecedor de identidade** painel, cole o conteúdo do XML de metadados (que transferiu a partir do portal do Azure) na **metadados** caixa.

    ![Adicionar as definições do fornecedor de identidade](./media/saphana-tutorial/sap2.png)

    b. Se o conteúdo do documento XML é válido, o processo de análise extrai as informações que são necessárias para o **assunto, o ID de entidade e o emissor** campos na **dados gerais** área de ecrã. Ele também extrai as informações necessárias para os campos de URL no **destino** ecrã área, por exemplo, o **URL de Base e o URL de SingleSignOn (*)** campos.

    ![Adicionar as definições do fornecedor de identidade](./media/saphana-tutorial/sap3.png)

    c. No **Name** caixa da **dados gerais** área de ecrã, introduza um nome para o novo provedor de identidade de SAML SSO.

    > [!NOTE]
    > O nome do SAML IDP é obrigatório e tem de ser exclusivo. É apresentado na lista de IDPs de SAML disponível que é apresentado quando seleciona SAML como o método de autenticação para aplicações de SAP HANA XS para utilizar. Por exemplo, pode fazê-lo **autenticação** área da ferramenta de administração do artefacto de XS de ecrã.

3. Selecione **guardar** para guardar os detalhes do fornecedor de identidade SAML e adicionar o IDP SAML novo à lista de IDPs de SAML conhecidos.

    ![Botão Guardar](./media/saphana-tutorial/sap4.png)

4. No Studio HANA, nas propriedades do sistema a **Configuration** separador, filtrar as definições por **saml**. Em seguida, ajustar a **assertion_timeout** partir **10 seg.** para **seg 120**.

    ![definição de assertion_timeout](./media/saphana-tutorial/sap7.png)

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

Nesta secção, vai ativar Eduarda Almeida utilizar o Azure início de sessão único, concedendo acesso ao SAP HANA.

1. No portal do Azure, selecione **aplicações empresariais**, selecione **todos os aplicativos**, em seguida, selecione **SAP HANA**.

    ![Painel de aplicações empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, escreva e selecione **SAP HANA**.

    ![A ligação de SAP HANA na lista de aplicações](common/all-applications.png)

3. No menu à esquerda, selecione **utilizadores e grupos**.

    ![A ligação "Utilizadores e grupos"](common/users-groups-blade.png)

4. Clique nas **adicionar utilizador** botão, em seguida, selecione **utilizadores e grupos** no **adicionar atribuição** caixa de diálogo.

    ![O painel Adicionar atribuição](common/add-assign-user.png)

5. Na **utilizadores e grupos** caixa de diálogo select **Eduarda Almeida** na lista de utilizadores, em seguida, clique o **selecionar** na parte inferior do ecrã.

6. Se está esperando a qualquer valor de função a asserção de SAML, em seguida, no **selecionar função** caixa de diálogo selecione a função adequada para o utilizador na lista, em seguida, clique o **selecione** na parte inferior do ecrã.

7. Na **adicionar atribuição** clique da caixa de diálogo a **atribuir** botão.

### <a name="create-sap-hana-test-user"></a>Criar utilizador de teste de SAP HANA

Para ativar a utilizadores do Azure AD iniciar sessão no SAP HANA, tem de aprovisioná-los no SAP HANA.
Suporta o SAP HANA **just-in-time aprovisionamento**, que está por ativada por predefinição.

Se precisar de criar manualmente um utilizador, siga os passos seguintes:

>[!NOTE]
>Pode alterar a autenticação externa que o utilizador utiliza. Eles podem autenticar com um sistema externo, como o Kerberos. Para obter informações detalhadas sobre identidades externas, contacte o seu [administrador de domínio](https://cloudplatform.sap.com/contact.html).

1. Abra o [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) como administrador e, em seguida, ativar o utilizador de DB para SAML SSO.

    ![Criar utilizador](./media/saphana-tutorial/sap5.png)

2. Selecione a caixa de verificação invisível à esquerda da vírgula **SAML**e, em seguida, selecione a **configurar** ligação.

3. Selecione **adicionar** para adicionar o IDP de SAML.  Selecione o IDP SAML apropriado e, em seguida, selecione **OK**.

4. Adicionar a **identidades externas** (no caso, BrittaSimon) ou escolha **qualquer**. Em seguida, selecione **OK**.

   > [!Note]
   > Se o **qualquer** não está selecionada a caixa de verificação, em seguida, o nome de utilizador no HANA tem de corresponder exatamente ao nome do utilizador, o UPN antes do sufixo de domínio. (Por exemplo, BrittaSimon@contoso.com torna-se BrittaSimon no HANA.)

5. Para fins de teste, atribuir todos **XS** funções para o utilizador.

    ![Atribuir funções](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Deve dar permissões que são adequadas para seus casos de uso apenas.

6. Guarde o utilizador.

### <a name="test-single-sign-on"></a>Testar o início de sessão único 

Nesta secção, vai testar a configuração do Azure AD única início de sessão com o painel de acesso.

Quando clica no mosaico de SAP HANA no painel de acesso, deve ser automaticamente conectado para o SAP HANA para o qual configura o SSO. Para obter mais informações sobre o painel de acesso, consulte [introdução ao painel de acesso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos Adicionais

- [Lista de tutoriais sobre como integrar aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)

- [O que é o acesso condicional no Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

